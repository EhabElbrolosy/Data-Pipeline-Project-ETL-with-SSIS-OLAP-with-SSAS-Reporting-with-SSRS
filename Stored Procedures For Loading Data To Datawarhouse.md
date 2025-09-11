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


CREATE PROCEDURE  Load_To_Fact_Order_Items as 
BEGIN
	INSERT INTO [Supply_Chain_Datawarhouse].[dbo].[Fact_Order_Items]
		(
		Order_ID,
		Customer_ID,
		Shipping_ID,
		Category_ID,
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
		)
	SELECT 
		ORD.Order_ID,
		CUS.Customer_ID,
		SHI.Shipping_ID,
		CAT.Category_ID,
		STG.Benefit_Per_Order,
		STG.[Days_For_Shipping_(Real)], 
		STG.[Days_For_Shipping_(Scheduled)],
		STG.Sales_Per_Customer,
		STG.Late_Delivery_Risk,
		STG.Order_Item_Discount,
		STG.Order_Item_Discount_Rate,
		STG.Order_Item_Product_Price,
		STG.Order_Item_Profit_Ratio,
		STG.Sales,
		STG.Order_Item_Total,
		STG.Order_Profit_Per_Order
	FROM [sales_2017].[dbo].[Staging_Area_Combined_Data] as STG
	JOIN [Supply_Chain_Datawarhouse].[dbo].[DIM_Orders] as ORD
		 ON STG.[Order id] = ORD.Order_ID
```


