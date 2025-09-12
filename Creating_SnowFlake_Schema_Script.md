```sql

CREATE DATABASE Supply_Chain_Datawarhouse
use supply_chain_sales

create table Dim_Customer (
	Customer_id int primary key,
	Customer_Name varchar(100),
	Customer_Password varchar(100),
	Customer_Email varchar(100),
	Customer_Segment varchar(100)
	Constraint CK_Customer_Segment Check (Customer_Segment in ('Home Office','Consumer','Corporate')),
	Customer_Country varchar(100),
	Customer_City varchar(100),
	Customer_State varchar(100),
	Customer_Street varchar(100),
	Customer_Zipcode int
);

Create table Dim_Payment (
	Payment_ID INT Identity(1,1) primary key,
	Payment varchar (15)
);

create table Dim_Category (
	Category_ID INT Primary key,
	Category_Name Varchar(100)
);

CREATE TABLE DIM_Product (
	Product_ID INT Primary key,
	Product_Name Varchar(100) Not Null,
	Product_Description Varchar(100),
	Product_Price INT Not Null,
	Product_Status INT
	Constraint CH_Status Check (Product_Status IN (0,1)),
	Category_ID Int
	Constraint FK_Category foreign key (Category_ID) references Dim_Category (Category_ID)
);

CREATE TABLE Dim_Orders (
	Order_ID int Primary Key,
	Order_Status varchar(100),
	Order_Country Varchar(100),
	Order_City Varchar(100),
	Order_Region Varchar(100),
	Order_State Varchar(100),
)

Create table Dim_Shipping (
	Shipping_ID Int Identity Primary key,
	Shipping_Mode varchar(100),
	Delivery_Status Varchar(100)
	Constraint CK_Delivery_Status Check (Delivery_Status in ('Late delivery','Advance shipping','Shipping on time','Shipping canceled'))
);

CREATE TABLE Dim_Date (
	Date_Key INT Primary key,
	Full_Date Date,
	Year int,
	Quarter int,
	Month int,
	Day int,
	Day_Name varchar(15),
	Month_Name varchar(15)
)

Create table Fact_Order_Items (
	Order_ID INT Primary key,
	Customer_ID int,
	Shipping_ID Int,
	Payment_ID INT,
	Category_ID Int,
	Benefit_Per_Order Decimal(16,10),
	[Days_For_Shipping_(Real)] Tinyint,
	[Days_For_Shipping_(Scheduled)] Tinyint,
	Sales_Per_Customer Decimal(16,10),
	Late_Delivery_Risk Tinyint,
	Order_Item_Discount Decimal(16,10),
	Order_Item_Discount_Rate Decimal(16,10),
	Order_Item_Product_Price Decimal(16,10),
	Order_Item_Profit_Ratio Decimal(16,10),
	Sales Decimal(16,10),
	Order_Item_Total Decimal(16,10),
	Order_Profit_Per_Order Decimal(16,10),
	Order_Date_Key INT,
	Shipping_Date_Key INT,
	Constraint FK_Order_ID Foreign key (Order_ID) REFERENCES Dim_Orders (Order_ID),
	CONSTRAINT FK_Customer_ID Foreign KEY (Customer_iD) REFERENCES Dim_Customer (Customer_ID),
	CONSTRAINT FK_Shipping_ID Foreign KEY (Shipping_ID) REFERENCES Dim_Shipping (Shipping_ID),
	CONSTRAINT FK_Payment_ID Foreign key (Payment_ID) REFERENCES Dim_Payment (Payment_ID),
	CONSTRAINT FK2_Category Foreign key (Category_ID) REFERENCES Dim_Category (Category_ID),
	Constraint FK_Order_Date Foreign key (Order_Date_Key) REFERENCES Dim_Date (Date_Key),
	Constraint FK_Shipping_Date Foreign KEY (Shipping_Date_Key) REFERENCES Dim_Date (Date_Key)
);
```
