# Поисковая система Jive
# CROC's search system "JIVE"


### Этот проект - результат работы в летней IT школе КРОК'a 

## Проблема:

В КРОКе сейчас есть внутренняя корпоративная социальная сеть, Jive. Внутри нее можно общаться с другими людьми, публиковать свои записи, организовываться в группы. Можно ставить лайки друг другу, получать внутреннюю валюту, "бобриков", смотреть очереди в столовую. Но самое главное, там есть некоторый набор документов-инструкций по работе с этой системой. 


Проблема в том, что для поиска документов внутри Jive'а используется дефолтный поиск по четким словам. Иногда он даже не находил документы, хотя они точно были.

## Решение:

Для решения этой проблемы я использовал вот такую [библиотеку](https://github.com/epfml/sent2vec "sent2vec"), которая является надстройкой над [библиотекой Facebook'a](https://github.com/facebookresearch/fastText "fastText")

Суть в том, что кроме документов инструкций было выгружено 200 обсуждений с форума Jive'a. 

Я обучил модель на этих обсуждениях. ~~Да, да, всего 200 штук~~ 

После чего вычислил вектора для каждого документа.

Теперь, когда пользователь делает поисковый запрос в систему, он обрабатывается, переводится в вектор с помощью модели, после чего считается косинусное расстояние от поискового вектора до векторов документов. Пользователю выводятся ближайшие три документа.

### Подробнее:

0. * Пререквизиты:

* Нужно скачать [sent2vec](https://github.com/epfml/sent2vec "sent2vec")
* Убедиться, что все грязные файлы помещены в *./Dirty/Data* и *./Dirty/Discussions*


1. * Подготовка:

* Для начала работы с системой, следует запустить файл ModelCreate.py

* Внутри него сначала создадутся необходимые каталоги, вот так: ![file structure](https://github.com/MaximArtemev/Croc_search_module/blob/master/File%20structure.png?raw=true)

* Затем будут очищены все файлы из *./Dirty* и помещены в *./Clean/*

* В файл *./Technical/test.txt* будут записаны все документы, все слова которых нормализованы. 

* В файл *./Technical/train.txt* будут записаны все обсуждения, все слова которых нормализованы.

2. Модель:

* Модель обучается на обсуждениях в *./Technical/train.txt* 

* Я пытался обучить модель на статьях википедии, даже написал свой парсер для нее, лол. К сожалению, обученная таким образом модель практически не различала разные документы, она просто давала им практически одинаковые вектора.

* Примерно такая же ситуация произошла с обучением на статьях с habrahabr. 

* В итоге пришлось использовать то что есть и связано с КРОКом, поэтому обучение идет на 200 обсуждениях :(

3. Поисковый запрос:

* Пользователь формирует поисковый запрос, он обрабатывается в ModelPredict, функцией MAKE_SEARCH для чистого текста или MAKE_FILE_SEARCH если запрос сохранен в файле.

* Обе функции делают примерно такое:

* 1. приводят запрос к нормальному виду
* 2. находят вектор запроса с помощью модели
* 3. находят косинусное расстояние с каждым вектором документа
* 4. выдают три наиболее релевантных документа

4. Конец

* В принципе я описал как это работает. Если будут вопросы, пишите мне

* 1. mail = mrartemev@edu.hse.ru
* 2. telegram -> @MRArtemev


