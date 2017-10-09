The Fairlay Private API allows you to POST requests (creating/changing markets and orders, transfering funds, etc.), costs 0.1mBTC per 100.000 requests, [see below](https://github.com/Fairlay/FairlayDotNetClient#private-api) on how to create your developer API account. This is the low level documentation, if you need a working sample or are more comfortable reading code rather than specifications, head over to [https://github.com/Fairlay/FairlayDotNetClient](https://github.com/Fairlay/FairlayDotNetClient)

All requests must be send via TCP in the following format

> signature|nonce|userid|requestID|requestData

every request must end with "<"ENDOFDATA">"

You must sign "nonce|userid|requestID|requestData" with your RSA key and provide your signature with all requests.

This is a sample GETBALANCE Request:

> uiskPc9GqWAdUt1hJ+4VeuxyCNtbs0iY3kSbGQZ7HvwHkCe5H2n9UcX5v1Gl7Mb99XnhBiVzkne5bdkgmLdnlzzEBPLYnt8zwWHcqxXvIYiDpLQhsVfdkvyYwH1zgrUwAZu08VmY0ax34zxjXJdK68TVs9Y1m7akXkw5/NvIwXk=|635972931146604657|100012|22|<ENDOFDATA> 

All answers are returned in the following format

> signature|nonce|serverID|Response 

The response is usually JSON-encoded:

>  rfV2ylPLbDbBKEy0yi71xZS/fZbyozd7+smpit1cR8ZeB0qo+stRDttTkCxqF0towkS0bf3lkg4amSbvka6K//QX/F1BHdFNqpkFXQHB9jh2eyR2WgXblKeGRbgw+mma+1P/kKNBtf3qhOUiqOgzONwlTEusiLzqHil6HLTQTKY=|635972681796848020|66|{"PrivReservedFunds":334.0493827437273000001,"MaxFunds":0.0,"PrivUsedFunds":207.1029999999836,"AvailableFunds":126.9463827437437000001}

If there is an error, the server returns an error message starting with "XError: " for general errors or "YError: " if there was an error in a subtask of a bulk change order reqeust. 


# API Calls

## GETPUBLICKEY

> wvcdZiLhZj2bo9r77sCL7YvvQ0x9ziUq2QsQJ3e1zpdWVW31wIE5jQwmBmPtNeRtLyErwUB10m9L3ZzQuUJLLpC9vZN1VjOH1LZnFw3JlcV5xTI+qUTiDRlq4wQ+9LCKPeY9u9DPKoJRSGSYP+ZSpBNk5xQ7Af/ehplWPKT6X4A=|635978817532384436|777888|GETPUBLICKEY

Response:  returns the server's public key in XML format.


## GETSERVERTIME 2

> signature|nonce|userid|2

Response: returns the server's time in ticks.

Note: the server time can also be interfered from the nonce in every response.


## CREATE ACCOUNT 20 

> signature|nonce|userid|20|<RSAKeyValue><Modulus>1g1p6dleaZfBac+aKDVfDwnh2AN9l/GW9qYuDDVRg8v9muUs7qoIcYqaZbuZq792MB1kqmWvyQui4a9HLodFbAwowBQ+IVceNYnEUFVyVrIgfzUWVvJ5kgxTDCNV3Z0xSq8GuqD1wpdSAWi4vPvmWg3KCjbbr30MzWaniNLauRU=</Modulus><Exponent>AQAB</Exponent></RSAKeyValue>|999999
creation and all future requests.  

Returns:  User info object

Note: Use  https://superdry.apphb.com/tools/online-rsa-key-converter  to convert PEM RSA keys to XML format.


## CREATE API ACCOUNT 23

Example:  to create an API account call:

> signature|nonce|userid|23|API Account ID|<RSAKeyValue><Modulus>1g1p6dleaZfBac+aKDVfDwnh2AN9l/GW9qYuDDVRg8v9muUs7qoIcYqaZbuZq792MB1kqmWvyQui4a9HLodFbAwowBQ+IVceNYnEUFVyVrIgfzUWVvJ5kgxTDCNV3Z0xSq8GuqD1wpdSAWi4vPvmWg3KCjbbr30MzWaniNLauRU=</Modulus><Exponent>AQAB</Exponent></RSAKeyValue>

API Account ID:   can be any integer from 1 to 9. You can have up to 9 seperate API Accounts. Each API Account has it's own nonce, public key and it's own privileges.

returns "New APIUser Added"


## USE DIFFERENT API ACCOUNT (+1000)

By default you use API Account  0.  If you want to use a different API Account you have to add 1000 * API Account ID  to the request ID.

Example:
If you like to get your balance using API Account #5 you call:

> signature|nonce|userid|5022|

Note that you need to register API Account #5 before you can use it.


## SET API ACCOUNT TO READONLY 49

> signature|nonce|userid|5049|

this will permanently set your API Account #5 to Read Only. This cannot be undone.

You can also set your API Account #0 to read only. But be careful with it:

> signature|nonce|userid|49|


## GETMARKETS 7

> signature|nonce|userid|7|{"Cat":0,"RunnerAND":["Arsenal","Chelsea"],"TitleAND":null,"TitleNOT":["Corners","Throwin"],"Comp":"Premier Leag","TypeOR":null,"PeriodOR":[1],"SettleOR":null,"ToSettle":false,"OnlyMyCreatedMarkets":false,"Descr":null,"ChangedAfter":"2016-01-01T22:01:01","SoftChangedAfter":"0001-01-01T00:00:00","OnlyActive":false,"MinPop":0.0,"MaxMargin":103.0,"NoZombie":false,"FromClosT":"2016-05-01T00:00:00","ToClosT":"0001-01-01T00:00:00","FromID":0,"ToID":100,"SortPopular":false}

GETMARKETS returns all markets that apply to the given filter. 

Cat: is the Category, see A2) for more information. 0 queries all Categories.
RunnerAND: All strings provided must be contained in the names of the runners of the market.
TitleNOT: = None of the strings may appear in the title of the market
TypeOr:   Only the Market Types given will be returned. If set to null, all market types will be returned. See A2) for Market Types
PeriodOr: Similiar to TypeOr See A2) for Market Periods
SettleOr: See A2)  for Settlement Types
ToSettle:  If set to true, it will only return unsettled markets where the resolution time has passed.
NoZombie:  NoEmpty markets (without any open order)
Descr:   The given string must appear in the market description.
ChangedAfter:   Only returns markets, where the meta data was changed after the given date. Usually the Closing and Settlement Dates of a market is the only data that changes.
SoftChangedAfter:  returns all markets, where either the the meta data or the orderbook has changed since the given date.

Returns:  a list of market objects


## GETMARKETSORDERBOOK 67

> signature|nonce|userid|67|{"Cat":0,"RunnerAND":["Arsenal","Chelsea"],"TitleAND":null,"TitleNOT":["Corners","Throwin"],"Comp":"Premier Leag","TypeOR":null,"PeriodOR":[1],"SettleOR":null,"ToSettle":false,"OnlyMyCreatedMarkets":false,"Descr":null,"ChangedAfter":"2016-01-01T22:01:01","SoftChangedAfter":"0001-01-01T00:00:00","OnlyActive":false,"MinPop":0.0,"MaxMargin":103.0,"NoZombie":false,"FromClosT":"2016-05-01T00:00:00","ToClosT":"0001-01-01T00:00:00","FromID":0,"ToID":100,"SortPopular":false}

Returns: A long-string Dictionary with the marketID - orderbook key-value pair.


## GETMARKET 6

> signature|nonce|userid|6|marketid

Returns: market object


## CREATEMARKET 11

> signature|nonce|userid|11|{"Comp":"SCOTUS","Descr":"This market resolves to Yes, if the Senate confirms Merrick Garland’s Supreme Court nomination before President Obama leaves the office. The resolution date of this market may be accelerated. Bets matched after the antedated resolution date will be voided. https://www.coindesk.com","Title":"Will Senate confirm Merrick Garland's SCOTUS nomination?","CatID":15,"ClosD":"2016-11-01T00:00:00","SettlD":"2017-01-20T00:00:00","Ru":[{"Name":"Yes","InvDelay":0,"VisDelay":6000},{"Name":"No","InvDelay":0,"VisDelay":6000}],"_Type":2,"_Period":1,"SettlT":0,"Comm":0.02,"PrivCreator":784741,"CreatorName":"USERNAME"}

Comp: Competition
CatID: Category (see A2))
ClosD:  Closing Date
SettlD: Resolution Date
Ru:  InvDelay must always be 0, VisDelay must always be set to 6000
_Type: See A2)
_Period: See A2)
SettlT:  For settlement types see A2)
Comm:  0.02 - must always be 0.02.
PrivCreator:  must match your userid and will remain privat
CreatorName:  must match your Username and is public

Returns:  market id (no json)

# CREATEORDER 62 (use requestID 61 for market making) 

> signature|nonce|userid|12|marketid|runnerid|bidorask|price|amount|type|matchesubuser|pendingperiod

example:
> signature|nonce|userid|62|71601335718|0|1|1.56|100.1|2|testorder|6000

market id:  on which market the order will be placed
runner id:  on which runner the order will be placed: 0 for the 1st runner, 1 for the 2nd runner of the market and so on.
bidorask:   0 places a bid order, against the runner.  1 places a bet on the runner to win. 
price:  the decimal odds at which the order is placed
amount:  the amount in mBTC
matchedsubuser: custom string
type:  see unmatched order types in A2)   
pendingperiod: should be set to 6000ms

Returns: unmatched order object


## CANCELORDER 75

> signature|nonce|userid|75|marketid|runnerid|orderid

Returns: a list of all corresponding matched orders objects for the cancelled unmatched order. See A1) for the object specification.


## CANCELALLORDERS 16

> signature|nonce|userid|16

Returns:  how many unmatched orders were cancelled:   "5 Orders were cancelled"


## CHANGEORDER 17 

is a combination of cancelorder and createorder

> signature|nonce|userid|17|marketid|runnerid|orderid|price|amount

Returns: unmatched order object


## BULKCHANGEORDER MAKER 109 (experts)

Changes, cancels and creates many orders at once.  

> signature|nonce|userid|109|Array of REQChangeOrder object

https://github.com/Fairlay/FairlayDotNetClient/blob/master/src/Private/Datatypes/REQChangeOrder.cs

example:

> [{"Mid":73911957931,"Rid":13,"Oid":635980470611670494,"Type":1,"Am":1665.0,"Pri":5.587,"Sub":null,"Boa":0,"Mct":0},{"Mid":73395769791,"Rid":0,"Oid":635980470611720522,"Am":9355.0,"Pri":0.0,"Sub":null,"Boa":1,"Mct":0},{"Mid":73569399509,"Rid":0,"Oid":6359804706645670494,"Am":193.0,"Pri":49.800,"Boa":0,"Mct":0},{"Mid":73570791053,"Rid":2,"Oid":-1,"Am":1514.0,"Pri":6.954,"Sub":"myLine","Boa":1,"Mct":6000}]



Returns: an array of REQChangeOrder objects. The Res property of each object contains the status of each bet. Res is either a serialized UnmatchedOrder  order object  (https://github.com/Fairlay/FairlayDotNetClient/blob/master/src/Private/Datatypes/UnmatchedOrder.cs)  or contains an error message like  (Res = ) "YError: Market closed"  for example.

## GETNEW 24

> signature|nonce|userid|24|timeinticks

Returns: all unmatched orders and matched orders that were created, cancelled or changed after the given time.


## TRANSFERFUNDS 81

> signature|nonce|userid|81|transferobject

example for transferobject:   {"FromU":777888,"ToU":777889,"Descr":"test transfer","TType":1,"Amount":2.0}

FromU: must match your userid
ToU: must be an existing user
TType:  custom field, can have any int value

Returns:  transferobject if successful


## SETMM 73

this sets up an fully automated LMSR market maker.  Google the term to get familiar with the usage. 

> signature|nonce|userid|73|lmsrmarketmakerobject

example for lmsrmarketmakerobject:
{"UserID":777888,"MarketID":61659266392,"Runner":5,"Enabled":true,"InitShareLimit":350.0,"B":1300.0,"CancelAll":"2016-06-05T13:34:56", "ShareStop":1400.0,"InitProb":[0.3,0.1,0.2,0.2,0.2],"DiminishBack":[0.01,0.01,0.01,0.0,0.02],"DiminishLay":[0.0,0.0,0.0,0.01,0.01],"coolOffSeconds":36000.0,"coolOffFactor":4.0}

Default values
{"UserID":[userid],"MarketID":61659266392,"Runner":5,"Enabled":true,"InitShareLimit":300.0,"B":1000.0,"CancelAll":"2016-06-05T13:34:56", "ShareStop":9999.0,"InitProb":[0.2,0.2,0.2,0.2,0.2],"DiminishBack":[0.00,0.00,0.00,0.0,0.00],"DiminishLay":[0.01,0.01,0.01,0.01,0.01],"coolOffSeconds":1.0,"coolOffFactor":2.0}

UserID: must match your userid
MarketID:  must be provided
Runner:  # of Runners / must match the # of runners of the market
Enabled: must be set to true
InitShareLimit (must be > 1):    Shares that are offered in one order.  Stake + Winnings from one order are 350mBTC
B (must be > 10):    ~ is about the same as the maxium possible loss of the market maker
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

## GETMM 70

gets the current LMSR MM from the user for a given market. 

> signature|nonce|userid|70|marketid

example for lmsrmarketmakerobject:
{"UserID":777888,"MarketID":61659266392,"Runner":5,"Enabled":true,"InitShareLimit":350.0,"B":1300.0,"CancelAll":"2016-06-05T13:34:56", "ShareStop":1400.0,"InitProb":[0.3,0.1,0.2,0.2,0.2],"DiminishBack":[0.01,0.01,0.01,0.0,0.02],"DiminishLay":[0.0,0.0,0.0,0.01,0.01],"coolOffSeconds":36000.0,"coolOffFactor":4.0}

## CHANGETIME 84

Changes Closing and Settlement Date

> signature|nonce|userid|84|ChangeTimeObject

Example:   {"MID":76650963889,"ClosD":"2016-06-11T01:00:00Z","SetlD":"2016-06-11T03:00:00Z"}

returns: MarketTime changed.

  
## MENT 85
gets all bankroll adjustments after a given time. 

> signature|nonce|userid|85|timeinticks

returns a JSON encoded Statement Object:

long ID:  is not unique globally; is the time in milliseconds from Jan 1th 2016 
string Descr :  optional description, contains the market ID in case of a settlement.
int T :  0-99  stands for a transfer (same like ttype in transfer object)  includes deposits & cashouts, 100 admin, 200  market  settlement w/o commission, 201 market settlement with commission, 250 unsettlement, 300 commission bonus)
decimal Am  : amount credited
decimal Bank : total bankroll after the adjustment.

## SETTLEMARKET 86

Settles a given market. Note that the total betting volume of the market will be deducted from the available balance for 2 to 3 days. Unless the user has special rights, a user can only settle markets he created. 

> signature|nonce|userid|86|SettleRequestObject

returns  "Market settled"  or some kind of "XError: ..."

Example:

> 86|{"Mid":77588905280,"Runner":0,"Win":1,"Half":false,"Dec":0.0,"ORed":0.0}

Mid:  is the market ID
Runner:  determines the Runner which won (0 means that the 1st Runner won, 1 means that the 2nd Runner won and so on). If a market shall be voided the Runner must be set to -1
Win:  Must be set to 1
Half:  should be set to "false". Only needed for  +- 0.25  and +-0.75  soccer  spread and over/under markets. If a market is half won or half lost, set Half to "true";
Dec:   If the market is not binary, but has a decimal outcome, this needs to be set to the result.  [Not supported yet]
ORed:   Odds reduction  [only for Horse racing - not needed in general]


## TRANSFERFUNDS 

Transfers funds to another user / this is also used to request withdrawals

reqID: 81

reqData: json encoded MUserTransfer Object

response: json encoded MUserTransfer Object

example:
> signature|nonce|userid|81| {"FromU":1001001,"ToU":1001002,"Descr":"test transfer","TType":1,"Amount":2.0}

FromU: must match your userid
ToU: must be an existing user
TType:  custom field, can have any int valu

## SETAPIACCOUNTTOREADONLY 

reqID: 49

reqData: None

response: "success"

Example:

> signature|nonce|userid|5049|

This will permanently set your API Account #5 to Read Only. Note that this cannot be undone.
You can also set your API Account #0 to read only. 

## GETMARKETSORDERBOOK  

reqID: 67

reqData: json encoded MarketFilter Object

response: long/string Dictionary  with market IDs and orderbooks.

Example:

> signature|nonce|userid|67|{"Cat":0,"RunnerAND":["Arsenal","Chelsea"],"TitleAND":null,"TitleNOT":["Corners","Throwin"],"Comp":"Premier Leag","TypeOR":null,"PeriodOR":[1],"SettleOR":null,"ToSettle":false,"OnlyMyCreatedMarkets":false,"Descr":null,"ChangedAfter":"2016-01-01T22:01:01","SoftChangedAfter":"0001-01-01T00:00:00","OnlyActive":false,"MinPop":0.0,"MaxMargin":103.0,"NoZombie":false,"FromClosT":"2016-05-01T00:00:00","ToClosT":"0001-01-01T00:00:00","FromID":0,"ToID":100,"SortPopular":false}

## CREATEMARKET   (NOT UP TO DATE)

[https://github.com/Fairlay/FairlayDotNetClient/blob/master/src/Public/MarketX.cs](https://github.com/Fairlay/FairlayDotNetClient/blob/master/src/Public/MarketX.cs)

reqID: 11
reqData: json encoded MarketX Object

response: long market ID

example:

> signature|nonce|userid|11|{"Comp":"SCOTUS","Descr":"This market resolves to Yes, if the Senate confirms Merrick Garland’s Supreme Court nomination before President Obama leaves the office. The resolution date of this market may be accelerated. Bets matched after the antedated resolution date will be voided. https://www.coindesk.com","Title":"Will Senate confirm Merrick Garland's SCOTUS nomination?","CatID":15,"ClosD":"2016-11-01T00:00:00","SettlD":"2017-01-20T00:00:00","Ru":[{"Name":"Yes","InvDelay":0,"VisDelay":6000},{"Name":"No","InvDelay":0,"VisDelay":6000}],"_Type":2,"_Period":1,"SettlT":0,"Comm":0.02,"PrivCreator":784741,"CreatorName":"USERNAME"}

*Comp*: Competition
*CatID*: Category (for a list of all Categories, please visit https://github.com/Fairlay/CSharpSampleClient-Main-/blob/master/Market.cs)
*ClosD*:  Closing Date
*SettlD*: Resolution Date
*Ru*:  InvDelay must always be 0, VisDelay must always be set to 6000
*_Type*: for a list of all MarketTypes please visit https://github.com/Fairlay/CSharpSampleClient-Main-/blob/master/Market.cs
*_Period*:  for a list of all MarketPeriods please visit https://github.com/Fairlay/CSharpSampleClient-Main-/blob/master/Market.cs
*SettlT*:  for a list of all SettleTypes please visit https://github.com/Fairlay/CSharpSampleClient-Main-/blob/master/Market.cs
*Comm*:  0.02 - must always be 0.02 in order to be listed on Fairlay. Please contact info@fairlay.com if you need a reduced commission.
*PrivCreator*:  must match your userid
*CreatorName*:  your public name of your choice


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

> {"UserID":777888,"MarketID":61659266392,"Runner":5,"Enabled":true,"InitShareLimit":350.0,"B":1300.0,"CancelAll":"2016-06-05T13:34:56", "ShareStop":1400.0,"InitProb":[0.3,0.1,0.2,0.2,0.2],"DiminishBack":[0.01,0.01,0.01,0.0,0.02],"DiminishLay":[0.0,0.0,0.0,0.01,0.01],"coolOffSeconds":36000.0,"coolOffFactor":4.0}

Default values
> {"UserID":[userid],"MarketID":61659266392,"Runner":5,"Enabled":true,"InitShareLimit":300.0,"B":1000.0,"CancelAll":"2016-06-05T13:34:56", "ShareStop":9999.0,"InitProb":[0.2,0.2,0.2,0.2,0.2],"DiminishBack":[0.00,0.00,0.00,0.0,0.00],"DiminishLay":[0.01,0.01,0.01,0.01,0.01],"coolOffSeconds":1.0,"coolOffFactor":2.0}

*UserID*: must match your userid
*MarketID*:  must be provided
*Runner*:  # of Runners / must match the # of runners of the market
*Enabled*: must be set to true, disable a MM bye setting this to false.
*InitShareLimit* (must be > 1):    Shares that are offered in one order.  Stake + Winnings from each order are 350mBTC
*B* (must be > 10):    ~ is proportional to the maxium possible loss of the market maker if it starts at 50/50
*CancelAll* (must be set to a future date):   Date where the market maker stops. Set to year 2100 if the mm should run forever
*ShareStop* (must be > 1):   amount of exposure in shares before the market maker stops.  Should be set higher than  B in regular cases.
*InitProb*:   the initial probability estimation for all runners
*DiminishBack* (must be non-negative):   In general the LMSR market maker runs on 0% margin, i.e. it doesn't make any profit.  If more margin should be added, you can worsen the odds for each bid orders for every runner.  0.01 worsens bid odds from  80% to 81%  (or 1.25 to 1.2345)  for example. 
*DiminishLay*:   same for all ask orders. 

cool off adds temporary additional margin to markets with increased activity and should be applied to markets that can have exogenous shocks or where real probabilities can deviate from the initial probability distribution. 
*coolOffFactor* (must be >=1):    if set to 4.0 the odds will worsen 4.0 times more than expected from the usual lmsr market maker.
*coolOffSeconds* (must be >=1):    The time after which the coolOff period will be over. If set to 36000 the additional market margin will reduce step by step over an period of 10 hours. 

If no cool off is required, set coolOffSeconds to 1.
There is only one market maker per market. Setting one, overwrites the old one.

## GETLMSRMARKETMAKER 

retrieves all current LMSR Marketmaker

> reqID: 70

> reqData: -1

response: json encoded marketID/LMSR Dictionary
