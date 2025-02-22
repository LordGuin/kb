# Apache Spark
## Что такое Apache Spark?

**Spark** — фреймворк с открытым исходным кодом для обработки большого объема данных, 
опубликованный в 2010 году румынским ученым Матеем Захария (Matei Zaharia) и призванный ускорить работу Hadoop,
другой платформы для работы с Big Data.
Одна из особенностей Hadoop MapReduce — промежуточные данные вычислений хранятся на накопителях. 
Это снижает скорость доступа к данным, что неприемлемо для некоторых классов аналитических задач. 
Apache Spark был призван решить «медлительность» Hadoop.
Спустя три года проект был передан фонду Apache. 

Apache Spark предлагает иной архитектурный подход, называемый RDD (resilient distributed dataset). 
В его основе лежат мультимножества, распределенные между узлами кластера. 
Кластер представляется как ацикличный направленный граф, где узлы кластера являются мультимножествами, 
а ребра графа — операциями. Для работы с RDD доступно несколько абстракций: Dataframe API и Dataset API.

Apache Spark не входит в фреймворк Apache Hadoop, однако имеет с ним совместимость. 
Узлы кластера можно запускать через YARN, а хранить данные на HDFS. 
Знание Hadoop необязательно: кластер Spark может быть запущен даже вручную, 
а для разработки и тестов можно выбрать псевдораспределенный режим, 
в котором каждый узел кластера занимает одно вычислительное ядро процессора. 

Новый фреймворк появился не на ровном месте. Hadoop имеет ряд особенностей, 
которые ограничивают его применимость в различных задачах. 
Поговорим об улучшениях, которые принес Spark.

## Преимущества Apache Spark

Начнем обзор с фундаментальных вещей. Apache Spark использует парадигму резидентных вычислений, 
то есть обрабатываемые данные хранятся в оперативной памяти, ускоряя многократный доступ к загруженным данным. 
Благодаря такому подходу Spark можно использовать в задачах, 
недоступных для Hadoop, — например, в машинном обучении.

Как отмечалось ранее, Spark является самостоятельным приложением, поэтому для тестирования 
его возможностей нет необходимости настраивать распределенный кластер. 
Apache Spark состоит из множества модулей, но в отличие от Hadoop модули Spark 
предоставляют новые способы взаимодействия пользователя с кластером. 

Основой Spark является модуль Spark Core, написанный на языке программирования Scala. 
«Ядро» занимается запуском распределенных вычислений, планированием заданий и базовыми операциями ввода-вывода. 
Ядро предоставляет Java API для управления с помощью других JVM-совместимых языков программирования. 
Дополнительно предоставляются интерфейсы для Python, .NET и R. 

Как отмечалось ранее, в основе вычислений Spark лежат операции над мультимножествами. 
Все мультимножества доступны только для чтения, а операции изменения создают новые мультимножества, 
которые хранят историю действий.

Список действий для получения текущего мультимножества — один из способов достижения отказоустойчивости в кластере. 
В случае частичной потери данных в кластере остается «инструкция» по восстановлению данных, 
которая «прозрачна» для конечного пользователя.

Действия над мультимножествами определяются программистом в функциональном стиле при разработке приложения 
в виде последовательности простых действий над одним или несколькими элементами.

Парадигма функционального программирования подходит не всем, поэтому рассмотрим другие модули для Spark.

## Spark SQL

Многие аналитики используют в работе интерактивные запросы. 
Средства Spark Core не позволяют совершать запросы к хранящимся данным в режиме реального времени, 
более того, «ядро» требует навыков функционального программирования. 

В этом случае поможет модуль Spark SQL, который вводит поддержку языка запросов SQL 
для структурированных наборов данных. Spark SQL — это диалект SQL, 
который обладает множеством известных операторов и ключевых слов, свойственных диалектам реляционных баз данных. 
Тем не менее, в нем есть и уникальные синтаксические конструкции. 
Например, выражение для загрузки JSON-файла в Spark:

CREATE TEMPORARY TABLE people
    USING org.apache.spark.sql.json
    OPTIONS (path '/home/selectel/example.json')

Помимо интерактивного интерфейса командной строки, Spark SQL предоставляет ODBC/JDBC-интерфейсы, 
позволяющие корпоративным инструментам для анализа данных подключаться к Spark, как к классическим базам данных.

## Spark Streaming

Устройства и программы современного мира генерируют огромное количество данных. 
Некоторые данные необходимо обрабатывать по мере их поступления. Для этого существует модуль Spark Streaming. 

Данный модуль отбирает небольшое количество данных из входного потока, 
создает из них мультимножество и выполняет заданные преобразования. 
Эти операции повторяются до тех пор, пока модулю предоставляются новые данные. 
По умолчанию Spark Streaming умеет получать данные из брокеров сообщений, в том числе из Apache Kafka.

## Spark MLLib 

**Spark MLLib** — это библиотека, реализующая алгоритмы машинного обучения и статистические алгоритмы на базе кластеров Apache Spark. 
Как упоминалось ранее, Spark использует парадигму резидентных вычислений, подходящую для задач машинного обучения. 

Разработка модуля MLLib началась вместе с разработкой Spark. 
Предполагалось, что MLLib заменит существующий проект для машинного обучения Apache Mahout. 
Использование распределенной архитектуры Spark позволило получить почти девятикратный прирост 
производительности по сравнению с Apache Mahout. 

После принятия Spark в проекты верхнего уровня Apache Mahout получил интерфейс для работы с Spark, 
а MLLib — значительное расширение функциональности. 

## Graphx

Мы рассмотрели структурированные запросы, потоковую обработку и методы машинного обучения. 
Осталась теория графов. Модуль Graphx «закрывает» эту область.

**Graphx** — модуль-исполнитель распределенной обработки графовых структур. 
Так как в архитектуре Spark мультимножества доступны только для чтения, 
то Graphx неприменим для изменяющихся графов. Модуль Graphx, как и Spark, 
начинался в виде научной работы, но впоследствии был передан в фонд Apache.

## Заключение

Итак, мы закончили высокоуровневый обзор проекта Apache Spark — его преимуществ, 
принципа работы, инструментов. В целом, его можно охарактеризовать как «швейцарский нож» 
из мира распределенных вычислений и больших данных. 
Совместимость с инструментами Apache Hadoop и наличие модулей обработки специфичных структур 
данных существенно расширяют область применения Spark.