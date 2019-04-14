2004
Netscape 6.0
Первый чат.
Мой первый код выглядел как-то так.
Я скопировал его из интернета. Я не умел писать на js, php.
Я даж программирования не знал. Открыл текстовый файлик и нажал ctrl+c, ctrl+v.
Автор прикрепил архив c исходниками и уже с этим можно было играться.
Простенький чат на js c бекендом на php.
Отдельный челлендж был поставить на винде Apache.

```js
function postToIframe(url, data, target) {
  var transport = getOrCreateForm();
  transport.action = url;
  transport.target = target;

  var html = [];
  for (var key in data) {
    html.push(
      "<input type='hidden' name=\"" + key + '" value="' + data[key] + '">'
    );
  }
  transport.innerHTML = html.join("");
  transport.submit();
}
```

И это без перезагрузки страницы. Сумасшедший костыль - создавать скрытую форму в айфрейме.
Сабмитить ее. Ответ же получать через отдельный айфрейм, опрашивая его раз в N - секунд таким кодом.

```js
function iframeGet(url, onSuccess) {
  var iframeName = Math.random();
  var iframe = createIframe(iframeName, url);

  CallbackRegistry[iframeName] = function(data) {
    onSuccess(data);
  };

  iframe.onload = function() {
    iframe.parentNode.removeChild(iframe);
    delete CallbackRegistry[iframeName];
  };
}
```

Вот весь веб живет на таких костылях с самого начала.

2010

```js
function bindEvent(element, eventName, eventHandler) {
  if (element.addEventListener) {
    element.addEventListener(eventName, eventHandler, false);
  } else if (element.attachEvent) {
    element.attachEvent("on" + eventName, eventHandler);
  }
}
bindEvent(window, "resize", function() {
  var card = getElementByClassName("card");
  var scores = getElementByClassName("scores");
  card.style.width = window.innerHeight;
  scores.innerHtml = getScores();
});
```

В 2010 я был в универе. Пописывал простенькие сайты. Игрался с JS и Java.
У меня был маленький внутренний проектик. На нем была карточка с картинкой и голосовалка под ней.
Я специально поднял исходники как он изменялся со временем.
Именно на этом проекте я пробовал все новые технологии и подходы.
Синтаксис и код упрощены, поэтому здесь будут синтаксические ошибки и т.д.

2011-2012
JQuery

```js
function getScores() {
  var scores = "";
  for (var i in scores) {
    if (scores.hasOwnProperty(i)) {
      scores += '<img src="' + i + 'star.png"></img>';
    }
  }
  return scores;
}

var scores = getScores();
(function(scores) {
  $(window).resize(function() {
    $(".card").css("width", window.innerHeight);
    $(".card>.scores").html(scores);
  });
})(scores);
```

getScores лежит в одном файле, IIFE лежит в другом файле.
Между ними общая переменная, что подклеивается между ними.
Собиралось все это добро с помощью скриптов на Ant.

В 2011 я начал работать Java-программистом.
Главное отличие это насколько сильно различался тулинг.
У тебя есть авторефакторинги, генерация кода, переходы по коду, дебаггер.
Сборка с помощью Ant, Maven
Отслеживание зависимостей.
Репозитории с открытым кодом, где можно выкачать код зависимости и посмотреть что внутри.
Это был реальный шок.

JS - язык нравился гораздо больше.
Функции высшего порядка и рефлексия из коробки позволяла творить чудеса.
Количество абстракций уменьшалось в разы. Не нужно учить все эти адаптеры над билдерами и стратегиями.
Гибкость просто зашкаливала.
Однако тулинг был провалом.
Из-за динамической природы языка и слабой типизации утверждать наверняка что тебе приходит на вход было нельзя.
Может объект, а может массив или даже boolean с number.

В JS - все было глухо. Так я думал.
// Добавить историю про Node.js и резкий взлет фронтенда

Тогда я делал соц сеть для врачей. Привет Ване Кулькову и компании Bell Integrator.
Задачи требовали быть Fullstack с перекосом во Frontend.
Все бы ничего, но нам нужно было делать скайп в браузере для общения врач-пациент.
С видео, аудио, аттачами, чатом, шарингом экрана.
Первую версию я писал 3 месяца. Буквально не выходил с работы. Работал по 10-12 часов в сутки.
Багов была куча.
С изменением состояния глобальных переменных.
Несовпадением типов при сравнении.
undefined is not a function
Конфликты имен для глобальных переменных

Я решил с этим бороться и изучать фронтенд.
На глаза мне попалась статья Миши Давыдова про модульный фронтенд с паттерном Sandbox.
И я понял что это как раз то, что надо.
Каждый модуль - отдельный файл. Он регистрирует в глобальном неймспейсе свой провайдеры.
А вот вызов идет из главного файла - аналог main

Late 2013
Backbone

```js
Views.Card = Backbone.View.extend({
  tagName: "section",
  template: template,
  initialize: function() {
    this.scoreView = new Views.ScoreCollection({
      collection: this.model.get("scores")
    });
  },
  render: function() {
    window.onresize = function(e) {
      onSizeChange(window.innerWidth);
    };
    this.$el.append(this.scoreView.$el);
    this.scoreView.render();
  },
  onSizeChange: function(width) {
    this.view.$.css("width", width);
    this.model.set("width", width);
  }
});
```

Все еще нет модулей. Но подход с IIFE модулями работает неплохо.
Код делится на куски. Т.к. они меньше их можно прочитать целиком.
В голове нужно держать код только текущего модуля, а не всей системы целиком.

На всех углах трубили что надо разделять
View представление - html
Украшательства - css
Логику и Модели - js.
И никогда не смешивать эти понятия вместе.

Однако я понял, что надо разделять js и js.

// Дополнить аргументы

Важность разделения логики было сложно переоценить.
Особенно когда мне понадобилось вставить календарь в несколько разных мест приложения.

Для реюза надо

1. Пробросить данные в глобальный стейт.
2. Отрезать зависимости от глобального стейта.
3. Определить как вьюха влияет на глобальный стейт.
4. Не хранить данные в DOM

Поэтому мы сам фронтенд начали разделять на работу с DOM - view.
И работу с данными - collections.
Backbone себя показывал хорошо. Действительно структурировал код.
Можно в голове держать структуру кода.
А переходы в webstorm работали корректно.
Но не без ложки дегтя

2013
Marionette

```js
Views.Card = Marionette.BossView.extend({
  tagName: "section",
  template: template,
  subViews: {
    scoreView: function() {
      return Views.ScoreCollection({
        collection: this.model.get("scores")
      });
    }
  },
  modelEvents: {
    "change:size": "onSizeChange"
  },
  onSizeChange: function(width) {
    this.view.$.css("width", width);
    App.vent.trigger("change:size:track", width);
  }
});
```

Больше нет функции render. И рендеринга ручками для дочерних вьюх.
Даже какая-то декларативность в этом прослеживается.
Во вторых появилась шина данных с событием change:size.
Это значит, что изменять размер мы можем без ручной подписки в каждой отдельной вьюхе на window.resize.
У этой схемы есть проблемы с

1. ссылка на объект model
   если мы поменяем model он поменяется сразу везде - независимо от нашего желания.
2. Неконтролируемый поток событий.
   Он связывает многие вьюхи со многими. т.к. из любого места я могу сделать:
   App.vent.trigger
3. Код связан неявно через строковые литералы

2013

```js
define(["project/views/cards/score/score_collection"], function(
  ScoreCollection
) {
  return Marionette.BossView.extend({});
});
```

```js
var ScoreCollection = require("project/views/cards/score/score_collection");
module.exports = Marionette.BossView.extend({});
```

2014
ES6 + React

```jsx
import ScoreCollection from "project/views/cards/score/score_collection";
export default React.createClass({
  render: function() {
    <section style={{ width: this.props.width }}>
      <ScoreCollection scores={this.props.scores} />
    </section>;
  }
});
```

// Расписать преимущества инкапсулированных подписок и 1 way data flow

2014-2016
State management почему это важно подвести из прошлого

2016-2019
Сражение за типизацию

Помните я жаловался на тулинг.
Рефакторинги, переходы к классам
Базой для них являются типы.
А в js типов нет. Частая ситуация - переменной была строка, а стало число и т.д.

Тут возникло несколько направлений:

1. Сделать надмножество для JS - TS, Flow
2. Написать язык без фатальных недостатков - Reason, Dart, Kotlin
3. Компилить существующий через web assembly - TS, Rust, C# etc.

Языки появились гораздо раньше.
веб-приложения становятся все сложнее.
Они уже заменяют кучу десктопных приложений.
Да там еще и легаси накопилось.
Лучшая борьба с легаси это рефакторинги.
Т.к. нельзя переписывать весь фронт заново.
Особенно если у вас миллион строк.

Будущее?
Сейчас браузер это платформа. См. Chrome OS, Firefox OS.
Очевидно, что DOM работает не самым оптимальным образом см. Vs Code терминал.
Поэтому потребление памяти и процессорного времени растет.
Т.к. увеличивается количество отображаемых данных.

Поэтому неудивительно, что новые решения используют доступ к GPU напрямую.
Так они не зависят от оберток рендеринга в браузере.
Примеры:
WebGL, Canvas

Недавно анонсированный Flutter For Web тоже работает поверх канваса.

Blazor & web Assembly?

Возможно спека по Web Components дойдет до повсеместного внедрения и станет стандартом?

Или быть может станут поддерживать из коробки JSX и оптимизировать рендеринг Virtual DOM элементов?

Кто знает.
Одно можно сказать наверняка - хотя скорость выхода новых хайповых штук и замедлилась.
Это не значит, что в ближайшем будущем нам не нужно учить новые технологии.
