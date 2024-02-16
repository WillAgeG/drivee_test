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
  
    - Параметры курьера:
      ■ Гео-координаты курьера

  ## Решение

  Для решение такой задачи нам нужно использовать ["Венгерский алгоритм".](https://habr.com/ru/articles/422009/)

  Нам не нужно искать минимальное преодолённое расстояние между заказом (А) и курьером. 
  Задача сводится к тому, чтобы найти и распределить всех курьеров так, чтобы скорость доставки ВСЕМ клиентам была минимальна.
  Поэтому если мы будем искать минимальное преодолённое расстояние между заказом (А) и курьером, то найдем такое решение которое будет оптимально только для 1 заказа.
  Здесь я имею ввиду, что нужно рассматривать все заказы и всех курьеров.

  Это картинка поможет лучше понять о чем я хочу написать:

  ![image](https://github.com/WillAgeG/drivee_test/assets/124677224/ffde4e3a-63e0-4382-8701-1f3212d1d014)

  Здесь мы наглядно видим, что наша задача это как матрица. В котором нам нужно проанализировать все квадратики, так чтобы найти минимальное время для ВСЕХ заказов использовав всех курьеров.

  Если даже немного самим посмотреть на эту матрицу, то мы можем видеть, что для Гены и Маши скорость доставки заказа 1 - 1 минута. И для заказа лучше использовать Гену, 
  так как для заказа 2 Маша выполнит её за 2 минуты.

  Венгерский алгоритм найдет оптимальнное решение.

  После того как мы выполнили 1 из задач, то возникают вопросы такие как:
  
    1. Как найти время в пути для каждого курьера?
    
    2. В процессе выполнения заказов может потребоваться корректировка назначений в зависимости от изменения ситуации 
    (например, из-за задержек в движении или освобождения курьера после выполнения предыдущего заказа). Как это решить?
    
    3. Как нам необходимо стремиться к равномерному распределению задач между курьерами, 
    чтобы избежать перегрузки отдельных курьеров и недостаточной загруженности других?
    
    4. Как правильно регулярно переоценивать распределение заказов с учетом новой информации 
    (новые заказы, изменения в доступности курьеров, условиях на дорогах и т.д.)?

  Еще могут возникнуть вопросы. Действительно ли это оптимальное решение? Скажу сразу, что нет. Решение могло бы быть более оптимальным если мы знали всё обо всём.
  В будущем можно использовать машинное обучение для предсказания оптимальных маршрутов и распределения на основе исторических данных о времени доставки, 
  загруженности дорог и других факторов. И даже так это не будет максимально оптимально.
  
  ### Для решения 1 вопроса. Нам нужно использовать [API 2GIS Distance Matrix API.](https://docs.2gis.com/ru/api/navigation/distance-matrix/overview)

  Почему я считаю, что нам нужно использовать стороннее решение для нашей задачи?

  Я считаю, что в реальной прикладной такой задаче, оптимально использовать решение другой компании.
  У нас будут САМЫЕ актуальные данные о картах. Данные о пробках, закрытых дорогах и многое другое.
  Почему именно 2GIS, а не Яндекс.Карты или Google Maps. Ответ такой:

  Я ищу такое решение которое могло бы быть оптимальным для города Якутск и там 2GIS более распространён чем, Яндекс.Карты или Google Maps.
  Для Москвы и Московской области. Лучше использовать Яндекс.Карты, так как там Яндекс.Карты более распространенный продукт.

  ### Для решения 2 и 4 вопроса.

  Я думаю, что нужно обновлять API 2GIS Distance Matrix API каждые 2 минуты. Чтобы накопилось определенное кол-во заказов и курьеров чтобы давать более оптимальные заказы. 
  Для оптимального обновления данных нужно проанализировать данные на реальных данных, но это где-то от 15 сек до 4 минут. Поэтому и было использовано 2 минуты.

  Также считаю, что если произошла задержка Курьера, то в приложении об этом сообщить, если курьер сам того хочет.
  Вдруг он поломал руку. И сменить этого курьера на другого, если необходимо. Лучше в этой ситуации взять ближайшего курьера и уменьшить время выполнения заказа для системы до нуля.

  ### Для решения 3 вопроса.
  
  Думаю, что нужно искуственно уменьшать время между курьером и клиентом на определенное количество секунд, чтобы этому курьеру упало больше заказов, но как понять, что кто-то сделал больше заказов, а кто-то меньше?

  Нужно ввести новую переменную как рейтинг. В рейтинге будут учитываться ВСЁ.
    
    - Как часто опаздывает курьер для выполнения своего заказа. (Учитывать)
    
    - Как часто курьер отменяет заказ.
    
    - Насколько хорошо курьер выполняет свою работу. (Рейтинг как в Drivee)
    
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
