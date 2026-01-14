# Day 2

![A screenshot of a computer AI-generated content may be
incorrect.](./media/image1.png)

![A screenshot of a computer AI-generated content may be
incorrect.](./media/image2.png)

![A screenshot of a computer AI-generated content may be
incorrect.](./media/image3.png)

![A screenshot of a computer AI-generated content may be
incorrect.](./media/image4.png)

## Hint: Use update policy to parse xml into silver table

//CHALLENGE 2.1

.create table shipping_silver (

Provider: string,

OrderNumber: string,

EventTime: datetime,

Status: string,

SourceCity: string,

SourceCountry: string,

SourceLatitude: real,

SourceLongitude: real,

DestinationName: string,

DestinationStreet: string,

DestinationCity: string,

DestinationZip: string,

DestinationCountry: string,

DestinationLatitude: real,

DestinationLongitude: real,

WeightKg: real,

ProductSize: string,

ProductQuantity: int,

ProductId: string

)

![A screenshot of a computer AI-generated content may be
incorrect.](./media/image5.png)

![A screenshot of a computer AI-generated content may be
incorrect.](./media/image6.png)

//Let\`s expand the whole xml and project only the fields needed

shipping

| extend parsed = parse_xml(data)

| extend ShippingEvent = parsed.ShippingEvent

| extend Products = ShippingEvent.ShippingContents.Product

| extend

Provider = tostring(ShippingEvent.Provider),

OrderNumber = tostring(ShippingEvent.OrderNumber),

EventTime = todatetime(ShippingEvent.EventTime),

Status = tostring(ShippingEvent.Status),

SourceCity = tostring(ShippingEvent.SourceDistributionCenter.City),

SourceCountry =
tostring(ShippingEvent.SourceDistributionCenter.Country),

SourceLatitude =
todouble(ShippingEvent.SourceDistributionCenter.Latitude),

SourceLongitude =
todouble(ShippingEvent.SourceDistributionCenter.Longitude),

DestinationName = tostring(ShippingEvent.DestinationAddress.Name),

DestinationStreet = tostring(ShippingEvent.DestinationAddress.Street),

DestinationCity = tostring(ShippingEvent.DestinationAddress.City),

DestinationZip = tostring(ShippingEvent.DestinationAddress.Zip),

DestinationCountry = tostring(ShippingEvent.DestinationAddress.Country),

DestinationLatitude =
todouble(ShippingEvent.DestinationAddress.Latitude),

DestinationLongitude =
todouble(ShippingEvent.DestinationAddress.Longitude),

Weight = todouble(ShippingEvent.WeightKg),

ProductSize = tostring(Products.Size),

ProductQuantity = toint(Products.Quantity),

ProductId = tostring(Products.ProductId)

| project

Provider,

OrderNumber,

EventTime,

Status,

SourceCity,

SourceCountry,

SourceLatitude,

SourceLongitude,

DestinationName,

DestinationStreet,

DestinationCity,

DestinationZip,

DestinationCountry,

DestinationLatitude,

DestinationLongitude,

Weight,

ProductSize,

ProductQuantity,

ProductId

![A screenshot of a computer AI-generated content may be
incorrect.](./media/image7.png)

![A screenshot of a computer AI-generated content may be
incorrect.](./media/image8.png)

.set-or-append shipping_silver \<|

Shipping

| extend parsed = parse_xml(data)

| extend ShippingEvent = parsed.ShippingEvent

| extend Products = ShippingEvent.ShippingContents.Product

| project

Provider = tostring(ShippingEvent.Provider),

OrderNumber = tostring(ShippingEvent.OrderNumber),

EventTime = todatetime(ShippingEvent.EventTime),

Status = tostring(ShippingEvent.Status),

SourceCity = tostring(ShippingEvent.SourceDistributionCenter.City),

SourceCountry =
tostring(ShippingEvent.SourceDistributionCenter.Country),

SourceLatitude =
todouble(ShippingEvent.SourceDistributionCenter.Latitude),

SourceLongitude =
todouble(ShippingEvent.SourceDistributionCenter.Longitude),

DestinationName = tostring(ShippingEvent.DestinationAddress.Name),

DestinationStreet = tostring(ShippingEvent.DestinationAddress.Street),

DestinationCity = tostring(ShippingEvent.DestinationAddress.City),

DestinationZip = tostring(ShippingEvent.DestinationAddress.Zip),

DestinationCountry = tostring(ShippingEvent.DestinationAddress.Country),

DestinationLatitude =
todouble(ShippingEvent.DestinationAddress.Latitude),

DestinationLongitude =
todouble(ShippingEvent.DestinationAddress.Longitude),

Weight = todouble(ShippingEvent.WeightKg),

ProductSize = tostring(Products.Size),

ProductQuantity = toint(Products.Quantity),

ProductId = tostring(Products.ProductId)

![A screenshot of a computer AI-generated content may be
incorrect.](./media/image9.png)

shipping_silver

| take 100

![A screenshot of a computer AI-generated content may be
incorrect.](./media/image10.png)

![A screenshot of a computer AI-generated content may be
incorrect.](./media/image11.png)

//another way to do it is through a function

.create-or-alter function with (folder = "UpdatePolicyFunctions",
skipvalidation = "true") ParseShippingXML()

{

shipping_raw

| extend parsed = parse_xml(data)

| extend ShippingEvent = parsed.ShippingEvent

| extend Products = ShippingEvent.ShippingContents.Product

| project

Provider = tostring(ShippingEvent.Provider),

OrderNumber = tostring(ShippingEvent.OrderNumber),

EventTime = todatetime(ShippingEvent.EventTime),

Status = tostring(ShippingEvent.Status),

SourceCity = tostring(ShippingEvent.SourceDistributionCenter.City),

SourceCountry =
tostring(ShippingEvent.SourceDistributionCenter.Country),

SourceLatitude =
todouble(ShippingEvent.SourceDistributionCenter.Latitude),

SourceLongitude =
todouble(ShippingEvent.SourceDistributionCenter.Longitude),

DestinationName = tostring(ShippingEvent.DestinationAddress.Name),

DestinationStreet = tostring(ShippingEvent.DestinationAddress.Street),

DestinationCity = tostring(ShippingEvent.DestinationAddress.City),

DestinationZip = tostring(ShippingEvent.DestinationAddress.Zip),

DestinationCountry = tostring(ShippingEvent.DestinationAddress.Country),

DestinationLatitude =
todouble(ShippingEvent.DestinationAddress.Latitude),

DestinationLongitude =
todouble(ShippingEvent.DestinationAddress.Longitude),

Weight = todouble(ShippingEvent.WeightKg),

ProductSize = tostring(Products.Size),

ProductQuantity = toint(Products.Quantity),

ProductId = tostring(Products.ProductId)

}

![A screenshot of a computer AI-generated content may be
incorrect.](./media/image12.png)

![A screenshot of a computer AI-generated content may be
incorrect.](./media/image13.png)

![A screenshot of a computer AI-generated content may be
incorrect.](./media/image14.png)

.alter table shipping_silver policy update

@'\[{  "IsEnabled": true, "Source": "shipping", "Query":
"ParseShippingXML()",  "IsTransactional": false,
"PropagateIngestionProperties": false }\]'

![A screenshot of a computer AI-generated content may be
incorrect.](./media/image15.png)

![A screenshot of a computer AI-generated content may be
incorrect.](./media/image16.png)

//Verify your function is working

ParseShippingXML()

| take 1000

![A screenshot of a computer AI-generated content may be
incorrect.](./media/image17.png)

//Verify policy exists

.show table shipping_silver policy update

![A screenshot of a computer AI-generated content may be
incorrect.](./media/image18.png)

//Verify you have data, if not rerun the notebok, and redo the continuos
ingestion for Blob

shipping_silver

| take 10000

![A screenshot of a computer AI-generated content may be
incorrect.](./media/image19.png)

//get latest shipping details

 .create materialized-view with (backfill=true) LatestOrderStatus on
table shipping_silver

{

    shipping_silver

    | summarize arg_max(EventTime, \*) by OrderNumber

}

![A screenshot of a computer AI-generated content may be
incorrect.](./media/image20.png)

## //CHALLENGE 2.2

//clik on jPath instead of inline when on payload, click on ProdId \*
copy path

products1

| extend Operator = payload.op

| where  Operator == ("c")

| extend ProductId = \['payload'\]\['after'\]\['ProductId'\]

| extend ProductName = payload.after.ProductName

| extend SKU = payload.after.SKU

| extend Brand = payload.after.Brand

| extend Category = payload.after.Category

| extend UnitCost = payload.after.UnitCost

![A screenshot of a computer AI-generated content may be
incorrect.](./media/image21.png)

.create table products_silver (

ProductId: string,

ProductName: string,

SKU: string,

Brand: string,

Category: string,

UnitCost: int

)

![A screenshot of a computer AI-generated content may be
incorrect.](./media/image22.png)

.set-or-append products_silver \<|

products_raw

| extend Operator = payload.op

| where Operator == ("c")

| extend ProductId = tostring(\['payload'\]\['after'\]\['ProductId'\])

| extend ProductName = tostring(payload.after.ProductName)

| extend SKU = tostring(payload.after.SKU)

| extend Brand = tostring(payload.after.Brand)

| extend Category = tostring(payload.after.Category)

| extend UnitCost = toint(payload.after.UnitCost)

| project ProductId, ProductName, SKU, Brand, Category, UnitCost

![A screenshot of a computer AI-generated content may be
incorrect.](./media/image23.png)

![A screenshot of a computer AI-generated content may be
incorrect.](./media/image24.png)

![A screenshot of a computer AI-generated content may be
incorrect.](./media/image25.png)

![A screenshot of a computer AI-generated content may be
incorrect.](./media/image26.png)

![A screenshot of a computer AI-generated content may be
incorrect.](./media/image27.png)

![A screenshot of a computer AI-generated content may be
incorrect.](./media/image28.png)

![A screenshot of a computer AI-generated content may be
incorrect.](./media/image29.png)

![A screenshot of a computer AI-generated content may be
incorrect.](./media/image30.png)

![A screenshot of a computer AI-generated content may be
incorrect.](./media/image31.png)

![A screenshot of a computer AI-generated content may be
incorrect.](./media/image32.png)

![A screenshot of a computer AI-generated content may be
incorrect.](./media/image33.png)

![A screenshot of a computer AI-generated content may be
incorrect.](./media/image34.png)

//stop shipping from production with a defect

manufacturing

| where ingestion_time()\> ago(24h)

| top 1 by DefectProbability desc

| join kind=inner external_table('operators') on OperatorId

| join (shipping_silver | where Status == "Dispatched") on ProductId

| join (shipping_provider | project Provider = trim(" ", Provider),
contact, HQAddress) on Provider

| join products_silver on ProductId

| project OrderNumber, ProductName, ShippingProvider = Provider1 ,
ProviderNUmber =contact , HQAddress

![A screenshot of a computer AI-generated content may be
incorrect.](./media/image35.png)

//stop operator from production with a defect

manufacturing

| where ingestion_time()\> ago(1h)

| top 1 by DefectProbability desc

| join kind=inner external_table('operators') on OperatorId

| join (shipping_silver | where Status == "Dispatched") on ProductId

| join (shipping_provider | project Provider = trim(" ", Provider)) on
Provider

| join products_silver on ProductId

| extend OperatorDetails = strcat(OperatorId, "-" ,OperatorName)

| extend ProductDetails = strcat(ProductId, "-" ,ProductName)

| project OperatorDetails, ProductDetails, Phone, AssetId, BatchId

![A screenshot of a computer AI-generated content may be
incorrect.](./media/image36.png)

## //CHALLENGE 2.3

//Show all operators details

external_table('operators')

| project Name, Phone, Email, Role, Shift

![A screenshot of a computer AI-generated content may be
incorrect.](./media/image37.png)

## //2.3.1 Current Defective Item Detail

//2.3.1 Current Defective Item Detail

manufacturing

| where ingestion_time()\> ago(24h)

| top 1 by DefectProbability desc

| join kind=inner external_table('operators') on OperatorId

| join (shipping_silver | where Status == "Dispatched") on ProductId

| join (shipping_provider | project Provider = trim(" ", Provider),
contact, HQAddress) on Provider

| join products_silver on ProductId

| project OrderNumber, ProductName, ShippingProvider = Provider1 ,
ProviderNUmber =contact , HQAddress, OperatorName, DefectProbability

![A screenshot of a computer AI-generated content may be
incorrect.](./media/image38.png)

// 2.3.2 Correlation between defective and non-defective products in 1
hour bins

manufacturing

| summarize count() by bin(todatetime(timestamp), 1h), DefectProbability

![A screenshot of a computer AI-generated content may be
incorrect.](./media/image39.png)

//2.3.3

// Current shipment status (count of orders by status).

shipping_silver

| summarize countOfOrders=count() by Status

| project Status, NumberofOrders = countOfOrders

| order by NumberofOrders

![A screenshot of a computer AI-generated content may be
incorrect.](./media/image40.png)

//2.3.4

// shipment count by destination

shipping_silver

| summarize shipmentCount=count() by DestinationCountry,
DestinationCity, DestinationLatitude, DestinationLongitude

| project DestinationCountry, DestinationCity, DestinationLatitude,
DestinationLongitude, shipmentCount

![A screenshot of a computer AI-generated content may be
incorrect.](./media/image41.png)

# Create an operational dashboard for Fabrikam management

![A screenshot of a computer AI-generated content may be
incorrect.](./media/image42.png)

![A screenshot of a computer AI-generated content may be
incorrect.](./media/image43.png)

![A screenshot of a computer AI-generated content may be
incorrect.](./media/image44.png)

![A screenshot of a computer AI-generated content may be
incorrect.](./media/image45.png)

![A screenshot of a computer AI-generated content may be
incorrect.](./media/image46.png)

![A screenshot of a computer AI-generated content may be
incorrect.](./media/image47.png)

Create a real-time dashboard table that shows all operators handling
shipments in the last 24 hour.  
Include operator name, phone number, provider, total shipments handled,
defective shipment count, and average defect probability.  
Sort by total shipments in descending order and refresh automatically.

![A screenshot of a computer AI-generated content may be
incorrect.](./media/image48.png)

![A screenshot of a computer AI-generated content may be
incorrect.](./media/image49.png)

![A screenshot of a computer AI-generated content may be
incorrect.](./media/image50.png)

// This KQL query was generated by AI:

// Create a real-time dashboard table that shows all operators handling
shipments in the last 24 hour.

//Include operator name, phone number, provider, total shipments
handled, defective shipment count, and average defect probability.

//Sort by total shipments in descending order and refresh automatically.

//

manufacturing

| where ingestion_time() \> ago(24h)

| join kind=inner (shipping_silver | where Status == "Dispatched") on
ProductId

| join kind=inner (shipping_provider | project Provider = trim(" ",
Provider), contact) on Provider

| summarize

    TotalShipments=count() ,

    DefectiveShipments=countif(DefectProbability \> 0),

    AvgDefectProbability=avg(DefectProbability)

    by OperatorName, contact, Provider

| sort by TotalShipments desc

| project OperatorName, PhoneNumber=contact, Provider, TotalShipments,
DefectiveShipments, AvgDefectProbability

![A screenshot of a computer AI-generated content may be
incorrect.](./media/image51.png)

![A screenshot of a computer AI-generated content may be
incorrect.](./media/image52.png)

![A screenshot of a computer AI-generated content may be
incorrect.](./media/image53.png)

![A screenshot of a computer AI-generated content may be
incorrect.](./media/image54.png)

![A screenshot of a computer AI-generated content may be
incorrect.](./media/image55.png)

shipping_silver

| where Status  in ("Dispatched", "PickedUp", "Routing")

| where EventTime \> ago(1h)

| summarize countByStatus=count() by Status

![A screenshot of a computer AI-generated content may be
incorrect.](./media/image56.png)

![A screenshot of a computer AI-generated content may be
incorrect.](./media/image57.png)

![A screenshot of a computer AI-generated content may be
incorrect.](./media/image58.png)

![A screenshot of a computer AI-generated content may be
incorrect.](./media/image59.png)

![A screenshot of a computer AI-generated content may be
incorrect.](./media/image60.png)

// This KQL query was generated by AI:

// manufacturing

//| where DefectProbability \>= 0.2

//| summarize arg_max(EventProcessedUtcTime, \*)

//| project

//    Provider,

//    ProductName,

//    OrderNumber,

//    OperatorName,

//    Phone,

//    max_DefectProbability = DefectProbability

//

manufacturing

| where DefectProbability \>= 0.2

| summarize arg_max(EventProcessedUtcTime, \*)

| join kind=leftouter (operators | project OperatorId,
OperatorName=Name, Phone) on OperatorId

| join kind=leftouter (products | project ProductId, ProductName) on
ProductId

| project

    Provider = "",

    ProductName,

    OrderNumber = "",

    OperatorName,

    Phone,

    max_DefectProbability = DefectProbability

![A screenshot of a computer AI-generated content may be
incorrect.](./media/image61.png)

![A screenshot of a computer AI-generated content may be
incorrect.](./media/image62.png)

![A screenshot of a computer AI-generated content may be
incorrect.](./media/image63.png)

shipping

| extend xmlData = parse_xml(data)

| project

    country =
tostring(\['xmlData'\]\['ShippingEvent'\]\['DestinationAddress'\]\['Country'\]),

    city =
tostring(\['xmlData'\]\['ShippingEvent'\]\['DestinationAddress'\]\['City'\])

| summarize countCountry=count() by country, city

| lookup sites on $left.country == $right.Country and $left.city ==
$right.City

| project country, city, Longitude, Latitude, countCountry

| where isnotnull(Longitude) and isnotnull(Latitude)

| render scatterchart with (kind = map, xcolumn = Longitude, ycolumns =
Latitude, series = country, title = "Delivery Country by Destination")

![A screenshot of a computer AI-generated content may be
incorrect.](./media/image64.png)

![A screenshot of a computer AI-generated content may be
incorrect.](./media/image65.png)

![A screenshot of a computer AI-generated content may be
incorrect.](./media/image66.png)

![A screenshot of a computer AI-generated content may be
incorrect.](./media/image67.png)

![A screenshot of a computer AI-generated content may be
incorrect.](./media/image68.png)

// This KQL query was generated by AI:

// Correlation between defective and non-defective products in 1 hour
bins

//

//Hint: Use the anomaly flag column you defined in the SQL Code Operator
before, to see the defection & non-defective products and use that in a
line chart

//

manufacturing

| extend Defective = iff(Anamoly == "1", 1, 0), NonDefective =
iff(Anamoly == "0", 1, 0)

| summarize DefectiveCount=sum(Defective),
NonDefectiveCount=sum(NonDefective) by bin(EventProcessedUtcTime, 1h)

| project EventProcessedUtcTime, DefectiveCount, NonDefectiveCount

| render linechart

    with (

        title="Defective vs Non-Defective Products (1h bins)",

        xtitle="Time (1h bins)",

        ytitle="Count"

    )

![A screenshot of a computer AI-generated content may be
incorrect.](./media/image69.png)

![A screenshot of a computer AI-generated content may be
incorrect.](./media/image70.png)

![A screenshot of a computer AI-generated content may be
incorrect.](./media/image71.png)

![A screenshot of a computer AI-generated content may be
incorrect.](./media/image72.png)

![A screenshot of a computer AI-generated content may be
incorrect.](./media/image73.png)
