# WebSocket API Reference

## Beta Warning
Please be kindly noticed that currently this version is in BETA and we may keep developing. Some changes would be expected in later versions.

## Index
- [Request and subscription specification](#request-and-subscription-specification)
    - [End Point](#1-end-point)
    - [Data Compression](#2-data-compression)
    - [Heartbeat](#3-heartbeat)
    - [Message Format](#4-message-format)
        - [Request Message](#request-message)
        - [Response And Pushed Message](#response-and-pushed-message)
    - [Request And Response](#5-request-and-response)
    - [Subscribe](#6-subscribe)
    - [Unsubscribe](#7-unsubscribe)
- [API Reference](#api-reference)
    - [request tickers](#request-tickers)
    - [request ticker{symbol}](#request-tickersymbol)
    - [request kline](#request-kline)
    - [request depth](#request-depth)
    - [request orders](#request-orders)
    - [subscribe kline](#subscribe-kline)
    - [subscribe depth](#subscribe-depth)
    - [subscribe orders](#subscribe-orders)
    - [unsubscribe](#unsubscribe) 

## Request and subscription specification

### 1.end point
SSL Websocket Connection: `wss://api.tryminex.com:9010/ws`.

### 2.data compression
All return data of websocket APIs needs to be unzipped except heartbeat.

### 3.heartbeat
Websocket API need heartbeat after connection establishment, websocket client launch `ping` message to websocket server, and then websocket server response `pong` message.

Mind! `ping` message and `pong` message must accord with RFC6455's definition.

WebSocket client launch websocket `ping` frame with body, as follows:
```
{
    "ping" : "18212558000"  // Timestamp since Unix Epoch
}
```

WebSocket server response websockt `pong` frame with body, as follows:
```
{
    "pong" : ""18212558000"" // Request's ping value
}
```

WebSocket client must send heartbeat message eveny 5 seconds after connected with websocket server. If websocket server has not received heartbeat message within 10 seconds, websocket server will dissconnect this connection.

### 4.message format
#### request message
* **id**: [string] client genrate unique id, used to differentiate requests.
* **type**: [string] request type, detail definition will be defined in the following sections.
* **data**: [string] request data, detail definition will be defined in the following sections.

Example request:
```
// query all of available tickers
{
	"id": "1",
	"type": "symbol/all"
}

// query kline of USDT_TMX, period is 1 min.
{
	"id": "1",
	"type": "kline",
	"data": {
		"symbol": "USDT_TMX",
		"period": "1m"
	}
}
```

#### response and pushed message
* **id**: [string] same as the client's request id.
* **code**: [string] response coded, sucess, other code express exception occured.
* **data**: [string] response data, detail definition will be defined in the following sections.
* **desc**: [string] detailed code message 

Example response:
```
{
	"id": "1",
	"code": "1000",
	"data": "{\"symbol\":\"USDT_TMX\",\"high\":0,\"low\":0,\"staPrice\":0,\"lastPrice\":0,\"total\":0}",
	"desc": "success"
}
```

### 5.request and response
Request data, return only one data.

**correct example**
```
// request
{
	"id": "1",
	"type": "symbol",
	"data": {"symbol": "USDT_TMX" }
}
```

```
// response
{
	"id": "1",
	"code": "1000",
	"data": "{\"symbol\":\"USDT_TMX\",\"high\":0,\"low\":0,\"staPrice\":0,\"lastPrice\":0,\"total\":0}",
	"desc": "success"
}
```

**error request**
```
// request
{
	"id": "1",
	"type": "symbol",
	"data": {
		"symbol": "XXX_XXX"
	}
}
```

```
// response
{
	"id": "1",
	"code": "1003",
	"data": "",
	"desc": "unkown symbol"
}
```

### 6.subscribe
To receive data you have to send a "sub" message first.

**correct example**
```
/// request
{
	"id": "1",
	"type": "sub",
	"data": {
		"channels": [{
			"symbol": "USDT_TMX",
			"channelNames": ["kline", "trade", "order"]
		}]
	}
}
```

```
/// response
{
	"id": "1",
	"code": "1000",
	"data": "",
	"desc": "success"
}
```

After subscribe,you will receive updates upon any change.

```
// push message
{
	"id": "1",
	"code": "1000",
	"data": "[{
             	"symbol": "USDT_TMX",
             	"num": 9,
             	"price": 0.01,
             	"type": "SELL",
             	"time": "1533971821"
             }]",
	"desc": "success"
}
```

**error example**
```
/// request
{
	"id": "1",
	"type": "sub",
	"data": {
		"channels": [{
			"symbol": "USDT_TMX",
			"channelNames": ["xxxx"]
		}]
	}
}
```

```
/// response
{
	"id": "1",
	"code": "1002",
	"data": "",
	"desc": "not support"
}
```

### 7.unsubscribe
To stop receiving data from a channel you have to send a "unsubscribe" message.

**correct example**
```
// request
{
	"id": "1",
	"type": "unsub",
	"data": {
		"channels": [{
			"symbol": "USDT_TMX",
			"channelNames": ["kline"]
		}]
	}
}
```

```
// response
{
	"id": "1",
	"code": "1000",
	"data": "",
	"desc": "success"
}
```

**error example**

```
{
	"id": "1",
	"type": "unsub",
	"data": {
		"channels": [{
			"symbol": "USDT_TMX",
			"channelNames": ["xxxx"]
		}]
	}
}
```

```
/// response
{
	"id": "1",
	"code": "1002",
	"data": "",
	"desc": "not support"
}
```

## API Reference

### request tickers

* **description**
  Get ticker of all markets.

* **request message**
    * **id**: [string] client genrate unique id, used to differentiate requests.
    * **type**: [string] "symbol/all"
        ```
        {
            "id": "1", //client genrate unique id, used to differentiate requests.
            "type": "symbol/all"
        }
        ```

* **success response(after unzipped):**  
    * **id**: [string] same as the client's request id.
    * **code**: [string] response coded, sucess, other code express exception occured.
    * **data**: [string] response data, detail definition will be defined in the following sections.
    * **desc**: [string] detailed code message 
        ```
           {
           	"id": "1",
           	"code": "1000",
           	"data": "[{\"symbol\":\"USDT_TMX\",\"high\":0,\"low\":0,\"staPrice\":0,\"lastPrice\":0,\"total\":0}]",
           	"desc": "success"
           }
        ```
 
### request ticker{symbol}

* **description**
  Get ticker of one market.

* **request message**
    * **id**: [string] client genrate unique id, used to differentiate requests.
    * **type**: [string] "symbol"
    * **data**: [string] see as follows
        ```
            {
                "id": "1",
                "type": "symbol",
                "data": {
                    "symbol": "USDT_TMX"    // market symbol
                }
            }
        ```

* **success response(after unzipped):**  
    * **id**: [string] same as the client's request id.
    * **code**: [string] response coded, sucess, other code express exception occured.
    * **data**: [string] response data, detail definition will be defined in the following sections.
    * **desc**: [string] detailed code message 
        ```
           {
           	"id": "1",
           	"code": "1000",
           	"data": "[{\"symbol\":\"USDT_TMX\",\"high\":0,\"low\":0,\"staPrice\":0,\"lastPrice\":0,\"total\":0}]",
           	"desc": "success"
           }
        ```
 
### request kline

* **description**
  Get the kline information of specified market.

* **request message**
    * **id**: [string] client genrate unique id, used to differentiate requests.
    * **type**: [string] "kline"
    * **data**: [string] see as follows
        ```
            {
                "id": "1",
                "type": "kline",
                "data": {
                    "symbol": "USDT_TMX",   // market symbol
                    "period": "1m"          // one of 1m,5m,15m,30m,1h,1d
                }
            }
        ```

* **success response(after unzipped):**  
    * **id**: [string] same as the client's request id.
    * **code**: [string] response coded, sucess, other code express exception occured.
    * **data**: [string] response data, detail definition will be defined in the following sections.
    * **desc**: [string] detailed code message 
        ```
            {
                "id": "1",
                "code": "1000",
                "data": "[[\"symbol\":\"USDT_TMX\", \"time\":1534425360, \"open\":0.04,\"maxPrice\":0.04,\"lowPrice\":0.04,\"close\"0.04,\"amount\":0], \"klineType\": \"1m\"]"...
                "desc": "success"
            }
        ```

### request depth

* **description**
  Get the depth information of specified market.

* **request message**
    * **id**: [string] client genrate unique id, used to differentiate requests.
    * **type**: [string] "depth"
    * **data**: [string] see as follows
        ```
            {
                "id": "1",
                "type": "depth",
                "data": {
                    "symbol": "USDT_BTC",   // market symbol
                    "limit": 2              // limit the number of returned
                }
            }
        ```

* **success response(after unzipped):**  
    * **id**: [string] same as the client's request id.
    * **code**: [string] response coded, sucess, other code express exception occured.
    * **data**: [string] response data, detail definition will be defined in the following sections.
    * **desc**: [string] detailed code message 
        ```
            {
                "id": "1",
                "code": "1000",
                "data": "{\"buy\":[[5850,3],[5800,10]],\"sell\":[[6310,0.2],[6500,0.4]]}",
                "desc": "success"
            }
        ```

### request orders

* **description**
  Get the trade information of market.

* **request message**
    * **id**: [string] client genrate unique id, used to differentiate requests.
    * **type**: [string] "order"
    * **data**: [string] see as follows
        ```
            {
                "id": "1",
                "type": "order",
                "data": {
                    "symbol": "USDT_TMX",   // market symbol
                    "limit": 2              // limit the number of returned
                }
            }
        ```

* **success response(after unzipped):**  
    * **id**: [string] same as the client's request id.
    * **code**: [string] response coded, sucess, other code express exception occured.
    * **data**: [string] response data, detail definition will be defined in the following sections.
    * **desc**: [string] detailed code message 
        ```
        {
            "id": "1",
            "code": "1000",
            "data": "[{\"symbol\":\"USDT_TMX\",\"num\":9,\"price\":0.01,\"type\":\"SELL\",\"time\":\"1533971821\"}...]",
            "desc": "success"
        }
        ```

### subscribe kline

* **description**
  subscribe the kline information of specified market.

* **request message**
    * **id**: [string] client genrate unique id, used to differentiate requests.
    * **type**: [string] "sub"
    * **data**: [string] see as follows
        ```
            {
                "id": "1",      
                "type": "sub",
                "data": {
                    "channels": [{
                        "symbol": "USDT_TMX",
                        "channelNames": ["kline"]
                    }]
                }
            }
        ```

* **success response(after unzipped):**  
    * **id**: [string] same as the client's request id.
    * **code**: [string] response coded, sucess, other code express exception occured.
    * **data**: [string] response data, detail definition will be defined in the following sections.
    * **desc**: [string] detailed code message 
        ```
            {
                "id": "1",
                "code": "1000",
                "data": "",
                "desc": "success"
            }
        ```
* **pushed message(after unzipped):**
  Websocket server pushed messge, such as:
  
        ```
            {
                "id": "1",
                "code": "1000",
                "data": "[[\"symbol\":\"USDT_TMX\", \"time\":1534425360, \"open\":0.04,\"maxPrice\":0.04,\"lowPrice\":0.04,\"close\"0.04,\"amount\":0], \"klineType\": \"1m\"]"...
                "desc": "success"
            }
        ```

### subscribe depth

* **description**
  subscribe the depth information of specified market.

* **request message**
    * **id**: [string] client genrate unique id, used to differentiate requests.
    * **type**: [string] "sub"
    * **data**: [string] see as follows
        ```
            {
                "id": "1",      
                "type": "sub",
                "data": {
                    "channels": [{
                        "symbol": "USDT_TMX",
                        "channelNames": ["depth"]
                    }]
                }
            }
        ```

* **success response(after unzipped):**  
    * **id**: [string] same as the client's request id.
    * **code**: [string] response coded, sucess, other code express exception occured.
    * **data**: [string] response data, detail definition will be defined in the following sections.
    * **desc**: [string] detailed code message 
        ```
            {
                "id": "1",
                "code": "1000",
                "data": "",
                "desc": "success"
            }
        ```
* **pushed message(after unzipped):**
  Websocket server pushed messge, such as:
  
        ```
            {
                "id": "1",
                "code": "1000",
                "data": "{\"buy\":[[5850,3],[5800,10]],\"sell\":[[6310,0.2],[6500,0.4]]}",
                "desc": "success"
            }
        ```

### subscribe orders 

* **description**
  subscribe the trade information of specified market.

* **request message**
    * **id**: [string] client genrate unique id, used to differentiate requests.
    * **type**: [string] "sub"
    * **data**: [string] see as follows
        ```
            {
                "id": "1",      
                "type": "sub",
                "data": {
                    "channels": [{
                        "symbol": "USDT_TMX",
                        "channelNames": ["order"]
                    }]
                }
            }
        ```

* **success response(after unzipped):**  
    * **id**: [string] same as the client's request id.
    * **code**: [string] response coded, sucess, other code express exception occured.
    * **data**: [string] response data, detail definition will be defined in the following sections.
    * **desc**: [string] detailed code message 
        ```
            {
                "id": "1",
                "code": "1000",
                "data": "",
                "desc": "success"
            }
        ```
* **pushed message(after unzipped):**
  Websocket server pushed messge, such as:
  
        ```
            {
                "id": "1",
                "code": "1000",
                "data": "[{\"symbol\":\"USDT_TMX\",\"num\":9,\"price\":0.01,\"type\":\"SELL\",\"time\":\"1533971821\"}...]",
                "desc": "success"
            }
        ```

### unsubscribe

* **description**
  unsubscribe the information of specified channel.

* **request message**
    * **id**: [string] client genrate unique id, used to differentiate requests.
    * **type**: [string] "unsub"
    * **data**: [string] see as follows
        ```
        {
            "id": "1",
            "type": "unsub",
            "data": {
                "channels": [{
                    "symbol": "USDT_TMX",
                    "channelNames": ["kline"]
                }]
            }
        }
        ```

* **success response(after unzipped):**  
    * **id**: [string] same as the client's request id.
    * **code**: [string] response coded, sucess, other code express exception occured.
    * **desc**: [string] detailed code message 
        ```
        {
            "id": "1",
            "code": "1000",
            "data": "",
            "desc": "success"
        }
        ```
