# Как настроить авторизацию пользователя через VK ID в вашем web-приложении
Из этой статьи вы узнаете, как установить [VK SDK](https://dev.vk.com/libraries/vk-sdk) и настроить авторизацию пользователя через [VK ID](https://dev.vk.com/vkid/about) в web-приложении.
Перед настройкой авторизации ваше web-приложение должно быть [создано и настроено](https://dev.vk.com/vkid/create-application) на платформе для разработчиков.

## Поддерживаемые версии
Документация актуальна для версий 0.103-24411 [VK SDK](https://dev.vk.com/libraries/vk-sdk) для Web.

## Шаг 1. Установка VK SDK
Чтобы установить VK SDK, в командной строке выполните команду:
* **npm**
```
npm install @vkontakte/superappkit
```
* **yarn**
```
yarn add @vkontakte/superappkit
import { Connect } from '@vkontakte/superappkit'; // Импорт модуля для корректной работы VK ID. 
```

## Шаг 2. Авторизация через VK ID
Чтобы пользователь мог авторизоваться через VK ID в вашем web-приложении, в коде вызовите один из представленных в таблице методов модуля `Connect`.

**Метод** | **Описание** 
:--- | :---
Connect.redirectAuth() | Позволяет реализовать авторизацию с редиректом. VK ID откроет авторизацию в рамках текущей страницы. После успешной авторизации выполнит редирект на адрес, указанный в параметре url.  **Важно**: Адрес должен устанавливать безопасное соединение и использовать схему https. При передаче данных в открытом виде, то есть использовании http,возникнет бесконечная загрузка.
Connect.oneTapAuth() | 	Позволяет реализовать авторизацию с помощью всплывающего окна или кнопки. В первом аргументе AuthButtonType задается, какой элемент интерфейса будет использоваться — floating или button.
Connect.floatingOneTapAuth() | Позволяет реализовать авторизацию c помощью всплывающего окна в правом верхнем углу экрана. Является аналогом метода Connect.oneTapAuth('floating', ...).
Connect.buttonOneTapAuth() | Позволяет реализовать авторизацию с помощью кнопки, которую можно вставить в любое место.  Во втором аргументе доступно два свойства: container (в виде html-элеменов, куда будет вставлено окно с кнопкой) и options (ButtonOneTapAuthOptions, объект в этом параметре управляет показом элементов внутри окна с кнопкой).
Connect.userDataPolicy() | Позволяет реализовать авторизацию с использованием модального всплывающего окна с политиками и передаваемыми данными.


## Примеры использования методов

### Пример авторизации с редиректом
```JavaScript
Connect.redirectAuth({
  // Строка с url, куда выполнится редирект после авторизации.
  url: 'https://...',
  // Cостояние приложения или любая произвольная строка, которая будет добавлена к url после авторизации.
  state: '...',
  // Показ окна для выполнения действий пользователя, например ввода номера телефона.
  screen: '...'
});

// Важно: url должен иметь https схему. Если использовать http, из-за передачи данных в отркытом виде возникнет бесконечная загрузка.

// В url в query-string передается параметр payload с данными авторизации. Формат данных в payload - VKSilentTokenPayload.
// Если передать параметр state, то после успешной авторизации в url в query-string передастся параметр state с таким же значением, как и в методе Connect.redirectAuth().

// Если передать параметр screen со значением phone, то всегда будет показываться экран с вводом номера телефона,  даже если аккаунт пользователя найден.

Connect.redirectAuth({ url: string, state?: string, screen?: 'phone' });
```


### Пример авторизации c помощью всплывающего окна в правом верхнем углу экрана
```JavaScript
Connect.floatingOneTapAuth({
 callback: function(evt: any) {
   switch (type) {
    case ConnectEvents.OneTapAuthEventsSDK.LOGIN_SUCCESS:
      if (evt.payload.auth) {
        return onAuthUser(evt);}
      else {console.error(evt);}
        break;
// Для события LOGIN_SUCCESS нужно открыть полноценный VK ID, чтобы пользователь дорегистрировался или прошел проверку по номеру телефона.
    case ConnectEvents.OneTapAuthEventsSDK.FULL_AUTH_NEEDED:
    case ConnectEvents.OneTapAuthEventsSDK.PHONE_VALIDATION_NEEDED:
        return Connect.redirectAuth({ screen: 'phone', url: 'https://...'});}},
});
// Чтобы скрыть окно, используйте метод VKOneTapAuthResult.destroy().
const oneTapObj = Connect.floatingOneTapAuth(...);
...
oneTapObj.destroy();
```

### Пример авторизации в виде кнопки
```JavaScript
var const buttonOneTap = Connect.buttonOneTapAuth({
  callback: function(evt: any) {
    const type = evt.type; 
    if (!type) {
      return;
}
    switch (type) {
     ...
    case ConnectEvents.OneTapAuthEventsSDK.LOGIN_SUCCESS:
      return onAuthUser(evt);
// Для событий PHONE_VALIDATION_NEEDED и FULL_AUTH_NEEDED нужно открыть полноценный VK ID, чтобы пользователь дорегистрировался или прошел проверку по номеру телефона.
    case ConnectEvents.OneTapAuthEventsSDK.FULL_AUTH_NEEDED:
    case ConnectEvents.OneTapAuthEventsSDK.PHONE_VALIDATION_NEEDED:
    case ConnectEvents.ButtonOneTapAuthEventsSDK.SHOW_LOGIN:
      return Connect.redirectAuth({ screen: 'phone', url: 'https://...'
});
    case ConnectEvents.ButtonOneTapAuthEventsSDK.SHOW_LOGIN_OPTIONS:
// Если для параметра screen задать значение phone, можно сразу открыть окно ввода телефона в VK ID.
      return Connect.redirectAuth({ screen: 'phone', source: type 
}).then(onAuthUser, () => alert('Ошибка!'));
}
      return;
},
   options: {
      showAlternativeLogin: true, // Отображает кнопку входа другим способом.
      showAgreements: false, // В значении true отображает окно политик конфиденциальности, если пользователь еще не принимал политики.
      showAgreementsDialog: true, // Отображает диалоговое окно принятия политик.
      displayMode: 'default', // / Отображает данные пользователя. Возможные значения: default — только имя, name_phone — имя и телефон, phone_name — телефон и имя.
},
});
// Фрэйм с кнопкой можно передать в любой элемент.
if (buttonOneTap) {
document.getElementById('someHtmlElementId').appendChild(buttonOneTap.getF
rame());
}
```

### Пример авторизации с использованием модального всплывающего окна с политиками и данными
```JavaScript
Connect.userDataPolicy(...)
   .show()
   .then(() => {
       console.log('Policy was accepted');
     }); 
```
В ответ VK SDK вернет результат авторизации, данные пользователя (идентификатор, первую букву фамилии, имя, аватар и замаскированный номер телефона) и [Silent token](https://dev.vk.com/vkid/tokens/silent-token) (параметр token).

**Пример ответа**
```
payload: {
  auth: number; // Признак активной сессии с ВКонтакте на устройстве пользователя. Возможные значения: 0 — сессии нет,  1 — сессия есть.
  token: string; // Токен для совершения действий от лица пользователя. В данном случае возвращает Silent token, который необходимо обменять на Access token.
  ttl: number; // Срок жизни токена в секундах.
  type: string; // Тип токена. В данном случае — Silent token.
  user: {
    id: number;
    first_name: string;
    last_name: string;
    avatar: string;
    phone: string;
  }; // Данные пользователя: фамилия, имя, аватар и номер телефона.
  uuid: string; // Уникальный идентификатор, который выдается при обращении к VK SDK.
  oauthProvider?: string;
  external_user?: ExternalUser;
};
```

После получения результата авторизации обменяйте полученный Silent token из параметра token  на [Access token](https://dev.vk.com/vkid/tokens/access-token).

В результате успешной настройки пользователь сможет авторизоваться через [VK ID](https://dev.vk.com/vkid/about) в вашем web-приложении.