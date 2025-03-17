import argparse
import random
from django.core.exceptions import MultipleObjectsReturned, ObjectDoesNotExist
from datacenter.models import Schoolkid, Mark, Chastisement, Lesson, Commendation, Subject


def get_schoolkid(full_name):
    try:
        child = Schoolkid.objects.get(full_name=full_name)
        print(f"Ученик найден: {child.full_name}")
        return child
    except MultipleObjectsReturned:
        print(f"Найдено несколько учеников с именем '{full_name}'. Пожалуйста, уточните запрос.")
    except ObjectDoesNotExist:
        print(f"Ученик с именем '{full_name}' не найден.")



def fix_marks(child):
    bad_marks = Mark.objects.filter(schoolkid=child, points__in=[2,3])
    for mark in bad_marks:
        mark.points = 5
        mark.save()
    print(f"Оценки ученика {child.full_name} исправлены.")


def remove_chastisements(child):
    chastisements = Chastisement.objects.filter(schoolkid=child)
    chastisements.delete()
    print(f"Замечания ученика {child.full_name} удалены.")



def create_commendation(child, subject_name):
    year_of_study = child.year_of_study
    group_letter = child.group_letter
    unique_subject = Subject.objects.filter(title=subject_name, year_of_study=year_of_study).first()
    if not unique_subject:
        print(f"Предмет '{subject_name}' не найден. Проверьте правильность написания.")
        return
    unique_lessons = Lesson.objects.filter(year_of_study=year_of_study, group_letter=group_letter, subject=unique_subject).order_by('-date')
    last_lesson = unique_lessons.first()
    if not last_lesson:
        print(f"Уроки для предмета '{subject_name}' не найдены.")
        return
    commendation_texts = ["Отличная работа!",
                          "Ты настоящая звезда на уроках!",
                          "Твои знания впечатляют! Продолжай в том же духе!",
                          "Ты растешь над собой!",
                          "Твой талант просто неоспорим!",
                          "Я вижу, как ты стараешся!",
                          "Замечательно!",
                          "Я горжусь тобой!"
                          ]
    commendation_text = random.choice(commendation_texts)
    Commendation.objects.create(
        text=commendation_text,
        schoolkid=child,
        teacher=last_lesson.teacher,
        subject=last_lesson.subject,
        created=last_lesson.date
    )
    print(f"Похвала добавлена для ученика {child.full_name} по предмету '{subject_name}'.")


def main():
    parser = argparse.ArgumentParser(description='Управление записями учеников.')
    parser.add_argument('full_name', type=str, help='Полное имя ученика')
    parser.add_argument('--subject', type=str, help='Название предмета для похвалы')
    parser.add_argument('--fix_marks', action='store_true', help='Исправить плохие оценки')
    parser.add_argument('--remove_chastisements', action='store_true', help='Удалить замечания')

    args = parser.parse_args()

    if not args.full_name.strip():
        print("Ошибка: Полное имя ученика не может быть пустым.")
        return

    child = get_schoolkid(args.full_name)

    if child:
        if args.fix_marks:
            fix_marks(child)
        if args.remove_chastisements:
            remove_chastisements(child)
        if args.subject:
            if not args.subject.strip():
                print("Ошибка: Название предмета не может быть пустым.")
                return
            create_commendation(child, args.subject)


if __name__=="__main__":
    main()



