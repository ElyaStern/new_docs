# Описание методов Connect

**Метод** | **Описание** 
:--- | :---
Connect.redirectAuth() | Позволяет реализовать авторизацию с редиректом. VK ID откроет авторизацию в рамках текущей страницы. После успешной авторизации выполнит редирект на адрес, указанный в параметре url.  **Важно**: Адрес должен устанавливать безопасное соединение и использовать схему https. При передаче данных в открытом виде, то есть использовании http, возникнет бесконечная загрузка.
Connect.oneTapAuth() | 	Позволяет реализовать авторизацию с помощью всплывающего окна или кнопки. В первом аргументе AuthButtonType задается, какой элемент интерфейса будет использоваться — floating или button.
Connect.floatingOneTapAuth() | Позволяет реализовать авторизацию c помощью всплывающего окна в правом верхнем углу экрана. Является аналогом метода Connect.oneTapAuth('floating', ...).
