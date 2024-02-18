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

Для решение такой задачи нам нужно использовать буферный (балковый) подход с применением
множества алгоритмов и подходов.

Нам не нужно искать минимальное время между заказом и курьером. При применении жадного алгоритма 
мы учитываем только один заказ, но не множество заказов вот один из примеров:

### Жадный алгоритм:

При жадном подходе ближайший заказ получит тот, кто первый заказал курьера. При этом некоторые 
пользователи (те кто сделали заказ) могут вообще остаться без курьера. Поэтому, когда поступает 
запрос на поиск курьера, то сначала мы определяем в локальном геоиндексе ближайших курьеров. 
Затем уточняется время и длина маршрута подачи курьера и с учётом этой информации выбирается 
лучший вариант.

Если посмотреть на рисунок 1 (Жадный алгоритм) и на рисунок 2 (Венгерский алгоритм), то мы увидим, 
что при применении жадного алгоритма суммарное время подачи жадного алгоритма 12 мин, а венгерского 
алгоритма 8 мин.

    - Суммарное время подачи - это общее затраченное время водителями в примере это 2+10, то есть 12 минут. 
      В другом примере 5+3 = 8 минут.

![image](https://github.com/WillAgeG/drivee_test/assets/124677224/8c1f2ddc-fa93-4ae8-b568-898fe665d116)
Рисунок 1 - Жадный алгоритм

### Буферный (балковый) подход:

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
двудольных графов: с одной стороны — заказы, с другой — курьеры. Между заказами и исполнителями 
есть взвешенные рёбра (скоринг). Таким образом, одна из наших целей — минимизировать суммарное время 
подачи курьеров к заказам, максимизировав количество выполненных заказов (максимальное паросочетание). 
Один из наиболее известных способов решить такую задачу — [венгерский алгоритм.](https://habr.com/ru/articles/422009/)

![image](https://github.com/WillAgeG/drivee_test/assets/124677224/34df4e5f-c1a7-4a24-acbe-20cb4335be20)
Рисунок 3 - с одной стороны — заказы, с другой — курьеры (водители)

Задача сводится к тому, чтобы найти и распределить всех курьеров так, чтобы скорость 
доставки ВСЕМ клиентам была минимальна. Поэтому если мы будем искать минимальное 
преодолённое расстояние между заказом и курьером, то найдем такое решение которое 
будет оптимально только для 1 заказа. Имею ввиду, что нужно рассматривать все 
заказы и всех курьеров.

Это картинка (Рисунок 4) поможет лучше понять о чем я хочу написать:

![image](https://github.com/WillAgeG/drivee_test/assets/124677224/ffde4e3a-63e0-4382-8701-1f3212d1d014)
Рисунок 4 - заказы и курьеры

Здесь мы наглядно видим, что наша задача это как матрица. В котором нам нужно проанализировать 
все квадратики, так чтобы найти минимальное время для ВСЕХ заказов использовав всех курьеров.

Если немного подумать самим, посмотрев на эту матрицу, то мы можем видеть, что для Гены и Маши 
скорость доставки заказа 1 - 1 минута. И для заказа лучше использовать Гену, так как для 
заказа 2 Маша выполнит её за 2 минуты.

Венгерский алгоритм ищет оптимальнное решение для всех поэтому я его использовал.

Далее, чтобы курьеры меньше простаивали, нужно ввести новую переменную как веса. Какие проблемы она решает?

    - Предположим, что всего у нас 2 курьера и 2 заказа. Один курьер оражевого цвета, другой фиолетового.
      Если мы сразу распределим курьеров, то заказ А2 будет выполнятся долго если мы используем оранжевого курьера. 
      В этой ситуации лучше всего будет если два заказа возьмёт фиолетовый цвет, так как после выполнения своего 1
      заказа оранжевый будет на половине своего пути, а фиолетовый уже у заказа А2.

Пример:

![image](https://github.com/WillAgeG/drivee_test/assets/124677224/e340cdb1-961c-4b76-94af-1d7e7b71a0aa)


После того как мы выполнили 1 из задач, то возникают вопросы такие как:
  
    1. Как найти время в пути для каждого курьера?
    
    2. В процессе выполнения заказов может потребоваться корректировка назначений в зависимости 
    от изменения ситуации (например, из-за задержек в движении или освобождения курьера после 
    выполнения предыдущего заказа). Как это решить?
    
    3. Как нам необходимо стремиться к равномерному распределению задач между курьерами, 
    чтобы избежать перегрузки отдельных курьеров и недостаточной загруженности других?
    
    4. Как правильно регулярно переоценивать распределение заказов с учетом новой информации 
    (новые заказы, изменения в доступности курьеров, условиях на дорогах и т.д.)?

  Еще могут возникнуть вопросы. Действительно ли это оптимальное решение? Скажу сразу, что нет. 
  Решение могло бы быть более оптимальным если мы знали всё обо всём. В будущем можно использовать 
  машинное обучение для предсказания оптимальных маршрутов и распределения на основе исторических 
  данных о времени доставки, загруженности дорог и других факторов. И даже так это не будет 
  максимально оптимально.
  
  ### Для решения 1 вопроса. Нам нужно использовать [API 2GIS Distance Matrix API.](https://docs.2gis.com/ru/api/navigation/distance-matrix/overview)

  Почему я считаю, что нам нужно использовать стороннее решение для нашей задачи?

  Я считаю, что в реальной прикладной такой задаче, оптимально использовать решение другой компании.
  У нас будут САМЫЕ актуальные данные о картах. Данные о пробках, закрытых дорогах и многое другое.
  Почему именно 2GIS, а не Яндекс.Карты или Google Maps. Ответ такой:

  Я ищу такое решение которое могло бы быть оптимальным для города Якутск и там 2GIS более 
  распространён чем, Яндекс.Карты или Google Maps. Для Москвы и Московской области. Лучше 
  использовать Яндекс.Карты, так как там Яндекс.Карты более распространенный продукт.

  Я предполагаю, что не нужно искать ВСЕ расстояния между курьерами и точками А (Заказами) 
  посредством к API, так как если у нас высоконагруженная система, то мы максимум можем 
  использовать 25 запросов к API 2GIS или 1000 запросов в асинхронном режиме.
  
  Поэтому нужно использовать комбинированный метод расстояния между точками.

    - Для каждого заказа нужно найти все эвклидовы расстояния. Далее нужно взять 25 самых маленьких 
      расстояний между курьером и заказом потом обратиться к API 2GIS и найти время в пути для каждого 
      курьера до точки А. Далее это подставить в матрицу нашего венгерский алгоритма. Какие-то поля 
      будут пустыми в матрице и их лучше заполнить так:
      
         ■ Если берем среднее эвклидово расстояние(в м) умноженное на 2 и делим на среднее время 
           ходьбы человека на 1,38889 м/c (5 км/ч).

  Пример с фото:

  ![image](https://github.com/WillAgeG/drivee_test/assets/124677224/61ca88cd-7f95-41c8-ba75-94407809df62)

  Предположим, что у нас всего 3 реальных расстояния с учетом данных 2gis.

  ### Для решения 2 и 4 вопроса.

  Я думаю, что нужно обновлять API 2GIS Distance Matrix API каждые 2 минуты. Чтобы накопилось определенное 
  кол-во заказов и курьеров чтобы давать более оптимальные заказы. Для оптимального обновления данных нужно 
  проанализировать данные на реальных данных, но это где-то от 15 сек до 4 минут. Поэтому и было использовано 
  2 минуты.

  Также считаю, что если произошла задержка Курьера, то в приложении об этом сообщить, если курьер сам того 
  хочет. Вдруг он поломал руку. И сменить этого курьера на другого, если необходимо. Лучше в этой ситуации 
  взять ближайшего курьера и уменьшить время выполнения заказа для системы до нуля, чтобы был максимальный 
  приоритет.

  ### Для решения 3 вопроса.
  
  Думаю, что нужно искусственно уменьшать время между курьером и клиентом на определенное количество секунд, 
  чтобы этому курьеру упало больше заказов, но как понять, что кто-то сделал больше заказов, а кто-то меньше?

  Нужно ввести новую переменную как рейтинг. В рейтинге будет учитываться ВСЁ.
    
    - Как часто опаздывает курьер для выполнения своего заказа. 
      (Если система выявила, что среднее время выполнения заказа 8 минут, то если курьер превысит (8 мин * 2), 
      то есть 16 минут, то это считать как опоздание. Почему именно так? Я работал в Яндекс.Еда)
    
    - Как часто курьер отменяет заказ 
      (Если 3 и более раз за неделю, то давать санкции как умешьшенная плата на 15% на будущие 5 заказов.)
    
    - Насколько хорошо курьер выполняет свою работу. 
      (Рейтинг как в Drivee. Если у курьера 4.5 рейтинга и меньше, то ?????)
    
    - Если в течении 15 минут не пришел ни один заказ, то уменьшить время на 45 секунд. 
      Если более 15+3(18) минут, то на 45+30(75) секунд и так далее, но не более чем на 345(45+30*10) секунд.
      
      Система работает так:

      15 минут - уменьшить время на 45 секунд.
      18 минут - уменьшить время на 75 секунд.
      21 минута - уменьшить время на 105 секунд.
      24 минуты - уменьшить время на 135 секунд.
      и так далее до 45 минут.
      45 минут - уменьшить время на 345 секунд.

  Почему я использовал именно столько секунд, расписано [здесь](https://habr.com/ru/companies/yandex/articles/507448/). 
  Я посмотрел на графики Яндекс.Еда и проанализировав понял, что это более менее оптимальный вариант.
  </i>
</div>

## Код реализации есть в /src/drivee_test.pynb:
  - Вся работа сделана в Jupyter Notebook.
