# Начало работы с XSOLLA API 
## Введение
### Состав XSOLLA API
XSOLLA API включает в себя:
* [**Pay Station API**](https://developers.xsolla.com/ru/api/v2/pay-station/) — платежный интерфейс и методы токенизации;
* [**Store API**](https://developers.xsolla.com/ru/api/v2/store/) — методы для работы с такими модулями магазина, как *Виртуальная валюта*, *Виртуальные предметы*, *Подписки*, *Игровые ключи*, *Акции*, *Купоны*;
* [**Publisher Account API**](https://developers.xsolla.com/ru/api/v2/publisher-account/) — управление проектами и пользователями Личного кабинета, методы для работы с отчетами, тикетами поддержки;
* [**Login API**](https://developers.xsolla.com/ru/api/v2/login/) — методы аутентификации пользователей через собственный интерфейс. Подробнее можно узнать в [руководстве по интеграции](https://developers.xsolla.com/ru/doc/login/#guides_login_api_integration).
### Основные сведения
XSOLLA API основано на принципах [REST-архитектуры](https://en.wikipedia.org/wiki/Representational_state_transfer). Для обозначения ошибок и URL, аналогичных структуре каталогов, используются HTTP коды ответов. Ответы API и информация об ошибках возвращаются в виде JSON.

В API используются встроенные особенности HTTP, такие как HTTP аутентификация и HTTP методы, которые понимаются всеми HTTP клиентами. Помимо этого поддерживается CORS (Cross-origin resource sharing) для обеспечения безопасной работы с API клиентского приложения.

XSOLLA API работает со следующими базовыми URL:
* `https://api.xsolla.com` — Pay Station API, Store API, Publisher Account API;
* `https://login.xsolla.com/api` — Login API.

> ***Примечание!*** 
> ~~~ 
> Многие ресурсы включают параметр merchant_id, который указывает, 
> что приложение работает именно от вашего имени. 
> ~~~

## Запросы и ответы
Запросы к XSOLLA APi:
* Отправляются по протоколу HTTP.
* Используют [TLS](https://habr.com/ru/post/258285/) версии 1.2 и выше.
* Должны содержать параметры аутентификации (Подробнее см. раздел [Аутентификация](https://github.com/aalexeeva/summer_school/blob/master/XSOLLA%20Getting%20started%20ru.md#%D0%B0%D1%83%D1%82%D0%B5%D0%BD%D1%82%D0%B8%D1%84%D0%B8%D0%BA%D0%B0%D1%86%D0%B8%D1%8F)).
* Для запросов типа *PUT* и *POST* должны сожержать дополнительный заголовок `Content-Type: application/json`.

Пример:
```
Authorization: Basic <your_authorization_basic_key>
Content-Type: application/json
```
> ***Предупреждение!*** 
> ~~~
> По умолчанию все запросы возвращают ответ в виде JSON с заголовком `Content-Type: application/json`.
> ~~~

## Изменения в XSOLLA API
### Версионирование
Все разделы XSOLLA API поддерживают версионирование. Новая версия выпускается всякий раз, когда появляются несовместимые с текущей версией изменения. 

Версия обозначается идентификатором ("v1"/"v2" и т. п.), который указывается в URL после префикса `/merchant`.

В начале работы с API рекомендуется использовать самую последнюю версию. 
Если не была указана версия команды, то по умолчанию будет вызвана первая версия.

> ***Предупреждение!*** 
> ~~~
> Целостность работы API гарантируется в рамках одной версии.
> ~~~

### Возможности, подлежащие изменениям
XSOLLA может добавлять следующие функциональные возможности API:
* Новые ресурсы API.
* Необязательные параметры запроса.
* Новые свойства к существующим ответам API.
* Новые значения для параметров, имеющих перечисление возможных значений.
* Новые типы вебхуков и новые параметры в JSON.
* Необязательные заголовки в HTTP-запросы.

Также XSOLLA может добавлять, изменять или удалять незадокументированные функциональные возможности в любое время.

Во время работы API может:
* Отклонять любой запрос, если его параметры принимают недопустимые значения.
* Принимать ошибочно сформированные запросы из-за избыточно мягкого анализа синтаксиса. Однако, если в дальнейшем проверка будет более строгой, подобные запросы могут быть отклонены.

> ***Предупреждение!*** 
> ~~~
> Ваш клиент должен продолжать работать независимо от вышеперечисленных изменений. 
> К примеру, новые параметры JSON, которые не распознаются вашим клиентом, не должны мешать его работе.
> ~~~

### Последние изменения для версии 2.0
Изменен URL команды: `https://api.xsolla.com/merchant/v2/merchants/{merchant_id}/reports/transactions/registry.{format}`.

Изменен формат ответа:
* По отмененным транзакциям заполняются отрицательные значения для полей:

   **Payment system fee, fixed**; **Xsolla revenue share, fixed**; **Sales Tax**; **Virtual currency purchase amount**; **Simple checkout purchase amount**; **Items purchase amount**; **Repatriation costs, total**; **Fees covered, fixed**; **Fees covered by Balance**; **Payout amount from payment system**; **Payment amount from Balance**; **Pin codes purchase amount**.

* Добавлены новые поля:

   **Payment system type** — тип способа оплаты; **VAT** — VAT, который видит пользователь; **VAT, %** — процент VAT в стране; **Refund reason comment** — комментарий к причине отмены транзакции; **Direct account** — указание на то, является ли аккаунт прямым или нет.

* Удалены поля:

   **Payment system external fee (%)**; **Payment system external fee amount**; **Payment system external fee currency**.
* Переименованы поля:

   **VAT** -> **Deducted VAT**; **VAT (%)** -> **Deducted VAT, %**.
* Значения следующих полей не заполняются, если данные для них равны 0:

   **Virtual currency amount**; **Virtual currency purchase amount**; **Simple checkout purchase amount**; **Pin codes purchase amount**.

* Изменения значений полей по умолчанию:

   * Значение поля **Virtual currency purchase currency** не заполняется, если **Virtual currency purchase amount** равно 0.

   * Значение поля **Simple checkout purchase currency** не заполняется, если **Simple checkout purchase amount** равно 0.

   * Значение поля **Pin codes purchase currency** не заполняется, если **Pin codes purchase amount** равно 0. Поле **Pin codes purchase cart content** остается пустым.

   * Значение поля **Deducted VAT, %** заполняется 0, если значение поля **Deducted VAT** равно 0.

## Аутентификация
XSOLLA API использует базовую HTTP-аутентификацию. 
Все запросы к API должны содержать заголовок `Authorization: Basic <your_authorization_basic_key>`, где **<your_authorization_basic_key>** — пара **merchant_id:api_key**, закодированная по стандарту Base64.

Значения параметров **merchant_id** и **api_key** вы можете найти в [Личном кабинете XSOLLA](https://publisher.xsolla.com/) следующим образом:
* **merchant_id**: находится в *Настройки компании* > *Компания* в поле *ID продавца*;
* **api_key**: находится в *Настройки компании* в поле *Ключ API*.

> ***Предупреждение!*** 
> ~~~
> Никому не сообщайте ваш ключ API, так как он дает доступ к управлению аккаунтом и проектами в Личном кабинете.
> ~~~

Примеры:

***HTTP***
```HTTP
GET https://api.xsolla.com/merchant/v2/merchants/{merchant_id}/events/messages
Headers:
  Authorization: Basic <your_authorization_basic_key>
```

***CURL***
```CURL
curl --request GET \
--url 'https://api.xsolla.com/merchant/v2/merchants/{merchant_id}/events/messages' \
--header 'authorization: Basic <your_authorization_basic_key>'
```

***PHP***
```PHP
<?php

// if you use Xsolla SDK for PHP
use Xsolla\SDK\API\XsollaClient;
$xsollaClient = XsollaClient::factory(array(
    'merchant_id' => MERCHANT_ID,
    'api_key' => API_KEY
));
$eventsList = $client->ListEvents(array());

// if you don’t use Xsolla SDK for PHP
$client = new http\Client;
$request = new http\Client\Request;

$request->setRequestUrl('https://api.xsolla.com/merchant/v2/merchants/{merchant_id}/events/messages');
$request->setRequestMethod('GET');
$request->setHeaders(array(
  'authorization' => 'Basic <your_authorization_basic_key>'
));

$client->enqueue($request)->send();
$response = $client->getResponse();

echo $response->getBody();
```

***C#***
```C#
var client = new RestClient("https://api.xsolla.com/merchant/v2/merchants/{merchant_id}/events/messages");
var request = new RestRequest(Method.GET);
request.AddHeader("authorization", "Basic <your_authorization_basic_key>");
IRestResponse response = client.Execute(request);
```

***PYTHON***
```PYTHON
import http.client

conn = http.client.HTTPSConnection("api.xsolla.com")

headers = { 'authorization': "Basic <your_authorization_basic_key>" }

conn.request("GET", "/merchant/v2/merchants/{merchant_id}/events/messages", headers=headers)

res = conn.getresponse()
data = res.read()
```

***RUBY***
```RUBY
require 'uri'
require 'net/http'

url = URI("https://api.xsolla.com/merchant/v2/merchants/{merchant_id}/events/messages")

http = Net::HTTP.new(url.host, url.port)
http.use_ssl = true
http.verify_mode = OpenSSL::SSL::VERIFY_NONE

request = Net::HTTP::Get.new(url)
request["authorization"] = 'Basic <your_authorization_basic_key>'

response = http.request(request)
puts response.read_body
```

***JAVA***
```JAVA
OkHttpClient client = new OkHttpClient();

Request request = new Request.Builder()
  .url("https://api.xsolla.com/merchant/v2/merchants/{merchant_id}/events/messages")
  .get()
  .addHeader("authorization", "Basic <your_authorization_basic_key>")
  .build();

Response response = client.newCall(request).execute();
```

***JS***
```JS
var data = null;

var xhr = new XMLHttpRequest();
xhr.withCredentials = true;

xhr.addEventListener("readystatechange", function () {
  if (this.readyState === this.DONE) {
    console.log(this.responseText);
  }
});
xhr.open("GET", "https://api.xsolla.com/merchant/v2/merchants/{merchant_id}/events/messages");
xhr.setRequestHeader("authorization", "Basic <your_authorization_basic_key>");

xhr.send(data);
```

## Команды управления ресурсами
Все команды XSOLLA API указывают на тип данных, который должен быть обработан, и на действие, которое требуется совершить с этими данными. Стандартный список действий:
* Создание: `POST` - создает и сохраняет сущность соответствующего типа.
* Список: `GET` - возвращает список сущностей, удовлетворяющих запросу.
* Получение: `GET` - возвращает данные по конкретному идентификатору, которые вы присылаете в запросе.
* Замена: `PUT` - заменяет все поля для сущности, переданной в запросе.
* Изменение: `PATCH` - изменяет только указанные поля существующего объекта, который соответствует идентификатору из запроса
* Удаление: `DELETE` - удаляет существующий объект, который соответствует идентификатору из запроса.

## Формат даты
Все представления дат передаются в строках согласно ISO 8601. Дату можно передавать либо в формате UTC (например, 2013-01-15T00:00:00Z), либо в виде смещения от UTC для обозначения часового пояса (например, 2013-01-15T00:00:00-08:00 8 часов от UTC).

## Постраничная навигация
Результат запроса должен выводиться постранично. Это означает, что вместо вывода всех результатов выполнения запроса выводится только часть. Для этого необходимо использовать параметры **limit** и **offset**.

## Типы ошибок
XSOLLA использует стандартные HTTP коды для обозначения успешных или неуспешных запросов. В общем случае код в 2xx диапазоне обозначает успех, код диапазона 4хх означает ошибку в результате передачи некорректных параметров (например, обязательный параметр не передан, или авторизация не прошла и т.д.), код 5хх диапазона означает серверную ошибку.

Список поддерживаемых HTTP ответов:
* 200, 201, 204 — успешный ответ.
* 400 Bad Request — отсутствует обязательный параметр, полное описание можно найти в теле ответа.
* 401 Unauthorized — недействительный ключ API.
* 402 Request Failed — параметры верны, но запрос не прошел.
* 403 Forbidden — нет прав доступа, полное описание можно найти в теле ответа.
* 404 Not Found — соответствующего ресурса нет по данному URI.
* 409, 422 — параметры запроса не верны.
* 412 Precondition Failed — ошибка возникает, когда проект не активирован (используется в методе получения токена).
* 415 Unsupported Media Type — *Content-Type: application/json HTTP* заголовок не был отправлен.
* 500, 502, 503, 504 Server Errors — серверная ошибка.

Не все ошибки в точности соответствуют HTTP кодам. Например, если запрос был верным, но не смог завершиться успешно, будет возвращена ошибка 422.

В случае ошибочного ответа возвращается JSON объект со следующими полями:

Поле | Тип | Описание
---- | --- | --------
http_status_code | integer | HTTP код
message | string | Понятное сообщение с описанием ошибки. Текст всегда на английском языке. Поле не должно использоваться в случае какой-либо ошибки, так как его значение может измениться в будущем
extended_message | string | Расширенное описание ошибки
request_id | string | Уникальный ID запроса, используется, чтобы помочь разработчикам XSOLLA диагностировать проблему

Пример JSON объекта с ошибкой:
```
{
    "http_status_code": 500,
    "message": "Internal Server Error",
    "extended_message": null,
    "request_id": "6445b85"
}
```

## Токен
Для обеспечения безопасности проведения платежа XSOLLA API использует токен, который содержит внутри себя платежные параметры, вместо прямого получения данных через HTTP GET запрос на страницу оплаты. Перед открытием страницы оплаты необходимо получить новый токен. Время жизни токена — 24 часа.

### Получение токена
Можно создать токен с любыми параметрами пользователя. При получении токена необходимо отправить эти параметры на сторону разработчиков API, которые после успешной оплаты отправят их обратно. Токен может содержать только описанные ниже параметры, либо параметры, предопределенные партнером.

**HTTP-ЗАПРОС**
``` POST https://api.xsolla.com/merchant/v2/merchants/{merchant_id}/token```

Поле | Тип | Описание
---- | --- | --------
user |object|Объект с информацией о пользователе.
user.id|object|Обязательный объект с данными об ID пользователя.
user.id.value|string|ID пользователя.
user.name|object|Объект с информацией о нике пользователя.
user.name.value|string|Ник пользователя.
user.email|object|Объект с информацией об email пользователя.
user.email.value|string|Email пользователя. Должен быть валидным в соответствии с протоколом RFC 822.
user.phone|object|Объект с информацией о телефоне пользователя.
user.phone.value|string|Номер телефона пользователя.
user.country|object|Объект с информацией о стране пользователя.
user.country.value|string|Двухбуквенное обозначение страны согласно стандарту ISO 3166-1 alpha-2.
user.country.allow_modify|boolean|Разрешение изменить страну на странице оплаты. По умолчанию 'false'.
user.attributes|object|Объект с данными об атрибутах пользователя, необходимых для фильтрации списка предметов. Параметры передаются в JSON хэше парами ключ-значение.
user.steam_id|object|Объект с данными о Steam ID пользователя.
user.steam_id.value|string|Steam ID пользователя.
user.tracking_id|object|Объект с данными о Tracking ID пользователя.
user.tracking_id.value|string|Уникальный Tracking ID (используется для проведения рекламных кампаний).
user.public_id.value|string|Параметр, позволяющий однозначно идентифицировать пользователя. В отличие от user ID, известен пользователю (адрес электронной почты, никнейм, и т.д.). Параметр может использоваться при оплате покупки вне игрового магазина (например, кнопка игры в терминалах оплаты).
user.utm|object|Объект с данными о характеристиках трафика.
user.utm.utm_source|string|Источник трафика.
user.utm.utm_medium|string|Канал трафика (контекстная реклама, медийная реклама, email-рассылка).
user.utm.utm_campaign|string|Название кампании (транслитерированное или переведенное на английский язык).
user.utm.utm_term|string|Ключевое слово кампании. При использовании этого параметра в статистике будут собираться данные по тем ключевым словам, которые используются для таргетинга рекламной кампании (а не по поисковым запросам). В Google Analytics содержимое метки utm_term попадает в единый отчет с поисковыми запросами.
user.utm.utm_content|string|Содержание кампании.
user.is_legal|boolean|Является ли пользователь юридическим лицом.
user.legal|object|Объект с реквизитами юридического лица. Объект и все его параметры являются обязательными, если в user.is_legal передано значение ‘true’.
user.legal.name|string|Полное юридическое наименование.
user.legal.address|string|Полный юридический адрес.
user.legal.vat_id|string|Индивидуальный идентификатор налогоплательщика.
user.legal.country|string|Двухбуквенное обозначение страны согласно стандарту ISO 3166-1 alpha-2.
settings|object|Объект, содержащий настройки проекта.
settings.external_id|string|Внешний ID транзакции.
settings.project_id|integer|ID игры в XSOLLA. Находится в Личном кабинете. Обязательный.
settings.language|string|Язык интерфейса. Используется двухбуквенное обозначение (в нижнем регистре) согласно стандарту ISO 639-1.
settings.return_url|string|URL страницы, на которую пользователь будет перенаправлен после совершения платежа. Параметры 'user_id', 'foreigninvoice', 'invoice_id' и 'status' будут автоматически добавлены к ссылке.
settings.currency|string|Предпочтительная валюта платежа. Используется трехбуквенное обозначение валюты согласно стандарту ISO 4217.
settings.mode|string|Атрибут для проведения тестовых платежей, в который необходимо передать значение 'sandbox'. При проведении тестового платежа URL для страницы оплаты будет https://sandbox-secure.xsolla.com.
settings.payment_method|integer|ID способа оплаты.
settings.payment_widget|string|Виджет оплаты. Принимает значения 'paybycash' или 'giftcard'. При передаче этого параметра пользователь перенаправляется на виджет Pay with Cash или Gift Cards соответственно.
settings.ui|object|Объект с настройками интерфейса.
settings.ui.theme|string|Внешний вид интерфейса оплаты. Может принимать значения 'default' (по умолчанию) или 'default_dark'.
settings.ui.size|string|Размер платежного интерфейса. В зависимости от требуемых размеров платежного интерфейса, параметр может принимать следующие значения: <ul><li>'small': Наименьший размер платежного интерфейса. Используется в случаях, когда размеры окна строго ограничены (размер: 620 x 630).</li><li>'medium': рекомендуемый размер платежного интерфейса, оптимален при открытии в lightbox (размер: 740 x 760).</li><li>'large': желательно открывать в новом окне/вкладке (размер: 820 x 840).</li></ul>
settings.ui.version|string|Тип устройства. Может принимать значения 'desktop' (по умолчанию) или 'mobile'.
settings.ui.desktop|object|Объект с данными настроек для desktop-версии.
settings.ui.desktop.header|object|Объект с настройками header.
settings.ui.desktop.header.is_visible|boolean|Видимость header. Показывает, должен ли header отображаться на странице оплаты.
settings.ui.desktop.header.visible_logo|boolean|Видимость логотипа. Если значение 'true', то логотип будет отображаться в header (необходимо сначала прислать файл с логотипом вашему менеджеру).
settings.ui.desktop.header.visible_name|boolean|Видимость названия игры. Должно ли название игры отображаться в header.
settings.ui.desktop.header.visible_purchase|boolean|Видимость описания покупки (purchase.description.value) для отображения в header, по умолчанию ‘true’.
settings.ui.desktop.header.type|string|Внешний вид header. Может принимать значения 'compact' (название игры и ID пользователя не будут показываться в header) или 'normal'.
settings.ui.desktop.header.close_button|boolean|Видимость кнопки "Закрыть" в настольной версии платежного интерфейса. Нажатие на кнопку закрывает платежный интерфейс и перенаправляет пользователя на адрес, указанный в параметре "settings.return_url". По умолчанию 'false'.
settings.ui.desktop.subscription_list|object|Объект с данными настройки списка подписок.
settings.ui.desktop.subscription_list.layout|string|Шаблон списка. Принимает значения 'list' (по умолчанию) или 'grid'.
settings.ui.desktop.subscription_list.description|string|Описание списка подписок, которое появится перед списком рекуррентных планов в интерфейсе оплаты.
settings.ui.desktop.subscription_list.display_local_price|boolean|Если значение 'true' и если локальная валюта пользователя отличается от базовой валюты плана, пользователь будет видеть две цены: цену в локальной валюте и цену в базовой валюте плана.
settings.ui.desktop.virtual_item_list|object|Объект с данными настройки списка предметов.
settings.ui.desktop.virtual_item_list.layout|string|Шаблон списка. Принимает значения 'list' (по умолчанию) или 'grid'.
settings.ui.desktop.virtual_item_list.button_with_price|boolean|Если значение 'true', то цена за предмет будет показана внутри кнопки. Если 'false', то цена будет слева от кнопки. По умолчанию 'false'.
settings.ui.desktop.virtual_item_list.view|string|Вывод списка групп виртуальных предметов либо в виде вертикального меню, либо над окном в виде горизонтального меню. Принимает значения 'horizontal_navigation' или 'vertical' (по умолчанию).
settings.ui.desktop.virtual_currency_list|object|Объект с данными настройки списка пакетов виртуальной валюты.
settings.ui.desktop.virtual_currency_list.description|string|Здесь вы можете передать текст про виртуальную валюту. Текст появится перед списком пакетов виртуальной валюты в интерфейсе оплаты.
settings.ui.desktop.virtual_currency_list.button_with_price|boolean|Если значение 'true', то цена за предмет будет показана внутри кнопки. Если 'false', то цена будет слева от кнопки. По умолчанию 'false'.
settings.ui.header.visible_virtual_currency_balance|boolean|Видимость элемента header в интерфейсе оплаты. По умолчанию 'true'.
settings.ui.mobile.mode|string|Пользователь может совершить платеж только через сохраненные способы оплаты. Принимает значение 'saved_accounts'.
settings.ui.mobile.header.close_button|boolean|Видимость кнопки "Закрыть" в мобильной версии платежного интерфейса. Нажатие на кнопку закрывает платежный интерфейс и перенаправляет пользователя на адрес, указанный в параметре "settings.return_url". По умолчанию 'false'.
settings.ui.mobile.footer.is_visible|boolean|Видимость footer в мобильной версии платежного интерфейса.
settings.ui.license_url|string|Ссылка на лицензионное соглашение.
settings.ui.components|object|Объект с данными настройки пунктов меню.
settings.ui.components.virtual_items|object|Объект с данными настройки меню предметов.
settings.ui.components.virtual_items.order|integer|Место вкладки в меню.
settings.ui.components.virtual_items.hidden|boolean|Видимость вкладки в меню.
settings.ui.components.virtual_items.selected_group|string|Группа, которая будет выбрана при открытии вкладки.
settings.ui.components.virtual_items.selected_item|string|Предмет, который будет выбран при открытии вкладки. Должен быть передан артикул предмета.
settings.ui.components.virtual_currency|object|Объект с данными настройки меню виртуальной валюты.
settings.ui.components.virtual_currency.custom_amount|boolean|Возможность ввода произвольного количества виртуальной валюты в интерфейсе оплаты.
settings.ui.components.virtual_currency.order|integer|Место вкладки в меню.
settings.ui.components.virtual_currency.hidden|boolean|Видимость вкладки в меню.
settings.ui.components.subscriptions|object|Объект с данными настройки меню подписок.
settings.ui.components.subscriptions.order|integer|Место вкладки в меню.
settings.ui.components.subscriptions.hidden|boolean|Видимость вкладки в меню.
settings.ui.mode|string|Платежный интерфейс в режиме Личного кабинета. Принимает значение 'user_account'. Header содержит только навигационное меню Личного кабинета; исключается возможность выбора предмета и оплата покупки; режим Личного кабинета доступен только в desktop-режиме.
settings.ui.user_account|object|Объект с данными об аккаунте пользователя.
settings.ui.user_account.info|object|Страница 'Мой аккаунт'.
settings.ui.user_account.info.order|integer|Место вкладки в меню.
settings.ui.user_account.info.enable|boolean|Видимость вкладки в меню. По умолчанию 'false'.
settings.ui.user_account.history|object|Страница 'История' пользователя.
settings.ui.user_account.history.order|integer|Место вкладки в меню.
settings.ui.user_account.history.enable|boolean|Видимость вкладки в меню. По умолчанию 'false'.
settings.ui.user_account.payment_accounts|object|Страница 'Мои платежные аккаунты'.
settings.ui.user_account.payment_accounts.order|integer|Место вкладки в меню.
settings.ui.user_account.payment_accounts.enable|boolean|Видимость вкладки в меню. По умолчанию 'false'.
settings.ui.user_account.subscriptions|object|Страница 'Управление подписками'.
settings.ui.user_account.subscriptions.order|integer|Место вкладки в меню.
settings.ui.user_account.subscriptions.enable|boolean|Видимость вкладки в меню. По умолчанию 'false'.
settings.shipping_enabled|boolean|Отображение страницы ввода адреса заставки. По умолчанию 'false'.
purchase|object|Объект с информацией о заказе.
purchase.virtual_currency|object|Объект с данными о виртуальной валюте.
purchase.virtual_currency.quantity|float|Количество виртуальной валюты в заказе.
purchase.virtual_currency.currency|string|Валюта пакета виртуальной валюты, на основе которой будут сделаны все расчеты.
purchase.virtual_items|object|Объект с данными о предметах.
purchase.virtual_items.currency|string|Валюта предмета в заказе, на основе которой будут сделаны все расчеты.
purchase.virtual_items.items|array|Массив с данными о предмете.
purchase.virtual_items.items.sku|string|ID предмета (артикул).
purchase.virtual_items.items.amount|integer|Количество экземпляров предмета в заказе.
purchase.virtual_items.available_groups|array|Массив с данными о группах предметов. Только предметы из указанных групп будут показаны в интерфейсе оплаты.
purchase.subscription|object|Объект с данными о подписке.
purchase.subscription.plan_id|string|ID рекуррентного плана.
purchase.subscription.operation|string|Тип операции, применяемой к плану подписки пользователя. Чтобы изменить план подписки, необходимо передать в данном параметре значение ‘change_plan’. В параметре purchase.subscription.plan_id необходимо передать ID нового плана подписки.
purchase.subscription.product_id|string|ID рекуррентного продукта.
purchase.subscription.currency|string|Валюта рекуррентного плана в заказе, на основе которой будут сделаны все расчеты.
purchase.subscription.available_plans|array|Массив с данными о планах подписок. Только планы из этого списка будут показаны в платежном интерфейсе.
purchase.subscription.trial_days|integer|Количество дней триального периода.
purchase.pin_codes|object|Объект с данными о ключе.
purchase.pin_codes.currency|string|Валюта ключа в заказе, на основе которой будут сделаны все расчеты.
purchase.pin_codes.codes|array|Массив с данными о ключах.
purchase.pin_codes.codes.digital_content|string|Артикул игры, настраивается в Личном кабинете.
purchase.pin_codes.codes.drm|string|DRM-платформа, на которой игра будет доступна. Может принимать значения 'steam', 'playstation', 'xbox', 'uplay', 'origin', 'drmfree', 'gog', 'epicgames', 'nintendo_eshop', 'discord_game_store' или 'oculus'. Пожалуйста, убедитесь, что нужная DRM-платформа настроена в Личном кабинете. Если значение не передано в токене, будет выбрана платформа, указанная пользователем в платежном интерфейсе.
purchase.pin_codes.upgrade|object|Объект с информацией об апгрейде.
purchase.pin_codes.upgrade.id_user_history|integer|ID записи, содержащей данные о пользователе и пакетах, которыми он владеет.
purchase.pin_codes.upgrade.id|integer|ID апгрейда.
purchase.checkout|object|Объект с информацией о заказе.
purchase.checkout.currency|string|Валюта заказа. Используется трехбуквенное обозначение валюты согласно стандарту ISO 4217.
purchase.checkout.amount|float|Сумма заказа.
purchase.description|object|Объект с описанием предмета.
purchase.description.value|string|Описание предмета. Будет отображаться в интерфейсе оплаты и в email чеке.
purchase.gift|object|Объект с информацией о подарке.
purchase.gift.giver_id|string|ID дарителя.
purchase.gift.message|string|Сообщение от дарителя.
purchase.gift.hide_giver_from_receiver|string|Видимость данных дарителя получателю подарка. По умолчанию 'true'.
purchase.gift.friends|array|Массив с данными о друзьях.
purchase.gift.friends.id|string|ID получателя подарка.
purchase.gift.friends.name|string|Никнейм получателя подарка.
purchase.gift.friends.email|string|Email получателя подарка.
purchase.coupon_code|object|Объект с информацией о промокоде на скидку или бонусы при покупке.
purchase.coupon_code.value|string|Значение промокода.
purchase.coupon_code.hidden|boolean|Видимость поля для ввода промокода в интерфейсе оплаты. По умолчанию 'false'.
custom_parameters|object|Дополнительные параметры, которые передаются в JSON хеше парами ключ-значение.

Если какой-либо параметр был передан в некорректном формате, токен не может быть выдан. Будет возвращен 422 HTTP код, в JSON объекте в теле ответа будет содержаться информация об ошибке. В параметре "extended_message" указывается, какие именно параметры были переданы неверно.

Пример JSON объекта с ошибкой:
```
{
    "extended_message": {
        "global_errors": [],
        "property_errors": {
            "settings.project_id": [
                "string value found, but an integer is required"
            ]
        }
    }
}
```
Примеры запросов и ответов:

***HTTP***
```HTTP
ЗАПРОС
POST https://api.xsolla.com/merchant/v2/merchants/{merchant_id}/token

Headers:
  Authorization: Basic <your_authorization_basic_key>
Content-Type: application/json

Body:
  {
  "purchase": {
    "virtual_currency": {
      "quantity": 100
    },
    "virtual_items": {
      "items": [
        {
          "amount": 1,
          "sku": "SKU01"
        }
      ]
    }
  },
  "settings": {
    "currency": "USD",
    "language": "en",
    "project_id": 16184,
    "ui": {
      "components": {
        "virtual_currency": {
          "custom_amount": true
        }
      },
      "desktop": {
        "virtual_item_list": {
          "button_with_price": true,
          "layout": "list"
        }
      },
      "size": "medium"
    }
  },
  "user": {
    "country": {
      "allow_modify": true,
      "value": "US"
    },
    "email": {
      "value": "john.smith@mail.com"
    },
    "id": {
      "value": "user_2"
    },
    "name": {
      "value": "John Smith"
    }
  }
}
ОТВЕТ
{
  "token": "eop57k1boA7nnYPtewZ6KEXJyJADEwRT"
}
```

***CURL***
```CURL
ЗАПРОС
curl --request POST \
  --url https://api.xsolla.com/merchant/v2/merchants/{merchant_id}/token \
  --header 'authorization: Basic <your_authorization_basic_key>' \
  --header 'content-type: application/json' \
  --data '{"user":{"id":{"value":"user_2"},"name":{"value":"John Smith"},"email":{"value":"john.smith@mail.com"},"country":{"value":"US","allow_modify":true}},"settings":{"project_id":16184,"currency":"USD","language":"en","ui":{"size":"medium","desktop":{"virtual_item_list":{"layout":"list","button_with_price":true}},"components":{"virtual_currency":{"custom_amount":true}}}},"purchase":{"virtual_currency":{"quantity":100},"virtual_items":{"items":[{"sku":"SKU01","amount":1}]}}}'
ОТВЕТ
{
  "token": "eop57k1boA7nnYPtewZ6KEXJyJADEwRT"
}
```

***PHP***
```PHP
<?php

$client = new http\Client;
$request = new http\Client\Request;

$body = new http\Message\Body;
$body->append('{"user":{"id":{"value":"user_2"},"name":{"value":"John Smith"},"email":{"value":"john.smith@mail.com"},"country":{"value":"US","allow_modify":true}},"settings":{"project_id":16184,"currency":"USD","language":"en","ui":{"size":"medium","desktop":{"virtual_item_list":{"layout":"list","button_with_price":true}},"components":{"virtual_currency":{"custom_amount":true}}}},"purchase":{"virtual_currency":{"quantity":100},"virtual_items":{"items":[{"sku":"SKU01","amount":1}]}}}');

$request->setRequestUrl('https://api.xsolla.com/merchant/v2/merchants/{merchant_id}/token');
$request->setRequestMethod('POST');
$request->setBody($body);

$request->setHeaders(array(
  'authorization' => 'Basic <your_authorization_basic_key>',
  'content-type' => 'application/json'
));

$client->enqueue($request)->send();
$response = $client->getResponse();

echo $response->getBody();
ОТВЕТ
{
  "token": "eop57k1boA7nnYPtewZ6KEXJyJADEwRT"
}
```

***C#***
```C#
ЗАПРОС
var client = new RestClient("https://api.xsolla.com/merchant/v2/merchants/{merchant_id}/token");
var request = new RestRequest(Method.POST);
request.AddHeader("authorization", "Basic <your_authorization_basic_key>");
request.AddHeader("content-type", "application/json");
request.AddParameter("application/json", "{\"user\":{\"id\":{\"value\":\"user_2\"},\"name\":{\"value\":\"John Smith\"},\"email\":{\"value\":\"john.smith@mail.com\"},\"country\":{\"value\":\"US\",\"allow_modify\":true}},\"settings\":{\"project_id\":16184,\"currency\":\"USD\",\"language\":\"en\",\"ui\":{\"size\":\"medium\",\"desktop\":{\"virtual_item_list\":{\"layout\":\"list\",\"button_with_price\":true}},\"components\":{\"virtual_currency\":{\"custom_amount\":true}}}},\"purchase\":{\"virtual_currency\":{\"quantity\":100},\"virtual_items\":{\"items\":[{\"sku\":\"SKU01\",\"amount\":1}]}}}", ParameterType.RequestBody);
IRestResponse response = client.Execute(request);
ОТВЕТ
{
  "token": "eop57k1boA7nnYPtewZ6KEXJyJADEwRT"
}
```

***PYTHON***
```PYTHON
ЗАПРОС
import http.client

conn = http.client.HTTPSConnection("api.xsolla.com")

payload = "{\"user\":{\"id\":{\"value\":\"user_2\"},\"name\":{\"value\":\"John Smith\"},\"email\":{\"value\":\"john.smith@mail.com\"},\"country\":{\"value\":\"US\",\"allow_modify\":true}},\"settings\":{\"project_id\":16184,\"currency\":\"USD\",\"language\":\"en\",\"ui\":{\"size\":\"medium\",\"desktop\":{\"virtual_item_list\":{\"layout\":\"list\",\"button_with_price\":true}},\"components\":{\"virtual_currency\":{\"custom_amount\":true}}}},\"purchase\":{\"virtual_currency\":{\"quantity\":100},\"virtual_items\":{\"items\":[{\"sku\":\"SKU01\",\"amount\":1}]}}}"

headers = {
    'content-type': "application/json",
    'authorization': "Basic <your_authorization_basic_key>"
    }

conn.request("POST", "/merchant/v2/merchants/{merchant_id}/token", payload, headers)

res = conn.getresponse()
data = res.read()

print(data.decode("utf-8"))
ОТВЕТ
{
  "token": "eop57k1boA7nnYPtewZ6KEXJyJADEwRT"
}
```

***RUBY***
```RUBY
ЗАПРОС
require 'uri'
require 'net/http'

url = URI("https://api.xsolla.com/merchant/v2/merchants/{merchant_id}/token")

http = Net::HTTP.new(url.host, url.port)
http.use_ssl = true
http.verify_mode = OpenSSL::SSL::VERIFY_NONE

request = Net::HTTP::Post.new(url)
request["content-type"] = 'application/json'
request["authorization"] = 'Basic <your_authorization_basic_key>'
request.body = "{\"user\":{\"id\":{\"value\":\"user_2\"},\"name\":{\"value\":\"John Smith\"},\"email\":{\"value\":\"john.smith@mail.com\"},\"country\":{\"value\":\"US\",\"allow_modify\":true}},\"settings\":{\"project_id\":16184,\"currency\":\"USD\",\"language\":\"en\",\"ui\":{\"size\":\"medium\",\"desktop\":{\"virtual_item_list\":{\"layout\":\"list\",\"button_with_price\":true}},\"components\":{\"virtual_currency\":{\"custom_amount\":true}}}},\"purchase\":{\"virtual_currency\":{\"quantity\":100},\"virtual_items\":{\"items\":[{\"sku\":\"SKU01\",\"amount\":1}]}}}"

response = http.request(request)
puts response.read_body
ОТВЕТ
{
  "token": "eop57k1boA7nnYPtewZ6KEXJyJADEwRT"
}
```

***JAVA***
```JAVA
ЗАПРОС
OkHttpClient client = new OkHttpClient();

MediaType mediaType = MediaType.parse("application/json");
RequestBody body = RequestBody.create(mediaType, "{\"user\":{\"id\":{\"value\":\"user_2\"},\"name\":{\"value\":\"John Smith\"},\"email\":{\"value\":\"john.smith@mail.com\"},\"country\":{\"value\":\"US\",\"allow_modify\":true}},\"settings\":{\"project_id\":16184,\"currency\":\"USD\",\"language\":\"en\",\"ui\":{\"size\":\"medium\",\"desktop\":{\"virtual_item_list\":{\"layout\":\"list\",\"button_with_price\":true}},\"components\":{\"virtual_currency\":{\"custom_amount\":true}}}},\"purchase\":{\"virtual_currency\":{\"quantity\":100},\"virtual_items\":{\"items\":[{\"sku\":\"SKU01\",\"amount\":1}]}}}");
Request request = new Request.Builder()
  .url("https://api.xsolla.com/merchant/v2/merchants/{merchant_id}/token")
  .post(body)
  .addHeader("content-type", "application/json")
  .addHeader("authorization", "Basic <your_authorization_basic_key>")
  .build();

Response response = client.newCall(request).execute();
ОТВЕТ
{
  "token": "eop57k1boA7nnYPtewZ6KEXJyJADEwRT"
}
```

***JS***
```JS
ЗАПРОС
var data = JSON.stringify({
  "user": {
    "id": {
      "value": "user_2"
    },
    "name": {
      "value": "John Smith"
    },
    "email": {
      "value": "john.smith@mail.com"
    },
    "country": {
      "value": "US",
      "allow_modify": true
    }
  },
  "settings": {
    "project_id": 16184,
    "currency": "USD",
    "language": "en",
    "ui": {
      "size": "medium",
      "desktop": {
        "virtual_item_list": {
          "layout": "list",
          "button_with_price": true
        }
      },
      "components": {
        "virtual_currency": {
          "custom_amount": true
        }
      }
    }
  },
  "purchase": {
    "virtual_currency": {
      "quantity": 100
    },
    "virtual_items": {
      "items": [
        {
          "sku": "SKU01",
          "amount": 1
        }
      ]
    }
  }
});

var xhr = new XMLHttpRequest();
xhr.withCredentials = true;

xhr.addEventListener("readystatechange", function () {
  if (this.readyState === this.DONE) {
    console.log(this.responseText);
  }
});

xhr.open("POST", "https://api.xsolla.com/merchant/v2/merchants/{merchant_id}/token");
xhr.setRequestHeader("content-type", "application/json");
xhr.setRequestHeader("authorization", "Basic <your_authorization_basic_key>");

xhr.send(data);
ОТВЕТ
{
  "token": "eop57k1boA7nnYPtewZ6KEXJyJADEwRT"
}
```

### Список дополнительных параметров

Вы можете передавать в токене в объекте custom_parameters дополнительные параметры, которые могут использоваться для настройки антифрод-фильтров. Рекомендуемые параметры приведены в таблице ниже, при необходимости данный список можно расширить. ([подробнее](https://developers.xsolla.com/ru/recipes/general/antifraud/)).

Поле | Тип | Описание
---- | --- | --------
registration_date|string|Дата регистрации аккаунта согласно стандарту [ISO 8601](https://en.wikipedia.org/wiki/ISO_8601).
total_hours|integer|Общее количество часов, проведенных в игре.
total_characters|integer|Количество персонажей игрока.
social_networks_added|boolean|Наличие профилей игрока в социальных сетях.
profile_image_added|boolean|Наличие изображения профиля.
active_date|string|Дата последнего посещения согласно стандарту [ISO 8601](https://en.wikipedia.org/wiki/ISO_8601).
total_friends|integer|Количество друзей игрока.
additional_verification|boolean|Наличие дополнительных способов защиты аккаунта.
win_rate|integer|Рейтинг побед игрока.
last_change_password_date|string|Дата последней смены пароля согласно стандарту [ISO 8601](https://en.wikipedia.org/wiki/ISO_8601).
chat_activity|boolean|Наличие записей в чате.
forum_activity|boolean|Наличие записей в форуме.
total_bans|integer|Количество банов игрока в чате/на форуме.
profile_completed|boolean|Наличие дополнительной информации в профиле.
notifications_enabled|boolean|Наличие подписки на рассылку уведомлений.
user_level|integer|Уровень игрока, репутация или ранг.
karma_points|integer|Карма игрока.
total_sum|float|Общая сумма платежей.
non_premium_currency|float|Сумма непремиальной валюты игрока.
total_game_events|integer|Количество внутриигровых событий, в которых участвовал игрок.
total_gifts|integer|Количество подарков, отправленных или полученных игроком.
tutorial_completed|boolean|Показатель завершения обучения в игре.
completed_tasks|integer|Количество выполненных заданий.
items_used|boolean|Показатель использования купленных в игре предметов.
pvp_activity|boolean|Показатель участия игрока в PvP.
total_clans|integer|Количество кланов, в которых состоит игрок.
unlocked_achievements|integer|Количество разблокированных умений.
total_inventory_value|float|Суммарная стоимость инвентаря во внутриигровой валюте.
character_customized|boolean|Показатель настройки персонажа.
session_time|string|Период времени, который пользователь проводит в игре, согласно стандарту [ISO 8601](https://en.wikipedia.org/wiki/ISO_8601).

## Оповещения
### Введение
Для оповещения о событиях, которые происходят с транзакцией в XSOLLA, используется **webhook**. Оповещения можно использовать для автоматизации бэк-офиса и функций, таких как отображение статуса.

Webhook — это сервис для оповещения о событиях, таких как:
* Совершение платежей, включая покупку виртуальной валюты, покупку предметов и проч.
* Рекуррентные платежи и действия с подписками.
* Chargeback/refund по транзакции.

В большинстве случаев действие, которое вызывает webhook, является следствием действия пользователя на странице оплаты. Тем не менее и другие действия могут вызывать webhook.

Например, можно отменить платеж через XSOLLA API или платежная система оповестит разработчиков об оспариваемой операции.

Примеры действий в результате обработки webhook:
* Пополнение баланса пользователя.
* Предоставление новых предметов пользователю.
* Начало предоставления подписки.
* Блокировка пользователя в случае подозрения в мошенничестве.

Оповещения должны приниматься со следующих IP адресов: `185.30.20.0/24`, `185.30.21.0/24`.

В базе данных не должно быть двух успешных транзакций с одинаковым ID. Если система отправила webhook с ID, который уже есть в  базе, то необходимо вернуть результат предыдущей обработки данной транзакции.  Покупка не должна быть засчитана пользователю еще раз, а в базе данных не должно быть дублирующих записей.

XSOLLA не гарантирует, что скрипт обработки получит все оповещения. Поскольку Интернет не является на 100% надежным, webhook может быть потерян или задержан. Для решения этих проблем сервис оповещений включает в себя механизм повторных оповещений, который отправляет оповещение, пока скрипт не подтвердит получение оповещения. Webhook может быть повторно отправлен в течение 12 часов с момента первой попытки. Максимальное количество попыток — 12.

> ***Примечание!*** 
> ~~~
> Хотя Интернет является частой причиной проблем с webhook, наиболее вероятной причиной чаще всего является проблема в логике скрипта обработки.
> ~~~

### Подпись запроса и ответ
Подпись обеспечивает безопасность передачи данных. 
Генерация подписи состоит из двух шагов:
1. Конкатенация JSON из тела запроса и секретного ключа проекта. 
1. Применение SHA-1 криптографической хэш-функции к получившейся на первом шаге строке.

Необходимо проверить, что сформированная подпись совпадает с подписью, отправленной в HTTP заголовке.

Примеры подписей запросов:

***HTTP***
```HTTP
ЗАПРОС
POST /your_uri HTTP/1.1
Host: your.host
Accept: application/json
Content-Type: application/json
Content-Length: 165
Authorization: Signature 52eac2713985e212351610d008e7e14fae46f902

{"notification_type":"user_validation","user":{"ip":"127.0.0.1","phone":"18777976552","email":"email@example.com","id":1234567,"name":"Xsolla User","country":"US"}}
```

***CURL***
```CURL
ЗАПРОС
$curl -v 'https://your.hostname/your/uri' \
-X POST \
-H 'Authorization: Signature 52eac2713985e212351610d008e7e14fae46f902' \
-d '{"notification_type":"user_validation","user":{"ip":"127.0.0.1","phone":"18777976552","email":"email@example.com","id":1234567,"name":"Xsolla User","country":"US"}}'
```

XSOLLA использует HTTP коды ответа для обозначения успешного или ошибочного запроса. Код 204 обозначает успешную обработку оповещения. В случае возникновения ошибки необходимо вернуть 400 код (например, отсутствует обязательный параметр, или пополнение баланса невозможно). Код 500 обозначает временную серверную ошибку.

### Типы оповещения
Тип оповещения передается в параметре 'notification_type'.

Поле | Описание
---- | --------
user_validation|Проверка существования пользователя в игре.
user_search|Получение информации о пользователе по параметру Public user ID.
payment|Оповещение об успешном платеже.
refund|Оповещение об отмене платежа.
afs_reject|Оповещение об отмене транзакциии AFS системой.
create_subscription|Оповещение о создании подписки.
update_subscription|Оповещение о продлении подписки или о смене каких-либо параметров внутри подписки.
cancel_subscription|Оповещение об отмене подписки.
get_pincode|Запрос на получение ключа.
user_balance_operation|Оповещение об изменении баланса пользователя (тип операции отправляется в оповещении operation_type).
redeem_key|Оповещение об активации ключа.
upgrade_refund|Оповещение об отмене апгрейда.
inventory_get|Отправка списка предметов из инвентаря игры на вторичный рынок.
inventory_pull|Отправка предметов из инвентаря игры на вторичный рынок.
inventory_push	Получение предметов в инвентарь игры со вторичного рынка.

### Проверка существования пользователя
Сервер XSOLLA отправляет запрос на webhook URL, чтобы удостовериться, что данный пользователь существует в игре. 

Поле | Тип | Описание
---- | --- | --------
user|object|Объект с информацией о пользователе.
user.ip|string|IP адрес пользователя.
user.phone|string|Номер телефона пользователя (в международном формате).
user.email|string|Email пользователя.
user.id|string|ID пользователя. Обязательный.
user.name|string|Имя пользователя.
user.country|string|Двухбуквенное обозначение страны согласно стандарту ISO 3166-1 alpha-2.

Примеры проверки:

***HTTP***
```HTTP
ЗАПРОС
POST /your/uri HTTP/1.1
Host: your.hostname
Accept: application/json
Content-Type: application/json
Content-Length: 240
Authorization: Signature 13342703ccaca5064ad33ba451d800c5e823db8f

{
    "notification_type":"user_validation",
    "user": {
        "ip": "127.0.0.1",
        "phone": "18777976552",
        "email": "email@example.com",
        "id": "1234567",
        "name": "Xsolla User",
        "country": "US"
    }
}
ОТВЕТ
HTTP/1.1 204 No Content
```

***CURL***
```CURL
ЗАПРОС
$ curl -v 'https://your.hostname/your/uri' \
-X POST \
-H 'Accept: application/json' \
-H 'Content-Type: application/json' \
-H 'Authorization: Signature 13342703ccaca5064ad33ba451d800c5e823db8f' \
-d '{
    "notification_type":"user_validation",
    "user": {
        "ip": "127.0.0.1",
        "phone": "18777976552",
        "email": "email@example.com",
        "id": "1234567",
        "name": "Xsolla User",
        "country": "US"
    }
}'
ОТВЕТ
```

***PHP***
```PHP
ЗАПРОС
<?php

$request = array(
    'notification_type' => 'user_validation',
    'user' => array(
        'ip' => '127.0.0.1',
        'phone' => '18777976552',
        'email'=> 'email@example.com',
        'id'=> '1234567',
        'country' => 'US'
    )
);
ОТВЕТ
<?php

use Xsolla\SDK\Webhook\WebhookServer;
use Xsolla\SDK\Webhook\Message\Message;
use Xsolla\SDK\Exception\Webhook\XsollaWebhookException;

$callback = function (Message $message) {
    if ($message->isUserValidation()) {
       $userArray = $message->getUser();
       $userId = $message->getUserId();
       $messageArray = $message->toArray();
       //TODO if user not found, you should throw InvalidUserException
    }
};

$webhookServer = WebhookServer::create($callback, PROJECT_KEY);
$webhookServer->start();
```

### Поиск пользователя
Public User ID — параметр, по которому можно однозначно идентифицировать пользователя и который хорошо известен пользователю в отличие от User ID (в качестве Public User ID может быть email, никнейм и т.д.). Этот webhook используется, когда есть возможность совершить оплату вне игры (например, при оплате на кнопку игры в терминале).

Поле | Тип | Описание
---- | --- | --------
notification_type|string|Тип оповещения. Обязательный.
user|object|Объект с информацией о пользователе. Обязательный.
user.public_id|string|Public ID пользователя.
user.id|string|ID пользователя.

Примеры поиска пользователя: 

***HTTP***
```HTTP
ЗАПРОС
POST /your/uri HTTP/1.1
Host: your.hostname
Accept: application/json
Content-Type: application/json
Content-Length: 240
Authorization: Signature 13342703ccaca5064ad33ba451d800c5e823db8f

{
    "notification_type":"user_search",
    "user": {
        "public_id": "public_email@example.com"
    }
}
ОТВЕТ
HTTP/1.1 200 OK
Content-Type: application/json

{
    "user": {
        "public_id": "public_email@example.com",
        "phone": "18777976552",
        "email": "email@example.com",
        "id": "1234567",
        "name": "Xsolla User"
    }
}
```

***CURL***
```CURL
ЗАПРОС
$ curl -v 'https://your.hostname/your/uri' \
-X POST \
-H 'Accept: application/json' \
-H 'Content-Type: application/json' \
-H 'Authorization: Signature 13342703ccaca5064ad33ba451d800c5e823db8f' \
-d '{
    "notification_type":"user_search",
    "user": {
        "public_id": "public_email@example.com"
    }
}'
ОТВЕТ
{
    "user": {
        "public_id": "public_email@example.com",
        "phone": "18777976552",
        "email": "email@example.com",
        "id": "1234567",
        "name": "Xsolla User"
    }
}
```

***PHP***
```PHP
ЗАПРОС
<?php

$request = array(
    'notification_type' => 'user_search',
    'user' => array(
        'public_id' => 'public_email@example.com'
    )
);
ОТВЕТ
<?php
​
use Xsolla\SDK\Webhook\WebhookServer;
use Xsolla\SDK\Webhook\Message\Message;
​
$callback = function (Message $message) {
    if ($message instanceof \Xsolla\SDK\Webhook\Message\UserSearchMessage) {
        $userArray = $message->getUser();
        $userPublicId = $message->getUserPublicId();
        // TODO get a user from your database and fill the user data to model.
        $user = new \Xsolla\SDK\Webhook\User();
        $user->setId('user_id')
            ->setPublicId($userPublicId)
            ->setEmail('user_email') //Optional field
            ->setPhone('user_phone') //Optional field
            ->setName('user_name'); //Optional field
        //TODO if user not found, you should throw InvalidUserException
        return new \Xsolla\SDK\Webhook\Response\UserResponse($user);
    }
};
$webhookServer = WebhookServer::create($callback, PROJECT_KEY);
$webhookServer->start();
```

### Успешный платеж
Когда пользователь успешно совершает оплату, XSOLLA отправляет детали о платеже на webhook URL.

Поле | Тип | Описание
---- | --- | --------
notification_type|string|Тип оповещения. Обязательный.
purchase|object|Объект с информацией о заказе.
purchase.virtual_currency|object|Объект с данными о приобретенной виртуальной валюте.
purchase.virtual_currency.name|string|Название виртуальной валюты.
purchase.virtual_currency.sku|string|Артикул пакета виртуальной валюты (если задан для пакета виртуальной валюты).
purchase.virtual_currency.quantity|float|Количество бонусного товара.
purchase.virtual_currency.currency|string|Трехбуквенное обозначение виртуальной валюты заказа согласно стандарту ISO 4217.
purchase.virtual_currency.amount|float|Сумма покупки.
purchase.checkout|object|Объект с информацией о заказе.
purchase.checkout.currency|string|Трехбуквенное обозначение валюты заказа согласно стандарту ISO 4217.
purchase.checkout.amount|float|Сумма заказа.
purchase.subscription|object|Объект с данными о подписке.
purchase.subscription.plan_id|string|ID плана (внешний id, если план был создан через API).
purchase.subscription.subscription_id|integer|ID подписки в базе данных XSOLLA.
purchase.subscription.product_id|string|ID продукта (если был отправлен в токене).
purchase.subscription.tags|array|Теги плана.
purchase.subscription.date_create|string|Дата и время создания подписки согласно стандарту ISO 8601.
purchase.subscription.date_next_charge|string|Дата и время следующего списания согласно стандарту ISO 8601.
purchase.subscription.currency|string|Трехбуквенное обозначение валюты рекуррентного плана согласно стандарту ISO 4217.
purchase.subscription.amount|float|Сумма покупки.
purchase.virtual_items|object|Объект с данными о предметах в покупке.
purchase.virtual_items.items|array|Массив с данными о предмете.
purchase.virtual_items.items.sku|string|ID предмета (артикул).
purchase.virtual_items.items.amount|integer|Количество этого предмета в заказе.
purchase.virtual_items.currency|string|Трехбуквенное обозначение валюты заказа согласно стандарту ISO 4217.
purchase.virtual_items.amount|integer|Сумма заказа.
purchase.pin_codes|object|Массив с данными о ключах.
purchase.pin_codes.digital_content|string|Артикул игры, настраивается в Личном кабинете.
purchase.pin_codes.drm|string|DRM-платформа, на которой игра будет доступна. Может принимать значения 'steam', 'playstation', 'xbox', 'uplay', 'origin', 'drmfree', 'gog', 'epicgames', 'nintendo_eshop', 'discord_game_store' или 'oculus'. Перед использованием необходимо убедиться, что нужная DRM-платформа настроена в Личном кабинете.
purchase.pin_codes.currency|string|Трехбуквенное обозначение валюты покупки согласно стандарту ISO 4217.
purchase.pin_codes.amount|float|Стоимость ключа.
purchase.pin_codes.upgrade|object|Объект с информацией об апгрейде.
purchase.pin_codes.upgrade.digital_content_from|object|Объект с информацией о пакете пользователя, с которого был произведен апгрейд.
purchase.pin_codes.upgrade.digital_content_from.digital_content|string|Артикул игры, настраивается в Личном кабинете.
purchase.pin_codes.upgrade.digital_content_from.DRM|string|DRM-платформа игры.
purchase.pin_codes.upgrade.digital_content_to|object|Объект с информацией о пакете, на который пользователь перешел в рамках апгрейда.
purchase.pin_codes.upgrade.digital_content_to.digital_content|string|Артикул игры, настраивается в Личном кабинете.
purchase.pin_codes.upgrade.digital_content_to.DRM|string|DRM-платформа игры.
purchase.pin_codes.upgrade.currency|string|Трехбуквенное обозначение валюты покупки согласно стандарту ISO 4217.
purchase.pin_codes.upgrade.amount|float|Сумма покупки.
purchase.gift|object|Объект с информацией о подарке.
purchase.gift.giver_id|string|ID дарителя.
purchase.gift.receiver_ID|string|ID получателя подарка.
purchase.gift.receiver_email|string|Email получателя подарка.
purchase.gift.message|string|Сообщение от дарителя.
purchase.gift.hide_giver_from_receiver|string|Флаг, показывающий видимость дарителя получателю подарка.
purchase.total|object|Объект с данными об общей стоимости покупки. Обязательный.
purchase.total.currency|string|Трехбуквенное обозначение валюты заказа согласно стандарту ISO 4217.
purchase.total.amount|float|Общая сумма покупки.
purchase.promotions|array|Массив с данными акций, которые действуют на данную покупку.
purchase.promotions.technical_name|string|Техническое название акции.
purchase.promotions.id|integer|ID акции.
purchase.coupon|object|Объект с информацией о купоне (если при создании подписки был использован купон).
purchase.coupon.coupon_code|string|Код купона.
purchase.coupon.campaign_code|string|Код кампании купонов.
user|object|Объект с информацией о пользователе.
user.ip|string|IP адрес пользователя.
user.phone|string|Номер телефона пользователя (в международном формате).
user.email|string|Email пользователя.
user.id|string|ID пользователя. Обязательный.
user.name|string|Имя пользователя.
user.country|string|Двухбуквенное обозначение страны пользователя согласно стандарту ISO 3166-1 alpha-2.
user.zip|string|Почтовый индекс.
transaction|object|Объект с информацией о транзакции, связанной с этой операцией. Обязательный.
transaction.id|integer|ID транзакции.
transaction.external_id|string|Внешний ID транзакции.
transaction.payment_date|string|Дата платежа.
transaction.payment_method|integer|ID способа оплаты в системе XSOLLA.
transaction.dry_run|integer|Признак тестовой транзакции: 1 — тестовый платеж, 0 — реальный платеж.
transaction.agreement|integer|ID соглашения.
payment_details|object|Объект с финансовыми данными платежа. Обязательный.
payment_details.payment|object|Объект с данными о сумме, которую оплатил пользователь.
payment_details.payment.currency|string|Трехбуквенное обозначение валюты согласно стандарту ISO 4217.
payment_details.payment.amount|string|Сумма.
payment_details.payment_method_sum|object|Объект с данными о сумме, которая была оплачена из платежной системы.
payment_details.payment_method_sum.currency|string|Трехбуквенное обозначение валюты согласно стандарту ISO 4217.
payment_details.payment_method_sum.amount|string|Сумма.
payment_details.xsolla_balance_sum|object|Объект с данными о сумме, которая была оплачена с XSOLLA-баланса.
payment_details.xsolla_balance_sum.currency|string|Трехбуквенное обозначение валюты согласно стандарту ISO 4217.
payment_details.xsolla_balance_sum.amount|string|Сумма.
payment_details.payout|object|Объект с данными о сумме выплаты.
payment_details.payout.currency|string|Трехбуквенное обозначение валюты согласно стандарту ISO 4217.
payment_details.payout.amount|float|Сумма.
payment_details.vat|object|Размер VAT (только для Евросоюза).
payment_details.vat.currency|string|Трехбуквенное обозначение валюты согласно стандарту ISO 4217.
payment_details.vat.amount|float|Сумма.
payment_details.payout_currency_rate|float|Курс валюты платежа к валюте выплаты.
payment_details.xsolla_fee|object|Размер комиссии XSOLLA.
payment_details.xsolla_fee.currency|string|Трехбуквенное обозначение валюты согласно стандарту ISO 4217.
payment_details.xsolla_fee.amount|float|Сумма.
payment_details.payment_method_fee|object|Размер комиссии платежной системы.
payment_details.payment_method_fee.currency|string|Трехбуквенное обозначение валюты согласно стандарту ISO 4217.
payment_details.payment_method_fee.amount|float|Сумма.
payment_details.sales_tax|object|Размер налога (только для США).
payment_details.sales_tax.currency|string|Трехбуквенное обозначение валюты согласно стандарту ISO 4217.
payment_details.sales_tax.amount|float|Сумма.
payment_details.repatriation_commission|object|Объект с информацией о затратах на репатриацию, возлагаемых на XSOLLA третьими сторонами.
payment_details.repatriation_commission.currency|string|Трехбуквенное обозначение валюты затрат на репатриацию согласно стандарту ISO 4217.
payment_details.repatriation_commission.amount|float|Сумма затрат на репатриацию.
custom_parameters|object|Дополнительные параметры.

Примеры проведения платежа: 

***HTTP***
```HTTP
ЗАПРОС
POST /your_uri HTTP/1.1
Host: your.host
Accept: application/json
Content-Type: application/json
Content-Length: 1721
Authorization: Signature 34553d151e656110c656696c919f9a10e05de542

{
    "notification_type":"payment",
    "purchase":{
        "virtual_currency":{
            "name":"Coins",
            "sku":"test_package1",
            "quantity":10,
            "currency":"USD",
            "amount":100
        },
        "subscription":{
            "plan_id": "b5dac9c8",
            "subscription_id": "10",
            "product_id": "Demo Product",
            "date_create": "2014-09-22T19:25:25+04:00",
            "date_next_charge": "2014-10-22T19:25:25+04:00",
            "currency": "USD",
            "amount": 9.99
        },
        "checkout":{
            "currency":"USD",
            "amount":50
        },
        "virtual_items":{
            "items":[
                {
                    "sku": "test_item1",
                    "amount":1
                }
            ],
            "currency":"USD",
            "amount":50
        },
        "total":{
            "currency":"USD",
            "amount":200
        },
        "promotions":[{
            "technical_name":"Demo Promotion",
            "id":"853"
        }],
        "coupon":{
            "coupon_code":"ICvj45S4FUOyy",
            "campaign_code":"1507"
        }
    },
    "user": {
        "ip": "127.0.0.1",
        "phone": "18777976552",
        "email": "email@example.com",
        "id": "1234567",
        "name": "Xsolla User",
        "country": "US"
    },
    "transaction":{
        "id":1,
        "external_id":1,
        "payment_date":"2014-09-24T20:38:16+04:00",
        "payment_method":1,
        "dry_run":1,
        "agreement":1
    },
    "payment_details":{
        "payment":{
            "currency":"USD",
            "amount":230
        },
        "vat": {
            "currency": "USD",
            "amount": 0
        },
        "payout_currency_rate": 1,
        "payout":{
            "currency":"USD",
            "amount":200
        },
        "xsolla_fee":{
            "currency":"USD",
            "amount":10
        },
        "payment_method_fee":{
            "currency":"USD",
            "amount":20
        },
        "repatriation_commission":{
            "currency":"USD",
            "amount":"10"
        }
    },
    "custom_parameters":{
        "parameter1":"value1",
        "parameter2":"value2"
    }
}
ОТВЕТ
HTTP/1.1 204 No Content
```

***CURL***
```CURL
ЗАПРОС
$curl -v 'https://your.hostname/your/uri' \
-X POST \
-d '{
    "notification_type":"payment",
    "purchase":{
        "virtual_currency":{
            "name":"Coins",
            "sku":"test_package1",
            "quantity":10,
            "currency":"USD",
            "amount":100
        },
        "subscription":{
            "plan_id": "b5dac9c8",
            "subscription_id": "10",
            "product_id": "Demo Product",
            "date_create": "2014-09-22T19:25:25+04:00",
            "date_next_charge": "2014-10-22T19:25:25+04:00",
            "currency": "USD",
            "amount": 9.99
        },
        "checkout":{
            "currency":"USD",
            "amount":50
        },
        "virtual_items":{
            "items":[
                {
                    "sku": "test_item1",
                    "amount":1
                }
            ],
            "currency":"USD",
            "amount":50
        },
        "total":{
            "currency":"USD",
            "amount":200
        },
        "promotions":[{
            "technical_name":"Demo Promotion",
            "id":"853"
        }],
        "coupon":{
             "coupon_code":"ICvj45S4FUOyy",
             "campaign_code":"1507"
        }
    },
    "user": {
        "ip": "127.0.0.1",
        "phone": "18777976552",
        "email": "email@example.com",
        "id": "1234567",
        "name": "Xsolla User",
        "country": "US"
    },
    "transaction":{
        "id":1,
        "external_id":1,
        "payment_date":"2014-09-24T20:38:16+04:00",
        "payment_method":1,
        "dry_run":1,
        "agreement":1
    },
    "payment_details":{
        "payment":{
            "currency":"USD",
            "amount":230
        },
        "vat": {
            "currency": "USD",
            "amount": 0
        },
        "payout_currency_rate": 1,
        "payout":{
            "currency":"USD",
            "amount":200
        },
        "xsolla_fee":{
            "currency":"USD",
            "amount":10
        },
        "payment_method_fee":{
            "currency":"USD",
            "amount":20
        },
        "repatriation_commission":{
            "currency":"USD",
            "amount":"10"
        }
    },
    "custom_parameters":{
        "parameter1":"value1",
        "parameter2":"value2"
    }
}'
ОТВЕТ
```

***PHP***
```PHP
ЗАПРОС
<?php

$request = array(
    'notification_type' => 'payment',
    'purchase' => array(
        'virtual_currency' => array(
            'name' => 'Coins',
            'quantity' => 100,
            'currency' => 'USD',
            'amount' => 9.99
        ),
        'total' => array(
            'currency' => 'USD',
            'amount' => 9.99
        )
    ),
    'user' => array(
        'ip' => '127.0.0.1',
        'phone' => '18777976552',
        'email' => 'email@example.com',
        'id' => '1234567',
        'country' => 'US'
    ),
    'transaction' => array(
        'id' => 87654321,
        'payment_date' => '2014-09-23T19:25:25+04:00',
        'payment_method' => 1380,
        'dry_run' => 1
    ),
    'payment_details' => array(
        'payment' => array(
            'currency' => 'USD',
            'amount' => 9.99
        ),
        'vat' => array(
            'currency' => 'USD',
            'amount' => 0
        ),
        'payout_currency_rate' => 1,
        'payout' => array(
            'currency' => 'USD',
            'amount' => 9.49
        ),
        'xsolla_fee' => array(
            'currency' => 'USD',
            'amount' => 0.19
        ),
        'payment_method_fee' => array(
            'currency' => 'USD',
            'amount' => 0.31
        ),
        'repatriation_commission' => array(
            'currency' => 'USD',
            'amount' => 0.2
        )
    )
);
ОТВЕТ
<?php

use Xsolla\SDK\Webhook\WebhookServer;
use Xsolla\SDK\Webhook\Message\Message;
use Xsolla\SDK\Exception\Webhook\XsollaWebhookException;

$callback = function (Message $message) {
    if ($message->isPayment()) {
        $userArray = $message->getUser();
        $paymentArray = $message->getTransaction();
        $paymentId = $message->getPaymentId();
        $externalPaymentId = $message->getExternalPaymentId();
        $paymentDetailsArray = $message->getPaymentDetails();
        $customParametersArray = $message->getCustomParameters();
        $isDryRun = $message->isDryRun();
        $messageArray = $message->toArray();
        // TODO if the payment delivery fails for some reason, you should throw XsollaWebhookException
    }
};

$webhookServer = WebhookServer::create($callback, PROJECT_KEY);
$webhookServer->start();
```

### Отмена платежа
При отмене платежа XSOLLA присылает детали отмененной транзакции на webhook URL.

Поле | Тип | Описание
---- | --- | --------
notification_type|string|Тип оповещения. Обязательный.
purchase|object|Объект с информацией о заказе.
purchase.virtual_currency|object|Объект с данными о приобретенной виртуальной валюте.
purchase.virtual_currency.name|string|Название виртуальной валюты.
purchase.virtual_currency.quantity|float|Количество бонусного товара.
purchase.virtual_currency.currency|string|Трехбуквенное обозначение валюты заказа согласно стандарту ISO 4217.
purchase.virtual_currency.amount|float|Сумма покупки.
purchase.checkout|object|Объект с информацией о заказе.
purchase.checkout.currency|string|Трехбуквенное обозначение валюты заказа согласно стандарту ISO 4217.
purchase.checkout.amount|float|Сумма заказа.
purchase.subscription|object|Объект с данными о подписке.
purchase.subscription.plan_id|string|ID плана (внешний id, если план был создан через API).
purchase.subscription.tags|array|Теги плана.
purchase.subscription.subscription_id|integer|ID подписки в базе данных XSOLLA.
purchase.subscription.date_create|string|Дата и время создания подписки согласно стандарту ISO 8601.
purchase.subscription.currency|string|Трехбуквенное обозначение валюты рекуррентного плана согласно стандарту ISO 4217.
purchase.subscription.amount|float|Сумма покупки.
purchase.virtual_items|object|Объект с данными о предметах в покупке.
purchase.virtual_items.items|array|Массив с данными о предмете.
purchase.virtual_items.items.sku|string|ID предмета (артикул).
purchase.virtual_items.items.amount|integer|Количество этого предмета в заказе.
purchase.virtual_items.currency|string|Трехбуквенное обозначение валюты заказа согласно стандарту ISO 4217.
purchase.virtual_items.amount|integer|Сумма заказа.
purchase.pin_codes|object|Объект с данными о ключе.
purchase.pin_codes.upgrade|object|Объект с информацией об апгрейде.
purchase.pin_codes.upgrade.digital_content_from|object|Объект с информацией о пакете пользователя, с которого был произведен апгрейд.
purchase.pin_codes.upgrade.digital_content_from.digital_content|string|Артикул игры, настраивается в Личном кабинете.
purchase.pin_codes.upgrade.digital_content_from.DRM|string|DRM-платформа игры.
purchase.pin_codes.upgrade.digital_content_to|object|Объект с информацией о пакете, на который пользователь перешел в рамках апгрейда.
purchase.pin_codes.upgrade.digital_content_to.digital_content|string|Артикул игры, настраивается в Личном кабинете.
purchase.pin_codes.upgrade.digital_content_to.DRM|string|DRM-платформа игры.
purchase.pin_codes.upgrade.currency|string|Трехбуквенное обозначение валюты покупки согласно стандарту ISO 4217.
purchase.pin_codes.upgrade.amount|float|Сумма покупки.
purchase.total|object|Объект с данными об общей стоимости покупки.
purchase.total.currency|string|Трехбуквенное обозначение валюты заказа согласно стандарту ISO 4217.
purchase.total.amount|float|Общая сумма покупки.
user|object|Объект с информацией о пользователе.
user.ip|string|IP адрес пользователя.
user.phone|string|Номер телефона пользователя (в международном формате).
user.email|string|Email пользователя.
user.id|string|ID пользователя. Обязательный.
user.name|string|Имя пользователя.
user.country|string|Двухбуквенное обозначение страны пользователя согласно стандарту ISO 3166-1 alpha-2.
user.zip|string|Почтовый индекс.
transaction|object|Объект с информацией о транзакции, связанной с этой операцией. Обязательный.
transaction.id|integer|ID транзакции.
transaction.external_id|string|Внешний ID транзакции.
transaction.dry_run|integer|Признак тестовой транзакции: 1 — тестовый платеж, 0 — реальный платеж.
transaction.agreement|integer|ID соглашения.
refund_details|object|Объект с финансовыми данными рефанда.
refund_details.code|integer|ID кода.
refund_details.reason|string|Причина отмены.
refund_details.author|string|Автор рефанда.
payment_details|object|Объект с финансовыми данными платежа. Обязательный.
payment_details.payment|object|Объект с данными о сумме, которую оплатил пользователь.
payment_details.payment.currency|string|Трехбуквенное обозначение валюты согласно стандарту ISO 4217.
payment_details.payment.amount|string|Сумма.
payment_details.payment_method_sum|object|Объект с данными о сумме, которая была оплачена из платежной системы.
payment_details.payment_method_sum.currency|string|Трехбуквенное обозначение валюты согласно стандарту ISO 4217.
payment_details.payment_method_sum.amount|string|Сумма.
payment_details.xsolla_balance_sum|object|Объект с данными о сумме, которая была оплачена с XSOLLA-баланса.
payment_details.xsolla_balance_sum.currency|string|Трехбуквенное обозначение валюты согласно стандарту ISO 4217.
payment_details.xsolla_balance_sum.amount|string|Сумма.
payment_details.payout|object|Объект с данными о сумме выплаты.
payment_details.payout.currency|string|Трехбуквенное обозначение валюты согласно стандарту ISO 4217.
payment_details.payout.amount|float|Сумма.
payment_details.vat|object|Размер VAT (только для Евросоюза).
payment_details.vat.currency|string|Трехбуквенное обозначение валюты согласно стандарту ISO 4217.
payment_details.vat.amount|float|Сумма.
payment_details.payout_currency_rate|float|Курс валюты платежа к валюте выплаты.
payment_details.xsolla_fee|object|Размер комиссии XSOLLA.
payment_details.xsolla_fee.currency|string|Трехбуквенное обозначение валюты согласно стандарту ISO 4217.
payment_details.xsolla_fee.amount|float|Сумма.
payment_details.payment_method_fee|object|Размер комиссии платежной системы.
payment_details.payment_method_fee.currency|string|Трехбуквенное обозначение валюты согласно стандарту ISO 4217.
payment_details.payment_method_fee.amount|float|Сумма.
payment_details.sales_tax|object|Размер налога (только для США).
payment_details.sales_tax.currency|string|Трехбуквенное обозначение валюты согласно стандарту ISO 4217.
payment_details.sales_tax.amount|float|Сумма.
payment_details.repatriation_commission|object|Объект с информацией о затратах на репатриацию, возлагаемых на XSOLLA третьими сторонами.
payment_details.repatriation_commission.currency|string|Трехбуквенное обозначение валюты затрат на репатриацию согласно стандарту ISO 4217.
payment_details.repatriation_commission.amount|float|Сумма затрат на репатриацию.
custom_parameters|object|Дополнительные параметры.

Коды отмены:

Код | Причина отмены | Описание
--- | -------------- | --------
1.|Cancellation by the user request / the game request.|Используется, если отмена произошла из Личного кабинета.
2.|Chargeback.|Используется, если по транзакции был chargeback.
3.|Integration Error.|Используется в случае проблем с интеграцией между XSOLLA и игрой. В этом случае не рекомендуется заносить пользователя в черный список.
4.|Fraud.|Используется в случае потенциального фрода.
5.|Test Payment.|Используется в случае совершения тестового платежа с последующей отменой. В этом случае не рекомендуется заносить пользователя в черный список.
6.|Expired Invoice.|Используется, если был выбран способ оплаты с системой отложенного платежа.
7.|PS debt cancel.|Используется, если транзакция была совершена через платежную систему, которая не произвела выплату по данной транзакции. В этом случае не рекомендуется заносить пользователя в черный список.
8.|Cancellation by the PS request.|Используется, когда платежная система запросила отмену транзакции. В этом случае не рекомендуется заносить пользователя в черный список.
9.|Cancellation by the user request.|Используется, если игра или заказ не удовлетворяют требованиям пользователя по каким-либо причинам. В этом случае не рекомендуется заносить пользователя в черный список.
10.|Cancellation by the game request.|Используется, когда игра просит отменить транзакцию. В этом случае не рекомендуется заносить пользователя в черный список.
11.|Account holder called to report fraud.|Используется, когда владелец аккаунта сообщил, что не совершал данный платеж.
12.|Friendly fraud.|Используется, если нам сообщили о friendly fraud.

Примеры отмены платежа:

***HTTP***
```HTTP
ЗАПРОС
POST /your_uri HTTP/1.1
Host: your.host
Accept: application/json
Content-Type: application/json
Content-Length: 1220
Authorization: Signature 31bd5924dd6cbc9cbe99d331c4a086a57291f9d7

{
    "notification_type":"refund",
    "purchase":{
        "virtual_currency":{
            "name": "Coins",
            "quantity":10,
            "currency":"USD",
            "amount":100
        },
        "subscription":{
            "plan_id": "b5dac9c8",
            "subscription_id": "10",
            "date_create": "2014-09-22T19:25:25+04:00",
            "currency": "USD",
            "amount": 9.99
        },
        "checkout":{
            "currency":"USD",
            "amount":50
        },
        "virtual_items":{
            "items":[
                {
                    "sku": "test_item1",
                    "amount":1
                }
            ],
            "currency":"USD",
            "amount":50
        },
        "total":{
            "currency":"USD",
            "amount":200
        }
    },
    "user": {
        "ip": "127.0.0.1",
        "phone": "18777976552",
        "email": "email@example.com",
        "id": "1234567",
        "name": "Xsolla User",
        "country": "US"
    },
    "transaction":{
        "id":1,
        "external_id":1,
        "dry_run":1,
        "agreement":1
    },
    "refund_details":{
        "code":1,
        "reason":"Fraud"
    },
    "payment_details":{
        "xsolla_fee":{
            "currency":"USD",
            "amount":"10"
        },
        "payout":{
            "currency":"USD",
            "amount":"200"
        },
        "payment_method_fee":{
            "currency":"USD",
            "amount":"20"
        },
        "payment":{
            "currency":"USD",
            "amount":"230"
        },
        "repatriation_commission":{
            "currency":"USD",
            "amount":"10"
        }
    }
}
ОТВЕТ
HTTP/1.1 204 No Content
```

***CURL***
```CURL
ЗАПРОС
$ curl -v 'https://your.hostname/your/uri' \
-X POST \
-d '{
        "notification_type":"refund",
        "purchase":{
            "virtual_currency":{
                "name": "Coins",
                "quantity":10,
                "currency":"USD",
                "amount":100
            },
            "subscription":{
                "plan_id": "b5dac9c8",
                "subscription_id": "10",
                "date_create": "2014-09-22T19:25:25+04:00",
                "currency": "USD",
                "amount": 9.99
            },
            "checkout":{
                "currency":"USD",
                "amount":50
            },
            "virtual_items":{
                "items":[
                    {
                        "sku": "test_item1",
                        "amount":1
                    }
                ],
                "currency":"USD",
                "amount":50
            },
            "total":{
                "currency":"USD",
                "amount":200
            }
        },
        "user": {
            "ip": "127.0.0.1",
            "phone": "18777976552",
            "email": "email@example.com",
            "id": "1234567",
            "name": "Xsolla User",
            "country": "US"
        },
        "transaction":{
            "id":1,
            "external_id":1,
            "dry_run":1,
            "agreement":1
        },
        "refund_details":{
            "code":1,
            "reason":"Fraud"
        },
        "payment_details":{
            "xsolla_fee":{
                "currency":"USD",
                "amount":"10"
            },
            "payout":{
                "currency":"USD",
                "amount":"200"
            },
            "payment_method_fee":{
                "currency":"USD",
                "amount":"20"
            },
            "payment":{
                "currency":"USD",
                "amount":"230"
            },
            "repatriation_commission":{
                "currency":"USD",
                "amount":"10"
            }
        }
    }
}'
ОТВЕТ
```

***PHP***
```PHP
ЗАПРОС
<?php

$request = array(
    'notification_type' => 'refund',
    'purchase' => array(
        'virtual_currency' => array(
            'name' => 'Coins',
            'quantity' => 100,
            'currency' => 'USD',
            'amount' => 9.99
        ),
        'total' => array(
            'currency' => 'USD',
            'amount' => 9.99
        )
    ),
    'user' => array(
        'ip' => '127.0.0.1',
        'phone' => '18777976552',
        'email' => 'email@example.com',
        'id' => '1234567',
        'country' => 'US'
    ),
    'transaction' => array(
        'id' => 87654321,
        'payment_date' => '2014-09-23T19:25:25+04:00',
        'payment_method' => 1380,
        'dry_run' => 1
    ),
    'refund_details' => array(
            'code' => 1,
            'reason' => 'Fraud'
    ),
    'payment_details' => array(
        'payment' => array(
            'currency' => 'USD',
            'amount' => 9.99
        ),
        'vat' => array(
            'currency' => 'USD',
            'amount' => 0
        ),
        'payout_currency_rate' => 1,
        'payout' => array(
            'currency' => 'USD',
            'amount' => 9.49
        ),
        'xsolla_fee' => array(
            'currency' => 'USD',
            'amount' => 0.19
        ),
        'payment_method_fee' => array(
            'currency' => 'USD',
            'amount' => 0.31
        ),
        'repatriation_commission' => array(
            'currency' => 'USD',
            'amount' => 0.2
        )
    )
);
ОТВЕТ
<?php

use Xsolla\SDK\Webhook\WebhookServer;
use Xsolla\SDK\Webhook\Message\Message;
use Xsolla\SDK\Exception\Webhook\XsollaWebhookException;

$callback = function (Message $message) {
    if ($message->isRefund()) {
        $userArray = $message->getUser();
        $paymentArray = $message->getTransaction();
        $paymentId = $message->getPaymentId();
        $externalPaymentId = $message->getExternalPaymentId();
        $paymentDetailsArray = $message->getPaymentDetails();
        $customParametersArray = $message->getCustomParameters();
        $isDryRun = $message->isDryRun();
        $refundArray = $message->getRefundDetails();
        $messageArray = $message->toArray();
        // TODO if you cannot handle the refund, you should throw XsollaWebhookException
    }
};

$webhookServer = WebhookServer::create($callback, PROJECT_KEY);
$webhookServer->start();
```

### Отмена апгрейда
Если пользователь отменил платеж, связанный с апгрейдом, XSOLLA отправляет данные об отмененных апгрейдах и текущем пакете на webhook URL.

Поле | Тип | Описание
---- | --- | --------
notification_type|string|Тип оповещения. Обязательный.
purchase|object|Объект с информацией о покупке. Обязательный.
purchase.pin_codes|object|Объект с информацией о купленных пакетах игры.
purchase.pin_codes.purchase_type|string|Тип покупки. Принимает значения “regular” – покупка пакета, “upgrade” – апгрейд пакета.
purchase.pin_codes.digital_content|string|Артикул игры, настраивается в Личном кабинете.
purchase.pin_codes.DRM|string|DRM-платформа игры.
purchase.pin_codes.currency|string|Трехбуквенное обозначение валюты покупки согласно стандарту ISO 4217.
purchase.pin_codes.amount|float|Сумма покупки.
purchase.pin_codes.transaction|object|Объект с информацией о транзакции.
purchase.pin_codes.transaction.id|integer|ID транзакции.
purchase.pin_codes.upgrade|object|Объект с информацией об апгрейде.
purchase.pin_codes.upgrade.digital_content_from|object|Объект с информацией о пакете пользователя, с которого был произведен апгрейд.
purchase.pin_codes.upgrade.digital_content_from.digital_content|string|Артикул игры, настраивается в Личном кабинете.
purchase.pin_codes.upgrade.digital_content_from.DRM|string|DRM-платформа игры.
purchase.pin_codes.upgrade.digital_content_to|object|Объект с информацией о пакете, на который пользователь перешел в рамках апгрейда.
purchase.pin_codes.upgrade.digital_content_to.digital_content|string|Артикул игры, настраивается в Личном кабинете.
purchase.pin_codes.upgrade.digital_content_to.DRM|string|DRM-платформа игры.
ownership|object|Объект с информацией о пакетах, которыми владеет пользователь. Обязательный.
ownership.digital_content|string|Артикул игры, настраивается в Личном кабинете.
ownership.DRM|string|DRM-платформа игры.

Примеры отмены апгрейда:

***HTTP***
```HTTP
ЗАПРОС
POST /your_uri HTTP/1.1
Host: your.host
Accept: application/json
Content-Type: application/json
Authorization: Signature <signature>

{
  "notification_type": "upgrade_refund",
  "purchase": {
    "pin_codes": [
      {
        "purchase_type": "regular",
        "digital_content": "silver",
        "DRM": "drmfree",
        "currency": "USD",
        "amount": "40",
        "transaction": {
          "id": "361697569"
        }
      },
      {
        "purchase_type": "upgrade",
        "upgrade": {
          "digital_content_from": {
            "digital_content": "silver",
            "DRM": "drmfree"
          },
          "digital_content_to": {
            "digital_content": "gold",
            "DRM": "drmfree"
          }
        },
        "currency": "USD",
        "amount": "20",
        "transaction": {
          "id": "361697570"
        }
      },
      {
        "purchase_type": "upgrade",
        "upgrade": {
          "digital_content_from": {
            "digital_content": "gold",
            "DRM": "drmfree"
          },
          "digital_content_to": {
            "digital_content": "platinum",
            "DRM": "drmfree"
          }
        },
        "currency": "USD",
        "amount": "20",
        "transaction": {
          "id": "361697571"
        }
      }
    ]
  },
  "ownership": {
    "digital_content": null,
    "DRM": null
  }
}
ОТВЕТ
```

***CURL***
```CURL
ЗАПРОС
$ curl -v 'https://your.hostname/your/uri' \
-X POST \
-d '{
  "notification_type": "upgrade_refund",
  "purchase": {
    "pin_codes": [
      {
        "purchase_type": "regular",
        "digital_content": "silver",
        "DRM": "drmfree",
        "currency": "USD",
        "amount": "40",
        "transaction": {
          "id": "361697569"
        }
      },
      {
        "purchase_type": "upgrade",
        "upgrade": {
          "digital_content_from": {
            "digital_content": "silver",
            "DRM": "drmfree"
          },
          "digital_content_to": {
            "digital_content": "gold",
            "DRM": "drmfree"
          }
        },
        "currency": "USD",
        "amount": "20",
        "transaction": {
          "id": "361697570"
        }
      },
      {
        "purchase_type": "upgrade",
        "upgrade": {
          "digital_content_from": {
            "digital_content": "gold",
            "DRM": "drmfree"
          },
          "digital_content_to": {
            "digital_content": "platinum",
            "DRM": "drmfree"
          }
        },
        "currency": "USD",
        "amount": "20",
        "transaction": {
          "id": "361697571"
        }
      }
    ]
  },
  "ownership": {
    "digital_content": null,
    "DRM": null
  }
}'
ОТВЕТ
```

***PHP***
```PHP
ЗАПРОС
<?php

$request = array (
  'notification_type' => 'upgrade_refund',
  'purchase' =>
  array (
    'pin_codes' =>
    array (
      0 =>
      array (
        'purchase_type' => 'regular',
        'digital_content' => 'silver',
        'DRM' => 'drmfree',
        'currency' => 'USD',
        'amount' => '40',
        'transaction' =>
        array (
          'id' => '361697569',
        ),
      ),
      1 =>
      array (
        'purchase_type' => 'upgrade',
        'upgrade' =>
        array (
          'digital_content_from' =>
          array (
            'digital_content' => 'silver',
            'DRM' => 'drmfree',
          ),
          'digital_content_to' =>
          array (
            'digital_content' => 'gold',
            'DRM' => 'drmfree',
          ),
        ),
        'currency' => 'USD',
        'amount' => '20',
        'transaction' =>
        array (
          'id' => '361697570'
        ),
      ),
      2 =>
      array (
        'purchase_type' => 'upgrade',
        'upgrade' =>
        array (
          'digital_content_from' =>
          array (
            'digital_content' => 'gold',
            'DRM' => 'drmfree',
          ),
          'digital_content_to' =>
          array (
            'digital_content' => 'platinum',
            'DRM' => 'drmfree',
          ),
        ),
        'currency' => 'USD',
        'amount' => '20',
        'transaction' =>
        array (
          'id' => '361697571'
        ),
      ),
    ),
  ),
  'ownership' =>
  array (
    'digital_content' => NULL,
    'DRM' => NULL,
  ),
)
ОТВЕТ
```

### Транзакция отклонена при проверке AFS
Если транзакция была отклонена при проверке AFS, XSOLLA присылает детали транзакции на webhook URL. Для включения оповещения необходимо обратиться к аккаунт-менеджеру проекта.

Поле | Тип | Описание
---- | --- | --------
notification_type|string|Тип оповещения. Обязательный.
user|object|Объект с информацией о пользователе.
user.ip|string|IP адрес пользователя.
user.phone|string|Номер телефона пользователя (в международном формате).
user.email|string|Email пользователя.
user.id|string|ID пользователя. Обязательный.
user.name|string|Имя пользователя.
user.country|string|Двухбуквенное обозначение страны пользователя согласно стандарту ISO 3166-1 alpha-2.
user.zip|string|Почтовый индекс.
transaction|object|Объект с информацией о транзакции, связанной с этой операцией. Обязательный.
transaction.id|integer|ID транзакции.
transaction.external_id|string|Внешний ID транзакции.
transaction.dry_run|integer|Признак тестовой транзакции: 1 — тестовый платеж, 0 — реальный платеж.
transaction.agreement|integer|ID соглашения.
refund_details|object|Объект с финансовыми данными рефанда.
refund_details.code|integer|ID кода.
refund_details.reason|string|Причина отмены.
refund_details.author|string|Автор рефанда.

Примеры отклонения транзакции при проверке AFS:

***HTTP***
```HTTP
ЗАПРОС
POST /your_uri HTTP/1.1
Host: your.host
Accept: application/json
Content-Type: application/json
Content-Length: 1220
Authorization: Signature 31bd5924dd6cbc9cbe99d331c4a086a57291f9d7

{
    "notification_type":"afs_reject",
    "user": {
        "ip": "127.0.0.1",
        "phone": "18777976552",
        "email": "semail@example.com,
        "id": "1234567",
        "name": "Xsolla User",
        "country": "US"
    },
    "transaction":{
        "id":1,
        "external_id":1,
        "dry_run":1,
        "agreement":1
    },
    "refund_details":{
        "code":4,
        "reason":"Potential fraud"
    }
}
ОТВЕТ
HTTP/1.1 204 No Content
```

***CURL***
```CURL
ЗАПРОС
$ curl -v 'https://your.hostname/your/uri' \
-X POST \
-d '{
  "notification_type":"afs_reject",
  "user": {
      "ip": "127.0.0.1",
      "phone": "18777976552",
      "email": "semail@example.com,
      "id": "1234567",
      "name": "Xsolla User",
      "country": "US"
  },
  "transaction":{
      "id":1,
      "external_id":1,
      "dry_run":1,
      "agreement":1
  },
  "refund_details":{
      "code":4,
      "reason":"Potential fraud"
  }
}'
ОТВЕТ
```

***PHP***
```PHP
ЗАПРОС
<?php

$request = array(
    'notification_type' => 'afs_reject',
    'user' => array(
        'ip' => '127.0.0.1',
        'phone' => '18777976552',
        'email' => 'email@example.com',
        'id' => '1234567',
        'country' => 'US'
    ),
    'transaction' => array(
        'id' => 87654321,
        'payment_date' => '2014-09-23T19:25:25+04:00',
        'payment_method' => 1380,
        'dry_run' => 1
    ),
    'refund_details' => array(
            'code' => 4,
            'reason' => 'Potential fraud'
    )
);
ОТВЕТ
<?php

use Xsolla\SDK\Webhook\WebhookServer;
use Xsolla\SDK\Webhook\Message\Message;
use Xsolla\SDK\Exception\Webhook\XsollaWebhookException;

$callback = function (Message $message) {
    if ($message->isRefund()) {
        $userArray = $message->getUser();
        $paymentArray = $message->getTransaction();
        $paymentId = $message->getPaymentId();
        $externalPaymentId = $message->getExternalPaymentId();
        $customParametersArray = $message->getCustomParameters();
        $isDryRun = $message->isDryRun();
        $refundArray = $message->getRefundDetails();
        $messageArray = $message->toArray();
        // TODO if you cannot handle the refund, you should throw XsollaWebhookException
    }
};

$webhookServer = WebhookServer::create($callback, PROJECT_KEY);
$webhookServer->start();
```

### Создание подписки
Когда пользователь создает подписку, XSOLLA присылает оповещение на webhook URL.

Поле | Тип | Описание
---- | --- | --------
notification_type|string|Тип оповещения. Обязательный.
user|object|Объект с информацией о пользователе.
user.id|string|ID пользователя. Обязательный.
user.name|string|Имя пользователя.
subscription|object|Объект с данными о подписке.
subscription.plan_id|string|ID плана (внешний id, если план был создан через API).
subscription.tags|array|Теги плана.
subscription.subscription_id|integer|ID подписки в базе данных XSOLLA.
subscription.product_id|string|ID продукта (если был отправлен в токене).
subscription.date_create|string|Дата и время создания подписки согласно стандарту ISO 8601.
subscription.date_next_charge|string|Дата и время следующего списания согласно стандарту ISO 8601.
subscription.trial|object|Объект с информацией о триальном периоде подписки.
subscription.trial.value|integer|Длительность триального периода.
subscription.trial.type|string|Тип триального периода: day.

Примеры создания подписки:

***HTTP***
```HTTP
ЗАПРОС
POST /your/uri HTTP/1.1
Host: your.hostname
Accept: application/json
Content-Type: application/json
Content-Length: 240
Authorization: Signature 13342703ccaca5064ad33ba451d800c5e823db8f

{
    "notification_type":"create_subscription",
    "user":{
        "id":"1234567",
        "name":"Xsolla User"
    },
    "subscription":{
        "plan_id":"b5dac9c8",
        "subscription_id":"10",
        "product_id":"Demo Product",
        "date_create":"2014-09-22T19:25:25+04:00",
        "date_next_charge":"2015-01-22T19:25:25+04:00",
        "trial": {
                "value": 90,
                "type": "day"
            }
    }
}
ОТВЕТ
HTTP/1.1 204 No Content
```

***CURL***
```CURL
ЗАПРОС
$ curl -v 'https://your.hostname/your/uri' \
-X POST \
-H 'Accept: application/json' \
-H 'Content-Type: application/json' \
-H 'Authorization: Signature 13342703ccaca5064ad33ba451d800c5e823db8f' \
-d '{
        "notification_type":"create_subscription",
        "user":{
            "id":"1234567",
            "name":"Xsolla User"
        },
        "subscription":{
            "plan_id":"b5dac9c8",
            "subscription_id":"10",
            "product_id":"Demo Product",
            "date_create":"2014-09-22T19:25:25+04:00",
            "date_next_charge":"2015-01-22T19:25:25+04:00",
            "trial": {
                    "value": 90,
                    "type": "day"
                }
        }
    }'
ОТВЕТ
```

***PHP***
```PHP
ЗАПРОС
<?php

$request = array(
    'notification_type' => 'create_subscription',
    'user' => array(
        'id' => '1234567',
        'name' => 'Xsolla User'
    ),
    'subscription' => array(
        'plan_id' => 'b5dac9c8',
        'subscription_id' => '10',
        'product_id' => 'Demo Product',
        'date_create' => '2014-09-22T19:25:25+04:00',
        'date_next_charge' => '2015-01-22T19:25:25+04:00',
        'trial' =>  array(
                'value' =>  90,
                'type' =>  'day'
            )
    )
);
ОТВЕТ
<?php

use Xsolla\SDK\Webhook\WebhookServer;
use Xsolla\SDK\Webhook\Message\Message;
use Xsolla\SDK\Exception\Webhook\XsollaWebhookException;

$callback = function (Message $message) {
    if ($message instanceof CreateSubscriptionMessage) {
       $messageArray = $message->toArray();
       // TODO if the subscription creation fails for some reason, you should throw XsollaWebhookException
    }
};

$webhookServer = WebhookServer::create($callback, PROJECT_KEY);
$webhookServer->start();
```

### Изменение подписки
В случае изменения каких-либо параметров ('plan_id', 'date_next_charge') подписки и в случае каждого продления подписки, XSOLLA отправляет оповещение "update_subscription" на webhook URL.

Поле | Тип | Описание
---- | --- | --------
notification_type|string|Тип оповещения. Обязательный.
user|object|Объект с информацией о пользователе.
user.id|string|ID пользователя. Обязательный.
user.name|string|Имя пользователя.
subscription|object|Объект с данными о подписке.
subscription.plan_id|string|ID плана (внешний id, если план был создан через API).
subscription.tags|array|Теги плана.
subscription.subscription_id|integer|ID подписки в базе данных XSOLLA.
subscription.product_id|string|ID продукта (если был отправлен в токене).
subscription.date_next_charge|string|Дата и время следующего списания согласно стандарту ISO 8601.

Примеры изменения подписки:

***HTTP***
```HTTP
ЗАПРОС
POST /your/uri HTTP/1.1
Host: your.hostname
Accept: application/json
Content-Type: application/json
Content-Length: 240
Authorization: Signature 13342703ccaca5064ad33ba451d800c5e823db8f

{
    "notification_type":"update_subscription",
    "user":{
        "id":"1234567",
        "name":"Xsolla User"
    },
    "subscription":{
        "plan_id":"b5dac9c8",
        "subscription_id":"10",
        "product_id":"Demo Product",
        "date_next_charge":"2015-01-22T19:25:25+04:00"
    }
}
ОТВЕТ
HTTP/1.1 204 No Content
```

***CURL***
```CURL
ЗАПРОС
$ curl -v 'https://your.hostname/your/uri' \
-X POST \
-H 'Accept: application/json' \
-H 'Content-Type: application/json' \
-H 'Authorization: Signature 13342703ccaca5064ad33ba451d800c5e823db8f' \
-d '{
        "notification_type":"update_subscription",
        "user":{
            "id":"1234567",
            "name":"Xsolla User"
        },
        "subscription":{
            "plan_id":"b5dac9c8",
            "subscription_id":"10",
            "product_id":"Demo Product",
            "date_next_charge":"2015-01-22T19:25:25+04:00"
        }
    }'
ОТВЕТ
```

***PHP***
```PHP
ЗАПРОС
<?php

$request = array(
    'notification_type' => 'update_subscription',
    'user' => array(
        'id' => '1234567',
        'name' => 'Xsolla User'
    ),
    'subscription' => array(
        'plan_id' => 'b5dac9c8',
        'subscription_id' => '10',
        'product_id' => 'Demo Product',
        'date_next_charge' => '2015-01-22T19:25:25+04:00'
    )
);
ОТВЕТ
<?php

use Xsolla\SDK\Webhook\WebhookServer;
use Xsolla\SDK\Webhook\Message\Message;
use Xsolla\SDK\Exception\Webhook\XsollaWebhookException;

$callback = function (Message $message) {
  if ($message instanceof UpdateSubscriptionMessage) {
     $messageArray = $message->toArray();
     // TODO if the subscription renewing fails for some reason, you should throw XsollaWebhookException
  }
};

$webhookServer = WebhookServer::create($callback, PROJECT_KEY);
$webhookServer->start();
```

### Отмена подписки
Когда подписка отменяется по каким-либо причинам, XSOLLA присылает оповещение на webhook URL.

Поле | Тип | Описание
---- | --- | --------
notification_type|string|Тип оповещения. Обязательный.
user|object|Объект с информацией о пользователе.
user.id|string|ID пользователя. Обязательный.
user.name|string|Имя пользователя.
subscription|object|Объект с данными о подписке.
subscription.plan_id|string|ID плана (внешний id, если план был создан через API).
subscription.tags|array|Теги плана.
subscription.subscription_id|integer|ID подписки в базе данных XSOLLA.
subscription.product_id|string|ID продукта (если был отправлен в токене).
subscription.date_create|string|Дата и время создания подписки согласно стандарту ISO 8601.
subscription.date_end|string|Дата и время окончания срока действия подписки согласно стандарту ISO 8601.

Примеры изменения подписки:

***HTTP***
```HTTP
ЗАПРОС
POST /your/uri HTTP/1.1
Host: your.hostname
Accept: application/json
Content-Type: application/json
Content-Length: 240
Authorization: Signature 13342703ccaca5064ad33ba451d800c5e823db8f

{
    "notification_type":"cancel_subscription",
    "user":{
        "id":"1234567",
        "name":"Xsolla User"
    },
    "subscription":{
        "plan_id":"b5dac9c8",
        "subscription_id":"10",
        "product_id":"Demo Product",
        "date_create":"2014-09-22T19:25:25+04:00",
        "date_end":"2015-01-22T19:25:25+04:00"
    }
}
ОТВЕТ
HTTP/1.1 204 No Content
```

***CURL***
```CURL
ЗАПРОС
$ curl -v 'https://your.hostname/your/uri' \
-X POST \
-H 'Accept: application/json' \
-H 'Content-Type: application/json' \
-H 'Authorization: Signature 13342703ccaca5064ad33ba451d800c5e823db8f' \
-d '{
        "notification_type":"cancel_subscription",
        "user":{
            "id":"1234567",
            "name":"Xsolla User"
        },
        "subscription":{
            "plan_id":"b5dac9c8",
            "subscription_id":"10",
            "product_id":"Demo Product",
            "date_create":"2014-09-22T19:25:25+04:00",
            "date_end":"2015-01-22T19:25:25+04:00"
        }
    }'
ОТВЕТ
```

***PHP***
```PHP
ЗАПРОС
<?php

$request = array(
    'notification_type' => 'cancel_subscription',
    'user' => array(
        'id' => '1234567',
        'name' => 'Xsolla User'
    ),
    'subscription' => array(
        'plan_id' => 'b5dac9c8',
        'subscription_id' => '10',
        'product_id' => 'Demo Product',
        'date_create' => '2014-09-22T19:25:25+04:00',
        'date_end' => '2015-01-22T19:25:25+04:00',
    )
);
ОТВЕТ
<?php

use Xsolla\SDK\Webhook\WebhookServer;
use Xsolla\SDK\Webhook\Message\Message;
use Xsolla\SDK\Exception\Webhook\XsollaWebhookException;

$callback = function (Message $message) {
    if ($message instanceof CancelSubscriptionMessage) {
       $messageArray = $message->toArray();
       // TODO if the subscription canceling fails for some reason, you should throw XsollaWebhookException
    }
};

$webhookServer = WebhookServer::create($callback, PROJECT_KEY);
$webhookServer->start();
```

### Получение ключа
XSOLLA отправляет API вызовы к серверу для получения ключа игры после каждого успешного платежа.

Поле | Тип | Описание
---- | --- | --------
notification_type|string|Тип оповещения.
user|object|Объект с информацией о пользователе.
user.id|string|ID пользователя. Обязательный.
user.name|string|Имя пользователя.
pin_code|object|Объект с информацией о ключе.
pin_code.digital_content|string|Артикул игры.
pin_code.DRM|string|DRM-платформа, на которой игра будет доступна.

Примеры получения ключа:

***HTTP***
```HTTP
ЗАПРОС
POST /your/uri HTTP/1.1
Host: your.hostname
Accept: application/json
Content-Type: application/json
Content-Length: 240
Authorization: Signature 13342703ccaca5064ad33ba451d800c5e823db8f

{
    "notification_type":"get_pincode",
    "user":{
        "id":"1234567",
        "name":"Xsolla User"
    },
    "pin_code":{
        "digital_content":"Game SKU",
        "DRM":"Steam"
    }
}
ОТВЕТ
HTTP/1.1 200 OK
Content-Type: application/json
{
    "pin_code": "PIN_CODE"
}
```

***CURL***
```CURL
ЗАПРОС
$ curl -v 'https://your.hostname/your/uri' \
-X POST \
-H 'Accept: application/json' \
-H 'Content-Type: application/json' \
-H 'Authorization: Signature 13342703ccaca5064ad33ba451d800c5e823db8f' \
-d '{
        "notification_type":"get_pincode",
        "user":{
            "id":"1234567",
            "name":"Xsolla User"
        },
        "pin_code":{
            "digital_content":"Game SKU",
            "DRM":"Steam"
        }
    }'
ОТВЕТ
```

***PHP***
```PHP
ЗАПРОС
<?php

$request = array (
       'notification_type' => 'get_pincode',
       'user' =>
           array (
               'id' => '1234567',
               'name' => 'Xsolla User',
           ),
       'pin_code' =>
           array (
               'digital_content' => 'Game SKU',
               'DRM' => 'Steam',
           ),
   );
ОТВЕТ
<?php

use Xsolla\SDK\Webhook\WebhookServer;
use Xsolla\SDK\Webhook\Message\Message;
use Xsolla\SDK\Exception\Webhook\XsollaWebhookException;

$callback = function (Message $message) {
    if ($message instanceof GetPinCodeMessage) {
        $userArray = $message->getUser();
        $drmSku = $message->getDRM();
        $digitalContentSku = $message->getDigitalContent();
        // TODO get a pin code from your database or generate a new one. Put the pin code into variable $newPinCode
        $newPinCode = 'NEW_PIN_CODE';
        // TODO if the pin code creation or generation fail for some reason, you should throw XsollaWebhookException
        return new \Xsolla\SDK\Webhook\Response\PinCodeResponse($newPinCode);
    }
};
$webhookServer = WebhookServer::create($callback, PROJECT_KEY);
$webhookServer->start();
```

### Активация ключа
Когда пользователь активирует ключ, XSOLLA присылает оповещение на webhook URL.

Поле | Тип | Описание
---- | --- | --------
notification_type|string|Тип оповещения.
key|string|Ключ активации.
sku|string|Уникальный ID пакета ключей.
user_id|string|ID пользователя.
activation_date|datetime|Дата активации ключа в формате ГГГГММДДЧЧММСС согласно стандарту ISO 8601.
user_country|string|Двухбуквенное обозначение страны пользователя согласно стандарту ISO 3166-1 alpha-2.
restriction|object|Объект с настройками кластера регионального ограничения. Кластер включает в себя тип ограничения, список стран, серверов и языковых версий, для которых доступна игра.
restriction.sku|string|Уникальный ID кластера.
restriction.name|string|Имя кластера.
restriction.types|array|Массив типов ограничений.
restriction_countries|array|Массив стран, входящих в кластер.
restriction.servers|array|Массив серверов игры.
restriction.locales|array|Массив языковых версий игры.

Примеры активации ключа:

***HTTP***
```HTTP
ЗАПРОС
POST /your_uri HTTP/1.1
Host: your.host
Accept: application/json
Content-Type: application/json
Content-Length: 165
Authorization: Signature 52eac2713985e212351610d008e7e14fae46f902

{
  "notification_type": "redeem_key",
  "key": "wqdqwwddq9099022",
  "sku": "123",
  "user_id": "sample_user",
  "activation_date": "2018-11-20T08:38:51+03:00",
  "user_country": "EN",
  "restriction": {
      "name": "cls_1",
      "types": [
           "activation"
        ],
        "countries": [
             "RU"
        ]
  }
}
ОТВЕТ
HTTP/1.1 204 No Content
```

***CURL***
```CURL
ЗАПРОС
$ curl -v 'https://your.hostname/your/uri' \
-X POST \
-H 'Accept: application/json' \
-H 'Content-Type: application/json' \
-H 'Authorization: Signature 13342703ccaca5064ad33ba451d800c5e823db8f' \
-d '{
     "notification_type": "redeem_key",
  "key": "wqdqwwddq9099022",
  "sku": "123",
  "user_id": "sample_user",
  "activation_date": "2018-11-20T08:38:51+03:00",
  "user_country": "EN",
  "restriction": {
      "name": "cls_1",
      "types": [
           "activation"
        ],
        "countries": [
             "RU"
         ]
    }
}'
ОТВЕТ
```

***PHP***
```PHP
ЗАПРОС
<?php
$request = array(
    'notification_type' => 'redeem_key',
    'key' => ‘wqdqwwddq9099022’,
    'sku' => 123,
    'user_id' => ‘sample_user’,
    'activation_date' => ‘2018-11-20T08:38:51+03:00’,
    'user_country' => ‘EN’,
    'restriction' =>
           array(
                'name' => ‘cls_1’,
                'types' =>
                        array(
                            ‘activation’
                        ),
                'countries' =>
                        array(
                            ‘RU’
                        ),
             ),
);  
ОТВЕТ
<?php

$response = null;
```

### Получение списка друзей
API должен быть реализован на стороне партнера. Максимальный размер списка друзей — 2000. Подробнее можно узнать в [рецепте](https://developers.xsolla.com/ru/recipes/store/gifting/).

HTTP-ЗАПРОС

```
GET https://your.webhook.url?notification_type=friends_list&user=user_id&query=frien&offset=10&limit=20&sign=12dfg3f5gdsf4g5s6dfg2sdg1
notification_type
```

Поле | Тип | Описание
---- | --- | --------
string|friends_list|Идентификатор , определяющий тип запрос на список друзей.
user|string|Уникальный идентификатор пользователя, который делает покупку другу.
query|string|Имя или идентификатор друга. Может передавать часть имени или идентификатора друга.
limit|string|Лимит количества элементов на странице. Обязательный.
offset|integer|Номер элемента, c которого выполняется вывод на странице (нумерация ведется с 0).
sign|string|Строка для подписи формируется следующим образом:<ul><li>notification_type + значения параметров, отсортированных по ключу в алфавитном порядке + secret_key</li><li>Второй шаг — применение SHA-1 криптографической хэш-функции к получившейся на первом шаге строке.</li></ul>

Примеры получения списка друзей:

***HTTP***
```HTTP
ЗАПРОС
GET https://your.webhook.url?notification_type=friends_list&user=user_id&query=frien&offset=10&limit=20&sign=12dfg3f5gdsf4g5s6dfg2sdg1 HTTP/1.1
Host: your.host
Accept: application/json
Content-Type: application/json
Content-Length: 1220
Authorization: Signature 31bd5924dd6cbc9cbe99d331c4a086a57291f9d7
ОТВЕТ
HTTP/1.1 200 OK
Content-Type: application/json

[
  {
    "friends": [
      {
        "id": "1",
        "name": "doctor",
        "email": "doctor@hospital.com",
        "image_url": "https://partner/link/doctor.jpg"
      },
      {
        "id": "2",
        "name": "cook",
        "email": "cook@kitchen.com",
        "image_url": "https://partner/link/cook.jpg"
      },
      {
        "id": "3",
        "name": "teacher",
        "email": "teacher@school.com"
      },
      {
        "id": "4",
        "name": "god",
        "email": "god@heaven.com",
        "image_url": "https://partner/link/god.jpg"
      }
      ],
    "total": 10
  }
]
```

***CURL***
```CURL
ЗАПРОС
$ curl -v 'https://your.webhook.url?notification_type=friends_list&user=user_id&query=frien&offset=10&limit=20&sign=12dfg3f5gdsf4g5s6dfg2sdg1' \
-X GET \
-u merchant_id:merchant_api_key
ОТВЕТ
[
  {
  "friends": [
      {
        "id": "1",
        "name": "John Carter",
        "email": "carter@xsolla.com",
        "image_url": "https://partner/link/doctor.jpg"
      },
      {
        "id": "2",
        "name": "John Smith",
        "email": "smith@xsolla.com",
        "image_url": "https://partner/link/cook.jpg"
      }
    ],
  "total": 10
  }
]
```

### Баланс пользователя: внутренняя операция
Когда пользователь совершает платеж, XSOLLA присылает специальное оповещение об изменении баланса пользователя.

Поле | Тип | Описание
---- | --- | --------
notification_type|string|Тип оповещения.
operation_type|string|Тип операции.
id_operation|integer|ID операции в базе данных XSOLLA.
user|object|Объект с информацией о пользователе.
user.id|string|ID пользователя. Обязательный.
user.name|string|Имя пользователя.
user.email|string|Email пользователя.
virtual_currency_balance|object|Объект с данными о балансе пользователя.
virtual_currency_balance.old_value|string|Значение баланса до совершения данной операции.
virtual_currency_balance.new_value|string|Значение баланса после совершения данной операции.
virtual_currency_balance.diff|string|Количество виртуальной валюты в заказе.
transaction|object|Объект с информацией о транзакции, связанной с этой операцией. Обязательный.
transaction.id|integer|ID транзакции.
transaction.date|string|Дата транзакции.

Примеры: 

***HTTP***
```HTTP
ЗАПРОС
POST /your/uri HTTP/1.1
Host: your.hostname
Accept: application/json
Content-Type: application/json
Content-Length: 240
Authorization: Signature 13342703ccaca5064ad33ba451d800c5e823db8f

{
    "virtual_currency_balance":{
        "old_value":"0",
        "new_value":"200",
        "diff":"200"
    },
    "user":{
        "name":"Xsolla User",
        "id":"1234567",
        "email":"email@example.com"
    },
    "transaction":{
        "id":"123456789",
        "date":"2015-05-19T15:54:40+03:00"
    },
    "operation_type":"payment",
    "notification_type":"user_balance_operation",
    "id_operation":"66989"
}
ОТВЕТ
HTTP/1.1 204 No Content
```

***CURL***
```CURL
ЗАПРОС
$ curl -v 'https://your.hostname/your/uri' \
-X POST \
-H 'Accept: application/json' \
-H 'Content-Type: application/json' \
-H 'Authorization: Signature 13342703ccaca5064ad33ba451d800c5e823db8f' \
-d '{
        "virtual_currency_balance":{
            "old_value":"0",
            "new_value":"200",
            "diff":"200"
        },
        "user":{
            "name":"Xsolla User",
            "id":"1234567",
            "email":"email@example.com"
        },
        "transaction":{
            "id":"123456789",
            "date":"2015-05-19T15:54:40+03:00"
        },
        "operation_type":"payment",
        "notification_type":"user_balance_operation",
        "id_operation":"66989"
    }'
ОТВЕТ
```

***PHP***
```PHP
ЗАПРОС
<?php

$request = array(
    'virtual_currency_balance' => array(
        'old_value' => '0',
        'new_value' => '200',
        'diff' => '200'
    ),
    'user' => array(
        'name' => 'Xsolla User',
        'id' => '1234567',
        'email' => 'email@example.com'
    ),
    'transaction => array(
        'id' => '123456789',
        'date' => '2015-05-19T15:54:40+03:00'
    ),
    'operation_type' => 'payment',
    'notification_type' => 'user_balance_operation',
    'id_operation' => '66989'
);
ОТВЕТ
<?php

use Xsolla\SDK\Webhook\WebhookServer;
use Xsolla\SDK\Webhook\Message\Message;
use Xsolla\SDK\Exception\Webhook\XsollaWebhookException;

$callback = function (Message $message) {
    if ($message instanceof UserBalanceMessage) {
       $messageArray = $message->toArray();
       // TODO if the user balance operation fails for some reason, you should throw XsollaWebhookException
    }
};

$webhookServer = WebhookServer::create($callback, PROJECT_KEY);
$webhookServer->start();
```

### Баланс пользователя: покупка в игре
Когда пользователь совершает покупку в игре (например, покупка предметов), XSOLLA присылает специальное оповещение об изменении баланса пользователя.

Поле | Тип | Описание
---- | --- | --------
notification_type|string|Тип оповещения.
operation_type|string|Тип операции.
id_operation|integer|ID операции в базе данных XSOLLA.
user|object|Объект с информацией о пользователе.
user.id|string|ID пользователя. Обязательный.
user.name|string|Имя пользователя.
user.email|string|Email пользователя.
virtual_currency_balance|object|Объект с данными о балансе пользователя.
virtual_currency_balance.old_value|string|Значение баланса до совершения данной операции.
virtual_currency_balance.new_value|string|Значение баланса после совершения данной операции.
virtual_currency_balance.diff|string|Количество виртуальной валюты в заказе.
items_operation_type|string|Тип операции с предметами.
items|array|Массив данных о предмете в заказе.
items.sku|string|ID предмета (артикул).
items.amount|integer|Количество экземпляров предмета в заказе.

Примеры покупки в игре:

***HTTP***
```HTTP
ЗАПРОС
POST /your/uri HTTP/1.1
Host: your.hostname
Accept: application/json
Content-Type: application/json
Content-Length: 240
Authorization: Signature 13342703ccaca5064ad33ba451d800c5e823db8f

{
    "virtual_currency_balance":{
        "old_value":"0",
        "new_value":"200",
        "diff":"200"
    },
    "user":{
        "name":"Xsolla User",
        "id":"1234567",
        "email":"email@example.com"
    },
    "operation_type":"inGamePurchase",
    "notification_type":"user_balance_operation",
    "items_operation_type": "add",
         "items": [{
         "sku": "1468",
         "amount": "2"
         }],
    "id_operation":"66989"
}
ОТВЕТ
HTTP/1.1 204 No Content
```

***CURL***
```CURL
ЗАПРОС
$ curl -v 'https://your.hostname/your/uri' \
-X POST \
-H 'Accept: application/json' \
-H 'Content-Type: application/json' \
-H 'Authorization: Signature 13342703ccaca5064ad33ba451d800c5e823db8f' \
-d '{
        "virtual_currency_balance":{
            "old_value":"0",
            "new_value":"200",
            "diff":"200"
        },
        "user":{
            "name":"Xsolla User",
            "id":"1234567",
            "email":"email@example.com"
        },
        "operation_type":"inGamePurchase",
        "notification_type":"user_balance_operation",
        "items_operation_type": "add",
             "items": [{
             "sku": "1468",
             "amount": "2"
             }],
        "id_operation":"66989"
    }'
ОТВЕТ
```

***PHP***
```PHP
ЗАПРОС
<?php

$request = array(
    'virtual_currency_balance' => array(
            'old_value' => '0',
            'new_value' => '200',
            'diff' => '200'
    ),
    'user' => array(
        'name' => 'Xsolla User',
        'id' => '1234567',
        'email' => 'email@example.com'
    ),
    'operation_type' => 'inGamePurchase',
    'notification_type' => 'user_balance_operation',
    'items_operation_type' =>  'add',
         'items' =>  array(
             'sku' =>  '1468',
             'amount' =>  '2'
         ),
    'id_operation' => '66989'
);
ОТВЕТ
<?php

use Xsolla\SDK\Webhook\WebhookServer;
use Xsolla\SDK\Webhook\Message\Message;
use Xsolla\SDK\Exception\Webhook\XsollaWebhookException;

$callback = function (Message $message) {
    if ($message instanceof UserBalanceMessage) {
       $messageArray = $message->toArray();
       // TODO if the user balance operation fails for some reason, you should throw XsollaWebhookException
    }
};

$webhookServer = WebhookServer::create($callback, PROJECT_KEY);
$webhookServer->start();
```

### Баланс пользователя: активация купона
Если пользователь активировал купон для получения предметов или виртуальной валюты в игре, XSOLLA присылает специальное оповещение об изменении баланса пользователя.

Поле | Тип | Описание
---- | --- | --------
notification_type|string|Тип оповещения.
operation_type|string|Тип операции.
id_operation|integer|ID операции в базе данных XSOLLA.
user|object|Объект с информацией о пользователе.
user.id|string|ID пользователя. Обязательный.
user.name|string|Имя пользователя.
user.email|string|Email пользователя.
virtual_currency_balance|object|Объект с данными о балансе пользователя.
virtual_currency_balance.old_value|string|Значение баланса до совершения данной операции.
virtual_currency_balance.new_value|string|Значение баланса после совершения данной операции.
virtual_currency_balance.diff|string|Количество виртуальной валюты в заказе.
items_operation_type|string|Тип операции с предметами.
items|array|Массив данных о предмете в заказе.
items.sku|string|ID предмета (артикул).
items.amount|integer|Количество экземпляров предмета в заказе.
coupon|object|Объект с данными о купоне.
coupon.coupon_code|string|Код купона.
coupon.campaign_code|string|Код кампании купонов.

Примеры активации купона:

***HTTP***
```HTTP
ЗАПРОС
POST /your/uri HTTP/1.1
Host: your.hostname
Accept: application/json
Content-Type: application/json
Content-Length: 240
Authorization: Signature 13342703ccaca5064ad33ba451d800c5e823db8f

{
    "virtual_currency_balance":{
        "old_value":"0",
        "new_value":"0",
        "diff":"0"
    },
    "user":{
        "name":"Xsolla User",
        "id":"1234567",
        "email":"email@example.com"
    },
    "operation_type":"coupon",
    "notification_type":"user_balance_operation",
    "items_operation_type": "add",
         "items": [{
             "sku": "1468",
             "amount": "2"
         }],
    "id_operation":"66989",
    "coupon": {
         "coupon_code": "test123",
         "campaign_code": "Xsolla Campaign"
    }
}
ОТВЕТ
HTTP/1.1 204 No Content
```

***CURL***
```CURL
ЗАПРОС
$ curl -v 'https://your.hostname/your/uri' \
-X POST \
-H 'Accept: application/json' \
-H 'Content-Type: application/json' \
-H 'Authorization: Signature 13342703ccaca5064ad33ba451d800c5e823db8f' \
-d '{
        "virtual_currency_balance":{
            "old_value":"0",
            "new_value":"0",
            "diff":"0"
        },
        "user":{
            "name":"Xsolla User",
            "id":"1234567",
            "email":"email@example.com"
        },
        "operation_type":"coupon",
        "notification_type":"user_balance_operation",
        "items_operation_type": "add",
             "items": [{
                 "sku": "1468",
                 "amount": "2"
             }],
        "id_operation":"66989",
        "coupon": {
             "coupon_code": "test123",
             "campaign_code": "Xsolla Campaign"
        }
    }'
ОТВЕТ
```

***PHP***
```PHP
ЗАПРОС
<?php

$request = array(
    'virtual_currency_balance' => array(
        'old_value' => '0',
        'new_value' => '0',
        'diff' => '0'
    ),
    'user' => array(
        'name' => 'Xsolla User',
        'id' => '1234567',
        'email' => 'email@example.com'
    ),
    'operation_type' => 'coupon',
    'notification_type' => 'user_balance_operation',
    'items_operation_type' =>  'add',
         'items' =>  array(
             'sku' =>  '1468',
             'amount' =>  '2'
         ),
    'id_operation' => '66989',
    'coupon' =>  array(
         'coupon_code' =>  'test123',
         'campaign_code' =>  'Xsolla Campaign'
    )
);
ОТВЕТ
<?php

use Xsolla\SDK\Webhook\WebhookServer;
use Xsolla\SDK\Webhook\Message\Message;
use Xsolla\SDK\Exception\Webhook\XsollaWebhookException;

$callback = function (Message $message) {
    if ($message instanceof UserBalanceMessage) {
       $messageArray = $message->toArray();
       // TODO if the user balance operation fails for some reason, you should throw XsollaWebhookException
    }
};

$webhookServer = WebhookServer::create($callback, PROJECT_KEY);
$webhookServer->start();
```

### Баланс пользователя: изменение вручную
Если необходимо изменить баланс пользователя вручную, можно использовать тип операции "Internal".

Поле | Тип | Описание
---- | --- | --------
notification_type|string|Тип оповещения.
operation_type|string|Тип операции.
id_operation|integer|ID операции в базе данных XSOLLA.
user|object|Объект с информацией о пользователе.
user.id|string|ID пользователя. Обязательный.
user.name|string|Имя пользователя.
user.email|string|Email пользователя.
virtual_currency_balance|object|Объект с данными о балансе пользователя.
virtual_currency_balance.old_value|string|Значение баланса до совершения данной операции.
virtual_currency_balance.new_value|string|Значение баланса после совершения данной операции.
virtual_currency_balance.diff|string|Количество виртуальной валюты в заказе.

Примеры изменения баланса:

***HTTP***
```HTTP
ЗАПРОС
POST /your/uri HTTP/1.1
Host: your.hostname
Accept: application/json
Content-Type: application/json
Content-Length: 240
Authorization: Signature 13342703ccaca5064ad33ba451d800c5e823db8f

{
    "virtual_currency_balance":{
        "old_value":"0",
        "new_value":"100",
        "diff":"100"
    },
    "user":{
        "name":"Xsolla User",
        "id":"1234567",
        "email":"email@example.com"
    },
    "operation_type":"internal",
    "notification_type":"user_balance_operation",
    "id_operation":"67002"
}
ОТВЕТ
HTTP/1.1 204 No Content
```

***CURL***
```CURL
ЗАПРОС
$ curl -v 'https://your.hostname/your/uri' \
-X POST \
-H 'Accept: application/json' \
-H 'Content-Type: application/json' \
-H 'Authorization: Signature 13342703ccaca5064ad33ba451d800c5e823db8f' \
-d '{
        "virtual_currency_balance":{
            "old_value":"0",
            "new_value":"100",
            "diff":"100"
        },
        "user":{
            "name":"Xsolla User",
            "id":"1234567",
            "email":"email@example.com"
        },
        "operation_type":"internal",
        "notification_type":"user_balance_operation",
        "id_operation":"67002"
    }'
ОТВЕТ
```

***PHP***
```PHP
<?php

$request = array(
    'virtual_currency_balance' => array(
        'old_value' => '0',
        'new_value' => '100',
        'diff' => '100'
    ),
    'user' => array(
        'name' => 'Xsolla User',
        'id' => '1234567',
        'email' => 'email@example.com'
    ),
    'operation_type' => 'internal',
    'notification_type' => 'user_balance_operation',
    'id_operation' => '67002'
);
```

### Баланс пользователя: отмена платежа
Когда пользователь отменяет платеж, XSOLLA присылает специальное оповещение об изменении баланса пользователя.

Поле | Тип | Описание
---- | --- | --------
notification_type|string|Тип оповещения.
operation_type|string|Тип операции.
id_operation|integer|ID операции в базе данных XSOLLA.
user|object|Объект с информацией о пользователе.
user.id|string|ID пользователя. Обязательный.
user.name|string|Имя пользователя.
user.email|string|Email пользователя.
virtual_currency_balance|object|Объект с данными о балансе пользователя.
virtual_currency_balance.old_value|string|Значение баланса до совершения данной операции.
virtual_currency_balance.new_value|string|Значение баланса после совершения данной операции.
virtual_currency_balance.diff|string|Количество виртуальной валюты в заказе.
transaction|object|Объект с информацией о транзакции, связанной с этой операцией. Обязательный.
transaction.id|integer|ID транзакции.
transaction.date|string|Дата транзакции.
items_operation_type|string|Тип операции с предметами.
items|array|Массив данных о предмете в заказе.
items.sku|string|ID предмета (артикул).
items.amount|integer|Количество этого предмета в заказе.

Примеры отмены платежа:

***HTTP***
```HTTP
POST /your/uri HTTP/1.1
Host: your.hostname
Accept: application/json
Content-Type: application/json
Content-Length: 240
Authorization: Signature 13342703ccaca5064ad33ba451d800c5e823db8f

{
    "virtual_currency_balance":{
        "old_value":"0",
        "new_value":"0",
        "diff":"0"
    },
    "user":{
        "name":"Xsolla User",
        "id":"1234567",
        "email":"email@example.com"
    },
    "transaction":{
        "id":"123456789",
        "date":"2015-05-19T15:54:40+03:00"
    },
    "operation_type":"cancellation",
    "notification_type":"user_balance_operation",
    "items_operation_type": "remove",
         "items": [{
             "sku": "1468",
             "amount": "2"
         }],
    "id_operation":"66989"
}
```

***CURL***
```CURL
$ curl -v 'https://your.hostname/your/uri' \
-X POST \
-H 'Accept: application/json' \
-H 'Content-Type: application/json' \
-H 'Authorization: Signature 13342703ccaca5064ad33ba451d800c5e823db8f' \
-d '{
        "virtual_currency_balance":{
            "old_value":"0",
            "new_value":"0",
            "diff":"0"
        },
        "user":{
            "name":"Xsolla User",
            "id":"1234567",
            "email":"email@example.com"
        },
        "transaction":{
            "id":"123456789",
            "date":"2015-05-19T15:54:40+03:00"
        },
        "operation_type":"cancellation",
        "notification_type":"user_balance_operation",
        "items_operation_type": "remove",
             "items": [{
                 "sku": "1468",
                 "amount": "2"
             }],
        "id_operation":"66989"
    }'
```

***PHP***
```PHP
<?php

$request = array(
     'virtual_currency_balance' => array(
         'old_value' => '0',
         'new_value' => '0',
         'diff' => '0'
     ),
     'user' => array(
         'name' => 'Xsolla User',
         'id' => '1234567',
         'email' => 'email@example.com'
     ),
     'transaction' => array(
         'id' => '123456789',
         'date' => '2015-05-19T15:54:40+03:00'
     ),
     'operation_type' => 'cancellation',
     'notification_type' => 'user_balance_operation',
     'items_operation_type' =>  'remove',
         'items' =>  array(
             'sku' =>  '1468',
             'amount' =>  '2'
         ),
     'id_operation' => '66989'
);
```

### Вторичный рынок: отправка списка предметов
Когда вторичный рынок запрашивает данные о предметах из инвентаря игры, XSOLLA присылает оповещение на webhook URL.

Поле | Тип | Описание
---- | --- | --------
notification_type|string|Тип оповещения.
project_id|integer|ID проекта.
payload|object|Объект с информацией о пользователе и вторичном рынке.
payload.user|object|Объект с информацией о пользователе.
payload.user.id|string|ID пользователя.
payload.secondary_market|object|Объект с информацией о вторичном рынке.
payload.secondary_market.id|string|ID вторичного рынка.

Примеры отправки списка предметов:

***HTTP***
```HTTP
POST /your_uri HTTP/1.1
Host: your.host
Content-Type: application/json
Authorization: Signature sha1(body + project_secret)

{
    "notification_type": "inventory_get",
    "project_id": 1024,
    "payload": {
         "user": {
             "id": "username"
              },
          "secondary_market": {
              "id": "1"
          }
     }  
}
```

***CURL***
```CURL
$ curl -v 'https://your.hostname/your/uri' \
-X POST \
-u merchant_id:merchant_api_key \
-H 'Content-Type: application/json' \
-d '{
    "notification_type": "inventory_get",
    "project_id": 1024,
    "payload": {
         "user": {
             "id": "username"
          },
          "secondary_market": {
              "id": "1"
          }
     }  
}
```

***PHP***
```PHP
<?php

$request = array(
 'notification_type' => 'inventory_get',
 'project_id' => 1024,
 'payload' => array(
   'user' => array(
     'id' => 'username'
   ),
   'secondary_market' => array(
     'id' => '1'
   ),
 ),
);
```

### Вторичный рынок: отправка предметов из игры
Когда вторичный рынок запрашивает предметы из инвентаря игры, XSOLLA присылает оповещение на webhook URL.


Поле | Тип | Описание
---- | --- | --------
notification_type|string|Тип оповещения.
project_id|integer|ID проекта.
payload|object|Объект с информацией о пользователе и вторичном рынке.
payload.user|object|Объект с информацией о пользователе.
payload.user.id|string|ID пользователя.
items|array|Массив с информацией о предметах.
items.sku|string|Артикул предмета.
items.instance_id|string|Уникальный ID предмета в игре.
payload.secondary_market|object|Объект с информацией о вторичном рынке.
payload.secondary_market.id|string|ID вторичного рынка.

Примеры отправки предметов:

***HTTP***
```HTTP
POST /your_uri HTTP/1.1
Host: your.host
Content-Type: application/json
Authorization: Signature sha1(body + project_secret)

{
    "notification_type": "inventory_pull",
    "project_id": 1024,
    "payload": {
        "user": {
            "id": "username"
        },
        "items": [
            {
                "sku": "sku1",
                "instance_id": "instance1"
            },
            {
                "sku": "sku2",
                "instance_id": "instance2"
            },
        ],
        "secondary_market": {
            "id": "1"
        }
    }
}
```

***CURL***
```CURL
$ curl -v 'https://your.hostname/your/uri' \
-X POST \
-u merchant_id:merchant_api_key \
-H 'Content-Type: application/json' \
-d '{
    "notification_type": "inventory_pull",
    "project_id": 1024,
    "payload": {
         "user": {
             "id": "username"
              },
         "items": [
            {
                "sku": "sku1",
                "instance_id": "instance1"
            },
            {
                "sku": "sku2",
                "instance_id": "instance2"
            }
          ],
          "secondary_market": {
              "id": "1"
          }
     }  
}
```

***PHP***
```PHP
<?php

$request = array(
  'notification_type' => 'inventory_pull',
  'project_id' => 1024,
  'payload' => array(
    'user' => array(
      'id' => 'username'
    ),
    'items' => array(
      array(
        'sku' => 'sku1',
        'instance_id' => 'instance1'
      ),
      array(
        'sku' => 'sku2',
        'instance_id' => 'instance2'
      ),
    )
    'secondary_market' => array(
      'id' => '1'
    ),
  ),
);
```

### Вторичный рынок: получение предметов в игру
Когда вторичный рынок отправляет предметы в инвентарь игры, XSOLLA присылает оповещение на webhook URL.

Поле | Тип | Описание
---- | --- | --------
notification_type|string|Тип оповещения.
project_id|integer|ID проекта.
payload|object|Объект с информацией о пользователе и вторичном рынке.
payload.user|object|Объект с информацией о пользователе.
payload.user.id|string|ID пользователя.
items|array|Массив с информацией о предметах.
items.sku|string|Артикул предмета.
items.instance_id|string|Уникальный ID предмета в игре.
payload.secondary_market|object|Объект с информацией о вторичном рынке.
payload.secondary_market.id|string|ID вторичного рынка.

Примеры получения предметов:

***HTTP***
```HTTP
POST /your_uri HTTP/1.1
Host: your.host
Content-Type: application/json
Authorization: Signature sha1(body + project_secret)

{
    "notification_type": "inventory_push",
    "project_id": 1024,
    "payload": {
        "user": {
            "id": "username"
        },
        "items": [
            {
                "sku": "sku1",
                "instance_id": "instance1"
            },
            {
                "sku": "sku2",
                "instance_id": "instance2"
            }
        ],
        "secondary_market": {
            "id": "1"
        }
    }
}
```

***CURL***
```CURL
$ curl -v 'https://your.hostname/your/uri' \
-X POST \
-u merchant_id:merchant_api_key \
-H 'Content-Type: application/json' \
-d '{
    "notification_type": "inventory_push",
    "project_id": 1024,
    "payload": {
         "user": {
             "id": "username"
              },
          "items": [
              {
                  "sku": "sku1",
                  "instance_id": "instance1"
              },
              {
                  "sku": "sku2",
                  "instance_id": "instance2"
              }
          ],
          "secondary_market": {
               "id": "1"
          }
     }  
}
```

***PHP***
```PHP
<?php

$request = array(
  'notification_type' => 'inventory_push',
  'project_id' => 1024,
  'payload' => array(
    'user' => array(
      'id' => 'username'
    )
    'items' => array(
      array(
        'sku' => 'sku1',
        'instance_id' => 'instance1'
      ),
      array(
        'sku' => 'sku2',
        'instance_id' => 'instance2'
      ),
    ),
    'secondary_market' => array(
      'id' => '1'
    ),
  ),
);
```

### Оповещения — ошибки
Коды ошибок:

Код | Описание
--- | --------
INVALID_USER|Неверный пользователь.
INVALID_PARAMETER|Неверный параметр.
INVALID_SIGNATURE|Невалидная подпись.
INCORRECT_AMOUNT|Некорректная сумма.
INCORRECT_INVOICE|Неверный заказ.

Пример ответа: 

```HTTP/1.1 400 Bad Request

{
    "error":{
        "code":"INVALID_USER",
        "message":"Invalid user"
    }
} 
```

