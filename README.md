# TRYMINEX Official API Documentation

## Beta Warning
Please be kindly noticed that currently this version is in BETA and we may keep developing. Some changes would be expected in later versions.

## Web Socket API document
The WebSocket API document can be found here: http://github.com/tryminexAPI/tryminex-exchange-api-doc/WEB_SOCKET_API.md

## Index
- [API End Point](#end-point)
    - [endpoint](#end-point)
- [Response Message](#response-message)
    - [response message](#response-message)
- [Rate Limiting](#rate-limiting)
- [Public API](#public-api)
    - [markets](#markets)
    - [tickers](#tickers)
    - [tickers{market}](#tickersmarket)
    - [depth](#depth)
    - [timestamp](#timestamp)
    - [klines](#klines)
- [Auth API](#auth-api)
    - [users](#users)
    - [account](#account)
    - [account{currency}](#account{currency})
    - [orders](#orders)
        - [list orders](#list_orders)
        - [list order](#list_order)
        - [my trades](#my_trades)
        - [create order](#create_order)
        - [cancel order](#cancel_order)
        - [cancel orders](#cancel_orders)
 
**End Point**
----
The base API endpoint is `https://api.tryminex.com:9000`

**Response Message**
----
if API request occured, the response will return HTTP status code, e.g. 200,401,403,404 etc.,and detailed response message body in json format.
* **code** :"10000" sucess, other code express exception occured
* **msg**  :detailed err message
* **data** :detailed response data
```
{
    "code": "10000",
    "msg": "success",
    "data": ...
}

```

**Rate Limiting**
---

For ALL API the maximum access rate is *200 requests per minute, any client initiates more request will be throttled with status code `429`.

**Public API**
----

### markets

* **Method:**
  `GET`
* **URL**
  /api/v1/symbols

* **Description**
  Get all available markets.
* **Success Response:**
    * **Code:** 200
    * **Response Body:** 

        ```
          {
              "code": "10000",
              "msg": "success",
              "data": [
                  {
                      "symbol": "USDT_BTC",// market symbol
                      "name": "BTC/USDT",// market name
                      "minAmount": 0.0001,// Order minimum amount.
                      "minPrice": 1e-8,// Order minimum price.
                      "minOrderVoume": 0.01// Order minimum total volume.
                  },
                  ...
                 
              ]
          }
        ```

### tickers

* **Method:**
  `GET`
 
* **URL**
  /api/v1/tickers

* **Description**
  Get ticker of all markets.
* **Success Response:**  
    * **Code:** 200
    * **Response Body:** 

        ```
           {
               "code": "10000",
               "msg": "success",
               "data": [
                   {
                       "symbol": "USDT_TMX",// market symbol
                       "high": 0.06874125,// Highest Price within last 24 hours
                       "low": 0.06812365, // Lowest price within last 24 hours
                       "staPrice": 0,// Starting Price within last 24 hours
                       "lastPrice": 0.04,// Last trade's price
                       "total": 0// Trade volume within last 24 hours
                   },
                   ...
               ]
           }
        ```
 
### tickers{market}

* **Method:**
  `GET`
* **URL**
  /api/v1/ticker/{symbol}
* **Parameters**
    * **symbol**: market symbol

* **Description**
  Get ticker of specific market.

* **Example Request:**
    * **Request:**
    `GET /api/v1/ticker/USDT_TMX`
  
    * **Success Response:**  
        * **Code:** 200
        * **Content:** 

        ```
       {
           "code": "10000",
           "msg": "success",
           "data": [
               {
                   "symbol": "USDT_TMX",// market symbol
                   "high": 0,// Highest Price within last 24 hours
                   "low": 0,// Lowest price within last 24 hours
                   "staPrice": 0,// Starting Price within last 24 hours
                   "lastPrice": 0.04,// Last trade's price
                   "total": 0// Trade volume within last 24 hours
               }
           ]
       }
        ```
 
### depth

* **Method:**
  `GET`
  
* **URL**
  /api/v1/depth

* **Description**
  Get the depth information of specified market.

* **Parameters**
    * **symbol`(required)`**: market symbol

    * **limit`(required)`**: Limit the number of returned price levels, minmum value:1, maximum value: 50.

* **Example Request:**
    * **Request:**
    `GET /api/v1/depth?symbol=USDT_TMX&limit=2`

    * **Success Response:**
        * **Code:** 200
        * **Content:**

        ```
       {
           "code": "10000",
           "msg": "success",
           "data": {
               "buy": [
                   [
                       5850, // Price
                       3  // Volume
                   ],
                   [
                       5800,
                       10
                   ]
               ],
               "sell": [
                   [
                       6310,
                       0.2
                   ],
                   [
                       6500,
                       0.4
                   ]
               ]
           }
       }
        ```

### timestamp

* **Method:**
  `GET`
  
* **URL**
  /api/v1/timestamp

* **Description**
  Get server current time, in seconds since Unix epoch.
  
* **Example Request:**
    * **Request:**
    `/api/v1/timestamp`
    * **Success Response:**  
        * **Code:** 200
        * **Content:** 

        ```
       {
           "code": "10000",
           "msg": "success",
           "data": {
               "timestamp": 153518178778
           }
       }
        ```
### klines

* **Method:**
  `GET`
* **URL**
  /api/v1/kline

* **Description**
  Get OHLC(k line) of specific market.

* **Parameters**
    * **symbol**: market symbol

    * period: Time period of K line, You can choose between 1, 5, 15, 30, 60,1440

* **Example Request:**
    * **Request:**
    `GET /api/v1/kline?symbol=USDT_BTC&period=1`
  
    * **Success Response:**  
        * **Code:** 200
        * **Content:** 

        ```
      {
          "code": "10000",
          "msg": "success",
          "data": [
              [
                  1534425360,// An integer represents the seconds elapsed since Unix epoch.
                  6310,// K line open price
                  6310,// K line highest price
                  6310,// K line lowest price
                  6310,// K line close price
                  0// K line volume
              ]...
          ]
      }
        ```
        
**Auth API**
----

`auth` endpoints requires 3 extra authentication parameters:

* **appid`(required)`**: your api key
* **nonce`(required)`**:  Current timestamp to milisecond (13 digits). For example in Javascript you can get it by calling + new Date(), and have 1532422489533 as the result for nonce. Server will only accept nonce within time window of ± 30 seconds.
* **timestamp`(required)`**: In seconds since Unix epoch.
* **sign`(required)`**: 
    * signature: can be generated by `HMAC-SHA256(payload, your_app_secret).to_hex`
    * `payload` is a string represents this request, combiled with HTTP method, request URI and request parameters:
        - HTTP method: e.g. "GET", "POST", etc
        - request URI: such as "/users/me", or "trades/my"
        - request parameters: parameters concated with "&", **MUST BE IN ALPHABETICAL ORDER** by parameters' name.

      Then concat the above 3 strings with `&`, you get the `payload`.

For example:

if your `api key` is `xxx`, and your `api secret` is `yyy`, then the `payload` is `GET&/api/v1/users/me&appid=xxx&nonce=xxx&timestamp=xxx`.

And you can make the request:

`GET /api/v1/users/me?appid=xxx&nonce=xxx&timestamp=xxx&sign=xxx`


### users

* **Method:**
  `GET`
 
* **URL**
  /api/v1/users/me

* **Description**
  Get your profile and accounts info.

* **Parameters**
    * **appid`(required)`**: Access Key
    * **nonce`(required)`**: Current timestamp to milisecond (13 digits).
    * **timestamp`(required)`**: Timestamp since Unix Epoch.
    * **sign`(required)`**: The signature of your request payload, generated using your secret key.
  
* **Example Request:**
    * **Request:**
    `GET /api/v1/users/me?appid=05778EA9AD6F48B8983A1826E973155A&nonce=xxx&timestamp=xxx&sign=xxx`
    
    * **Success Response:**  
        * **Code:** 200
        * **Response Body:** 
         ```
              {
                  "code": "10000",
                  "msg": "success",
                  "data": {
                      "nickname": "",
                      "mobile": "", // Your Phone 
                      "email": "helloworld@126.com", // Your Email
                      "accounts": [
                          {
                              "currency": "BTC",// Your BTC Account
                              "balance": 10.78786957974998,// Your current BTC balance amount
                              "locked": 0.022083,// Your current BTC locked amount
                              "loaned": 0// Your current BTC Loaning amount
                          },
                          ...
                      ]
                  }
              }
        ```

### account
* **Method:**
  `GET`
* **URL**
  /api/v1/account/all

* **Description**
  Get your accounts info.
* **Parameters**
    * **appid`(required)`**: Access Key.
    * **nonce`(required)`**: Current timestamp to milisecond (13 digits).
    * **timestamp`(required)`**: Timestamp since Unix Epoch.
    * **sign`(required)`**: The signature of your request payload, generated using your secret key.

* **Example Request:**
    * **Request:**
    * **Success Response:**
        * **Code:** 200
        * **Response Body:**

        ```
       {
           "code": "10000",
           "msg": "success",
           "data": [
               {
                   "currency": "BTC", // Your BTC Account
                   "balance": 10.78786957974998, ,// Your current BTC balance amount
                   "locked": 0.022083, // Your current BTC locked amount
                   "loaned": 0 // Your current BTC Loaning amount
               }，
              ....
           ]
       }
        ```

### account{currency}

* **Method:**
  `GET`
  
* **URL**
  /api/v1/account

* **Description**
  Get one of your specific accounts information.
* **Parameters**
    * **currency`(required)`**: The account currency.
    * **appid`(required)`**: Access Key.
    * **nonce`(required)`**: Current timestamp to milisecond (13 digits).
    * **timestamp`(required)`**: Timestamp since Unix Epoch.
    * **sign`(required)`**: The signature of your request payload, generated using your secret key.
   
* **Example Request:**
    * **Request:**
    `GET   /api/v1/account?currency=BTC&appid=xxx&nonce=&timestamp=&sign=`
    * **Success Response:**  
        * **Code:** 200
        * **Response Body:** 

 ```       {
            "code": "10000",
            "msg": "success",
            "data": {
                "currency": "BTC", // Your BTC Account
                "balance": 10.78786957974998,// Your current BTC balance amount
                "locked": 0.022083,// Your current BTC locked amount
                "loaned": 0 // Your current BTC Loaning amount
            }
        }
 ```

### list_orders

* **Method:**
  `GET`
  
* **URL**
  /api/v1/orders

* **Description**
  Get your orders, results is paginated.

* **Parameters**
    * **page`(required)`**:  Specify the page of paginated results. Default value: 1
    * **limit`(required)`**: Limit the number of returned price levels.
    * **appid`(required)`**: Access key.
    * **nonce`(required)`**: Current timestamp to milisecond (13 digits).
    * **timestamp`(required)`**: Timestamp since Unix Epoch.
    * **sign`(required)`**: The signature of your request payload, generated using your secret key.

* **Example Request:**
    * **Request:**
    * **Success Response:**  
        * **Code:** 200
        * **Response Body:** 

        ```
      {
          "code": "10000",
          "msg": "success",
          "data": [
              {
                  "symbol": "BTC_ETH",// Your BTC_ETH symbol
                  "orderNo": "7210ae281123431ab2f85a81f9706abd",// Your order id
                  "number": 1,// The amount user want to sell/buy
                  "tradedNumber": 0,// The executed volume
                  "remainNumber": 0,// The remaining volume
                  "price": 10000.00008,// Price for each unit.
                  "orderType": "SELL",// Either 'SELL' or 'BUY'.
                  "status": "PROCESSING",// One of 'WAITING','CANCEL','SUCCESS','PROCESSING'
                  "create": "2018-08-24 15:24:23"// Order creation time
              }
          ]
      }
        ```
        
### list_order

* **Method:**
  `GET`
  
* **URL**
  /api/v1/order

* **Description**
  Get information of specified order
* **Parameters**
    * **orderNo`(required)`**:  Your order id
    * **appid`(required)`**: Access key.
    * **nonce`(required)`**: Current timestamp to milisecond (13 digits).
    * **timestamp`(required)`**: Timestamp since Unix Epoch.
    * **sign`(required)`**: The signature of your request payload, generated using your secret key.

* **Example Request:**
    * **Request:**
    * **Success Response:**  
        * **Code:** 200
        * **Response Body:** 

        ```
             {
                 "code": "10000",
                 "msg": "success",
                 "data": {
                      "symbol": "BTC_ETH",// Your BTC_ETH symbol
                       "orderNo": "7210ae281123431ab2f85a81f9706abd",// Your order id
                       "number": 1,// The amount user want to sell/buy.
                       "tradedNumber": 0,// The executed volume
                       "remainNumber": 0,// The remaining volume
                       "price": 10000.00008,// Price for each unit.
                       "orderType": "SELL",// Either 'SELL' or 'BUY'.
                       "status": "PROCESSING",// One of 'WAITING','CANCEL','SUCCESS','PROCESSING'
                       "create": "2018-08-24 15:24:23"// Order creation time
                 }
             }
         ```
 

### my_trades

* **Method:**
  `GET`
  
* **URL**
  /api/v1/orders/history

* **Description**
  Get your executed trades. Trades are sorted in reverse creation order.

* **Parameters**
    * **page`(required)`**:  Specify the page of paginated results. Default value: 1
    * **limit`(required)`**: Limit the number of returned price levels.
    * **appid`(required)`**: Access key.
    * **nonce`(required)`**: Current timestamp to milisecond (13 digits).
    * **timestamp`(required)`**: Timestamp since Unix Epoch.
    * **sign`(required)`**: The signature of your request payload, generated using your secret key.

* **Example Request:**
    * **Request:**
        /api/v1/orders/history
    * **Success Response:**  
        * **Code:** 200
        * **Response Body:** 

        ```
          {
              "code": "10000",
              "msg": "success",
              "data": [
                  {
                      "symbol": "BTC_ETH",// Your BTC_ETH symbol
                      "orderNo": "7210ae281123431ab2f85a81f9706abd",// Your order id
                      "number": 1,// The amount user want to sell/buy.
                      "tradedNumber": 0,// The executed volume
                      "remainNumber": 0,// The remaining volume
                      "price": 10000.00008,// Price for each unit.
                      "orderType": "SELL",// Either 'SELL' or 'BUY'.
                      "status": "PROCESSING",// One of 'CANCEL','SUCCESS'
                      "create": "2018-08-24 15:24:23"// Order creation time
                  }
              ]
          }
        ```
      
### create_order
* **Method:**
  `POST`
  
* **URL**
  /api/v1/order/create

* **Description**
  Create a Sell/Buy order.

* **Parameters**
    * **symbol`(required)`**:  Market symbol. 
    * **tradeType`(required)`**:  One of 'BUY','SELL'
    * **price`(required)`**:  Price for each unit.
    * **amount`(required)`**:  The amount user want to sell/buy.
    * **appid`(required)`**: Access key.
    * **nonce`(required)`**: Current timestamp to milisecond (13 digits).
    * **timestamp`(required)`**: Timestamp since Unix Epoch.
    * **sign`(required)`**: The signature of your request payload, generated using your secret key.
    
* **Example Request:**
    * **Request:**
        ```
            POST /api/v1/order/create?appid=05778EA9AD6F48B8983A1826E973155A&nonce=xxx&timestamp=xxx&sign=xxx
            [body]
            symbol=BTC_ETH&tradeType=sell&price=10000.00008&amount=100
        
        ```

    * **Success Response:**
        * **Code:** 200
        * **Response Body:** 

        ```
                {
                    "code": "10000",
                    "msg": "success"
                }
        ```

### cancel_order
* **Method:**
  `GET`
  
* **URL**
  /api/v1/order/cancle

* **Description**
  Cancel an order.
  
* **Parameters**
    * **orderNo`(required)`**:  Order id.
    * **appid`(required)`**: Access key.
    * **nonce`(required)`**: Current timestamp to milisecond (13 digits).
    * **timestamp`(required)`**: Timestamp since Unix Epoch.
    * **sign`(required)`**: The signature of your request payload, generated using your secret key.
    
* **Example Request:**
    * **Request:**
    `GET /api/v1/order/cancle?orderNo=xxx&appid=05778EA9AD6F48B8983A1826E973155A&nonce=xxx&timestamp=xxx&sign=xxx`

    * **Success Response:**
        * **Code:** 200
        * **Response Body:** 

        ```
                {
                    "code": "10000",
                    "msg": "success"
                }
        ```
     
### cancel_orders
* **Method:**
  `GET`
  
* **URL**
  /api/v1/orders/clear

* **Description**
  Cancel all your orders.
* **Parameters**
    * **tradeType`(required)`**:  One of"ALL","BUY","SELL"
    * **appid`(required)`**: Access key.
    * **nonce`(required)`**: Current timestamp to milisecond (13 digits).
    * **timestamp`(required)`**: Timestamp since Unix Epoch.
    * **sign`(required)`**: The signature of your request payload, generated using your secret key.
    
* **Example Request:**
    * **Request:**
     ` GET /api/v1/orders/clear?tradeType=ALL&appid=05778EA9AD6F48B8983A1826E973155A&nonce=xxx&timestamp=xxx&sign=xxx`

    * **Success Response:**
        * **Code:** 200
        * **Response Body:** 

        ```
                {
                    "code": "10000",
                    "msg": "success",
                    "data":["1111","2222"]// List Orders has been cancled
                  
                }
        ```

