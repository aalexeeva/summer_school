# Начало работы с XSOLLA API 
## Введение
### Состав XSOLLA API
API Иксоллы включает в себя:
* [**Pay Station API**](https://developers.xsolla.com/ru/api/v2/pay-station/) — платежный интерфейс и методы токенизации;
* [**Store API**](https://developers.xsolla.com/ru/api/v2/store/) — методы для работы с такими модулями магазина, как *Виртуальная валюта*, *Виртуальные предметы*, *Подписки*, *Игровые ключи*, *Акции*, *Купоны*;
* [**Publisher Account API**](https://developers.xsolla.com/ru/api/v2/publisher-account/) — управление проектами и пользователями Личного кабинета, методы для работы с отчетами, тикетами поддержки;
* [**Login API**](https://developers.xsolla.com/ru/api/v2/login/) — методы аутентификации пользователей через собственный интерфейс (см. [руководство по интеграции](https://developers.xsolla.com/ru/doc/login/#guides_login_api_integration)).
### Основные сведения
XSOLLA API основано на принципах [REST-архитектуры](https://en.wikipedia.org/wiki/Representational_state_transfer). Для обозначения ошибок и URL, аналогичных структуре каталогов, используются HTTP коды ответов. Ответы API (включая ошибки) возвращаются в виде JSON.

В API используются встроенные особенности HTTP, такие как HTTP аутентификация и HTTP методы, которые понимаются всеми HTTP клиентами. Помимо этого поддерживается CORS (Cross-origin resource sharing) для обеспечения безопасной работы с API клиентского приложения.

XSOLLA API работает со следующими базовыми URL:
* `https://api.xsolla.com` — Pay Station API, Store API, Publisher Account API;
* `https://login.xsolla.com/api` — Login API.

> ***Примечание!*** 
> ~~~ 
> Многие ресурсы включают в себя параметр merchant_id, который указывает, 
> что приложение работает именно от вашего имени. 
> ~~~

## Запросы и ответы
Запросы к XSOLLA APi:
* отправляются по протоколу HTTP;
* используют [TLS](https://habr.com/ru/post/258285/) версии 1.2 и выше;
* должны содержать параметры аутентификации (см. раздел *Аутентификация*);
* для запросов типа *PUT* и *POST* должны сожержать дополнительный заголовок `Content-Type: application/json`.

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

В начале работы с API рекомендуется использовать его самую последнюю версию. 
Если не была указана версия команды, то по умолчанию будет вызвана первая версия.

> ***Предупреждение!*** 
> ~~~
> Целостность работы API гарантируется в рамках одной версии.
> ~~~

### Возможности, подлежащие изменениям
XSOLLA может добавлять следующие функциональные возможности API:
* новые ресурсы API;
* необязательные параметры запроса;
* новые свойства к существующим ответам API;
* новые значения для параметров, имеющих перечисление возможных значений;
* новые типы вебхуков и новые параметры в JSON;
* необязательные заголовки в HTTP-запросы.
Помимо этого XSOLLA может добавлять, изменять или удалять незадокументированные функциональные возможности в любое время.

Также API может:
* отклонять любой запрос, если его параметры принимают недопустимые значения;
* принимать ошибочно сформированные запросы из-за избыточно мягкого анализа синтаксиса, однако, если в дальнейшем проверка будет более строгой, подобные запросы могут быть отклонены.

> ***Предупреждение!*** 
> ~~~
> Ваш клиент должен продолжать работать независимо от этих изменений. 
> К примеру, новые параметры JSON, которые не распознаются вашим клиентом, не должны мешать его работе.
> ~~~

### Последние изменения для версии 2.0
Изменен URL команды: `https://api.xsolla.com/merchant/v2/merchants/{merchant_id}/reports/transactions/registry.{format}`.

Изменен формат ответа:
1. По отмененным транзакциям заполняются отрицательные значения для полей:

   **Payment system fee, fixed**; **Xsolla revenue share, fixed**; **Sales Tax**; **Virtual currency purchase amount**; **Simple checkout purchase amount**; **Items purchase amount**; **Repatriation costs, total**; **Fees covered, fixed**; **Fees covered by Balance**; **Payout amount from payment system**; **Payment amount from Balance**; **Pin codes purchase amount**.
1. Добавлены новые поля:

   **Payment system type** — тип способа оплаты; **VAT** — VAT, который видит пользователь; **VAT, %** — процент VAT в стране; **Refund reason comment** — комментарий к причине отмены транзакции; **Direct account** — указание на то, является ли аккаунт прямым или нет.
1. Удалены поля:

   **Payment system external fee (%)**; **Payment system external fee amount**; **Payment system external fee currency**.
1. Переименованы поля:

   **VAT** -> **Deducted VAT**; **VAT (%)** -> **Deducted VAT, %**.
1. Значения следующих полей не заполняются, если данные для них равны 0:

   **Virtual currency amount**; **Virtual currency purchase amount**; **Simple checkout purchase amount**; **Pin codes purchase amount**.
1. Изменения значений полей по умолчанию:

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
1. Создание: `POST` - создает и сохраняет сущность соответствующего типа.
1. Список: `GET` - возвращает список сущностей, удовлетворяющих запросу.
1. Получение: `GET` - возвращает данные по конкретному идентификатору, которые вы присылаете в запросе.
1. Замена: `PUT` - заменяет все поля для сущности, переданной в запросе.
1. Изменение: `PATCH` - изменяет только указанные поля существующего объекта, который соответствует идентификатору из запроса
1. Удаление: `DELETE` - удаляет существующий объект, который соответствует идентификатору из запроса.

## Формат даты
Все представления дат передаются в строках согласно ISO 8601. Дату можно передавать либо в формате UTC (например, 2013-01-15T00:00:00Z), либо в виде смещения от UTC для обозначения часового пояса (например, 2013-01-15T00:00:00-08:00 8 часов от UTC).

## Постраничная навигация
Результат запроса должен выводиться постранично. Это означает, что вместо вывода всех результатов выполнения запроса выводится только часть. Для этого необходимо использовать параметры **limit** и **offset**.

## Типы ошибок
XSOLLA использует стандартные HTTP коды для обозначения успешных или неуспешных запросов. В общем случае код в 2xx диапазоне обозначает успех, код диапазона 4хх означает ошибку в результате передачи некорректных параметров (например, обязательный параметр не передан, или авторизация не прошла и т.д.), код 5хх диапазона означает серверную ошибку.

Список поддерживаемых HTTP ответов:
* 200, 201, 204 — успешный ответ.
* 400 Bad Request — отсутствует обязательный параметр. Полное описание можно найти в теле ответа.
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
user.id|object|Объект с данными об ID пользователя. Обязательный.
user.id.value|string|ID пользователя.
user.name|object|Объект с информацией о нике пользователя.
user.name.value|string|Ник пользователя.
user.email|object|Объект с информацией об email пользователя.
user.email.value|string|Email пользователя. Должен быть валидным в соответствии с протоколом RFC 822.
user.phone|object|Объект с информацией о телефоне пользователя.
user.phone.value|string|Номер телефона пользователя.
user.country|object|Объект с информацией о стране пользователя.
user.country.value|string|Используется двухбуквенное обозначение страны согласно стандарту ISO 3166-1 alpha-2.
user.country.allow_modify|boolean|Может ли пользователь изменить страну на странице оплаты. 'false' по умолчанию.
user.attributes|object|Объект с данными об атрибутах пользователя, необходимых для фильтрации списка предметов. Параметры передаются в json хэше парами ключ-значение.
user.steam_id|object|Объект с данными о Steam ID пользователя.
user.steam_id.value|string|Steam ID пользователя.
user.tracking_id|object|Объект с данными о Tracking ID пользователя.
user.tracking_id.value|string|Уникальный Tracking ID (используется для проведения рекламных кампаний).
user.public_id.value|string|Параметр позволяет однозначно идентифицировать пользователя, а также, в отличие от user ID, известен пользователю (адрес электронной почты, никнейм, и т.д.). Параметр может использоваться при оплате покупки вне игрового магазина (например, кнопка игры в терминалах оплаты).
user.utm|object|Объект с данными о характеристиках трафика.
user.utm.utm_source|string|Источник трафика
user.utm.utm_medium|string|Канал трафика (контекстная реклама, медийная реклама, email-рассылка).
user.utm.utm_campaign|string|Название кампании. В данный параметр следует указывать транслитерированное или переведенное на английский язык название кампании.
user.utm.utm_term|string|Ключевое слово кампании. При использовании этого параметра в статистике будут собираться данные по тем ключевым словам, которые используются для таргетинга вашей рекламной кампании (а не по поисковым запросам). В Google Analytics содержимое метки utm_term попадает в единый отчет с поисковыми запросами.
user.utm.utm_content|string|Содержание кампании.
user.is_legal|boolean|Является ли пользователь юридическим лицом.
user.legal|object|Объект с реквизитами юридического лица. Объект и все его параметры являются обязательными, если в user.is_legal передано значение ‘true’.
user.legal.name|string|Полное юридическое наименование.
user.legal.address|string|Полный юридический адрес.
user.legal.vat_id|string|Индивидуальный идентификатор налогоплательщика.
user.legal.country|string|Страна регистрации. Используется двухбуквенное обозначение страны согласно стандарту ISO 3166-1 alpha-2.
settings|object|Объект, содержащий настройки проекта.
settings.external_id|string|Внешний ID транзакции.
settings.project_id|integer|ID игры в Иксолле. Вы можете найти этот параметр в Личном кабинете. Обязательный.
settings.language|string|Язык интерфейса. Используется двухбуквенное обозначение (в нижнем регистре) согласно стандарту ISO 639-1.
settings.return_url|string|Пользователь будет перенаправлен на данную страницу после совершения платежа. Параметры 'user_id', 'foreigninvoice', 'invoice_id' и 'status' будут автоматически добавлены к ссылке.
settings.currency|string|Предпочтительная валюта платежа. Используется трехбуквенное обозначение валюты согласно стандарту ISO 4217.
settings.mode|string|Передайте значение 'sandbox', чтобы провести тестовые платежи. Обратите внимание, что URL для страницы оплаты будет https://sandbox-secure.xsolla.com.
settings.payment_method|integer|ID способа оплаты.
settings.payment_widget|string|Виджет оплаты. Принимает значения 'paybycash' или 'giftcard'. При передаче этого параметра пользователь перенаправляется на виджет Pay with Cash или Gift Cards.
settings.ui|object|Объект с настройками интерфейса.
settings.ui.theme|string|Внешний вид интерфейса оплаты. Может принимать значения 'default' (по умолчанию) или 'default_dark'.
settings.ui.size|string|Размер платежного интерфейса. В зависимости от требуемых размеров платежного интерфейса, параметр может принимать следующие значения: <ul><li>small: Наименьший размер платежного интерфейса. Используется в случаях, когда размеры окна строго ограничены (размер: 620 x 630)</li><li>medium: Рекомендуемый размер платежного интерфейса. Оптимален при открытии в lightbox (размер: 740 x 760)</li><li>large: желательно открывать в новом окне/вкладке (размер: 820 x 840)</li></ul>
settings.ui.version|string|Тип устройства. Может принимать значения 'desktop' (по умолчанию) или 'mobile'.
settings.ui.desktop|object|Объект с данными настроек для desktop-версии.
settings.ui.desktop.header|object|Объект с настройками header.
settings.ui.desktop.header.is_visible|boolean|Должен ли header отображаться на странице оплаты.
settings.ui.desktop.header.visible_logo|boolean|Если значение 'true', то логотип будет отображаться в header (необходимо сначала прислать файл с логотипом вашему менеджеру).
settings.ui.desktop.header.visible_name|boolean|Должно ли название игры отображаться в header.
settings.ui.desktop.header.visible_purchase|boolean|Должно ли описание покупки (purchase.description.value) отображаться в header, по умолчанию ‘true’.
settings.ui.desktop.header.type|string|Внешний вид header. Может принимать значения 'compact' (название игры и ID пользователя не будут показываться в header) или 'normal'.
settings.ui.desktop.header.close_button|boolean|Показывать ли кнопку Закрыть в настольной версии платежного интерфейса. Нажатие на кнопку закрывает платежный интерфейс и перенаправляет пользователя на адрес, указанный в параметре "settings.return_url". 'false' по умолчанию.
settings.ui.desktop.subscription_list|object|Объект с данными настройки списка подписок.
settings.ui.desktop.subscription_list.layout|string|Шаблон списка. Принимает значения 'list' (по умолчанию) или 'grid'.
settings.ui.desktop.subscription_list.description|string|Здесь вы можете передать текст про подписки. Текст появится перед списком рекуррентных планов в интерфейсе оплаты.
settings.ui.desktop.subscription_list.display_local_price|boolean|Если значение 'true' и если локальная валюта пользователя отличается от базовой валюты плана, пользователь будет видеть две цены: цену в локальной валюте и цену в базовой валюте плана.
settings.ui.desktop.virtual_item_list|object|Объект с данными настройки списка предметов.
settings.ui.desktop.virtual_item_list.layout|string|Шаблон списка. Принимает значения 'list' (по умолчанию) или 'grid'.
settings.ui.desktop.virtual_item_list.button_with_price|boolean|Если значение 'true', то цена за предмет будет показана внутри кнопки. Если 'false', то цена будет слева от кнопки. 'false' по умолчанию.
settings.ui.desktop.virtual_item_list.view|string|Вывод списка групп виртуальных предметов либо в виде вертикального меню, либо над окном в виде горизонтального меню. Принимает значения 'horizontal_navigation' или 'vertical' (по умолчанию).
settings.ui.desktop.virtual_currency_list|object|Объект с данными настройки списка пакетов виртуальной валюты.
settings.ui.desktop.virtual_currency_list.description|string|Здесь вы можете передать текст про виртуальную валюту. Текст появится перед списком пакетов виртуальной валюты в интерфейсе оплаты.
settings.ui.desktop.virtual_currency_list.button_with_price|boolean|Если значение 'true', то цена за предмет будет показана внутри кнопки. Если 'false', то цена будет слева от кнопки. 'false' по умолчанию.
settings.ui.header.visible_virtual_currency_balance|boolean|Должен ли этот элемент быть видимым в интерфейсе оплаты. 'true' по умолчанию.
settings.ui.mobile.mode|string|Пользователь может совершить платеж только через сохраненные способы оплаты. Принимает значение 'saved_accounts'.
settings.ui.mobile.header.close_button|boolean|Показывать ли кнопку Закрыть в мобильной версии платежного интерфейса. Нажатие на кнопку закрывает платежный интерфейс и перенаправляет пользователя на адрес, указанный в параметре "settings.return_url". 'false' по умолчанию.
settings.ui.mobile.footer.is_visible|boolean|Скрывать или нет footer в мобильной версии платежного интерфейса.
settings.ui.license_url|string|Ссылка на лицензионное соглашение.
settings.ui.components|object|Объект с данными настройки пунктов меню.
settings.ui.components.virtual_items|object|Объект с данными настройки меню предметов.
settings.ui.components.virtual_items.order|integer|Место вкладки в меню.
settings.ui.components.virtual_items.hidden|boolean|Должна ли вкладка отображаться в меню.
settings.ui.components.virtual_items.selected_group|string|Группа, которая будет выбрана при открытии вкладки.
settings.ui.components.virtual_items.selected_item|string|Предмет, который будет выбран при открытии вкладки. Должен быть передан артикул предмета.
settings.ui.components.virtual_currency|object|Объект с данными настройки меню виртуальной валюты.
settings.ui.components.virtual_currency.custom_amount|boolean|Возможность ввода произвольного количества виртуальной валюты в интерфейсе оплаты.
settings.ui.components.virtual_currency.order|integer|Место вкладки в меню.
settings.ui.components.virtual_currency.hidden|boolean|Должна ли вкладка отображаться в меню.
settings.ui.components.subscriptions|object|Объект с данными настройки меню подписок.
settings.ui.components.subscriptions.order|integer|Место вкладки в меню.
settings.ui.components.subscriptions.hidden|boolean|Должна ли вкладка отображаться в меню.
settings.ui.mode|string|Платежный интерфейс в режиме Личного кабинета. Принимает значение 'user_account'. Header содержит только навигационное меню Личного кабинета; исключается возможность выбора предмета и оплата покупки; режим Личного кабинета доступен только в desktop-режиме.
settings.ui.user_account|object|Объект с данными об аккаунте пользователя.
settings.ui.user_account.info|object|Страница 'Мой аккаунт'.
settings.ui.user_account.info.order|integer|Место вкладки в меню.
settings.ui.user_account.info.enable|boolean|Должна ли вкладка отображаться в меню. 'false' по умолчанию.
settings.ui.user_account.history|object|Страница 'История' пользователя.
settings.ui.user_account.history.order|integer|Место вкладки в меню.
settings.ui.user_account.history.enable|boolean|Должна ли вкладка отображаться в меню. 'false' по умолчанию.
settings.ui.user_account.payment_accounts|object|Страница 'Мои платежные аккаунты'.
settings.ui.user_account.payment_accounts.order|integer|Место вкладки в меню.
settings.ui.user_account.payment_accounts.enable|boolean|Должна ли вкладка отображаться в меню. 'false' по умолчанию.
settings.ui.user_account.subscriptions|object|Страница 'Управление подписками'.
settings.ui.user_account.subscriptions.order|integer|Место вкладки в меню.
settings.ui.user_account.subscriptions.enable|boolean|Должна ли вкладка отображаться в меню. 'false' по умолчанию.
settings.shipping_enabled|boolean|Отображение страницы ввода адреса заставки. 'false' по умолчанию.
purchase|object|Объект с информацией о заказе.
purchase.virtual_currency|object|Объект с данными о виртуальной валюте.
purchase.virtual_currency.quantity|float|Количество виртуальной валюты в заказе.
purchase.virtual_currency.currency|string|Валюта пакета виртуальной валюты, на основе которой будут сделаны все расчеты.
purchase.virtual_items|object|Объект с данными о предметах.
purchase.virtual_items.currency|string|Валюта предмета в заказе, на основе которой будут сделаны все расчеты.
purchase.virtual_items.items|array|Массив с данными о предмете.
purchase.virtual_items.items.sku|string|ID предмета (артикул).
purchase.virtual_items.items.amount|integer|Количество этого предмета в заказе.
purchase.virtual_items.available_groups|array|Массив с данными о группах предметов. Только предметы из указанных групп будут показаны в интерфейсе оплаты.
purchase.subscription|object|Объект с данными о подписке.
purchase.subscription.plan_id|string|ID рекуррентного плана.
purchase.subscription.operation|string|Тип операции, применяемой к плану подписки пользователя. Чтобы изменить план подписки, передайте в параметре значение ‘change_plan’. В параметре purchase.subscription.plan_id необходимо передать ID нового плана подписки.
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
purchase.gift.hide_giver_from_receiver|string|Показывать ли данные дарителя получателю подарка. 'true' по умолчанию.
purchase.gift.friends|array|Массив с данными о друзьях.
purchase.gift.friends.id|string|ID получателя подарка.
purchase.gift.friends.name|string|Никнейм получателя подарка.
purchase.gift.friends.email|string|Email получателя подарка.
purchase.coupon_code|object|Объект с информацией о промокоде на скидку или бонусы при покупке.
purchase.coupon_code.value|string|Значение промокода.
purchase.coupon_code.hidden|boolean|Скрыть поле для ввода промокода в интерфейсе оплаты. 'false' по умолчанию.
custom_parameters|object|Ваши дополнительные параметры. Параметры передаются в json хеше парами ключ-значение.

Если какой-либо параметр был передан в некорректном формате, токен не может быть выдан. Будет возвращен 422 HTTP код, в JSON объекте в теле ответа будет содержаться информация об ошибке. В параметре "extended_message" указывается, какие именно параметры были переданы неверно.

Пример:
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
