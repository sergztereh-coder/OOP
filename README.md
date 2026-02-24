from statistics import mean

# Класс Student
class Student:
    def __init__(self, name, surname, gender):
        self.name = name
        self.surname = surname
        self.gender = gender
        self.finished_courses = []
        self.courses_in_progress = []
        self.grades = {}  # Хранит оценки в формате {"название курса": [оценки]}

    def average_grade(self):
        """Вычисляет среднюю оценку студента."""
        all_grades = [grade for grades_list in self.grades.values() for grade in grades_list]
        return round(mean(all_grades), 1) if all_grades else None

    def __str__(self):
        """Представление строки студента."""
        avg_grade = self.average_grade()
        courses_in_progress_str = ', '.join(self.courses_in_progress)
        finished_courses_str = ', '.join(self.finished_courses)
        return (
            f'Имя: {self.name}\n'
            f'Фамилия: {self.surname}\n'
            f'Средняя оценка за домашние задания: {avg_grade}\n'
            f'Курсы в процессе изучения: {courses_in_progress_str}\n'
            f'Завершенные курсы: {finished_courses_str}'
        )

    def __lt__(self, other):
        """Определяет порядок сравнения по среднему баллу (<)."""
        if isinstance(other, Student):
            return self.average_grade() < other.average_grade()
        return NotImplemented

    def __eq__(self, other):
        """Проверяет равенство по среднему баллу."""
        if isinstance(other, Student):
            return self.average_grade() == other.average_grade()
        return NotImplemented

    def rate_lecture(self, lecturer, course, grade):
        """Метод позволяет студенту оценить преподавателя."""
        if not isinstance(lecturer, Lecturer):
            print("Можно оценивать только преподавателей!")
            return 'Ошибка'
        
        if course not in lecturer.courses_attached or course not in self.courses_in_progress:
            print(f"Курс {course} не соответствует условиям.")
            return 'Ошибка'
        
        if course in lecturer.grades:
            lecturer.grades[course].append(grade)
        else:
            lecturer.grades[course] = [grade]

# Базовый класс Mentor
class Mentor:
    def __init__(self, name, surname):
        self.name = name
        self.surname = surname
        self.courses_attached = []  # Курсы, прикрепленные к ментору

# Производный класс Lecturer
class Lecturer(Mentor):
    def __init__(self, name, surname):
        super().__init__(name, surname)
        self.grades = {}  # Оценки лекций от студентов

    def average_grade(self):
        """Вычисляет средний рейтинг лектора."""
        all_grades = [grade for grades_list in self.grades.values() for grade in grades_list]
        return round(mean(all_grades), 1) if all_grades else None

    def __str__(self):
        """Представление строки лектора."""
        avg_grade = self.average_grade()
        return f'Имя: {self.name}\nФамилия: {self.surname}\nСредняя оценка за лекции: {avg_grade}'

    def __lt__(self, other):
        """Определяет порядок сравнения по среднему баллу (<)."""
        if isinstance(other, Lecturer):
            return self.average_grade() < other.average_grade()
        return NotImplemented

    def __eq__(self, other):
        """Проверяет равенство по среднему баллу."""
        if isinstance(other, Lecturer):
            return self.average_grade() == other.average_grade()
        return NotImplemented

# Производный класс Reviewer
class Reviewer(Mentor):
    def __str__(self):
        """Представление строки ревьюера."""
        return f'Имя: {self.name}\nФамилия: {self.surname}'

    def rate_hw(self, student, course, grade):
        """Метод позволяет ревьюеру поставить оценку за домашнее задание студенту."""
        if isinstance(student, Student) and course in self.courses_attached and course in student.courses_in_progress:
            if course in student.grades:
                student.grades[course].append(grade)
            else:
                student.grades[course] = [grade]
        else:
            return 'Ошибка'

# Функция для подсчета средней оценки студентов по курсу
def calculate_students_avg_grade(students, course_name):
    """
    Рассчитывает среднюю оценку студентов по указанному курсу.
    :param students: Список студентов
    :param course_name: Название курса
    :return: Среднее значение или None
    """
    grades = []
    for student in students:
        if course_name in student.grades:
            grades.extend(student.grades.get(course_name))
    
    return round(mean(grades), 1) if grades else None


# Функция для подсчета средней оценки лекторов по курсу
def calculate_lecturers_avg_grade(lecturers, course_name):
    """
    Рассчитывает среднюю оценку лекторов по указанному курсу.
    :param lecturers: Список лекторов
    :param course_name: Название курса
    :return: Среднее значение или None
    """
    grades = []
    for lecturer in lecturers:
        if course_name in lecturer.grades:
            grades.extend(lecturer.grades.get(course_name))
    
    return round(mean(grades), 1) if grades else None


# Основной блок программы
if __name__ == "__main__":
    # Создаем два экземпляра класса Student
    student1 = Student('Иван', 'Иванов', 'Мужской')
    student2 = Student('Анна', 'Петрова', 'Женский')

    # Назначаем курсы студентам
    student1.courses_in_progress.extend(['Python', 'Java'])
    student2.courses_in_progress.extend(['Python', 'C++'])

    # Создаем два экземпляра класса Reviewer
    reviewer1 = Reviewer('Сергей', 'Сергеев')
    reviewer2 = Reviewer('Елена', 'Васильева')

    # Прикрепляем курсы к рецензентам
    reviewer1.courses_attached.append('Python')
    reviewer2.courses_attached.append('Java')

    # Создаем два экземпляра класса Lecturer
    lecturer1 = Lecturer('Михаил', 'Николаев')
    lecturer2 = Lecturer('Ольга', 'Александрова')

    # Назначаем курсы преподавателям
    lecturer1.courses_attached.extend(['Python', 'Java'])
    lecturer2.courses_attached.extend(['Python', 'C++'])

    # Рецензенты ставят оценки за домашнюю работу
    reviewer1.rate_hw(student1, 'Python', 8)
    reviewer2.rate_hw(student2, 'Java', 9)

    # Студенты оценивают лекции
    student1.rate_lecture(lecturer1, 'Python', 9)
    student2.rate_lecture(lecturer2, 'C++', 8)

    # Вычислим средние оценки студентов по курсу Python
    students = [student1, student2]
    avg_student_grade_python = calculate_students_avg_grade(students, 'Python')
    print(f"Средняя оценка студентов по курсу Python: {avg_student_grade_python}")

    # Вычислим среднюю оценку лекторов по курсу Python
    lecturers = [lecturer1, lecturer2]
    avg_lecturer_grade_python = calculate_lecturers_avg_grade(lecturers, 'Python')
    print(f"Средняя оценка лекторов по курсу Python: {avg_lecturer_grade_python}")

    # Дополнительные оценки для тестирования
    reviewer1.rate_hw(student1, 'Python', 9)
    reviewer2.rate_hw(student2, 'Java', 8)

    # Повторный расчет средних оценок
    print(student1.average_grade())
    print(student2.average_grade())

    # Дополним оценки за лекции
    student1.rate_lecture(lecturer1, 'Python', 10)
    student2.rate_lecture(lecturer2, 'C++', 9)

    # Повторно выводим общую статистику
    print(calculate_lecturers_avg_grade([lecturer1], 'Python'))
    print(calculate_students_avg_grade([student1, student2], 'Python'))