```sql
--Dim_Customer
--------------------------------------------------------------------------------------------------------------------------------------
create procedure Load_To_Dim_Customer as 
BEGIN 
	INSERT INTO [Supply_Chain_Datawarhouse].[dbo].[Dim_Customer]
	select distinct [Customer Id],
	[Customer Fname], 
	[Customer Email],
	[Customer Password],
	[Customer Segment],
	[Customer City],
	[Customer Country],
	[Customer State],
	[Customer Street],	
	[Customer Zipcode] FROM [sales_2017].[dbo].[Staging_Area_Combined_Data]
END

--Dim_Orders
--------------------------------------------------------------------------------------------------------------------------------------
create procedure Load_To_Dim_Orders as 
BEGIN
	insert into [Supply_Chain_Datawarhouse].[dbo].[Dim_Orders]
	select distinct	
		[Order id],
		[Order status],
		[Order Country],
		[Order City],
		[Order Region],
		[Order State]
	FROM [sales_2017].[dbo].[Staging_Area_Combined_Data]
END
exec Load_To_Dim_Orders

--Dim_Category
--------------------------------------------------------------------------------------------------------------------------------------
CREATE PROCEDURE Load_To_Dim_Category as
BEGIN
	INSERT into [Supply_Chain_Datawarhouse].[dbo].[Dim_Category]
	SELECT DISTINCT [Category ID], [Category Name] 
	from [sales_2017].[dbo].[Staging_Area_Combined_Data]
END 

--Dim_Payment
--------------------------------------------------------------------------------------------------------------------------------------

CREATE PROCEDURE Load_To_Dim_Payment as
BEGIN 
	insert into [Supply_Chain_Datawarhouse].[dbo].[Dim_Payment]
	SELECT Distinct [payment] from [sales_2017].[dbo].[Staging_Area_Combined_Data]
END

--Dim_Product
 قابلني فيه مشكلة مش فاهمها :
كان بيعمل insert duplicated values في ال product_id نتيجة استخدام الjoin ف حليتها باستخدام row_number شلت الduplicates الاول
---------------------------------------------------------------------------------------------------

CREATE PROCEDURE Load_To_Dim_Product as
BEGIN 
	WITH CTE AS (
		select
			s.[Product Id],
			s.[Product Name],
			s.[Product Description],
			s.[Product Price],
			s.[Product Status],
			s.[Category Id],
			ROW_NUMBER() over (partition by s.[Product Id] order by s.[Product Id]) as total
		FROM [sales_2017].[dbo].[Staging_Area_Combined_Data] S
		join [Supply_Chain_Datawarhouse].[dbo].[Dim_Category] C
		ON S.[Category Id] = C.Category_ID
	)
	insert into [Supply_Chain_Datawarhouse].[dbo].[DIM_Product]
	(Product_ID, Product_Name, Product_Description, Product_Price, Product_Status, Category_ID
	)
	select
		[Product Id],
		[Product Name],
		[Product Description],
		[Product Price],
		[Product Status],
		[Category Id]
	FROM CTE where total =1
END

--Dim_Shipping
--------------------------------------------------------------------------------------------------------------------------------------
CREATE PROCEDURE Load_To_Dim_Shipping as
BEGIN
	insert into [Supply_Chain_Datawarhouse].[dbo].[Dim_Shipping]
	select Distinct [Shipping Mode], [delivery status]
	FROM [sales_2017].[dbo].[Staging_Area_Combined_Data]
END

--Dim_Date
--------------------------------------------------------------------------------------------------------------------------------------
create procedure Load_To_Dim_Date as
BEGIN
WITH Time_Range as (
select 
	MIN([order date (DateOrders)]) as [Start_Date],
	MAX([shipping date (DateOrders)]) as End_Date
FROM [sales_2017].[dbo].[Staging_Area_Combined_Data]
UNION ALL
SELECT dateadd(day,1,Start_Date), End_Date
FROM Time_Range
where Start_Date < End_Date)
insert into [Supply_Chain_Datawarhouse].[dbo].[Dim_Date]
(Date_Key,Full_Date,Year,Quarter,Month_Name,Day,Day_name,IsWeekend)

SELECT
	FORMAT(START_DATE,'yyyyMMdd') as Date_Key,
	FORMAT(START_DATE,'yyyy-MM-dd') as Full_Date,
	Datepart(year,START_DATE) as Year,
	Datepart(quarter,START_DATE) as Quarter,
	Datename(Month,START_DATE) as Month_Name,
	Datepart(Day,Start_Date) as Day,
	Datename(weekday,start_date) as Day_name,
	case when datepart(weekday,start_date) in (7,1) then 'Yes' else 'No'
	END as IsWeekend
from Time_Range
option	(maxrecursion 5000)
END

--Fact_Order_Items
--------------------------------------------------------------------------------------------------------------------------------------
CREATE PROCEDURE  Load_To_Fact_Order_Items as 
BEGIN
	INSERT INTO [Supply_Chain_Datawarhouse].[dbo].[Fact_Order_Items]
		(
		Order_ID,
		Customer_ID,
		Shipping_ID,
		Category_ID,
		Payment_ID,
		Benefit_Per_Order,
		[Days_For_Shipping_(Real)], 
		[Days_For_Shipping_(Scheduled)],
		Sales_Per_Customer,
		Late_Delivery_Risk,
		Order_Item_Discount,
		Order_Item_Discount_Rate,
		Order_Item_Product_Price,
		Order_Item_Profit_Ratio,
		Sales,
		Order_Item_Total,
		Order_Profit_Per_Order,
		Order_Date_Key,
		Shipping_Date_Key
		)
		SELECT 
		ORD.Order_ID,
		CUS.Customer_ID,
		SHP.Shipping_ID,
		CAT.Category_ID,
		PAY.Payment_ID,
		STG.[Benefit Per Order],
		STG.[Days For Shipping (Real)], 
		STG.[Days for shipment (scheduled)],
		STG.[Sales Per Customer],
		STG.[Late Delivery Risk],
		STG.[Order Item Discount],
		STG.[Order Item Discount Rate],
		STG.[Order Item Product Price],
		STG.[Order Item Profit Ratio],
		STG.[Sales],
		STG.[Order Item Total],
		STG.[Order Profit Per Order],
		DAT1.Date_Key,
		DAT2.Date_Key

	FROM [sales_2017].[dbo].[Staging_Area_Combined_Data] as STG
	left JOIN [Supply_Chain_Datawarhouse].[dbo].[DIM_Orders] as ORD
		 ON STG.[Order Id] = ORD.Order_ID
	left JOIN [Supply_Chain_Datawarhouse].[dbo].[Dim_Customer] as CUS
		 ON STG.[Customer Id] = CUS.Customer_id 
	left JOIN [Supply_Chain_Datawarhouse].[dbo].[Dim_Shipping] as SHP
		 ON STG.[Shipping Mode] = SHP.Shipping_Mode
	left JOIN [Supply_Chain_Datawarhouse].[dbo].[Dim_Category] as CAT
		 ON STG.[Category Id] = CAT.Category_ID
	left JOIN [Supply_Chain_Datawarhouse].[dbo].[Dim_Payment] as PAY
		 ON STG.PAYMENT = PAY.Payment
	left join [Supply_Chain_Datawarhouse].[dbo].[Dim_Date] as DAT1
		 ON cast(STG.[order date (DateOrders)] as date ) = DAT1.Full_Date
	LEFT JOIN [Supply_Chain_Datawarhouse].[dbo].[Dim_Date] as DAT2
		 ON  cast(STG.[shipping date (DateOrders)] as date ) = DAT2.Full_Date
END
```


