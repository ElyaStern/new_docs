# Как настроить авторизацию пользователя через VK ID в вашем web-приложении
Из этой статьи вы узнаете, как установить [VK SDK](https://dev.vk.com/libraries/vk-sdk) и настроить авторизацию пользователя через [VK ID](https://dev.vk.com/vkid/about) в web-приложении.
Перед настройкой авторизации ваше web-приложение должно быть [создано и настроено](https://dev.vk.com/vkid/create-application) на платформе для разработчиков.

## Поддерживаемые версии
Документация актуальна для версий 0.103-24411 [VK SDK](https://dev.vk.com/libraries/vk-sdk) для Web.

## Установка VK SDK
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

