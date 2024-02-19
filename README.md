<h1 align="left">
  Задача Drivee
</h1>

<div align="left">
<i>
Написать алгоритм распределения заказов между курьерами, чтобы был приоритет в
скорости доставки посылки клиентам

## Входные параметры:

    ● Список заказов
        ○ Параметры заказа:
            ■ Гео-координаты точки А (Откуда)
            ■ Гео-координаты точки Б (Куда)
            ■ Стоимость заказа
      
    ● Список курьеров
        ○ Параметры курьера:
            ■ Гео-координаты курьера

## Решение:

Для решения такой задачи нам нужно использовать буферный (балковый) подход с применением
множества алгоритмов и подходов.

Нам не нужно искать минимальное время между заказом и курьером. При применении жадного алгоритма 
мы учитываем только один заказ, но не множество заказов. Вот один из примеров:

### Жадный алгоритм:

При жадном подходе ближайший заказ получит тот, кто первый заказал курьера. При этом некоторые 
пользователи (те, кто сделали заказ) могут вообще остаться без курьеров. Поэтому, когда поступает 
запрос на поиск курьера, то сначала мы определяем в локальном геоиндексе ближайших курьеров. 
Затем уточняется время и длина маршрута подачи курьера. И с учётом этой информации выбирается 
лучший вариант.

Если посмотреть на рисунок 1 (Жадный алгоритм) и на рисунок 2 (Венгерский алгоритм), то мы увидим, 
что при применении жадного алгоритма суммарное время подачи жадного алгоритма - 12 мин, а венгерского 
алгоритма - 8 мин.

    - Суммарное время подачи - это общее затраченное время водителями. В примере это будет:
      2+10, то есть 12 минут. В другом примере: 5+3 = 8 минут.

![image](https://github.com/WillAgeG/drivee_test/assets/124677224/8c1f2ddc-fa93-4ae8-b568-898fe665d116)
Рисунок 1 - Жадный алгоритм

### Буферный (балковый) подход:
### (Реализовано так, что алгоритм работает до тех пор пока не закончатся заказы)

![image](https://github.com/WillAgeG/drivee_test/assets/124677224/6cf4f212-59bd-4e9a-b4ce-467010494f95)
Рисунок 2 - Венгерский алгоритм

Чтобы максимально удовлетворить спрос даже в самые нагруженные часы, нужно использовать 
множество подходов и алгоритмов. Один из них — буферное (балковое) назначение курьеров 
на заказы. В его основе лежит хорошо известная задача из области комбинаторной оптимизации 
— задача о назначениях. Вкратце её суть: пусть у нас есть N работ и M исполнителей, любой 
работник может выполнить любую задачу за время p(i,j)[0<=i<N, 0<=j<M]. Нужно назначить 
каждой задаче такого исполнителя, чтобы сократить суммарное время выполнения всех работ 
(при этом один исполнитель может взяться только за одну работу).

При решении такой задачи о назначениях наша «стоимость» выполнения заказа курьером — это значение 
функции скоринга от времени подачи автомобиля к пользователю. Задачу можно описать в терминах 
двудольных графов (см. ниже): с одной стороны — заказы, с другой — курьеры. Между заказами и исполнителями 
есть взвешенные рёбра (скоринг). Таким образом, одна из наших целей — минимизировать суммарное время 
подачи курьеров к заказам, максимизировав количество выполненных заказов (максимальное паросочетание). 
Один из наиболее известных способов решить такую задачу — [венгерский алгоритм.](https://habr.com/ru/articles/422009/)

![image](https://github.com/WillAgeG/drivee_test/assets/124677224/34df4e5f-c1a7-4a24-acbe-20cb4335be20)
Рисунок 3 - с одной стороны — заказы, с другой — курьеры (водители)

Задача сводится к тому, что нужно найти и распределить всех курьеров так, чтобы скорость 
доставки ВСЕМ клиентам была минимальна. Поэтому если мы будем искать минимальное 
преодолённое расстояние между заказом и курьером, то найдем такое решение, которое 
будет оптимально только для 1 заказа. Имею в виду, что нужно рассматривать все 
заказы и всех курьеров.

Это картинка (Рисунок 4) поможет лучше понять, о чем я хочу написать:

![image](https://github.com/WillAgeG/drivee_test/assets/124677224/ffde4e3a-63e0-4382-8701-1f3212d1d014)
Рисунок 4 - заказы и курьеры

Здесь мы наглядно видим, что наша задача - это как матрица, в которой нам нужно проанализировать 
все квадратики так, чтобы найти минимальное время для ВСЕХ заказов, использовав всех курьеров.

Если немного подумать самим, посмотрев на эту матрицу, то мы можем видеть, что для Гены и Маши 
скорость доставки заказа 1 - 1 минута. И для заказа лучше использовать Гену, так как для 
заказа 2 Маша сможет выполнить её за 2 минуты.

Венгерский алгоритм ищет оптимальное решение для всех, поэтому я его и использовал. 

Алгоритм будет срабатывать каждые 3 минуты, чтобы мы могли собрать больше данных курьеров и 
заказов и правильно их распределить. Тогда возникает вопрос: почему нельзя использовать алгоритм 
каждые 10 секунд? Кажется, что если мы будем быстрее назначать курьеров, то скорость всей системы 
вырастет, но это в корне неверно. Для того, чтобы это объяснить, приведу пример:

Предположим сейчас у нас активны 2 заказа и свободных курьеров тоже 2. И пусть эти заказы находятся
в конце города, а курьеры находятся в центре города. То кажется почему бы сразу не назначить курьеров
сразу. Если мы сразу отправим 2 курьера, то в центре города не останется курьеров вовсе. Поэтому лучше
немного подождать (1-2 минуты). Чтобы накопилось чуть-чуть больше заказов (например: 3). И если 3 заказ появится
в центре города, то лучше направить курьеров так, чтобы один курьер выполнял заказ в центре города, а другой 
за её окраинами.

Если система крайне высоконагруженная, то будет лучше, если алгоритм будет срабатывать каждые 2 минуты или
даже 1 минуту. Так как система крайне высоконагруженная, то кол-во заказов уже через минуту может быть
загружена на 1000 заказов. И вряд ли тут нужно будет ждать какое-либо время, но в нашей задаче Якутск.
Поэтому думаю вполне логично использовать её каждые 3 минуты. Система все равно не будет сильно загружаться.

(Реализовано так, что алгоритм работает до тех пор пока не закончатся заказы)

### Веса (Реализовано частично)

Далее, чтобы курьеры меньше простаивали, нужно ввести новую переменную "веса". Какие проблемы она решает?

Предположим, что всего у нас 2 курьера и 2 заказа (см. ниже). Один курьер оранжевого цвета, другой фиолетового. 
Если мы сразу распределим курьеров, то заказ А2 будет выполняться дольше, если мы задействуем оранжевого 
курьера. В этой ситуации лучше всего будет если два заказа возьмёт фиолетовый цвет, так как после 
выполнения своего 1 заказа оранжевый будет на половине своего пути, тогда как фиолетовый уже у заказа А2.

![image](https://github.com/WillAgeG/drivee_test/assets/124677224/e340cdb1-961c-4b76-94af-1d7e7b71a0aa)

Как работают веса:

Предпложим, что у нас появился новый заказ и система расчитала время в пути для каждого курьера.
Если мы будем учитывать в том числе курьеров, которые уже делают заказ, то сможем сэкономить время.
Предположим, что курьер 7 все еще выполняет заказ. Тогдв как для него высчитать веса? Тут все просто!
Необходимо лишь добавить оставшееся время за которое он выполнит свой заказ + расстояние от точки B 
до нового заказа. И, возможно, в редких ситуациях заказ будет распределятся курьеру, который занят на 
данный момент.

Невозможно полностью реальзовать - слишком высокая сложность алгоритма для компьютера. 
Эта часть ниже с фото, может быть реализована, но только для маленького количества заказов и курьеров.
Думаю, что можно, это реализовать, но сложность вырастает с O(n * log(m)) до O(n^2 * log(m)).

![image](https://github.com/WillAgeG/drivee_test/assets/124677224/9586497f-af82-478a-9c50-13eafd9b7b6a)

В будущем можно будет использовать веса не только, чтобы использовать курьеров, которые заняты, но и для
системы рейтинга: (Тут только рассуждения как потенциально можно было бы это использовать)

    - Как часто опаздывает курьер для выполнения своего заказа. 
    
    - Как часто курьер отменяет заказ.
    
    - Насколько хорошо курьер выполняет свою работу.
    
    - Если в течении 15 минут не пришел ни один заказ, то уменьшить 
      веса на какое-то определенное кол-во секунд.

## Как считать путь между курьером и заказом?

Почему нельзя использовать просто ближайшее расстояние между точками?

Дело в том, что это крайне неоптимально. Может возникнуть ситуация как здесь (см. ниже). Кажется, что 
водитель близко, но на самом деле он должен преодолеть большое расстояние.

![image](https://github.com/WillAgeG/drivee_test/assets/124677224/d8f883c4-457f-49f9-9682-9b852da0038a)

Думаю, для решения этого вопроса лучше всего использовать некоторые библиотеки из Python.
В будущем можно будет реализовать собственные графовые системы, но это может занять много времени.

    - Загрузка данных OpenStreetMap.
    - Предварительная обработка данных OSM.
    - Запуск собственного OSRM сервера.

Есть еще могут возникнуть такие проблемы, как сложность алгоритма. Возможные решения:

    - Купить более мощный сервер (Самый быстрый способ).
    - Оптимизировать код.
    - Использовать асинхроннные методы вычисления данных.
    - Стоит переписать код если он будет медленным. На язык Go.

## Архитектура приложения

-

## Код реализации

-

## Документация

-

## Как запустить продукт

-

## Что еще нереализовано в рамках тестового задания?

    - Не учитывается дорожная ситуация на картах.
    - Не все фишки есть которые были написаны выше.
    - Оптимизация хромает

Тесты:

    - Нет автотестов.
    - Нет симуляции курьеров и заказов.
    - Нет симуляции нагрузочного тестирования.

Архитектура:

    - Монолитная архитектура.
    - Не используется docker контейнер.
    - Нету базы данных.
    - Не самая лучшая реализация кода.
    - Нет мгновенной возможности сразу запустить в прод.

Фишки:

    - Возможность мультизаказа. Нельзя взять сразу множество заказов А до выполнения B.
    - Возможность видеть в реальном времени как работает буферный подход.
    - Нет учета если курьер пешеход или машина.
