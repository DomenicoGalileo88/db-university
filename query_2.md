QUERY GROUP BY:
- Contare quanti iscritti ci sono stati ogni anno

SELECT COUNT(`id`), YEAR(`enrolment_date`) 
FROM `students` 
GROUP BY YEAR(`enrolment_date`);

- Contare gli insegnanti che hanno l'ufficio nello stesso edificio

SELECT COUNT(`id`) AS `teachers_number`, `office_address` 
FROM `teachers` 
GROUP BY `office_address`;

- Calcolare la media dei voti di ogni appello d'esame

SELECT AVG(`vote`), `exam_id`
FROM `exam_student`
GROUP BY `exam_id`;

- Contare quanti corsi di laurea ci sono per ogni dipartimento

SELECT DISTINCT COUNT(`department_id`), `name` FROM `degrees` GROUP BY `name`;

QUERY JOINS:
- Selezionare tutti gli studenti iscritti al Corso di Laurea in Economia

SELECT DISTINCT `students`. `id`, `students`. `name`,`students`. `surname`, `degrees`. `name`
FROM `students`
JOIN `degrees`
ON `degrees`. `id` = `students`. `degree_id`
WHERE `degrees`. `name` = 'Corso di Laurea in Economia';

- Selezionare tutti i Corsi di Laurea Magistrale del Dipartimento di Neuroscienze

SELECT `degrees`. `id`, `degrees`. `name`, `degrees`. `level`, `departments`. `name`
FROM `degrees`
JOIN `departments` ON `degrees`. `department_id` = `departments`. `id`
WHERE `degrees`. `level` = 'magistrale' AND `departments`. `name` = 'Dipartimento di Neuroscienze';

- Selezionare tutti i corsi in cui insegna Fulvio Amato (id=44)

SELECT `courses`. `name`, `courses`. `description`,`courses`. `period`, `course_teacher`. `teacher_id`
FROM `courses`
JOIN `course_teacher` ON `courses`. `id` = `course_teacher`. `course_id`
WHERE `course_teacher`. `teacher_id` = 44;

- Selezionare tutti gli studenti con i dati relativi al corso di laurea a cui sono iscritti e il relativo dipartimento, in ordine alfabetico per cognome e nome

SELECT `students`. `surname` AS `student_surname`, `students`. `name` AS `student_name`, `degrees`. `name` AS `degree_name`, `degrees`. `level`, `degrees`. `address`, `degrees`. `email`, `degrees`. `website`, `departments`. `name` AS `department_name`
FROM `students`
LEFT JOIN `degrees` ON `students`. `degree_id` = `degrees`. `id`
LEFT JOIN `departments` ON `degrees`. `department_id` = `departments`. `id`
ORDER BY `students`. `surname`, `students`. `name` ASC;

- Selezionare tutti i corsi di laurea con i relativi corsi e insegnanti

SELECT `degrees`. `name` AS `degree_name`, `courses`. `name` AS `course_name`, `courses`. `period`, `courses`. `cfu`, `teachers`. `name` AS `teacher_name`, `teachers`. `surname` AS `teacher_surname`
FROM `degrees`
JOIN `courses` ON `courses`. `degree_id` = `degrees`. `id`
JOIN `teachers` ON `course_teacher`. `teacher_id` = `teachers`. `id`
JOIN `course_teacher` ON `course_teacher`. `course_id` = `courses`. `id`;

- Selezionare tutti i docenti che insegnano nel Dipartimento di Matematica (54)

SELECT `teachers`. `name` AS `teacher_name`, `teachers`. `surname` AS `teacher_surname`, `departments`. `name` AS `department_name`
FROM `teachers`
JOIN `course_teacher` ON `course_teacher`. `teacher_id` = `teachers`. `id`
JOIN `courses` ON `course_teacher`. `course_id` = `courses`. `id`
JOIN `degrees` ON `degrees`. `id` = `courses`. `degree_id`
JOIN `departments` ON `departments`. `id` = `degrees`. `department_id`
WHERE `departments`. `name` = 'Dipartimento di Matematica'

- BONUS: Selezionare per ogni studente quanti tentativi d’esame ha sostenuto per superare ciascuno dei suoi esami

SELECT `students`.`name`, `students`.`surname`, `courses`.`name` AS `course`, COUNT(*) AS `attempts`
FROM `students`
JOIN `exam_student` ON `students`.`id` = `exam_student`.`student_id`
JOIN `exams` ON `exams`.`id` = `exam_student`.`exam_id`
JOIN `courses` ON `exams`.`course_id` = `courses`.`id`
GROUP BY `courses`.`id`, `students`.`id`