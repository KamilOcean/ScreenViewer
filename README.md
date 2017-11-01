[![Build Status](https://travis-ci.org/RGRU/ScreenViewer.svg?branch=master)](https://travis-ci.org/RGRU/ScreenViewer)
[![js-standard-style](https://img.shields.io/badge/code%20style-standard-brightgreen.svg)](http://standardjs.com)

# RxJS ScreenViewer
Модуль определения типа экрана браузера при помощи RxJS. Код написан с использованием flow типизации.

Зачем он нужен? Намного удобнее архитектура адаптивного сайта, построенная на изменении типов экрана, а не на изменении конкретных величин. Эта дополнительная абстракция дает плюс к масштабируемости и простоте поддержки адаптивной архитектуры.

## Принцип работы
Модуль определяет соответстиве некоей величины (в конкретном примере это ширина экрана), передаваемой ему, с типом экрана, которому удовлетворяет эта ширина.

Например в модуль передается величина 940, которой соответствует тип экрана tablet. Это видно из карты соответствия ниже. Если величина 1300, то это desktop.

```js
/**
 * Карта типов разшенения, в зависимости от ширины экрана
 * @type {Object}
 */
screenMap = { // ВСЕ ЧТО МЕНЬШЕ И РАВНО
    '768': 'mobile',
    '990': 'tablet',
    '1260': 'tabletLandscape',
    '1760': 'desktop',
    '1761': 'desktopFull'
}
```

Модуль работает с Rx потоками данных, в нашем случае потоками изменения ширины экрана браузера. Какие потоки прослушивать - решение разработчика.

В наиболее частых случаях хватает трех потоков из трех событий:
    - Полная загрузка страницы (onload)
    - Полная загрузка документа (DOMContentLoaded)
    - Ресайз страницы (onresize)

## Как подключить
Подключаем модуль (он внутри использует RxJS, поэтому модуль должен быть доступен в окружении)

index.js:

```js
import screenViewer from './screenViewer';
```

Если нужно - перенастраиваем карту типов по-умолчанию, методом setup.

```js
screenViewer.setup({
    '320': 'mobile',
    '700': 'tablet',
    '1200': 'desktop'
})
```

В метод init$ передается массив потоков (например трех, описанных выше), с отфильтрованной велечиной ширины экрана

```js
import { Observable } from 'rxjs/Rx';

const targetEventList = [
    Observable.fromEvent(window, 'load'),
    Observable.fromEvent(document, 'DOMContentLoaded'),
    Observable.fromEvent(window, 'resize')
]

const screen$ = screenViewer.init$(targetEventList)
```

Теперь мы можем подписаться на образованный модулем поток, который будет отправлять данные в тот момент, когда изменяется тип экрана

```js
screen$.subscribe(console.log)
```