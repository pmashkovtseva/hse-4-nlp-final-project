# Финальный проект по курсу "Автоматическая обработка естественного языка"

## 1. Состав команды

* Полина Черноморченко
* Полина Машковцева
* Татьяна Перевощикова
* Александра Шахнова

## 2. Ход работы

На каждую из двух задач (выделение аспектов и определение тональности) проводилось по два эксперимента. Итоговые файлы - результат работы модели/метода с лушими метриками на dev-корпусе.

## 3. Эксперименты с выделением аспектов

### 3.1. CRF

* *краткое описание:*
* *мотивация:*
* *оценка:* accuracy 0.9 на тегах, считая О
* *об ошибках:*
* *файлы:*

### 3.2. LSTM

* *краткое описание:*
* *мотивация:*
* *оценка:*
* *об ошибках:*
* *файлы:*

## 4. Эксперименты с определением тональности

### 4.1. BERT с файн-тюнингом

* *краткое описание:* дообучение sberbank-ai/ruBert-base на задаче классификации последовательностей вида "@q@ [aspect expression] @q@ [review text]"
* *мотивация:* данный метод был выбран, поскольку в нашем распоряжении имелось значительное количество данных
* *оценка:* accuracy 0.91
* *об ошибках:* (скорее feature work) - возможно, увеличение train'a аугментацией повысило бы результирующее качество
* *файлы:* [обучение](https://github.com/pmashkovtseva/hse-4-nlp-final-project/blob/main/sentiment/bert/ab-project-asp-sent-train.ipynb), [модель](https://drive.google.com/drive/folders/1NtrsdLmdyGRZKDeJoTePSX7Fh2bxdw6f), [оценка на тестовых данных](https://github.com/pmashkovtseva/hse-4-nlp-final-project/blob/main/sentiment/bert/ab_asp_sent_testing.ipynb)

### 4.2.

* *краткое описание:*
* *мотивация:*
* *оценка:*
* *об ошибках:*
* *файлы:*

## 5. Определение общей тональности

* *краткое описание:* категории приписывается самый частый класс для ее эксплицитных аспектов, в случае равного количества приписывается более полярная метрика (neutral vs negative -> negative)
* *мотивация:* кажется, самый интуитивный способ выполнения подобной задачи - составить общую картину из частных проявлений.
* *файлы:* [сравнение и оценка качества](https://github.com/pmashkovtseva/hse-4-nlp-final-project/blob/main/results/ab_cats_count.ipynb)

## 6. Итоговые файлы

1.
2. [разметка тональности по категориям](https://github.com/pmashkovtseva/hse-4-nlp-final-project/blob/main/results/ab_dev_cats.txt)
