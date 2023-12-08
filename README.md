# GigaPack  <a href='https://gigapack.rusquant.io'><img src='img/logo_candle.png' align="right" height="139" /></a>

```shell
Сервис создан в рамках соревнования Хакатон «GoAlgo» от Московской Биржи
по созданию решений для инвесторов на рынке акций
```

GigaPack представляет из себя вычислительный слой над данными Алгопак и предоставляет инвесторам агрегированную по дням информацию по микроструктурным характеристикам фондового рынка.

В базовой структуре имеются следующие структуры:

* **Giga Candles** - это агрегированные свечи (на основе Super Candles), которые имеют более 600+ характеристик на каджую дневную свечу

* **Giga Tech** - это заранее посчитанные данные по техническому анализу по 100+ индикаторам

*  **Alter Giga и AlterTeсh** - это альтернативные данные основанные на технологии [блок ресемплинга]()

*  **Giga Momentum** - пример алгоритмической системы, которая использует в принятии решений как технические данные, так и микроструктурные характеристики от AlgoPack (через слой GigaPack). Данная торговая система проходит все стадии проверки на out-of-sample и альтернативную историю.

*  AI bots - примеры торговых алгоритмов на основе достижений машинного обучения, которые показывают, то как легко торговые роботы могут переобучаться. Большинство AI алгоритмов легко фитят рынок на истории и показывают 5-10 коэффициент Шарпа (при большом объеме времени и параметров, точно найдем - шарп не 10, но близко). Некоторые бумаги (в силу случайности) даже способны проходить традиционный out of sample. С использованием данных, Alter Giga и AlterTeсh можно выявлять такие случайности и не пропускать такие алгоритмы в живую торговлю.

   + ML, Random Forest - торговый робот на основе модели случайного леса  [ссылка на блокнот](https://swagger.rusquant.ru)
   + NN, LSTM - торговый робот на основе рекуррентной нейросеть с долгой краткосрочной памятью и 5 млн параметров на борту. [ссылка на блокнот](https://swagger.rusquant.ru)

### Архитектура решения
GigaPack имеет микросервисную архитектуру и использует технологии бессерверных вычислений на Yandex Cloud. Исторические данные по **Giga Candles** и **Giga Tech** являются предрассчитаными и хранятся в ClickHouse, c обновлением данных каждые 5 минут в течение последного торгового часа на Московской Бирже. Некоторые данные, такие как **Alter Giga и AlterTeсh** считаются "на лету", поэтому при большом количестве симуляций могут достаточно долго выдавать результат. 

Для взаимодействия с сервисом был разработан [REST API](http://swagger.rusquant.io) и использующие его библиотеки rusquant на [R](https://github.com/arbuzovv/rusquant/) и [Python](https://github.com/arbuzovv/pyrusquant). Помимо всего прочего  для пользователей не обладающих навыками программирования был разработ [портал на Shiny](http://gigapack.shinyapps.io/console//) позволяющий визуализировать гигасвечи и разнообразные микроструктурные эффекты.

![Alt text](img/architecture.png "Архитекрута GigaPack")


### GigaCandles - агрегированные на дни свечи SuperCandles


![Alt text](img/gigacandles.png "GigaCandles")

