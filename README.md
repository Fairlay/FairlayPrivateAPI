
# Introduction

The Fairlay Private API allows you to POST requests (creating/changing markets and orders, transfering funds, etc.), costs 0.1mBTC per 100.000 requests, see below on how to create your developer API account. This is the low level documentation, if you need a working sample or are more comfortable reading code rather than specifications, head over to https://github.com/Fairlay/FairlayDotNetClient

All requests must be send via TCP in the following format

```signature|nonce|userid|requestID|requestData<ENDOFDATA>```

Every request must end with ```<ENDOFDATA>```

You must sign ```nonce|userid|requestID|requestData``` with your RSA key and provide your signature with all requests.

This is a sample GETBALANCE Request:

```
uiskPc9GqWAdUt1hJ+4VeuxyCNtbs0iY3kSbGQZ7HvwHkCe5H2n9UcX5v1Gl7Mb99XnhBiVzkne5bdkgmLdnlzzEBPLYnt8zwWHcqxXvIYiDpLQhsVfdkvyYwH1zgrUwAZu08VmY0ax34zxjXJdK68TVs9Y1m7akXkw5/NvIwXk=|635972931146604657|100012|22|<ENDOFDATA>
``` 

All answers are returned in the following format
```
signature|nonce|serverID|Response 
``` 
The response is usually JSON-encoded:

``` rfV2ylPLbDbBKEy0yi71xZS/fZbyozd7+smpit1cR8ZeB0qo+stRDttTkCxqF0towkS0bf3lkg4amSbvka6K//QX/F1BHdFNqpkFXQHB9jh2eyR2WgXblKeGRbgw+mma+1P/kKNBtf3qhOUiqOgzONwlTEusiLzqHil6HLTQTKY=|635972681796848020|66|{"PrivReservedFunds":334.049,"MaxFunds":0.0,"PrivUsedFunds":207.12,"AvailableFunds":126.946}``` 

If there is an error, the server returns an error message starting with **XError:** for general errors or **YError:** if there was an error in a subtask of a bulk change order request. 

```

Please note the following rules regarding the order matching:

When two open orders are matched, a Matched Order is created in the PENDING state.
If the maker of the bet cancels his bet within a certain time period the bet goes into the state MAKERVOIDED and is void. The said time period is defined in each unmatched order and matched order as "makerCancelTime" or "makerCT". The maximum allowed time is also provided as VisDelay for each runner (which is usually 0, 3000 or 6000 milliseconds).
Every time the maker of a bet exerts his right to (maker)void a bet, a fee is deducted from his account. This fee is currently 300 requests or 0.003mBTC but will be increased if needed.
When a market is settled the orders go to one of the settled states VOID, WON, HALFWON, LOST or HALFLOST.
Decimal market go into the state DECIMALRESULT while the settlement value DecResult will be set.
Regarding Binary Markets, please note that if you place a lay (bid , 0) bet with stake 100 and odds 2.50 your liability will be 150mBTC. This is different to the Fairlay website, where the stake is always the liablity.

```
### Remember to use (YOUR API ID*1000)+Request ID  on requests. Example: If you create your API accont on Fairlay, site will set an ID to it, usually first API ID is 1. So a request to Get Orderbook with this account will be 1001. 

# API Calls

* [GET PUBLIC KEY](#getpublickey): "GETPUBLICKEY",
* [CREATE ACCOUNT](#20): 20
* [SET API ACCOUNT TO READONLY](#49): 49
* [SET FORCE NONCE TO FALSE](#49): 44
* [GET ORDERBOOK](#1): 1
* [GET SERVER TIME](#2): 2
* [GET ORDERBOOKS](#4): 4
* [GET MARKET](#6): 6
* [GET MARKETS](#7): 7
* [CREATE MARKET](#11): 11
* [CLEAR ALL ORDERS](#16): 16
* [CREATE ORDER](#62): 62
* [CREATE ORDER WITH CANCEL TIME](#13): 13
* [CANCEL ORDER](#15): 15
* [CANCEL ORDER WITH MATCHES](#75): 75
* [CANCEL MATCHED ORDER](#09): 9
* [CONFIRM MATCHED ORDER](#08): 8
* [CANCEL ORDERS ON MARKET](#10): 10
* [CANCEL ALL ORDERS](#16): 16
* [CHANGE ORDER](#17): 17
* [CHANGE ORDER CANCEL TIME](#18): 18
* [GET ME](#21): 21
* [GET MY BALANCE](#22): 22
* [GET NEW ORDERS](#24): 24
* [GET UNMATCHED ORDERS](#25): 25
* [GET MATCHED ORDERS](#27): 27
* [BULK CHANGE ORDER](#109): 109
* [GET MARKETS ORDERBOOK](#67): 67
* [SET MARKET MAKER](#73): 73
* [TRANSFER FUNDS/WITHDRAW](#81): 81
* [GET USER TRANSACTIONS](#82): 82
* [CHANGE MARKET CLOSING TIME](#84): 84
* [SETTLE MARKET](#86): 86


## <a name="getpublickey">GET PUBLIC KEY</a> (GETPUBLICKEY)

```signature|nonce|id|GETPUBLICKEY```

Response:  returns the server's public key in XML format.


## <a name="20">CREATE ACCOUNT</a> (20) 

> ```signature|nonce|userid|20|<RSAKeyValue><Modulus>1g1p6dleaZfBac+aKDVfDwnh2AN9l/GW9qYuDDVRg8v9muUs7qoIcYqaZbuZq792MB1kqmWvyQui4a9HLodFbAwowBQ+IVceNYnEUFVyVrIgfzUWVvJ5kgxTDCNV3Z0xSq8GuqD1wpdSAWi4vPvmWg3KCjbbr30MzWaniNLauRU=</Modulus>
> <Exponent>AQAB</Exponent>
> </RSAKeyValue>|999999```

Returns:  User info object


Note: Use  https://superdry.apphb.com/tools/online-rsa-key-converter  to convert PEM RSA keys to XML format.

## <a name="21">GET ME</a> (21) 

```
signature|nonce|userid|21
```
Returns:  User info object containing last connections, withdraw adresses, market makers, funds, API Accounts, screen name and ID.

## <a name="22">GET MY BALANCE</a> (22) 

```
signature|nonce|userid|22
```
Returns:  JSON object containing account balance information:

**PrivReservedFunds**: Total account funds in mBTC.
**PrivUsedFunds**: Amount of balance placed on opened markets in mBTC.
**AvailableFunds**: Amount of balance available to use in mBTC.
**SettleUsed**: Amount retained due to settlement in mBTC.
**CreatorUsed**: Amount retained by market creation in mBTC.
**RemainingRequests**: Requests remaining before new charge.


## <a name="49">SET API ACCOUNT TO READONLY</a> (49)

```
signature|nonce|userid|5049|
```

this will permanently set your API Account #5 to Read Only. This cannot be undone.

You can also set your API Account #0 to read only. But be careful with it:

```
signature|nonce|userid|49|
```

## <a name="44">SET FORCE NONCE TO FALSE</a> (44)

```
signature|nonce|userid|44|false
```

this will allow you to submit requests without correct nonce.




## <a name="2">GET SERVER TIME </a>(2)

```
signature|nonce|userid|1002
```

Response: returns the server's time in ticks.

```636451764120705300```

Note: the server time can also be interfered from the nonce in every response.

## <a name="1">GET ORDERBOOK</a> (1)

```signature|nonce|userid|1|marketID```

Response:  An array containing one object with orderbooks for each runner in market.
```
[{ "Bids": [ [3.954, 3.75] ], "Asks": [ [7.203, 2.06156] ], "S": 1 }, { "Bids": [ [5.627,2.63] ], "Asks": [], "S": 1}]
```
This is a return from a market with 2 runners.

## <a name="4">GET ORDERBOOKS</a>(4)

```
signature|nonce|userid|4|[marketID1, marketID2]
```
```
signature|nonce|userid|4|[121046999588, 121046400020]
```

Response:  An array of objects containing orderbooks for each runner in market.

```
[{ "Bids": [ [3.954, 3.75] ], "Asks": [ [7.203, 2.06156] ], "S": 1 }, { "Bids": [ [5.627,2.63] ], "Asks": [], "S": 1},{ "Bids": [ [3.954, 3.75] ], "Asks": [ [7.203, 2.06156] ], "S": 1 }, { "Bids": [ [5.627,2.63] ], "Asks": [], "S": 1}]
```

This is a return from 2 markets with 2 runners.

## <a name="7">GET MARKETS</a> (7)

```
signature|nonce|userid|7|{
"Cat":0,
"RunnerAND":["Arsenal","Chelsea"],
"TitleAND":null,
"TitleNOT":["Corners","Throwin"],
"Comp":"Premier Leag",
"TypeOR":null,
"PeriodOR":[1],
"SettleOR":null,
"ToSettle":false,
"OnlyMyCreatedMarkets":false,
"Descr":null,
"ChangedAfter":"2016-01-01T22:01:01",
"SoftChangedAfter":"0001-01-01T00:00:00",
"OnlyActive":false,
"MinPop":0.0,
"MaxMargin":103.0,
"NoZombie":false,
"FromClosT":"2016-05-01T00:00:00",
"ToClosT":"0001-01-01T00:00:00",
"FromID":0,
"ToID":100,
"SortPopular":false}
```

Returns all markets that apply to the given filter. 

**Cat**: is the Category, 0 queries all Categories.
**RunnerAND**: All strings provided must be contained in the names of the runners of the market.
**TitleNOT**: None of the strings may appear in the title of the market
**TypeOr**: Only the Market Types given will be returned. If set to null, all market types will be returned. See A2) for Market Types
**PeriodOr**: Similiar to TypeOr See A2) for Market Periods
**SettleOr**: See A2)  for Settlement Types
**ToSettle**: If set to true, it will only return unsettled markets where the resolution time has passed.
**NoZombie**: No empty markets (without any open order)
**Descr**: The given string must appear in the market description.
**ChangedAfter**: Only returns markets, where the meta data was changed after the given date. Usually the Closing and Settlement Dates of a market is the only data that changes.
**SoftChangedAfter**: Returns all markets, where either the the meta data or the orderbook has changed since the given date.

Returns:  a list of market objects

## <a name="6">GET MARKET</a> (6)
```
signature|nonce|userid|1006|marketid
```
Returns: market object

## <a name="67">GET MARKETS ORDERBOOK</a> (67)

```
signature|nonce|userid|67|{
"Cat":0,
"RunnerAND":["Arsenal","Chelsea"],
"TitleAND":null,
"TitleNOT":["Corners","Throwin"],
"Comp":"Premier Leag",
"TypeOR":null,
"PeriodOR":[1],
"SettleOR":null,
"ToSettle":false,
"OnlyMyCreatedMarkets":false,
"Descr":null,
"ChangedAfter":"2016-01-01T22:01:01",
"SoftChangedAfter":"0001-01-01T00:00:00",
"OnlyActive":false,
"MinPop":0.0,
"MaxMargin":103.0,
"NoZombie":false,
"FromClosT":"2016-05-01T00:00:00",
"ToClosT":"0001-01-01T00:00:00",
"FromID":0,
"ToID":100,
"SortPopular":false}
```

Returns: A long-string Dictionary with the marketID - orderbook key-value pair.


## <a name="11">CREATE MARKET</a> (11) 
```
signature|nonce|userid|11|{
"Comp":"Politics",
"Descr":"This market resolves to Yes, if the Senate confirms Merrick Garland’s Supreme Court nomination before President Obama leaves the office.",
"CatID":15,
"ClosD":"2016-11-01T00:00:00",
"SettlD":"2017-01-20T00:00:00",
"Ru":[{"Name":"Yes","InvDelay":0,"VisDelay":6000},{"Name":"No","InvDelay":0,"VisDelay":6000}],
"_Type":2,
"_Period":1,
"SettlT":0,
"Comm":0.02,
"PrivCreator":784741,
"CreatorName":"USERNAME"}
```
**Comp**: Competition
**CatID**: Category (see A2))
**ClosD**:  Closing Date
**SettlD**: Resolution Date
**Ru**:  InvDelay must always be 0, VisDelay must always be set to 6000
_Type: See A2)
_Period: See A2)
**SettlT**:  For settlement types see A2)
**Comm**:  0.02 - must always be 0.02.
**PrivCreator**:  must match your userid and will remain privat
**CreatorName**:  must match your Username and is public

Returns:  Market ID (no json)

## <a name="62">CREATE ORDER</a> (62) 
Note: (use requestID 61 for market making) 

```
signature|nonce|userid|62|marketid|runnerid|bidorask|price|amount|type|matchesubuser|pendingperiod
```

example:
```
 signature|nonce|userid|1062|71601335718|0|1|1.56|100.1|2|testorder|6000
```

**Market ID**:  on which market the order will be placed
**Runner ID**:  on which runner the order will be placed: 0 for the 1st runner, 1 for the 2nd runner of the market and so on.
**BidorAsk**:   0 places a bid order, against the runner.  1 places a bet on the runner to win. 
**price**:  the decimal odds at which the order is placed
**amount**:  the amount in mBTC
**matchedsubuser**: custom string
**type**:  see unmatched order types in A2)   
**pendingperiod**: should be set to 6000ms

Returns: unmatched order object
https://github.com/Fairlay/FairlayDotNetClient/blob/master/src/Private/Datatypes/UnmatchedOrder.cs

## <a name="75">CANCEL ORDER</a> (75)

```
signature|nonce|userid|75|marketid|runnerid|orderid
```

Returns: a list of all corresponding matched orders objects for the cancelled unmatched order.

## <a name="10">CANCEL MARKET ORDERS</a> (10)

```
signature|nonce|userid|10
```

Returns:  how many unmatched orders were cancelled:   
```2 Orders were cancelled```

## <a name="16">CANCEL ALL ORDERS</a> (16)

```
signature|nonce|userid|16
```

Returns:  how many unmatched orders were cancelled:   
```5 Orders were cancelled```


## <a name="17">CHANGE ORDER</a> (17)

Is a combination of "cancel order" and "create order"

```
signature|nonce|userid|17|marketid|runnerid|orderid|price|amount
```

Returns: unmatched order object
https://github.com/Fairlay/FairlayDotNetClient/blob/master/src/Private/Datatypes/UnmatchedOrder.cs


## <a name="109">BULK CHANGE ORDER MAKER</a> (109)

Changes, cancels and creates many orders at once.  

```
signature|nonce|userid|109|Array of Request ChangeOrder object
```

https://github.com/Fairlay/FairlayDotNetClient/blob/master/src/Private/Datatypes/REQChangeOrder.cs

example:

```
[{"Mid":73911957931,"Rid":13,"Oid":635980470611670494,"Type":1,"Am":1665.0,"Pri":5.587,"Sub":null,"Boa":0,"Mct":0},{"Mid":73395769791,"Rid":0,"Oid":635980470611720522,"Am":9355.0,"Pri":0.0,"Sub":null,"Boa":1,"Mct":0},{"Mid":73569399509,"Rid":0,"Oid":6359804706645670494,"Am":193.0,"Pri":49.800,"Boa":0,"Mct":0},{"Mid":73570791053,"Rid":2,"Oid":-1,"Am":1514.0,"Pri":6.954,"Sub":"myLine","Boa":1,"Mct":6000}]
```
Returns: an array of REQChangeOrder objects. The Res property of each object contains the status of each bet. Res is either a serialized UnmatchedOrder  order object  (https://github.com/Fairlay/FairlayDotNetClient/blob/master/src/Private/Datatypes/UnmatchedOrder.cs)  or contains an error message like  (Res = ) "YError: Market closed"  for example.

## <a name="24">GET NEW ORDERS</a> (24)

```
signature|nonce|userid|24|TimeinTicks
```

**TimeinTicks**: Time given in ticks, all orders after the given tick will be returned

Returns: all unmatched orders and matched orders that were created, cancelled or changed after the given time.

https://github.com/Fairlay/FairlayDotNetClient/blob/master/src/Private/Datatypes/ReturnUOrder.cs
https://github.com/Fairlay/FairlayDotNetClient/blob/master/src/Private/Datatypes/ReturnMOrder.cs

## <a name="25">GET UNMATCHED ORDERS</a> (25)

```
signature|nonce|userid|25|TimeinTicks
```

**TimeinTicks**: Time given in ticks, all orders after the given tick will be returned

Returns: all unmatched orders that were created, cancelled or changed after the given time.
https://github.com/Fairlay/FairlayDotNetClient/blob/master/src/Private/Datatypes/ReturnUOrder.cs

## <a name="27">GET MATCHED ORDERS</a> (27)

```
signature|nonce|userid|27|TimeinTicks
```

**TimeinTicks**: Time given in ticks, all orders after the given tick will be returned

Returns: all matched orders that were created, cancelled or changed after the given time.
https://github.com/Fairlay/FairlayDotNetClient/blob/master/src/Private/Datatypes/ReturnMOrder.cs

## <a name="81">TRANSFER FUNDS/WITHDRAW</a> (81)

```
signature|nonce|userid|81|transferobject
```

Example for transfer object: 

```
{"FromU":777888,"ToU":777889,"Descr":"test transfer","TType":1,"Amount":2.0}
```

**FromU**: must match your userid
**ToU**: must be an existing user
**TType**:  custom field, can have any int value

Returns:  transferobject if successful


## <a name="82">GET USER TRANSACTIONS</a> (82)

Get all user transactions since given time in ticks.

```
signature|nonce|userid|82|TimeinTicks
```

Returns: An array of transactions.
https://github.com/Fairlay/FairlayDotNetClient/blob/master/src/Private/Datatypes/MUserTransfer.cs
Every transaction has currency IDs in it. 
https://github.com/Fairlay/FairlayDotNetClient/blob/master/src/Private/Datatypes/CurrencyIds.cs


## <a name="73">SET MARKET MAKER</a> (73)

This sets up an fully automated LMSR market maker.  Google the term to get familiar with the usage. 

```
signature|nonce|userid|73|lmsrmarketmakerobject
```

example for lmsrmarketmakerobject:

```
{"UserID":777888,
"Runner":5,
"Enabled":true,
"InitShareLimit":350.0,
"B":1300.0,
"CancelAll":"2016-06-05T13:34:56", 
"ShareStop":1400.0,
"InitProb":[0.3,0.1,0.2,0.2,0.2],
"DiminishBack":[0.01,0.01,0.01,0.0,0.02],
"DiminishLay":[0.0,0.0,0.0,0.01,0.01],
"coolOffSeconds":36000.0,"coolOffFactor":4.0}
```
https://github.com/Fairlay/FairlayDotNetClient/blob/master/src/Private/Datatypes/LMSR.cs

Default values:
```
{"UserID":[userid],
"Runner":5,
"Enabled":true,
"InitShareLimit":300.0,
"B":1000.0,
"CancelAll":"2016-06-05T13:34:56", 
"ShareStop":9999.0,
"InitProb":[0.2,0.2,0.2,0.2,0.2],
"DiminishBack":[0.00,0.00,0.00,0.0,0.00],
"DiminishLay":[0.01,0.01,0.01,0.01,0.01],
"coolOffSeconds":1.0,"coolOffFactor":2.0}
```

**UserID**: must match your userid
**MarketID**:  must be provided
**Runner**:  # of Runners / must match the # of runners of the market
**Enabled**: must be set to true
**InitShareLimit** (must be > 1):    Shares that are offered in one order.  Stake + Winnings from one order are 350mBTC
**B** (must be > 10):    ~ is about the same as the maxium possible loss of the market maker
**CancelAll** (must be set to a future date):   Date where the market maker stops. Set to year 2100 if the mm should run forever
**ShareStop** (must be > 1):   amount of exposure in shares before the market maker stops.  Should be set higher than  B in regular cases.
**InitProb**:   the initial probability estimation for all runners
**DiminishBack** (must be non-negative):   In general the LMSR market maker runs on 0% margin, i.e. it doesn't make any profit.  If more margin should be added, you can worsen the odds for each bid orders for every runner.  0.01 worsens bid odds from  80% to 81%  (or 1.25 to 1.2345)  for example. 
**DiminishLay**:   same for all ask orders. 

*Cool off adds temporary additional margin to markets with increased activity and should be applied to markets that can have exogenous shocks or where real probabilities can deviate from the initial probability distribution. *

**coolOffFactor** (must be >=1):    if set to 4.0 the odds will worsen 4.0 times more than expected from the usual lmsr market maker.    
**coolOffSeconds** (must be >=1):    The time after which the coolOff period will be over. If set to 36000 the additional market margin will reduce step by step over an period of 10 hours. 

If no cool off is required, set coolOffSeconds to 1.

**Disable a MM by setting Enabled = false and other variables to a valid value. **

## <a name="70">GET MARKET MAKER</a> (70)

gets the current LMSR MM from the user for a given market. 
```
signature|nonce|userid|70|marketid
```
example for lmsrmarketmakerobject:
```
{"UserID":777888,"MarketID":61659266392,"Runner":5,"Enabled":true,"InitShareLimit":350.0,"B":1300.0,"CancelAll":"2016-06-05T13:34:56", "ShareStop":1400.0,"InitProb":[0.3,0.1,0.2,0.2,0.2],"DiminishBack":[0.01,0.01,0.01,0.0,0.02],"DiminishLay":[0.0,0.0,0.0,0.01,0.01],"coolOffSeconds":36000.0,"coolOffFactor":4.0}
```

## <a name="84">CHANGE MARKET CLOSING TIME</a> (84)

Changes Closing and Settlement Date

```
signature|nonce|userid|84|ChangeTimeObject
```

Example:
```
{"MID":76650963889,
"ClosD":"2016-06-11T01:00:00Z",
"SetlD":"2016-06-11T03:00:00Z"}
```
https://github.com/Fairlay/FairlayDotNetClient/blob/master/src/Private/Datatypes/ChangeTimeReq.cs

Returns: MarketTime changed.

  
## <a name="85">GET SETTLEMENTS</a> (85)

Gets all bankroll adjustments after a given time. 
```
signature|nonce|userid|85|timeinticks
```
Returns: A JSON encoded Statement Object:

**long ID**:  is not unique globally; is the time in milliseconds from Jan 1th 2016 
**Descr** :  optional description, contains the market ID in case of a settlement.
**T** :  0-99  stands for a transfer (same like ttype in transfer object)  includes deposits & cashouts, 100 admin, 200  market  settlement w/o commission, 201 market settlement with commission, 250 unsettlement, 300 commission bonus)
**Am**  : amount credited
**Bank** : total bankroll after the adjustment.

## <a name="86">SETTLE MARKET</a> (86)

Settles a given market. Note that the total betting volume of the market will be deducted from the available balance for 2 to 3 days. Unless the user has special rights, a user can only settle markets he created. 
```
signature|nonce|userid|86|SettleRequestObject
```
Example:
```
signature|nonce|userid|86|{
"Mid":77588905280,
"Runner":0,
"Win":1,
"Half":false,
"Dec":0.0,
"ORed":0.0}
```
**Mid**:  is the market ID
**Runner**:  determines the Runner which won (0 means that the 1st Runner won, 1 means that the 2nd Runner won and so on). If a market shall be voided the Runner must be set to -1
**Win**:  Must be set to 1
**Half**:  should be set to "false". Only needed for  +- 0.25  and +-0.75  soccer  spread and over/under markets. If a market is half won or half lost, set Half to "true";
**Dec**:   If the market is not binary, but has a decimal outcome, this needs to be set to the result.  [Not supported yet]
**ORed**:   Odds reduction  [only for Horse racing - not needed in general]

Returns:  "Market settled"  or some kind of "XError: ..."
