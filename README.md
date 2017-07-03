# Fairlay Private API Documentation v0

All requests must be send via TCP in the following format

    signature|nonce|userid|requestID|requestData

every request must end with "<"ENDOFDATA">"

You must sign "nonce|userid|requestID|requestData" with your RSA key and provide your signature with all requests.

This is a sample GETBALANCE Request:

uiskPc9GqWAdUt1hJ+4VeuxyCNtbs0iY3kSbGQZ7HvwHkCe5H2n9UcX5v1Gl7Mb99XnhBiVzkne5bdkgmLdnlzzEBPLYnt8zwWHcqxXvIYiDpLQhsVfdkvyYwH1zgrUwAZu08VmY0ax34zxjXJdK68TVs9Y1m7akXkw5/NvIwXk=|635972931146604657|100012|22|<ENDOFDATA>

All answers are returned in the following format

 >   signature|nonce|serverID|Response 

The response is usually JSON-encoded:

> rfV2ylPLbDbBKEy0yi71xZS/fZbyozd7+smpit1cR8ZeB0qo+stRDttTkCxqF0towkS0bf3lkg4amSbvka6K//QX/F1BHdFNqpkFXQHB9jh2eyR2WgXblKeGRbgw+mma+1P/kKNBtf3qhOUiqOgzONwlTEusiLzqHil6HLTQTKY=|635972681796848020|66|{"PrivReservedFunds":334.0493827437273000001,"MaxFunds":0.0,"PrivUsedFunds":207.1029999999836,"AvailableFunds":126.9463827437437000001}

If there is an error, the server returns an error message starting with "XError: " for general errors or "YError: " if there was an error in a subtask of a bulk change order reqeust. 


## USE Another API Account

By default you use the native API Account 0.  If you want to use a different API Account, you have to add 1000 * API Account ID  to the request ID. 

Example:

If you like to get your balance using API Account #1 you call:

signature|nonce|userid|1022|

Note that you need to register API Account #1 via the fairlay website before you can use it. Normal users do not have the private key to use their native API Account #0.


## GETPUBLICKEY

> wvcdZiLhZj2bo9r77sCL7YvvQ0x9ziUq2QsQJ3e1zpdWVW31wIE5jQwmBmPtNeRtLyErwUB10m9L3ZzQuUJLLpC9vZN1VjOH1LZnFw3JlcV5xTI+qUTiDRlq4wQ+9LCKPeY9u9DPKoJRSGSYP+ZSpBNk5xQ7Af/ehplWPKT6X4A=|635978817532384436|777888|GETPUBLICKEY

Response:  returns the server's public key in XML format.

## GETSERVERTIME 

reqID: 2

reqData: None

Response: returns the server's time in ticks.

Note: the server time can also be interfered from the nonce in every response.

## GETMARKET 

retrieves a single market

reqID: 6

reqData: long MarketID

response: json encoded MarketX Object

## TRANSFERFUNDS 


transfers funds to another user / this is also used to request withdrawals

reqID: 81

reqData: json encoded MUserTransfer Object

response: json encoded MUserTransfer Object

example:
signature|nonce|userid|81| {"FromU":1001001,"ToU":1001002,"Descr":"test transfer","TType":1,"Amount":2.0}

FromU: must match your userid
ToU: must be an existing user
TType:  custom field, can have any int valu

## SETAPIACCOUNTTOREADONLY 

reqID: 49

reqData: None

response: "success"

Example:

signature|nonce|userid|5049|

This will permanently set your API Account #5 to Read Only. Note that this cannot be undone.
You can also set your API Account #0 to read only. 

## GETMARKETSORDERBOOK  

reqID: 67

reqData: json encoded MarketFilter Object

response: long/string Dictionary  with market IDs and orderbooks.

Example:

signature|nonce|userid|67|{"Cat":0,"RunnerAND":["Arsenal","Chelsea"],"TitleAND":null,"TitleNOT":["Corners","Throwin"],"Comp":"Premier Leag","TypeOR":null,"PeriodOR":[1],"SettleOR":null,"ToSettle":false,"OnlyMyCreatedMarkets":false,"Descr":null,"ChangedAfter":"2016-01-01T22:01:01","SoftChangedAfter":"0001-01-01T00:00:00","OnlyActive":false,"MinPop":0.0,"MaxMargin":103.0,"NoZombie":false,"FromClosT":"2016-05-01T00:00:00","ToClosT":"0001-01-01T00:00:00","FromID":0,"ToID":100,"SortPopular":false}

## CREATEMARKET   (NOT UP TO DATE)

https://github.com/Fairlay/CSharpSampleClient-Main-/blob/master/Market.cs

reqID: 11
reqData: json encoded MarketX Object

response: long market ID

example:

signature|nonce|userid|11|{"Comp":"SCOTUS","Descr":"This market resolves to Yes, if the Senate confirms Merrick Garland’s Supreme Court nomination before President Obama leaves the office. The resolution date of this market may be accelerated. Bets matched after the antedated resolution date will be voided. https://www.coindesk.com","Title":"Will Senate confirm Merrick Garland's SCOTUS nomination?","CatID":15,"ClosD":"2016-11-01T00:00:00","SettlD":"2017-01-20T00:00:00","Ru":[{"Name":"Yes","InvDelay":0,"VisDelay":6000},{"Name":"No","InvDelay":0,"VisDelay":6000}],"_Type":2,"_Period":1,"SettlT":0,"Comm":0.02,"PrivCreator":784741,"CreatorName":"USERNAME"}

Comp: Competition
CatID: Category (for a list of all Categories, please visit https://github.com/Fairlay/CSharpSampleClient-Main-/blob/master/Market.cs)
ClosD:  Closing Date
SettlD: Resolution Date
Ru:  InvDelay must always be 0, VisDelay must always be set to 6000
_Type: for a list of all MarketTypes please visit https://github.com/Fairlay/CSharpSampleClient-Main-/blob/master/Market.cs
_Period:  for a list of all MarketPeriods please visit https://github.com/Fairlay/CSharpSampleClient-Main-/blob/master/Market.cs
SettlT:  for a list of all SettleTypes please visit https://github.com/Fairlay/CSharpSampleClient-Main-/blob/master/Market.cs
Comm:  0.02 - must always be 0.02 in order to be listed on Fairlay. Please contact info@fairlay.com if you need a reduced commission.
PrivCreator:  must match your userid
CreatorName:  your public name of your choice


## CANCELALLORDERS 

reqID: 16

reqData: None

response: how many unmatched orders were cancelled:   "5 Orders were cancelled"

cancels all open orders on all markets

## SETLMSRMARKETMAKER 

creates an automated LMSR market maker on a specific market

reqID: 73

reqData: json encoded LMSR Object

response: json encoded LMSR Object

example: 

{"UserID":777888,"MarketID":61659266392,"Runner":5,"Enabled":true,"InitShareLimit":350.0,"B":1300.0,"CancelAll":"2016-06-05T13:34:56", "ShareStop":1400.0,"InitProb":[0.3,0.1,0.2,0.2,0.2],"DiminishBack":[0.01,0.01,0.01,0.0,0.02],"DiminishLay":[0.0,0.0,0.0,0.01,0.01],"coolOffSeconds":36000.0,"coolOffFactor":4.0}

Default values
{"UserID":[userid],"MarketID":61659266392,"Runner":5,"Enabled":true,"InitShareLimit":300.0,"B":1000.0,"CancelAll":"2016-06-05T13:34:56", "ShareStop":9999.0,"InitProb":[0.2,0.2,0.2,0.2,0.2],"DiminishBack":[0.00,0.00,0.00,0.0,0.00],"DiminishLay":[0.01,0.01,0.01,0.01,0.01],"coolOffSeconds":1.0,"coolOffFactor":2.0}

UserID: must match your userid
MarketID:  must be provided
Runner:  # of Runners / must match the # of runners of the market
Enabled: must be set to true
InitShareLimit (must be > 1):    Shares that are offered in one order.  Stake + Winnings from each order are 350mBTC
B (must be > 10):    ~ is proportional to the maxium possible loss of the market maker if it starts at 50/50
CancelAll (must be set to a future date):   Date where the market maker stops. Set to year 2100 if the mm should run forever
ShareStop (must be > 1):   amount of exposure in shares before the market maker stops.  Should be set higher than  B in regular cases.
InitProb:   the initial probability estimation for all runners
DiminishBack (must be non-negative):   In general the LMSR market maker runs on 0% margin, i.e. it doesn't make any profit.  If more margin should be added, you can worsen the odds for each bid orders for every runner.  0.01 worsens bid odds from  80% to 81%  (or 1.25 to 1.2345)  for example. 
DiminishLay:   same for all ask orders. 

cool off adds temporary additional margin to markets with increased activity and should be applied to markets that can have exogenous shocks or where real probabilities can deviate from the initial probability distribution. 

coolOffFactor (must be >=1):    if set to 4.0 the odds will worsen 4.0 times more than expected from the usual lmsr market maker.    
coolOffSeconds (must be >=1):    The time after which the coolOff period will be over. If set to 36000 the additional market margin will reduce step by step over an period of 10 hours. 

If no cool off is required, set coolOffSeconds to 1.


Disable a MM by setting Enabled = false and other variables to a valid value. 

## GETLMSRMARKETMAKER 

retrieves all current LMSR Marketmaker

reqID: 70

reqData: -1

response: json encoded marketID/LMSR Dictionary



### OLD  IGNORE THE TEXT BELOW

## TRANSFERFUNDS 

transfers funds to another user. This is also used to request withdrawals.

reqID: 81

reqData: json encoded MUserTransfer Object

response: json encoded MUserTransfer Object



This is a free service to scrape all markets on Fairlay. For all POST requests (creating/changing markets and orders) use the paid API.
* The data is updated every 5 seconds;
* You have to accept GZIP, DEFLATE in your HEADER;
* Use the __*SoftChangedAfter*__ parameter for incremental calls. Retrieve the server time and only query markets that have changed after your last request;
* 12 incremental calls are allowed per minute (the __*SoftChangedAfter*__ parameter must be set to not more than 30 seconds in the past);
* 1 call without a proper __*SoftChangedAfter*__ parameter is allowed per IP per minute;
* When using the markets request make sure that your URI is not too long;
* All times are UTC;
* Strings are not case sensitive.

For more examples how this API should be used together with our paid API, please take a look at our sample clients in [C#](https://github.com/Fairlay/CSharpSampleClient/blob/master/GetAPI.cs) and [Python](https://github.com/Fairlay/PythonSampleClient/blob/master/client.py#L291).




## Servers

> `http://31.172.83.181:8080` <br/>
> `http://31.172.83.53:8080` *(is an alternate server with the same functionality)*



### Markets
Returns JSON encoded list of markets that apply to the given filter.

> `http://31.172.83.181:8080/free/markets/JSON-ENCODED-MARKETFILTER`

<br/>
MARKETFILTER object looks like this:

```json
{
        "Cat":0,
        "RunnerAND":["Arsenal","Chelsea"],
        "TitleAND":null,
        "TitleNOT":["Corners","Throwin"],
        "Comp":"Premier League",
        "TypeOR":null,
        "PeriodOR":[1],
        "SettleOR":null,
        "Descr":null,
        "ChangedAfter":"2016-01-01T22:01:01",
        "SoftChangedAfter":"0001-01-01T00:00:00",
        "OnlyActive":false,
        "NoZombie":false,
        "FromClosT":"2016-05-01T00:00:00",
        "ToClosT":"0001-01-01T00:00:00",
        "FromID":0,
        "ToID":10000
}
```

*Cat:* Category, see A2) for more information. 0 queries all Categories.

*TitleAND*: List of strings which all must appear in the title of the market.

*RunnerAND*: List of strings which all must be contained in at least one name of one runner of the market.

*TitleNOT: List of strings which none may appear in the title of the market.

*Comp*: Competition name. __null__ to get all competitions.

*TypeOr*: Market Types, see A2) for more information. Only the Market Types given will be returned. If set to null, all market types will be returned.

*PeriodOr*: Market Period, see A2) for more information.

*SettleOr*: Settlement Type, see A2) for more information.

*NoZombie*: If `True`, no empty markets will be returned (without any open order).

*Descr*: The given string must appear in the market description.

*ChangedAfter*: Return markets where the meta data was changed after the given date. Usually the Closing and Settlement
Dates of a market is the only data that changes.

*SoftChangedAfter*: Return all markets, where either the the meta data or the orderbook has changed since the given date.

*FromClosT*: Return markets where the closing time is greater than the given one.

*FromID* and *ToID*: Use for paging requests. __ToID__ has a default value of 300 if not set.


#### Examples:

Returns the first 100 non-empty soccer markets, where one of the runners is Portugal, the Title does not contain the words "Corners" or "Throwin" and the period of the match is full-time.

> `http://31.172.83.181:8080/free/markets/{"Cat":1,"NoZombie":true,"RunnerAND":["Portugal"], "TitleNOT":["Corners","Throwin"], "PeriodOR":[1],"FromID":0,"ToID":100}`

<br/>

Returns all active tennis matches of the type Over/Under or Outright where the odds or market data have changed after June 1st 12:01:30pm.

>`http://31.172.83.181:8080/free/markets/{"Cat":2,"TypeOr":[1,2],"SoftChangedAfter":"2016-06-01T12:01:30","OnlyActive":true,"ToID":10000}`

<br/>

Returns all active non-empty markets.

>`http://31.172.83.181:8080/free/markets/{"OnlyActive":true,"NoZombie":true,"ToID":100000}`

<br/>

### Competitions

Returns all competitions in the selected category.

> `http://31.172.83.181:8080/free/comps/CATEGORYID`

<br/>

Example for all soccer competitions:
> `http://31.172.83.181:8080/free/comps/1`

<br/>


