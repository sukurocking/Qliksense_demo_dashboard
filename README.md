--------------Data Load Editor Script------
CustomerMaster:
MAPPING LOAD
//     Customer,
    CustomerID as CustomerNo,
     CustomerName
//     EmailAddress,
//     EmailPromotion,
//     Phone,
//     Title
FROM [lib://DataFiles/CustomerMaster.xlsx]
(ooxml, embedded labels, table is Customer);



OrderHeader:
LOAD
    ApplyMap('CustomerMaster',CustomerNo,'NA') as CustomerName,
	AccountNumber,
    BillToAddressID,
    "Comment",
    ContactID,
    CreditCardApprovalCode,
    CreditCardID,
    CurrencyRateID,
    CustomerNo as CustomerID,
    DueDate,
    Year(DueDate) as DueYear,
    MonthName(DueDate) as DueMonthYear,
    DueDateOriginal,
	Year(DueDateOriginal) as DueYearOriginal,
    MonthName(DueDateOriginal) as DueMonthYearOriginal,
    Freight,
    OnlineOrderFlag,
    OrderDate,
    OrderDate as DateKey,
    Year(OrderDate) as OrderYear,
    MonthName(OrderDate) as OrderMonthYear,
    PurchaseOrderNumber,
    RevisionNumber,
    OrderID,
    SalesOrderNumber,
    SalesPersonID,
    ShipDate,
    ShipDateOriginal,
    ShipMethodID,
    ShipToAddressID,
    Status,
    SubTotal,
    TaxAmt,
    TerritoryID,
    TotalDue
//    ApplyMap('CustomerMaster', 'CustomerNo', 'CustomerName') as CustomerName
FROM [lib://DataFiles/OrderHeader.xlsx]
(ooxml, embedded labels, table is OrderHeader);

// drop table CustomerMaster;


ProductMaster:
LOAD
    "Class",
    "Color",
    DaysToManufacture,
    DiscontinuedDate,
    FinishedGoodsFlag,
    ListPrice,
    MakeFlag,
    Product,
    ProductID,
    ProductLine,
    ProductModelID,
    ProductName,
    ProductNumber,
    ProductSubcategoryID,
    ReorderPoint,
    SafetyStockLevel,
    SellEndDate,
    SellStartDate,
    Size,
    SizeUnitMeasureCode,
    StandardCost,
    Style,
    Weight,
    WeightUnitMeasureCode
FROM [lib://DataFiles/ProductMaster.xlsx]
(ooxml, embedded labels, table is Product);





OrderDetail:
LOAD
    CarrierTrackingNumber,
    CostAmount,
    MarginAmount,
    OrderQty,
    ProductNo as ProductID,
    SalesAmount,
    SalesOrderDetailID,
    SalesOrderID as OrderID,
    SpecialOfferID,
    UnitPrice,
    UnitPriceDiscount
FROM [lib://DataFiles/OrderDetail.xlsx]
(ooxml, embedded labels, table is OrderDetail);

//exit script;

TempTable:
Load min(OrderDate) as minorderdate, max(OrderDate) as maxorderdate
Resident OrderHeader;

Let vMinDate = Peek('minorderdate');
Let vMaxDate = Peek('maxorderdate');

Calendar:
LOAD *, Year(DateKey) as Year, MonthName(DateKey) as MonthYear;
Load
Date($(vMinDate) + IterNo() - 1)    as DateKey
AutoGenerate 1
While $(vMinDate) + IterNo() - 1 <= $(vMaxDate);

Drop table TempTable;

