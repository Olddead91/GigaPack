# GigaPack  <a href='https://gigapack.rusquant.io'><img src='img/logo_candle.png' align="right" height="139" /></a>

```shell
Сервис создан в рамках соревнования Хакатон «GoAlgo» от Московской Биржи
по созданию решений для инвесторов на рынке акций
```

GigaPack представляет из себя вычислительный слой над данными Алгопак и предоставляет инвесторам агрегированную по дням информацию по микроструктурным характеристикам фондового рынка.

В базовой структуре решения имеются следующие элементы:

* **Giga Candles** - это агрегированные свечи (на основе Super Candles), которые имеют более 600+ характеристик на каджую дневную свечу

* **Giga Tech** - это заранее посчитанные данные по техническому анализу по 100+ индикаторам

* **Giga Fields** - это список доступных характеристик свечей связанных как с техническим анализом, так и с микроструктурными данными

*  **Alter Giga и AlterTeсh** - это альтернативные данные основанные на технологии [блок ресемплинга]()

*  **Giga Momentum** - пример алгоритмической системы, которая использует в принятии решений как технические данные, так и микроструктурные характеристики от AlgoPack (через слой GigaPack). Данная торговая система проходит все стадии проверки на out-of-sample и альтернативную историю.

*  **AI bots** - примеры торговых алгоритмов на основе достижений машинного обучения, которые показывают, то как легко торговые роботы могут переобучаться. Большинство AI алгоритмов легко фитят рынок на истории и показывают 5-10 коэффициент Шарпа (при большом объеме времени и параметров, точно найдем - шарп не 10, но близко). Некоторые бумаги (в силу случайности) даже способны проходить традиционный out of sample. С использованием данных, Alter Giga и AlterTeсh можно выявлять такие случайности и не пропускать такие алгоритмы в живую торговлю.

   + **ML, Random Forest** - торговый робот на основе модели случайного леса  [ссылка на блокнот](https://swagger.rusquant.ru)
   + **NN, LSTM** - торговый робот на основе рекуррентной нейросеть с долгой краткосрочной памятью и 5 млн параметров на борту. [ссылка на блокнот](https://swagger.rusquant.ru)

### GigaCandles - агрегированные на дни свечи SuperCandles

Преобразование внутредневных карактеристик СуперСвечей может быть проведено разными способомами, например:

+ Вычисление 1, 2, 3 , ... моментов
+ Вычисление автокорреляционных явлений
+ Вычисление .. прочих характеристик

Таким образом мы получаем на каждый день количество характеристик в суперсвечах * количество преобразований. На текущем этапе для каждой свечи имеется более 600+ характеристик


![Alt text](img/gigacandles.png "GigaCandles")

### AlterGiga - альтернативные свечи: суть подхода

В связи с тем, что мы переходим к более крупному тайфрему мы сталкиваемся с недостатком наблюдений и естественно уменьшается статистическая значимость в разрабатываемых стратегиях. Для преободаления этого ограничений мы используем генерацию альтернативных ценовых траекторий и гигасвечей имеющих другие варианты развития событий, но сохраняющих временнный статистические закономерности.  

Мы предлагаем воспользоваться нашим сервисом генерации синтетических данных по любому финансовому инструменту.

Ключевые особенности нашего подхода:

+ **Сохранение зависимостей**: все темпоральные и кросс-темпоральные характеристики временных рядов полностью сохраняются, как линейные, так и любые иные;
+ **Корреляция с исходным рядом**: практически 100%, будет снижаться при роста числа блоков разбиения для бутстрэпа. Размер блока определяется пользователем и зависит от характеристик (памяти) стратегии;
+ **Эргодическая гипотеза**: сгенеренные сервисом альтернативные реализации цен, например SBER, обладают уникальным свойством - их средние показатели в точности соответствуют исходному ряду. По сути, речь идет о доступе к "параллельным вселенным", где торгуется выбранный финансовый инструмент;

![Alt text](img/altergiga.png "AlterGiga")

### Архитектура решения
GigaPack имеет микросервисную архитектуру и использует технологии бессерверных вычислений на Yandex Cloud. Исторические данные по **Giga Candles** и **Giga Tech** являются предрассчитаными и хранятся в ClickHouse, c обновлением данных каждые 5 минут в течение последного торгового часа на Московской Бирже. Некоторые данные, такие как **Alter Giga и AlterTeсh** считаются "на лету", поэтому при большом количестве симуляций могут достаточно долго выдавать результат. 

Для взаимодействия с сервисом GigaPack были разработаны/доработаны: 
+ [REST API](http://swagger.rusquant.io) сервис для взаимодействия с любым языком программирования
+ [rusquant](https://github.com/arbuzovv/rusquant/) библиотека для R
+ [pyrusquant](https://github.com/arbuzovv/pyrusquant) библиотека для Python
+ [вэб портал на Shiny](http://gigapack.shinyapps.io/main//) - позволяющий визуализировать гигасвечи и разнообразные микроструктурные эффекты.

![Alt text](img/architecture.png "Архитекрута GigaPack")

### Giga Momentum - торговая стратегия на основе моментума и данных AlgoPack
Для целей исследования была разработана торговая стратегия моментума на основе индикатора стохастика и дисбаланса из потока заявок.

![Alt text](img/momentum.png "Giga Momentum")

### Проверка на оверфитинг - сравнение Giga Momentum с AI алгоритмами
Проверка на оверфитинг стратегии - нормальная история, когда реальная траектория не отличается от виртуальной цены. В случае когда мы сталкиваемся оверфитингом стратегий
![Alt text](img/alternative_sharpe.png "Сравнение распределений коэффициента Шарпа на реальной истории и альтернативной для стратегии GigaMomentum")


### GigaPack monitor - вэб-портал для мониторинга данных AlgoPack
Как дополнительной решение, с целью мониторинга данных GigaPack мы создали [вебпортал](https://gigapack.shinyapps.io/main/) позволяющий оценить динамику и структуру данных.
![Alt text](img/webportal.png "Webportal")

### Структура файлов GigaPack

+ GigaMomenum.html - вычисление характеристик торговой стратегии.
+ GigaMomenum.html - вычисление характеристик торговой стратегии.
+ GigaMomenum.html - вычисление характеристик торговой стратегии.
