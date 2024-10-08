# OAuth

OAuth 2.0 — протокол аутентификации, позволяющий выдать одному сервису (приложению) права на доступ к ресурсам пользователя на другом сервисе. Протокол избавляет от необходимости доверять приложению логин и пароль, а также позволяет выдавать ограниченный набор прав, а не всё сразу.

OAuth 2.0 основан на использовании базовых веб-технологий: HTTP-запросах, редиректах и т. п. Поэтому использование OAuth возможно на любой платформе с доступом к интернету и браузеру: на сайтах, в мобильных и desktop-приложениях, плагинах для браузеров.

Клиент должен одним из четырёх способов получить от сервера авторизации (OAuth провайдера) токен доступа (Access Token). Далее клиент будет использовать этот Access Token в запросах к Серверу ресурсов, чтобы получать данные.

##### Особенности Access Token

- Ограниченное время жизни.
- Его можно отозвать. Если есть подозрение, что токен украли, то мы просто запрещаем получать данные с помощью этого токена.
- Компрометация токена не значит компрометирование логина и пароля. Из токена никак не получить логин и пароль.
- По токену может быть доступна только часть данных (**scope**). Клиент запрашивает список разрешений, которые необходимы ему для работы, а Владелец ресурсов, то есть пользователь, решает выдать такие права или нет. Например, можно выдать права только на просмотр списка контактов, тогда читать письма или редактировать контакты будет нельзя.

Помимо Access Token сервер авторизации может выдавать также Refresh Token. Это токен, который позволяет запросить новый **access_token** без участия Владельца ресурсов. Время жизни у такого токена намного больше Access Token, и его потеря гораздо серьёзнее: используя Refresh Token, злоумышленник может запросить у Сервера авторизации новый токен доступа и войти в систему.

Сервер авторизации должен располагать информацией о каждом клиенте, который делает к нему запрос, то есть каждый клиент должен быть зарегистрирован. Зарегистрировав клиента, мы получаем **client_id** и **client_secret** и обязаны передавать, как минимум **client_id** в каждом запросе.

Не все клиенты могут гарантировать сохранность **client_secret**, поэтому он есть не у всех. Например, SPA без бэкенда, теоретически достать оттуда можно что угодно.

##### Способы получения Access Token

1. По данным клиента (**Client Credentials**). Получаем токен по **client_id** и **client_secre**t.
2. По логину и паролю пользователя (**Resource Owner Password Credential**). Только для безопасных клиентов, заслуживающих полного доверия.
3. По авторизационному коду (**Authorization Code Grant**). Самый сложный и самый надёжный способ.
4. Неявно (**Implicit Grant**)

#### Client Credentials
1. API 1 передаёт серверу авторизации **client_id** и **client_secret**.
2.  В ответ он получает **access_token**, с помощью которого может обратиться к API 2.
3. API 1 обращается к API 2.
4. API 2 получает запрос с **access_token** и обращается к серверу авторизации для проверки действительности переданного токена (**RFC 7662**).
#### Resource Owner Password Credential
1. Владелец ресурсов передаёт свой логин и пароль Клиенту.
2. Клиент отправляет Авторизационному серверу логин и пароль клиента, а также свой **client_id** и **client_secret**.
3. 3. Если предоставленные пользователем учётные данные корректны, сервер авторизации вернёт ответ **application/json**, содержащий **access_token**.
#### Authorization Code Grant
1. Пользователь на сайте нажимает кнопку авторизации, например через Facebook.
2. Происходит редирект на авторизационный сервер.
3. Если активной сессии нет, то пользователь должен залогиниться. Если активная сессия есть, то просто подтвердить авторизацию.
4. Если всё пойдёт хорошо, вы получите ответ **HTTP 302**. Код авторизации включён в конце URL-адреса: в параметре **?code=SplewFEFofer**
5. Теперь, когда у вас есть код авторизации, вы должны обменять его на токены. Используя извлеченный код авторизации из предыдущего шага, вам нужно будет выполнить **POST** на URL-адрес токена.
6. Если все пойдет хорошо, вы получите ответ **HTTP 200** с полезной нагрузкой, содержащей значения **access_token**, **refresh_token**, **id_token** и **token_type**.
7. Чтобы вызвать ваш API из обычного веб-приложения, приложение должно передать полученный токен доступа в заголовке авторизации вашего HTTP-запроса.
#### Implicit Grant
Используется на одностраничных сайтах без бэкенда.
1. Пользователь на сайте жмёт на кнопку, происходит редирект на сервер авторизации.
2. Пользователь вводит логин и пароль, если он не авторизован.
3. Происходит редирект обратно, но **access_token** возвращается в URL сразу: 
   https://domain.com/back_path#access_token=8ijfwWEFFf0wefOofreW6rglk
