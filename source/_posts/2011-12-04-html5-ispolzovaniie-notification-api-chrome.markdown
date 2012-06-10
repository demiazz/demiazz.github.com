---
layout: post
title: "HTML5: Использование Notification API (Chrome)"
date: 2011-12-04 14:35
comments: true
categories: ['html5', 'webkit', 'chrome']
---

HTML5 включает в себя на данный момент много интересных API, которые
позволяют делать много интересных вещей. Одним из интересных API
является Notification API.

В данной статье я хочу рассмотреть реализацию данного API в браузерах на
основе Chromium (Chromium, Google Chrome и вероятно другие его клоны).

На данный момент спецификация находится в разработке.

<!-- more -->

## Что это такое?

Notification API позволяет выводить уведомления пользователю на основе
каких-то событий, либо пассивно (например, уведомления о новых
сообщениях электронной почты, событиях в календаре или новых твитах).

Чтобы использовать эти возможности, нужно всего четыре простых шага.

## Шаг 1: Проверка поддержки

Чтобы проверить поддержку NotificationAPI в браузере, нужно проверить
существование объекта `webkitNotification`. Данное имя выбрано на время
разработки стандарта, и в окончательной спецификации будет заменено на
`notifications()` функцию.

{% codeblock Проверка поддержки уведомлений в Webkit lang:javascript %}
// можно использовать объект window
if (window.webkitNotifications) {
  console.log("Уведомления поддерживаются!");
}
else {
  console.log("Уведомления не поддерживаются в вашей версии
браузера/операционной системы.");
}
{% endcodeblock %}

## Шаг 2: Создание уведомления

Существует два типа уведомлений, в зависимости от их контента: простой текст и HTML. Если ваше приложение использует оба типа уведомлений, вы можете создать функцию, которая будет создавать одно из них на основе передаваемых параметров.

{% codeblock Создание уведомления lang:javascript %}
function createNotificationInstance(options) {
  if (options.notificationType == 'simple') {
    return window.webkitNotifications.createNotification(
        'icon.png', 'Заголовок уведомления', 'Текст уведомления...');
  } else if (options.notificationType == 'html') {
    return window.webkitNotifications.createHTMLNotification('http://someurl.com');
  }
}
{% endcodeblock %}

## Шаг 3: Получение прав доступа у пользователя

Любой конструктор, который был продемонстрирован ранее, вызовет ошибку, если пользователь вручную не предоставил разрешение на показ уведомлений для сайта.

Можно использовать `try-catch` конструкцию, для отлова исключения и его обработки, но более правильным путем будет использовать метод `checkPermission`.

{% codeblock Пример запроса прав доступа lang:javascript %}
document.querySelector('#show_button').addEventListener('click', function() {
  if (window.webkitNotifications.checkPermission() == 0) { // 0 is PERMISSION_ALLOWED
    // function defined in step 2
    createNotificationInstance({ notificationType: 'html' });
  } else {
    window.webkitNotifications.requestPermission();
  }
}, false);
{% endcodeblock %}

Если веб-приложение не имеет разрешение на показ уведомлений, то можно использовать метод `requestPermission`, который покажет следующий infobar:

{% img center /images/posts/htm5-ispolzovanie-notification-api-chrome/notifications_infobar.png Запрос разрешения на показ уведомлений %}

Важно помнить, что метод `requestPermission` работает только в обработчиках событий, вызванных действиями пользователей, такие как события мыши или клавиатуры. Это сделано, чтобы избежать нежелательных показов infobar'а. В данном примере, пользовательское действие - это клик по кнопке с id "show_button".

Фрагмент приведенный выше никогда не будет работать, если пользователь явно не нажал кнопку или ссылку, которая вызывает `requestPermission` в каком-то месте кода.

## Шаг 4: Привязка listener'ов и других действий

{% codeblock Пример привязки показа уведомления lang:javascript %}
document.querySelector('#show_button').addEventListener('click', function() {
  if (window.webkitNotifications.checkPermission() == 0) { // 0 is PERMISSION_ALLOWED
    // function defined in step 2
    notification_test = createNotificationInstance({notificationType: 'html'});
    notification_test.ondisplay = function() { ... do something ... };
    notification_test.onclose = function() { ... do something else ... };
    notification_test.show();
  } else {
    window.webkitNotifications.requestPermission();
  }
}, false);
{% endcodeblock %}

Все эти события можно инкапсулировать в свой собственный класс, и сделать код чище, но это уже выходит за рамки статьи.

## Ссылки

Здесь можете посмотреть дополнительные ссылки по теме:

* [Оригинал статьи](http://www.html5rocks.com/en/tutorials/notifications/quick/)
* [Спецификация Notification API для Chromium](http://www.chromium.org/developers/design-documents/desktop-notifications/api-specification)
* [Обзор Notification API - W3C](http://dev.w3.org/2006/webapi/WebNotifications/publish/)
* [Спецификация Notification API - W3C](http://dev.w3.org/2006/webapi/WebNotifications/publish/Notifications.html)
* [Спецификация WebNotificationAPI - W3C](http://dev.w3.org/2006/webapi/WebNotifications/publish/WebNotifications.html)
* [Спецификация пользовательских разрешений](http://dev.w3.org/2009/dap/perms/FeaturePermissions.html#idl-NavigatorPermissions)

В оригинале статьи можно также посмотреть примеры реализации в конце статьи.

Немного ссылок по реализации уведомлений в Gecko 2.0:

* [navigator.MozNotification](https://developer.mozilla.org/en/DOM/navigator.mozNotification)
* [notification](https://developer.mozilla.org/en/DOM/notification)
* [Вывод уведомлений](https://developer.mozilla.org/en/DOM/Displaying_notifications)

## Пара заметок

Префикс `webkit` намекает на то, что вполне возможно, что уведомления уже доступны в других webkit-based браузерах, например Safari. Возможности протестировать это у меня пока нет.

Что касается других браузеров, то информации по реализации я пока не нашел, кроме как Mozilla Firefox. В будущем рассчитываю изучить этот момент, и найти информацию касательно планов разработки данной функциональности.

И в качестве вывода, хочется сказать пару слов удивления, почему до сих пор, я не видел иcпользования уведомлений ни в одном крупном веб-приложении, кроме продуктов Google (такие как GMail например), либо в расширениях Google Chrome.
