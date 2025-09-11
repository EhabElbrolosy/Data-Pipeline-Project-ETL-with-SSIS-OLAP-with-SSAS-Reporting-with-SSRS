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
--------------------------------------------------------------------------------------------------------------------------------------

CREATE PROCEDURE Load_To_Dim_Product as
BEGIN 
	insert into [Supply_Chain_Datawarhouse].[dbo].[DIM_Product]
	select distinct
	[Product Id],
	[Product Category Id],
	[Product Description],
	[Product Name],
	[Product Price],
    [Product Status]
	FROM [sales_2017].[dbo].[Staging_Area_Combined_Data] S
	join  [Supply_Chain_Datawarhouse].[dbo].[Dim_Category] C
	ON S.[Product Category Id] = C.Category_ID
END
```
