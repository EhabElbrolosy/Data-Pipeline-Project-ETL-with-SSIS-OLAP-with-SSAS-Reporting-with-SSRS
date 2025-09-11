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
```


