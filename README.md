# Финальный проект по курсу "Автоматическая обработка естественного языка"

## 1. Состав команды

* Полина Черноморченко
* Полина Машковцева
* Татьяна Перевощикова
* Александра Шахнова

## 2. Ход работы

На выделение аспектов проводилось два эксперимента, на определение тональности по аспектам - три. Итоговые файлы - результат работы модели/метода с лушими метриками на dev-корпусе.

## 3. Эксперименты с выделением аспектов

### 3.1. CRF

* *краткое описание:* тренировочный корпус приводится к формату BIO, выделяются признаки word.lower(), word.isupper(), POS и BOS (является ли слово началом предложения). дальнейшие эксперименты показали, что добавление признаков не вело к хоть сколько-нибудь значительному приросту качества. на размеченном и преобразованном корпусе обучался Averaged Perceptron для Conditional Random Field (изменение дефолтных параметров обучения только ухудшало качество, поэтому оставлены дефолтные).
* *мотивация:* задачу выделения аспектов, как и любую задачу выделения сущностей, легко свести к обучению модели на разметке BIO, и для этого традиционно используется CRF, т.к. на всем корпусе у него получаются хорошие показатели.
* *оценка:* accuracy 0.9 на тегах, считая О (из трейн-тест сплита); full match precision 0.79; full category accuracy 0.77
* *об ошибках:* судя по отчету из [тетрадки](https://github.com/pmashkovtseva/hse-4-nlp-final-project/blob/main/aspects/crf/apects_crf_train.ipynb), а именно большой разнице между accuracy и f1 для почти всех категорий, кроме *О* (особенно *I-*), главная проблема - в недообучении. модели было дано достаточно О-примеров, и с ними она справляется хорошо, однако со всеми остальными есть заметные проблемы.
* *файлы:* [обучение](https://github.com/pmashkovtseva/hse-4-nlp-final-project/blob/main/aspects/crf/apects_crf_train.ipynb), [модель](https://github.com/pmashkovtseva/hse-4-nlp-final-project/blob/main/aspects/crf/crf.sav), [оценка качества](https://github.com/pmashkovtseva/hse-4-nlp-final-project/blob/main/aspects/crf/aspects_crf_test.ipynb)

### 3.2. LSTM

* *краткое описание:* тренировочный корпус приводится к формату BIO, выделяются признаки word.lower(), POS и BOS. Берётся модель двунаправленного LSTM с предобученными эмбеддингами fasttext
* *мотивация:* задачу выделения аспектов, как и любую задачу выделения сущностей, легко свести к обучению модели на разметке BIO; кроме того, использование эмбеддингов может помочь модели использовать информацию о предложении и токенах в нем
* *оценка:*
* *об ошибках:*
* *файлы:* [тетрадка](https://github.com/pmashkovtseva/hse-4-nlp-final-project/blob/main/aspects/crf/lstm/aspect.ipynb)

## 4. Эксперименты с определением тональности

### 4.1. BERT с файн-тюнингом

* *краткое описание:* дообучение sberbank-ai/ruBert-base на задаче классификации последовательностей вида "@q@ [aspect expression] @q@ [review text]"
* *мотивация:* данный метод был выбран, поскольку в нашем распоряжении имелось значительное количество данных
* *оценка:* accuracy 0.91
* *об ошибках:* (скорее feature work) - возможно, увеличение train'a аугментацией повысило бы результирующее качество
* *файлы:* [обучение](https://github.com/pmashkovtseva/hse-4-nlp-final-project/blob/main/sentiment/bert/4.1/ab-project-asp-sent-train.ipynb), [модель](https://drive.google.com/drive/folders/1NtrsdLmdyGRZKDeJoTePSX7Fh2bxdw6f), [оценка на тестовых данных](https://github.com/pmashkovtseva/hse-4-nlp-final-project/blob/main/sentiment/bert/4.1/ab_asp_sent_testing.ipynb)

### 4.2. Использование тонального словаря

* *краткое описание:* разметка тональности аспектов с использованием тонального словаря [Картаслов](https://github.com/dkulagin/kartaslov/tree/master/dataset/kartaslovsent)
* *мотивация:* один из классических методов определения тональности.
* *оценка:* 0.29 (на аспектах из золотого стандарта)
* *об ошибках:* метод, кажется, совсем не подходящим для данной задачи, поскольку имеет большое количество сложностей:
    * в словаре нет необходимой нам разметки "both" или ей подобной, поэтому разметка аспектов осложняется;
    * если смотреть на value слова в словаре, то мы никак не будем различать "neutral" и "both»;
    * словарь не контекстно зависимый, поэтому многие оценки очевидно не соответствуют нашим данным. Так, например, прилагательное "высокий" имеет нейтральную оценку, а количество голосов за положительную оценку опережает количество за отрицательную. Однако, в нашем случае "высокая цена" скорее подразумевает отрицательную оценку, а "высокий уровень обслуживания" - положительную;
    * Аспекты состоят больше чем из одного слова, и не совсем понятно, как расчитывать тональность в данном случае;
    * В связи со всем перечисленным и очень низкой точностью было принято решение оставить этот метод.
(см. также тетрадку)
* *файлы:* [скрипт и оценка качества](https://github.com/pmashkovtseva/hse-4-nlp-final-project/blob/main/sentiment/dictionary/kartaslov.ipynb)

### 4.3 Еще немного экспериментов с BERT
* *краткое описание:* дообучение двух моделей (cointegrated/rubert-tiny и sberbank-ai/ruBert-base) с маскированием упоминания аспекта в отзывах с [MASK]
* *мотивация:* достаточное количество данных для задачи
* *оценка:* 0.69(cointegrated/rubert-tiny), 0.84(sberbank-ai/ruBert-base)
* *об ошибках:* более низкая точность у первой модели объясняется ее меньшим размером по сравнению со второй моделью; возможно, сохранение упоминания аспекта при обучении, а не полная замена его маской, дало бы более высокий результат, что доказывает файн-тюнинг BERT из 4.1. 
* файлы: [тетрадка](https://github.com/pmashkovtseva/hse-4-nlp-final-project/blob/main/sentiment/bert/4.2/bert_fn_2models.ipynb)

## 5. Определение общей тональности

* *краткое описание:* категории приписывается самый частый класс для ее эксплицитных аспектов, в случае равного количества приписывается более полярная метрика (neutral vs negative -> negative)
* *мотивация:* кажется, самый интуитивный способ выполнения подобной задачи - составить общую картину из частных проявлений.
* *файлы:* [сравнение и оценка качества](https://github.com/pmashkovtseva/hse-4-nlp-final-project/blob/main/results/ab_cats_count.ipynb)

## 6. Итоговые файлы

1. [разметка аспектов](https://github.com/pmashkovtseva/hse-4-nlp-final-project/blob/main/results/aspects_%D1%81rf.txt)
2. [разметка тональности по категориям](https://github.com/pmashkovtseva/hse-4-nlp-final-project/blob/main/results/ab_dev_cats.txt)


## 7. Распределение обязанностей

Полина Черноморченко - BERT 4.1, определение общей тональности

Полина Машковцева - CRF, заполнение отчета

Татьяна Перевощикова - тональный словарь, BERT 4.3

Александра Шахнова - LSTM

Все - обсуждение данных, методов, метрик, ошибок и способов улучшения моделей
