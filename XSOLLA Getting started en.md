# Getting started with XSOLLA API 
## Introduction
### The XSOLLA API composition 
The XSOLLA API includes:
* [**Pay Station API**](https://developers.xsolla.com/ru/api/v2/pay-station/) — payment interface and tokenization methods.
* [**Store API**](https://developers.xsolla.com/ru/api/v2/store/) — methods for working with such store modules as *Virtual currency*, *Virtual items*, *Subscriptions*, *Game keys*, *Promotions*, *Coupons*.
* [**Publisher Account API**](https://developers.xsolla.com/ru/api/v2/publisher-account/) — management of projects and Personal account users, methods for working with reports, support tickets.
* [**API Login**](https://developers.xsolla.com/ru/api/v2/login/) — methods of authenticating users through a native interface. More information can be found in the [integration guide](https://developers.xsolla.com/ru/doc/login/#guides_login_api_integration).

### Basic information
The XSOLLA API is based on the principles of [REST-architecture](https://en.wikipedia.org/wiki/Representational_state_transfer). HTTP response codes are used to indicate errors and URLs similar to the directory structure. API responses and error information are returned as JSON.

The API uses built-in HTTP features, such as HTTP authentication and HTTP methods, which are understood by all HTTP clients. In addition, CORS (Cross-origin resource sharing) is supported to provide secure work with the API of the client application.

The XSOLLA API works with the following base URLs:

* `https://api.xsolla.com` — Pay Station API, Store API, Publisher Account API;
* `https://login.xsolla.com/api` — Login API.

> ***Note!*** 
> ~~~ 
> Many resources include a merchant_id parameter that indicates, 
> that the app works on your behalf. 
> ~~~

## Requests and responses
Requests to XSOLLA API:
* Sent via the HTTP Protocol.
* Use [TLS](https://habr.com/ru/post/258285/) version 1.2 and above.
* Must contain the authentication parameters (see section [Authentication](#Authentication)).
* For requests of type *PUT* and *POST* must contain additional header `Content-Type: application/json`.

Example:
```
Authorization: Basic <your_authorization_basic_key>
Content-Type: application/json
```
> ***Warning!*** 
> ~~~
> By default, all requests return a response as a JSON with `Content-Type: application/json` header.
> ~~~

## Changes in the XSOLLA API
### Versioning
All sections of the XSOLLA API to support versioning. A new version is released whenever there are changes that are incompatible with the current version. 

The version is indicated by an identifier ("v1"/"v2", etc.), which is specified in the URL after the prefix `/merchant`.

We recommend to use the latest version to get started with the API. 
If the version is not specified, the first version will be called by default.

> ***Warning!*** 
> ~~~
> The integrity of the API is guaranteed within a single version.
> ~~~

### Features to be changed
XSOLLA can add the following API functionality:
* New API resources.
* Optional query parameters.
* New properties to existing API responses.
* New values for parameters that have an enumeration of possible values.
* New types of webhooks and new parameters in JSON.
* Optional headers in HTTP requests.

XSOLLA may also add, modify, or remove undocumented functionality at any time.

While the API is running, it can:
* Reject any request if its parameters are invalid.
* Accept malformed queries due to excessively soft parsing. However, if further verification is more stringent, such requests may be rejected.

> ***Warning!*** 
> ~~~
> Your customer must continue to operate independently of the above changes. 
> For example, new JSON parameters that are not recognized by your client should not interfere with its operation.
> ~~~

### Latest changes for version 2.0
Changed the URL command: `https://api.xsolla.com/merchant/v2/merchants/{merchant_id}/reports/transactions/registry.{format}`.

Changed the format of the response:
* Negative values for fields are filled in for cancelled transactions:

   **Payment system fee, fixed**; **Xsolla revenue share, fixed**; **Sales Tax**; **Virtual currency purchase amount**; **Simple checkout purchase amount**; **Items purchase amount**; **Repatriation costs, total**; **Fees covered, fixed**; **Fees covered by Balance**; **Payout amount from payment system**; **Payment amount from Balance**; **Pin codes purchase amount**.

* Added new fields:

   **Payment system type** — type of payment method; **VAT** — VAT that the user sees; **VAT, %** — VAT percentage in the country; **Refund reason comment** — comment to the reason for transaction cancellation; **Direct account** — an indication of whether the account is direct or not.

* Removed fields:

   **Payment system external fee (%)**; **Payment system external fee amount**; **Payment system external fee currency**.
* Renamed fields:

   **VAT** -> **Deducted VAT**; **VAT ( % ) * * - > * * Deducted VAT, %**.
* The values of the following fields are not populated if the data for them is 0:

   **Virtual currency amount**; **Virtual currency purchase amount**; **Simple checkout purchase amount**; **Pin codes purchase amount**.

* Change default field values:

   * Field value **Virtual currency currency purchase** not filled if **Virtual currency purchase amount** equal to 0.

   * The value of the **Simple checkout purchase currency** field is not populated if **Simple checkout purchase amount** is 0.

   * The value of the **Pin codes purchase currency** field is not filled in if **Pin codes purchase amount** is 0. The **Pin codes purchase cart content** field remains empty.

   * The value of the **Deducted VAT, %** field is filled in with 0 if the value of the **Deducted VAT** field is 0.

## Authentication
The XSOLLA API uses basic HTTP authentication. 
All API requests must include the `Authorization ' header: Basic <your_authorization_basic_key>`, where **<your_authorization_basic_key>** — pair **merchant_id:api_key** encoded according to Base64 standard.

Parameter values **merchant_id** and **api_key** can be found in [XSOLLA Personal account](https://publisher.xsolla.com/) in the following way:
* **merchant_id**: located in the *Settings* > *Company*, in the *seller ID*;
* **api_key**: located in *company Settings* in the *API Key*field.

> ***Warning!*** 
> ~~~
> Do not share your API key with anyone, as it gives access to account and project management in your Personal account.
> ~~~

Examples:


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

## Resource management commands
All XSOLLA API commands point to the type of data to be processed and the action to be taken with that data. Standard action list:
* Creation: `POST` - creates and stores an entity of the corresponding type.
* List: `GET` - returns a list of entities that meet the request.
* Obtaining: `GET` - returns data on a specific identifier that was send in the request.
* Replacement: `PUT` - replaces all fields for the entity passed in the request.
* Change: `PATCH` - changes only the specified fields of an existing object that matches the ID from the query
* Removal: `DELETE` - deletes an existing object that corresponds to the identifier from the query.

## Date format
All date representations are passed in strings according to ISO 8601. The date can be passed either in UTC (for example, 2013-01-15T00:00:00Z) or as an offset from UTC to indicate the time zone (for example, 2013-01-15T00:00:00-08:00 8 hours from UTC).

## Page navigation
The result of the query should be displayed page by page. This means that only part of the query is displayed instead of all the query results. To do this, use the **limit** and **offset** parameters.

## Type of error
XSOLLA uses standard HTTP codes to indicate successful or unsuccessful requests. In General, the code in the 2xx range indicates success, the code in the 4xx range indicates an error as a result of incorrect parameters (for example, a mandatory parameter is not passed, or authorization has not passed, etc.), the code in the 5xx range means a server error.

List of supported HTTP responses:
* 200, 201, 204 — successful response.
* 400 Bad Request — missing required parameter, full description can be found in the response body.
* 401 Unauthorized — invalid API key.
* 402 Request Failed — the parameters are correct, but the request failed.
* 403 Forbidden — no permissions, full description can be found in the response body.
* 404 Not Found — there is no corresponding resource for this URI.
* 409, 422 — request parameters not valid.
* 412 Precondition Failed — an error occurs when the project is not activated (used in the token acquisition method).
* 415 Unsupported Media Type — *Content-Type: application/json HTTP* header was not sent.
* 500, 502, 503, 504 Server Errors — server error.

Not all errors correspond exactly to HTTP codes. For example, if the request was correct but could not complete successfully, error 422 will be returned.

In case of an error response, a JSON object with the following fields is returned:

Field | Type | Description
---- | --- | --------
http_status_code | integer | HTTP code.
message | string | Clear error message. The text is always in English. The field should not be used in case of any error, as its value may change in the future.
extended_message | string | Extended error description.
request_id | string | Unique request ID, used to help XSOLLA developers diagnose the problem.

Example of JSON object with error:
```
{
    "http_status_code": 500,
    "message": "Internal Server Error",
    "extended_message": null,
    "request_id": "6445b85"
}
```

## Token
To ensure the payment security, the XSOLLA API uses a token that contains payment parameters inside itself, instead of directly receiving data via HTTP GET request to the payment page. Before opening the payment page, is needed to get a new token. Token lifetime is 24 hours.

### Get a token
The token can be created with any user settings. Upon token receipt, is needed to send these parameters to the API developers, who will send them back after successful payment. The token can contain only the parameters described below, or the parameters predefined by the partner.

**HTTP REQUEST**
`` POST https://api.xsolla.com/merchant/v2/merchants/{merchant_id}/token``

Field | Type | Description
---- | --- | --------
user |object|Object with user information.
user.id|object|Mandatory object with data about the user ID.
user.id.value|string|User ID.
user.name|object|Object with information about the user name.
user.name.value|string|User nickname.
user.email|object|Object with information about the user email.
user.email.value|string|User email. Must be valid under RFC 822.
user.phone|object|Object with information about the user phone.
user.phone.value|string|User phone number.
user.country|object|Object with information about the user country.
user.country.value|string|Two-letter country designation according to [ISO 3166-1 alpha-2](https://wikipedia.org/wiki/ISO_3166-1_alpha-2).
user.country.allow_modify|boolean|Allow to change country on payment page. Default is 'false'.
user.attributes|object|Object with user attribute data needed to filter the list of items. Parameters are passed to the JSON hash in key-value pairs.
user.steam_id|object|Object with user Steam ID data.
user.steam_id.value|string|User Steam ID.
user.tracking_id|object|Object with the user Tracking ID data.
user.tracking_id.value|string|Unique Tracking ID (used for advertising campaigns).
user.public_id.value|string|Parameter that uniquely identifies the user. Unlike user ID, it is known to the user (email address, nickname, etc.). The parameter can be used when paying for a purchase outside the game store (for example, the game button in the payment terminals).
user.utm|object|Object with data about the traffic characteristics.
user.utm.utm_source|string|Traffic source.
user.utm.utm_medium|string|Traffic channel (contextual advertising, display advertising, email-mailing).
user.utm.utm_campaign|string|Campaign name (transliterated or translated into English).
user.utm.utm_term|string|Campaign keyword. When using this option, the statistics data will be collected on those keywords, which are used for targeting advertising campaigns (no search queries). In Google Analytics, the content of the utm_term label falls into a single report with search queries.
user.utm.utm_content|string|The contents of the campaign.
user.is_legal|boolean|Whether the user is a legal entity.
user.legal|object|Object with the legal entity details. The object and all its parameters are required if the value ‘true’ is passed to user.is_legal.
user.legal.name|string|Full legal name.
user.legal.address|string|Full legal address.
user.legal.vat_id|string|Individual taxpayer ID.
user.legal.country|string|Two-letter country designation according to [ISO 3166-1 alpha-2](https://wikipedia.org/wiki/ISO_3166-1_alpha-2).
settings|object|Object containing project settings.
settings.external_id|string|External transaction ID.
settings.project_id|integer|Game ID in the XSOLLA. Is present in user account. Mandatory.
settings.language|string|Preferred interface language. Two-letter designation (lowercase) according to ISO 639-1 is used.
settings.return_url|string|URL of the page to which the user will be redirected after making a payment. The parameters 'user_id', 'foreigninvoice', 'invoice_id' and 'status' will be automatically added to the link.
settings.currency|string|Preferred payment currency. The three-letter currency designation according to ISO 4217 is used.
settings.mode|string|Attribute for making test payments to which the value of the 'sandbox' is to be transferred. When the payment is for test, the URL for the payment page will be <https://sandbox-secure.xsolla.com>.
settings.payment_method|integer|Payment method ID.
settings.payment_widget|string|Payment widget. Takes the values 'paybycash' or 'giftcard'. When this parameter is passed, the user is redirected to the 'Pay with Cash' or 'Gift Cards' widget, respectively.
settings.ui|object|Object with interface settings.
settings.ui.theme|string|The appearance of the interface charge. Can take values 'default' (default) or 'default_dark'.
settings.ui.size|string|Payment interface size. Depending on the required payment interface size, the parameter can take the following values: <ul><li>'small': the smallest payment interface size. Used in cases where the window size is strictly limited (size: 620 x 630).</li><li>'medium': recommended payment interface size is optimal when opened in lightbox (size: 740 x 760).</li><li>'large': it is desirable to open in a new window/tab (size: 820 x 840).</li></ul>
settings.ui.version|string|The type of the device. It can be either 'desktop' (default) or 'mobile'.
settings.ui.desktop|object|Object with settings data for the desktop version.
settings.ui.desktop.header|object|Object with header settings.
settings.ui.desktop.header.is_visible|boolean|Visibility of header. Indicates whether the header should be displayed on the payment page.
settings.ui.desktop.header.visible_logo|boolean|Logo visibility. If value is 'true', the logo will be displayed in the header (firstly send the file with the logo to  Manager).
settings.ui.desktop.header.visible_name|boolean|Game name visibility. Whether the game name should be displayed in the header.
settings.ui.desktop.header.visible_purchase|boolean|Visibility of the description of purchase (purchase.description.value) to be displayed in the header, default is ‘true’.
settings.ui.desktop.header.type|string|Appearance header. It can take the values 'compact' (the game name and user ID will not be shown in the header) or 'normal'.
settings.ui.desktop.header.close_button|boolean|Close button visibility in the desktop version of the payment interface. Clicking on the button closes the payment interface and redirects the user to the address specified in the "settings" parameter.return_url". Default is 'false'.
settings.ui.desktop.subscription_list|object|Object containing data to configure a list of subscriptions.
settings.ui.desktop.subscription_list.layout|string|List template. Takes the values 'list' (default) or 'grid'.
settings.ui.desktop.subscription_list.description|string|Description of the subscription list that appears before the list of recurrent plans in the payment interface.
settings.ui.desktop.subscription_list.display_local_price|boolean|If value is 'true' and if the user local currency is different from the plan base currency, the user will see two prices: the price in local currency and the price in the base plan currency.
settings.ui.desktop.virtual_item_list|object|Object containing data to configure a list of items.
settings.ui.desktop.virtual_item_list.layout|string|List template. Takes the values 'list' (default) or 'grid'.
settings.ui.desktop.virtual_item_list.button_with_price|boolean|If value is 'true', the item price will be shown inside the button. If 'false', the price will be located to the left of the button. Default is 'false'.
settings.ui.desktop.virtual_item_list.view|string|Displays a list of virtual item groups either as a vertical menu or as a horizontal menu above the window. Takes the values 'horizontal_navigation' or 'vertical' (default).
settings.ui.desktop.virtual_currency_list|object|Object containing data to configure a list of packages of virtual currency.
settings.ui.desktop.virtual_currency_list.description|string|Virtual currency description text. It appears before the list of virtual currency packages in the payment interface.
settings.ui.desktop.virtual_currency_list.button_with_price|boolean|If value is 'true', the item price will be shown inside the button. If it 'false', the price will be located to the left of the button. Default is 'false'.
settings.ui.header.visible_virtual_currency_balance|boolean|Header visibility in the payment interface. Default is 'true'.
settings.ui.mobile.mode|string|User can only make a payment using stored payment methods. Takes the value 'saved_accounts'.
settings.ui.mobile.header.close_button|boolean|"Close" button visibility in the mobile version of the payment interface. Clicking on the button closes the payment interface and redirects the user to the address specified in the "settings" parameter.return_url". Default is 'false'.
settings.ui.mobile.footer.is_visible|boolean|Footer visibility in the mobile version of the payment interface.
settings.ui.license_url|string|Link to license agreement.
settings.ui.components|object|Object with menu item settings data.
settings.ui.components.virtual_items|object|Object with item menu setting data.
settings.ui.components.virtual_items.order|integer|Tab's place in the menu.
settings.ui.components.virtual_items.hidden|boolean|Tab visibility in the menu.
settings.ui.components.virtual_items.selected_group|string|The group that will be selected when the tab is opened.
settings.ui.components.virtual_items.selected_item|string|Item to be selected when the tab is opened. The item number must be transferred.
settings.ui.components.virtual_currency|object|Object with the data settings menu for the virtual currency.
settings.ui.components.virtual_currency.custom_amount|boolean|Ability to enter an arbitrary amount of virtual currency in the payment interface.
settings.ui.components.virtual_currency.order|integer|Tab's place in the menu.
settings.ui.components.virtual_currency.hidden|boolean|Tab visibility in the menu.
settings.ui.components.subscriptions|object|Object with subscription menu settings data.
settings.ui.components.subscriptions.order|integer|Tab's place in the menu.
settings.ui.components.subscriptions.hidden|boolean|Tab visibility in the menu.
settings.ui.mode|string|Payment interface in the personal account mode. Takes the value 'user_account'. Header contains only the navigation menu of the Personal account; excludes the possibility of selecting an item and paying for the purchase; personal account mode is available only in desktop-mode.
settings.ui.user_account|object|Object with user account data.
settings.ui.user_account.info|object|'My Account' page.
settings.ui.user_account.info.order|integer|Tab's place in the menu.
settings.ui.user_account.info.enable|boolean|Visibility tab in the menu. Default is 'false'.
settings.ui.user_account.history|object|User History page.
settings.ui.user_account.history.order|integer|Tab's place in the menu.
settings.ui.user_account.history.enable|boolean|Tab visibility in the menu. Default is 'false'.
settings.ui.user_account.payment_accounts|object|'My payment accounts' page.
settings.ui.user_account.payment_accounts.order|integer|Tab's place in the menu.
settings.ui.user_account.payment_accounts.enable|boolean|Tab visibility in the menu. Default is 'false'.
settings.ui.user_account.subscriptions|object|Subscription Management page.
settings.ui.user_account.subscriptions.order|integer|Tab's place in the menu.
settings.ui.user_account.subscriptions.enable|boolean|Tab visibility in the menu. Default is 'false'.
settings.shipping_enabled|boolean|Display the page enter the address of the saver. Default is 'false'.
purchase|object|Object with order information.
purchase.virtual_currency|object|Object with virtual currency data.
purchase.virtual_currency.quantity|float|Amount of virtual currency in the order.
purchase.virtual_currency.currency|string|Currency of the virtual currency package, on the basis of which all calculations will be made.
purchase.virtual_items|object|Object with object data.
purchase.virtual_items.currency|string|Currency of the item in the order, on the basis of which all calculations will be made.
purchase.virtual_items.items|array|Array with item data.
purchase.virtual_items.items.sku|string|Item ID (vendor code).
purchase.virtual_items.items.amount|integer|Number of item copies  in the order.
purchase.virtual_items.available_groups|array|Array with data about groups of subjects. Only items from the specified groups will be shown in the payment interface.
purchase.subscription|object|Object with subscription data.
purchase.subscription.plan_id|string|Recurrent plan ID.
purchase.subscription.operation|string|Type of operation to apply to the user's subscription plan. To change the subscription plan, is needed to pass the ‘change_plan’value in this parameter. In the purchase parameter.subscription.plan_id is needed to pass the ID of the new subscription plan.
purchase.subscription.product_id|string|Recurrent product ID.
purchase.subscription.currency|string|Recurrent plan currency in the order, on the basis of which all calculations will be made.
purchase.subscription.available_plans|array|Array with data about subscription plans. Only plans from this list will be shown in the payment interface.
purchase.subscription.trial_days|integer|Number of days in the trial period.
purchase.pin_codes|object|Object with key data.
purchase.pin_codes.currency|string|Key currency in the order, on the basis of which all calculations will be made.
purchase.pin_codes.codes|array|Array with key data.
purchase.pin_codes.codes.digital_content|string|Game vendor code, configured in Personal account.
purchase.pin_codes.codes.drm|string|DRM-platform on which the game will be available. Can take values 'steam', 'playstation', 'xbox', 'uplay', 'origin', 'drmfree', 'gog', 'epicgames', 'nintendo_eshop', 'discord_game_store' or 'oculus'. Please make sure that the required DRM-platform is configured in Personal account. If the value is not passed in the token, the platform specified by the user in the payment interface will be selected.
purchase.pin_codes.upgrade|object|Object with upgrade information.
purchase.pin_codes.upgrade.id_user_history|integer|Record ID containing data about the user and the packages he/she owns.
purchase.pin_codes.upgrade.id|integer|Upgrade ID.
purchase.checkout|object|Object with order information.
purchase.checkout.currency|string|Order currency. The three-letter currency designation according to ISO 4217 is used.
purchase.checkout.amount|float|Order amount.
purchase.description|object|Object with an item description.
purchase.description.value|string|Item description. It will be displayed in the payment interface and email receipt.
purchase.gift|object|Object with information about the gift.
purchase.gift.giver_id|string|Gift giver ID.
purchase.gift.message|string|Message from the giver.
purchase.gift.hide_giver_from_receiver|string|Data visibility of the gift giver to the gift recipient. Default is 'true'.
purchase.gift.friends|array|Array with friends data.
purchase.gift.friends.id|string|Gift recipient ID .
purchase.gift.friends.name|string|Gift recipient nickname.
purchase.gift.friends.email|string|Gift recipient email.
purchase.coupon_code|object|Object with information about the promotional code for a discount or bonuses when buying.
purchase.coupon_code.value|string|Promo code value.
purchase.coupon_code.hidden|boolean|Field for entering the promo code visibility in the payment interface. Default is 'false'.
custom_parameters|object|Additional parameters sent in the JSON hash of pairs key-value.

If any parameter was passed in an incorrect format, the token cannot be issued. 422 HTTP code will be returned, the JSON object in the body of the response will contain information about the error. The "extended_message" parameter specifies which parameters were passed incorrectly.

Example of JSON object with error:
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
Sample requests and responses:


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

### List of additional parameters

In the token in the custom_parameters object,  the additional parameters can be passed. It can be used to configure antifraud filters. Recommended parameters are given in the table below, if necessary, this list can be expanded. [More information](https://developers.xsolla.com/ru/recipes/general/antifraud/).

Field | Type | Description
---- | --- | --------
registration_date|string|Account registration date according to [ISO 8601](https://en.wikipedia.org/wiki/ISO_8601).
total_hours|integer|Total number of hours spent in the game.
total_characters|integer|Number of player characters.
social_networks_added|boolean|Presence of player profiles in social networks.
profile_image_added|boolean|Presence profile image.
active_date|string|Date of last visit according to [ISO 8601](https://en.wikipedia.org/wiki/ISO_8601).
total_friends|integer|Number of player's friends.
additional_verification|boolean|Additional ways to protect Personal account.
win_rate|integer|Rating players of victories.
last_change_password_date|string|Date of last password change according to [ISO 8601](https://en.wikipedia.org/wiki/ISO_8601).
chat_activity|boolean|Presence of chat entries.
forum_activity|boolean|Presence of entries in the forum.
total_bans|integer|Number of player bans in chat/forum.
profile_completed|boolean|Availability of additional information in the profile.
notifications_enabled|boolean|Availability subscribe to notifications.
user_level|integer|Player level, reputation or rank.
karma_points|integer|Player's karma.
total_sum|float|Total amount of payments.
non_premium_currency|float|Player's non premium currency sum.
total_game_events|integer|Number of in-game events the player has participated in.
total_gifts|integer|Number of gifts sent or received by the player.
tutorial_completed|boolean|Indicator of completion of training in the game.
completed_tasks|integer|Number of completed tasks.
items_used|boolean|Indicator use purchased in-game items.
pvp_activity|boolean|Player participation Rate in PvP.
total_clans|integer|Number of clans the player is a member of.
unlocked_achievements|integer|Number of unlocked skills.
total_inventory_value|float|Total inventory value in-game currency.
character_customized|boolean|Character customization score.
session_time|string|Period of time the user spends in the game according to [ISO 8601](https://en.wikipedia.org/wiki/ISO_8601).

## Notifications
### Introduction
For event notifications that occur with the transaction in XSOLLA used **webhook**. Alerts can be used to automate back office and functions such as status display.

Webhook is a service for notification of events such as:
* Making payments, including purchase of virtual currency, purchase of items, etc.
* Recurring payments and subscription activities.
* Chargeback/refund on transaction.

In most cases, the action that the webhook calls is the result of a user action on the payment page. However, other actions can also call webhook.

For example, a payment can be cancelled via the XSOLLA API or a payment system will notify developers of a disputed transaction.

Examples of the webhook processing:
* Deposit balance of the user.
* Providing new items to the user.
* Start of subscription.
* Blocking the user in case of suspicion of fraud.

Alerts must be received from the following IP addresses: `185.30.20.0/24`, `185.30.21.0/24`.

There should not be two successful transactions with the same ID in the database. If the system sent a webhook with an ID that is already in the database, it is necessary to return the result of the previous processing of this transaction.  The purchase should not be counted to the user again, and there should be no duplicate records in the database.

XSOLLA does not guarantee that the processing script will receive all alerts. Because the Internet is not 100% reliable, the webhook can be lost or delayed. To resolve these issues, the alert service includes a re-alert mechanism that sends an alert until the script confirms that the alert has been received. The Webhook can be resubmitted within 12 hours of the first attempt. The maximum number of attempts is 12.

> ***Note!*** 
> ~~~
> Although the Internet is a common cause of problems with webhook, the most likely cause is most often a problem in the logic of the processing script.
> ~~~

### Signature of the request and response
The signature ensures the security of data transmission. 
Signature generation consists of two steps:
1. The concatenation of the JSON body from the request and a project secret key. 
1. Applying the SHA-1 cryptographic hash function to the resulting string in the first step.

The generated signature should be verify matches the signature sent in the HTTP header.

Sample request signatures:


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

XSOLLA uses HTTP response codes to indicate a successful or failed request. Code 204 indicates successful processing of the alert. If an error occurs, is needed to return the code 400 (for example, there is no mandatory parameter, or the balance can not be replenished). The code 500 indicates a temporary server error.

### Announcement type
The alert type is passed in the parameter 'notification_type'.

Field | Description
---- | --------
user_validation|Check the user existence in the game.
user_search|Get user information by public user ID parameter.
payment|Notification of successful payment.
refund|Notification about the payment cancellation.
afs_reject|Notification of cancellation of transaction AFS system.
create_subscription|Notification about the subscription creation.
update_subscription|Notification of subscription renewal or change of any parameters within the subscription.
cancel_subscription|Notification of subscription cancellation.
get_pincode|Request for key retrieval.
user_balance_operation|Notification about change of user balance (operation type is sent in the alert operation_type).
redeem_key|Alert activation key.
upgrade_refund|Notification about the upgrade cancellation.
inventory_get|Send a list of items from the game inventory to the secondary market.
inventory_pull|Sending items from the game inventory to the secondary market.
inventory_push|Getting items in the game inventory from the secondary market.

### Check that the user exists
The XSOLLA server sends a request to the webhook URL to verify that the user exists in the game. 

Field | Type | Description
---- | --- | --------
user|object|Object with user information.
user.ip|string|User IP address.
user.phone|string|User phone number (in international format).
user.email|string|User email.
user.id|string|User ID. Mandatory.
user.name|string|User name.
user.country|string|Two-letter country designation according to [ISO 3166-1 alpha-2](https://wikipedia.org/wiki/ISO_3166-1_alpha-2).

Examples of verification:


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

### User search
Public User ID — a parameter that can uniquely identify the user and which is well known to the user in contrast to the User ID (as Public User ID can be email, nickname, etc.). This webhook is used when it is possible to make a payment outside the game (for example, when paying for the game button in the terminal).

Field | Type | Description
---- | --- | --------
notification_type|string|Alert type. Mandatory.
user|object|Object with user information. Mandatory.
user.public_id|string|Public user ID.
user.id|string|User ID.

User search examples: 


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

### Successful payment
When the user successfully makes the payment, XSOLLA sends the payment details to the webhook URL.

Field | Type | Description
---- | --- | --------
notification_type|string|Alert type. Mandatory.
purchase|object|Object with order information.
purchase.virtual_currency|object|Object with data about the purchased virtual currency.
purchase.virtual_currency.name|string|Virtual currency name.
purchase.virtual_currency.sku|string|Vendor code of a package of virtual currency (if specified for a package of virtual currency).
purchase.virtual_currency.quantity|float|Quantity of bonus items.
purchase.virtual_currency.currency|string|Three-letter designation of the virtual currency of the order according to [ISO 4217](https://wikipedia.org/wiki/ISO_4217).
purchase.virtual_currency.amount|float|Purchase amount.
purchase.checkout|object|Object with order information.
purchase.checkout.currency|string|Three-letter currency symbol according to [ISO 4217](https://wikipedia.org/wiki/ISO_4217) standard.
purchase.checkout.amount|float|Order amount.
purchase.subscription|object|Object with subscription data.
purchase.subscription.plan_id|string|Plan ID (external ID if the plan was created through the API).
purchase.subscription.subscription_id|integer|Subscription ID in the XSOLLA database.
purchase.subscription.product_id|string|Product ID (if sent in token).
purchase.subscription.tags|array|Plan tags.
purchase.subscription.date_create|string|Date and time when the subscription was created according to the [ISO 8601](https://en.wikipedia.org/wiki/ISO_8601) standard.
purchase.subscription.date_next_charge|string|Date and time of the next charge according to [ISO 8601](https://en.wikipedia.org/wiki/ISO_8601).
purchase.subscription.currency|string|Three-letter currency designation of the recurrent plan according to [ISO 4217](https://wikipedia.org/wiki/ISO_4217) standard.
purchase.subscription.amount|float|Purchase amount.
purchase.virtual_items|object|Object with data about the items in the purchase.
purchase.virtual_items.items|array|Array with item data.
purchase.virtual_items.items.sku|string|Item ID (vendor code).
purchase.virtual_items.items.amount|integer|Quantity of this item in the order.
purchase.virtual_items.currency|string|Three-letter currency symbol according to [ISO 4217](https://wikipedia.org/wiki/ISO_4217) standard.
purchase.virtual_items.amount|integer|Order amount.
purchase.pin_codes|object|Array with key data.
purchase.pin_codes.digital_content|string|Game vendor code, configured in Personal account.
purchase.pin_codes.drm|string|DRM-platform on which the game will be available. Can take values 'steam', 'playstation', 'xbox', 'uplay', 'origin', 'drmfree', 'gog', 'epicgames', 'nintendo_eshop', 'discord_game_store' or 'oculus'. Before using, is needed to make sure that the required DRM-platform is configured in Personal account.
purchase.pin_codes.currency|string|Three-letter designation of the purchase currency according to [ISO 4217](https://wikipedia.org/wiki/ISO_4217).
purchase.pin_codes.amount|float|Key price.
purchase.pin_codes.upgrade|object|Object with upgrade information.
purchase.pin_codes.upgrade.digital_content_from|object|Object with information about the user package from which the upgrade was made.
purchase.pin_codes.upgrade.digital_content_from.digital_content|string|Game vendor code, configured in Personal account.
purchase.pin_codes.upgrade.digital_content_from.DRM|string|DRM-platform of the game.
purchase.pin_codes.upgrade.digital_content_to|object|Object with information about the package that the user has moved to as part of the upgrade.
purchase.pin_codes.upgrade.digital_content_to.digital_content|string|Game vendor code, configured in Personal account.
purchase.pin_codes.upgrade.digital_content_to.DRM|string|DRM-platform of the game.
purchase.pin_codes.upgrade.currency|string|Three-letter designation of the purchase currency according to [ISO 4217](https://wikipedia.org/wiki/ISO_4217).
purchase.pin_codes.upgrade.amount|float|Purchase amount.
purchase.gift|object|Object with information about the gift.
purchase.gift.giver_id|string|Giver ID.
purchase.gift.receiver_ID|string|Gift recipient ID.
purchase.gift.receiver|string|Gift recipient email.
purchase.gift.message|string|Message from the giver.
purchase.gift.hide_giver_from_receiver|string|Flag showing the gift giver visibility to the gift recipient.
purchase.total|object|Object with data on the total purchase price. Mandatory.
purchase.total.currency|string|Three-letter currency symbol according to [ISO 4217](https://wikipedia.org/wiki/ISO_4217) standard.
purchase.total.amount|float|Total purchase amount.
purchase.promotions|array|Array with the data of shares that act on a given purchase.
purchase.promotions.technical_name|string|Promotion technical name .
purchase.promotions.id|integer|Promotion ID.
purchase.coupon|object|Object with information about the coupon (if when creating the subscription has been used a coupon).
purchase.coupon.coupon_code|string|Coupon code.
purchase.coupon.campaign_code|string|Coupon campaign code.
user|object|Object with user information.
user.ip|string|User IP address.
user.phone|string|User phone number (in international format).
user.email|string|User email.
user.id|string|User ID. Mandatory.
user.name|string|User name.
user.country|string|Two-letter designation of the user's country according to [ISO 3166-1 alpha-2](https://wikipedia.org/wiki/ISO_3166-1_alpha-2).
user.zip|string|Zip or postal code.
transaction|object|Object with information about the transaction associated with this operation. Mandatory.
transaction.id|integer|Transaction ID.
transaction.external_id|string|External transaction ID.
transaction.payment_date|string|Payment date.
transaction.payment_method|integer|Payment method ID in the XSOLLA system.
transaction.dry_run|integer|Sign of test transaction: 1 — test payment, 0 — real payment.
transaction.agreement|integer|Agreement ID.
payment_details|object|Object with financial payment data. Mandatory.
payment_details.payment|object|Object with data on the amount paid by the user.
payment_details.payment.currency|string|Three-letter currency designation according to [ISO 4217](https://wikipedia.org/wiki/ISO_4217).
payment_details.payment.amount|string|Amount.
payment_details.payment_method_sum|object|Object with data on the amount that was paid from the payment system.
payment_details.payment_method_sum.currency|string|Three-letter currency designation according to [ISO 4217](https://wikipedia.org/wiki/ISO_4217).
payment_details.payment_method_sum.amount|string|Amount.
payment_details.xsolla_balance_sum|object|Object with data on the amount that was paid from the XSOLLA-balance.
payment_details.xsolla_balance_sum.currency|string|Three-letter currency designation according to [ISO 4217](https://wikipedia.org/wiki/ISO_4217).
payment_details.xsolla_balance_sum.amount|string|Amount.
payment_details.payout|object|Object with data on the amount of payment.
payment_details.payout.currency|string|Three-letter currency designation according to [ISO 4217](https://wikipedia.org/wiki/ISO_4217).
payment_details.payout.amount|float|Amount.
payment_details.vat|object|VAT amount (EU only).
payment_details.vat.currency|string|Three-letter currency designation according to [ISO 4217](https://wikipedia.org/wiki/ISO_4217).
payment_details.vat.amount|float|Amount.
payment_details.payout_currency_rate|float|Payout currency rate.
payment_details.xsolla_fee|object|Amount of XSOLLA fee.
payment_details.xsolla_fee.currency|string|Three-letter currency designation according to [ISO 4217](https://wikipedia.org/wiki/ISO_4217).
payment_details.xsolla_fee.amount|float|Amount.
payment_details.payment_method_fee|object|Payment system fee amount.
payment_details.payment_method_fee.currency|string|Three-letter currency designation according to [ISO 4217](https://wikipedia.org/wiki/ISO_4217).
payment_details.payment_method_fee.amount|float|Amount.
payment_details.sales_tax|object|Tax amount (USA only).
payment_details.sales_tax.currency|string|Three-letter currency designation according to [ISO 4217](https://wikipedia.org/wiki/ISO_4217).
payment_details.sales_tax.amount|float|Amount.
payment_details.repatriation_commission|object|Object with information on repatriation costs imposed on XSOLLA by third parties.
payment_details.repatriation_commission.currency|string|Three-letter currency symbol of the cost of repatriation in accordance with [ISO 4217](https://wikipedia.org/wiki/ISO_4217).
payment_details.repatriation_commission.amount|float|Amount of repatriation costs.
custom_parameters|object|Advanced options.

Examples of payment: 


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

### The payment was canceled
When the payment is cancelled, XSOLLA sends the details of the canceled transaction to the webhook URL.

Field | Type | Description
---- | --- | --------
notification_type|string|Alert type. Mandatory.
purchase|object|Object with order information.
purchase.virtual_currency|object|Object with data about the purchased virtual currency.
purchase.virtual_currency.name|string|Virtual currency name.
purchase.virtual_currency.quantity|float|Quantity of bonus items.
purchase.virtual_currency.currency|string|Three-letter currency symbol according to [ISO 4217](https://wikipedia.org/wiki/ISO_4217) standard.
purchase.virtual_currency.amount|float|Purchase amount.
purchase.checkout|object|Object with order information.
purchase.checkout.currency|string|Three-letter currency symbol according to [ISO 4217](https://wikipedia.org/wiki/ISO_4217) standard.
purchase.checkout.amount|float|Order amount.
purchase.subscription|object|Object with subscription data.
purchase.subscription.plan_id|string|Plan ID (external ID if the plan was created through the API).
purchase.subscription.tags|array|Plan tags.
purchase.subscription.subscription_id|integer|Subscription ID in the XSOLLA database.
purchase.subscription.date_create|string|Date and time when the subscription was created according to the [ISO 8601](https://en.wikipedia.org/wiki/ISO_8601) standard.
purchase.subscription.currency|string|Three-letter currency designation of the recurrent plan according to [ISO 4217](https://wikipedia.org/wiki/ISO_4217) standard.
purchase.subscription.amount|float|Purchase amount.
purchase.virtual_items|object|Object with data about the items in the purchase.
purchase.virtual_items.items|array|Array with item data.
purchase.virtual_items.items.sku|string|Item ID (vendor code).
purchase.virtual_items.items.amount|integer|Quantity of this item in the order.
purchase.virtual_items.currency|string|Three-letter currency symbol according to [ISO 4217](https://wikipedia.org/wiki/ISO_4217) standard.
purchase.virtual_items.amount|integer|Order amount.
purchase.pin_codes|object|Object with key data.
purchase.pin_codes.upgrade|object|Object with upgrade information.
purchase.pin_codes.upgrade.digital_content_from|object|Object with information about the user package from which the upgrade was made.
purchase.pin_codes.upgrade.digital_content_from.digital_content|string|Game vendor code, configured in Personal account.
purchase.pin_codes.upgrade.digital_content_from.DRM|string|DRM-platform of the game.
purchase.pin_codes.upgrade.digital_content_to|object|Object with information about the package that the user has moved to as part of the upgrade.
purchase.pin_codes.upgrade.digital_content_to.digital_content|string|Game vendor code, configured in Personal account.
purchase.pin_codes.upgrade.digital_content_to.DRM|string|DRM-platform of the game.
purchase.pin_codes.upgrade.currency|string|Three-letter designation of the purchase currency according to [ISO 4217](https://wikipedia.org/wiki/ISO_4217).
purchase.pin_codes.upgrade.amount|float|Purchase amount.
purchase.total|object|Object with data on the total purchase price.
purchase.total.currency|string|Three-letter currency symbol according to [ISO 4217](https://wikipedia.org/wiki/ISO_4217) standard.
purchase.total.amount|float|Total purchase amount.
user|object|Object with user information.
user.ip|string|User IP address.
user.phone|string|User phone number (in international format).
user.email|string|User email.
user.id|string|User ID. Mandatory.
user.name|string|User name.
user.country|string|Two-letter designation of the user's country according to [ISO 3166-1 alpha-2](https://wikipedia.org/wiki/ISO_3166-1_alpha-2).
user.zip|string|Zip or postal code.
transaction|object|Object with information about the transaction associated with this operation. Mandatory.
transaction.id|integer|Transaction ID.
transaction.external_id|string|External transaction ID.
transaction.dry_run|integer|Sign of test transaction: 1 — test payment, 0 — real payment.
transaction.agreement|integer|Agreement ID.
refund_details|object|Entity with the financial data of refund.
refund_details.code|integer|ID code.
refund_details.reason|string|Reason for cancellation.
refund_details.author|string|Refund author.
payment_details|object|Object with financial payment data. Mandatory.
payment_details.payment|object|Object with data on the amount paid by the user.
payment_details.payment.currency|string|Three-letter currency designation according to [ISO 4217](https://wikipedia.org/wiki/ISO_4217).
payment_details.payment.amount|string|Amount.
payment_details.payment_method_sum|object|Object with data about the amount that was paid from the payment system.
payment_details.payment_method_sum.currency|string|Three-letter currency designation according to [ISO 4217](https://wikipedia.org/wiki/ISO_4217).
payment_details.payment_method_sum.amount|string|Amount.
payment_details.xsolla_balance_sum|object|Object with data about the amount that was paid from the XSOLLA-balance.
payment_details.xsolla_balance_sum.currency|string|Three-letter currency designation according to [ISO 4217](https://wikipedia.org/wiki/ISO_4217).
payment_details.xsolla_balance_sum.amount|string|Amount.
payment_details.payout|object|Object with data about the amount of payment.
payment_details.payout.currency|string|Three-letter currency designation according to [ISO 4217](https://wikipedia.org/wiki/ISO_4217).
payment_details.payout.amount|float|Amount.
payment_details.vat|object|VAT amount (EU only).
payment_details.vat.currency|string|Three-letter currency designation according to [ISO 4217](https://wikipedia.org/wiki/ISO_4217).
payment_details.vat.amount|float|Amount.
payment_details.payout_currency_rate|float|Currency of payment the currency of payment.
payment_details.xsolla_fee|object|Amount of XSOLLA fee.
payment_details.xsolla_fee.currency|string|Three-letter currency designation according to [ISO 4217](https://wikipedia.org/wiki/ISO_4217).
payment_details.xsolla_fee.amount|float|Amount.
payment_details.payment_method_fee|object|Payment system fee amount.
payment_details.payment_method_fee.currency|string|Three-letter currency designation according to [ISO 4217](https://wikipedia.org/wiki/ISO_4217).
payment_details.payment_method_fee.amount|float|Amount.
payment_details.sales_tax|object|Tax amount (USA only).
payment_details.sales_tax.currency|string|Three-letter currency designation according to [ISO 4217](https://wikipedia.org/wiki/ISO_4217).
payment_details.sales_tax.amount|float|Amount.
payment_details.repatriation_commission|object|Object with information about repatriation costs imposed on XSOLLA by third parties.
payment_details.repatriation_commission.currency|string|Three-letter currency symbol of the cost of repatriation in accordance with [ISO 4217](https://wikipedia.org/wiki/ISO_4217).
payment_details.repatriation_commission.amount|float|Amount of repatriation costs.
custom_parameters|object|Advanced options.

Cancellation codes:

Code | Reason for cancellation | Description
--- | -------------- | --------
1.|Cancellation by the user/game request.|Used if the cancellation was made from the Personal account.
2.|Chargeback.|Is used if the transaction was a chargeback.
3.|Integration Error.|Used in case of problems with integration between XSOLLA and the game. In this case, it is not recommended to blacklist the user.
4.|Fraud.|Used in case of potential fraud.
5.|Test Payment.|Used in case of test payment with subsequent cancellation. In this case, it is not recommended to blacklist the user.
6.|Expired Invoice.|Used if a payment method with a deferred payment system has been selected.
7.|PS debt cancel.|Used if the transaction was made through a payment system that has not made a payment for the transaction. In this case, it is not recommended to blacklist the user.
8.|Cancellation by the PS request.|Used when the payment system has requested the cancellation of a transaction. In this case, it is not recommended to blacklist the user.
9.|Cancellation by the user request.|Used if the game or order does not meet the user requirements for any reason. In this case, it is not recommended to blacklist the user.
10.|Cancellation by the game request.|Used when the game asks to cancel a transaction. In this case, it is not recommended to blacklist the user.
11.|Account holder called to report fraud.|Used when the account holder has reported that he did not make this payment.
12.|Friendly fraud.|Used if we have been informed of a friendly fraud.

Examples of payment cancellations:


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

### Cancel the upgrade
If the user cancels the payment associated with the upgrade, XSOLLA sends the canceled upgrades and the current package data to the webhook URL.

Field | Type | Description
----- | ---- | -----------
notification_type|string|Alert type. Mandatory.
purchase|object|Object with purchase information. Mandatory.
purchase.pin_codes|object|Object with information about the purchased packages of the game.
purchase.pin_codes.purchase_type|string|Type of purchase. It takes the values “regular” – package purchase, “upgrade” – package upgrade.
purchase.pin_codes.digital_content|string|Game vendor code, configured in Personal account.
purchase.pin_codes.DRM|string|DRM-platform of the game.
purchase.pin_codes.currency|string|Three-letter designation of the purchase currency according to ISO 4217.
purchase.pin_codes.amount|float|Purchase amount.
purchase.pin_codes.transaction|object|Object with transaction information.
purchase.pin_codes.transaction.id|integer|Transaction ID.
purchase.pin_codes.upgrade|object|Object with upgrade information.
purchase.pin_codes.upgrade.digital_content_from|object|Object with information about the user package from which the upgrade was made.
purchase.pin_codes.upgrade.digital_content_from.digital_content|string|Game vendor code, configured in Personal account.
purchase.pin_codes.upgrade.digital_content_from.DRM|string|DRM-platform of the game.
purchase.pin_codes.upgrade.digital_content_to|object|Object with information about the package that the user has moved to as part of the upgrade.
purchase.pin_codes.upgrade.digital_content_to.digital_content|string|Game vendor code, configured in Personal account.
purchase.pin_codes.upgrade.digital_content_to.DRM|string|DRM-platform of the game.
ownership|object|Object with information about packages owned by the user. Mandatory.
ownership.digital_content|string|Game vendor code, configured in Personal account.
ownership.DRM|string|DRM-platform of the game.

Examples of cancellation of an upgrade:


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

### The transaction is declined when checking AFS
If the transaction was rejected during AFS validation, XSOLLA sends the transaction details to the webhook URL. To enable the alert, is needed to contact the account Manager of the project.

Field | Type | Description
---- | --- | --------
notification_type|string|Alert type. Mandatory.
user|object|Object with user information.
user.ip|string|User IP address .
user.phone|string|User phone number (in international format).
user.email|string|User email.
user.id|string|User ID. Mandatory.
user.name|string|User name.
user.country|string|Two-letter designation of the user's country according to [ISO 3166-1 alpha-2](https://wikipedia.org/wiki/ISO_3166-1_alpha-2).
user.zip|string|Zip or postal code.
transaction|object|Object with information about the transaction associated with this operation. Mandatory.
transaction.id|integer|Transaction ID.
transaction.external_id|string|External transaction ID.
transaction.dry_run|integer|Sign of test transaction: 1 — test payment, 0 — real payment.
transaction.agreement|integer|Agreement ID.
refund_details|object|Entity with the financial data of refund.
refund_details.code|integer|Code ID.
refund_details.reason|string|Reason for cancellation.
refund_details.author|string|Refund author.

Examples of transaction rejection when checking AFS:


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

### Create subscription
When a user creates a subscription, XSOLLA sends an alert to the webhook URL.

Field | Type | Description
---- | --- | --------
notification_type|string|Alert type. Mandatory.
user|object|Object with user information.
user.id|string|User ID. Mandatory.
user.name|string|User name.
subscription|object|Object with subscription data.
subscription.plan_id|string|Plan ID (external ID if the plan was created through the API).
subscription.tags|array|Plan tags.
subscription.subscription_id|integer|Subscription ID in the XSOLLA database.
subscription.product_id|string|Product ID (if sent in token).
subscription.date_create|string|Date and time when the subscription was created according to the [ISO 8601](https://en.wikipedia.org/wiki/ISO_8601) standard.
subscription.date_next_charge|string|Date and time of the next charge according to [ISO 8601](https://en.wikipedia.org/wiki/ISO_8601).
subscription.trial|object|Object with information about the trial subscription period.
subscription.trial.value|integer|Trial period duration.
subscription.trial.type|string|Trial period type: day.

Examples of creating subscriptions:


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

### Change subscription
In case of change of any parameters ('plan_id', 'date_next_charge') subscriptions and for each subscription renewal, XSOLLA sends an alert "update_subscription" in the webhook URL.

Field | Type | Description
---- | --- | --------
notification_type|string|Alert type. Mandatory.
user|object|Object with user information.
user.id|string|User ID. Mandatory.
user.name|string|User name.
subscription|object|Object with subscription data.
subscription.plan_id|string|Plan ID (external ID if the plan was created through the API).
subscription.tags|array|Plan tags.
subscription.subscription_id|integer|Subscription ID in the XSOLLA database.
subscription.product_id|string|Product ID (if sent in token).
subscription.date_next_charge|string|Date and time of the next charge according to [ISO 8601](https://en.wikipedia.org/wiki/ISO_8601).

Examples of changes to the subscription:


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

### Unsubscribe
When a subscription is cancelled for any reason, XSOLLA sends an alert to the webhook URL.

Field | Type | Description
---- | --- | --------
notification_type|string|Alert type. Mandatory.
user|object|Object with user information.
user.id|string|User ID. Mandatory.
user.name|string|User name.
subscription|object|Object with subscription data.
subscription.plan_id|string|Plan ID (external ID if the plan was created through the API).
subscription.tags|array|Plan tags.
subscription.subscription_id|integer|Subscription ID in the XSOLLA database.
subscription.product_id|string|Product ID (if sent in token).
subscription.date_create|string|Date and time the subscription when was created according to the [ISO 8601](https://en.wikipedia.org/wiki/ISO_8601) standard.
subscription.date_end|string|Expiration date and time of the subscription according to the [ISO 8601](https://en.wikipedia.org/wiki/ISO_8601) standard.

Examples of unsubscribing:


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

### Get key
XSOLLA sends API calls to the server to retrieve the game key after each successful payment.

Field | Type | Description
---- | --- | --------
notification_type|string|Alert type.
user|object|Object with user information.
user.id|string|User ID. Mandatory.
user.name|string|User name.
pin_code|object|Object with key information.
pin_code.digital_content|string|Game vendor code.
pin_code.DRM|string|DRM-platform on which the game will be available.

Examples of obtaining a key:


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

### Key activation
When the user activates the key, XSOLLA sends an alert to the webhook URL.

Field | Type | Description
---- | --- | --------
notification_type|string|Alert type.
key|string|Activation key.
sku|string|Key package unique ID.
user_id|string|User ID.
activation_date|datetime|Key activation date in YYYYMMDDHHMMSS format according to [ISO 8601](https://en.wikipedia.org/wiki/ISO_8601) standard.
user_country|string|Two-letter designation of the user's country according to [ISO 3166-1 alpha-2](https://wikipedia.org/wiki/ISO_3166-1_alpha-2).
restriction|object|Object with regional restriction cluster settings. The cluster includes the type of restriction, the list of countries, servers and language versions for which the game is available.
restriction.sku|string|Unique cluster ID.
restriction.name|string|Cluster name.
restriction.types|array|Array of constraint types.
restriction_countries|array|Array of countries included in the cluster.
restriction.servers|array|Array of game servers.
restriction.locales|array|Array of game language versions.

Examples of key activation:


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

### Get a friends list
The API must be implemented on the partner side. The maximum size of a friend list is 2000. More details can be found in [recipe](https://developers.xsolla.com/ru/recipes/store/gifting/).

**HTTP REQUEST**

```
GET https://your.webhook.url?notification_type=friends_list&user=user_id&query=frien&offset=10&limit=20&sign=12dfg3f5gdsf4g5s6dfg2sdg1
notification_type
```

Field | Type | Description
---- | --- | --------
string|friends_list|Identifier that determines the type of friend list request.
user|string|Unique identifier of the user who makes a purchase to a friend.
query|string|Friend name or ID. Can pass part of a friend's name or ID.
limit|string|Limit the number of elements on the page. Mandatory.
offset|integer|Number of the element from which output is performed on the page. (numbering is from 0).
sign|string|String for the signature is formed as follows:<ul><li>notification_type + parameter values sorted by key in alphabetical order + secret_key</li><li>apply SHA-1 cryptographic hash function to the resulting string in the first step.</li></ul>

Examples of getting a list of friends:


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

### The user's balance: internal operation
When a user makes a payment, XSOLLA sends a special notification about the change in the user's balance.

Field | Type | Description
---- | --- | --------
notification_type|string|Alert type.
operation_type|string|Operation type.
id_operation|integer|Operation ID in the XSOLLA database.
user|object|Object with user information.
user.id|string|User ID. Mandatory.
user.name|string|User name.
user.email|string|User email.
virtual_currency_balance|object|Object with user balance data.
virtual_currency_balance.old_value|string|Balance before the transaction.
virtual_currency_balance.new_value|string|Balance after the transaction.
virtual_currency_balance.diff|string|Amount of virtual currency in the order.
transaction|object|Object with information about the transaction associated with this operation. Mandatory.
transaction.id|integer|Transaction ID.
transaction.date|string|Transaction date.

Examples: 


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

### The user's balance: in-game purchase
When a user makes a purchase in the game (for example, buying items), XSOLLA sends a special notification about the change in the user's balance.

Field | Type | Description
---- | --- | --------
notification_type|string|Alert type.
operation_type|string|Type of operation.
id_operation|integer|Operation ID in the XSOLLA database.
user|object|Object with user information.
user.id|string|User ID. Mandatory.
user.name|string|User name.
user.email|string|User email.
virtual_currency_balance|object|Object with user balance data.
virtual_currency_balance.old_value|string|Balance before the transaction.
virtual_currency_balance.new_value|string|Balance after the transaction.
virtual_currency_balance.diff|string|Amount of virtual currency in the order.
items_operation_type|string|Type of operation with objects.
items|array|Array of data about the item in the order.
items.sku|string|Item ID (vendor code).
items.amount|integer|Number of copies of the item in the order.

Examples of in-game purchases:


ew_value":"200",
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

### The user's balance: coupon activation
If the user has activated the coupon to receive items or virtual currency in the game, XSOLLA sends a special notification about the change in the user's balance.

Field | Type | Description
---- | --- | --------
notification_type|string|Alert type.
operation_type|string|Type of operation.
id_operation|integer|Operation ID in the XSOLLA database.
user|object|Object with user information.
user.id|string|User ID. Mandatory.
user.name|string|User name.
user.email|string|User email.
virtual_currency_balance|object|Object with user balance data.
virtual_currency_balance.old_value|string|Balance before the transaction.
virtual_currency_balance.new_value|string|Balance after the transaction.
virtual_currency_balance.diff|string|Amount of virtual currency in the order.
items_operation_type|string|Type of operation with objects.
items|array|Array of data about the item in the order.
items.sku|string|Item ID (vendor code).
items.amount|integer|Number of copies of the item in the order.
coupon|object|Object with coupon data.
coupon.coupon_code|string|Coupon code.
coupon.campaign_code|string|Coupon campaign code.

Examples of coupon activation:


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

### The user's balance: manual change
When the user's balance should be changed manually, is needed to use the "Internal" operation type.

Field | Type | Description
---- | --- | --------
notification_type|string|Alert type.
operation_type|string|Type of operation.
id_operation|integer|Operation ID in the XSOLLA database.
user|object|Object with user information.
user.id|string|User ID. Mandatory.
user.name|string|User name.
user.email|string|User email.
virtual_currency_balance|object|Object with user balance data.
virtual_currency_balance.old_value|string|Balance before the transaction.
virtual_currency_balance.new_value|string|Balance after the transaction.
virtual_currency_balance.diff|string|Amount of virtual currency in the order.

Examples of balance changes:


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

### The user's balance: payment cancellation
When a user cancels a payment, XSOLLA sends a special notification when the user's balance changes.

Field | Type | Description
---- | --- | --------
notification_type|string|Alert type.
operation_type|string|Type of operation.
id_operation|integer|Operation ID in the XSOLLA database.
user|object|Object with user information.
user.id|string|User ID. Mandatory.
user.name|string|User name.
user.email|string|User email.
virtual_currency_balance|object|Object with user balance data.
virtual_currency_balance.old_value|string|Balance before the transaction.
virtual_currency_balance.new_value|string|Balance after the transaction.
virtual_currency_balance.diff|string|Amount of virtual currency in the order.
transaction|object|Object with information about the transaction associated with this operation. Mandatory.
transaction.id|integer|Transaction ID.
transaction.date|string|Transaction date.
items_operation_type|string|Type of operation with objects.
items|array|Array of data about the item in the order.
items.sku|string|Item ID (vendor code).
items.amount|integer|Quantity of this item in the order.

Examples of payment cancellations:


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

### Secondary market: send list of items
When the secondary market requests data about items from the game inventory, XSOLLA sends an alert to the webhook URL.

Field | Type | Description
---- | --- | --------
notification_type|string|Alert type.
project_id|integer|Project ID.
payload|object|Object with information about the user and the secondary market.
payload.user|object|Object with user information.
payload.user.id|string|User ID.
payload.secondary_market|object|Object with information about the secondary market.
payload.secondary_market.id|string|Secondary market ID.

Examples of sending a list of items:


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

### Secondary market: sending items from the game
When the secondary market requests items from the game's inventory, XSOLLA sends an alert to the webhook URL.


Field | Type | Description
---- | --- | --------
notification_type|string|Alert type.
project_id|integer|Project ID.
payload|object|Object with information about the user and the secondary market.
payload.user|object|Object with user information.
payload.user.id|string|User ID.
items|array|Array with information about items.
items.sku|string|Game vendor code.
items.instance_id|string|Unique in-game item ID.
payload.secondary_market|object|Object with information about the secondary market.
payload.secondary_market.id|string|Secondary market ID.

Examples of sending items:


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

### Secondary market: getting items into the game
When the secondary market sends items to the game inventory, XSOLLA sends an alert to the webhook URL.

Field | Type | Description
---- | --- | --------
notification_type|string|Alert type.
project_id|integer|Project ID.
payload|object|Object with information about the user and the secondary market.
payload.user|object|Object with user information.
payload.user.id|string|User ID.
items|array|Array with information about items.
items.sku|string|Game vendor code.
items.instance_id|string|Unique in-game item ID.
payload.secondary_market|object|Object with information about the secondary market.
payload.secondary_market.id|string|Secondary market ID.

Examples of obtaining items:


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

### Error alerts
Error code:

Code | Description
--- | --------
INVALID_USER|Wrong user.
INVALID_PARAMETER|The parameter is incorrect.
INVALID_SIGNATURE|Invalid signature.
INCORRECT_AMOUNT|Incorrect amount.
INCORRECT_INVOICE|Wrong invoice.

Response example: 

```
HTTP/1.1 400 Bad Request
{
    "error":{
        "code":"INVALID_USER",
        "message":"Invalid user"
    }
} 
```

