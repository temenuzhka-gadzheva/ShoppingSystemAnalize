CREATE TABLES AND DATABASE 

CREATE DATABASE ShoppingSystemDB;
USE ShoppingSystemDB;

CREATE TABLE Addresses(
  ID INT IDENTITY(1,1) PRIMARY KEY,
  Country NVARCHAR(20) NOT NULL,
  Town NVARCHAR(30) NOT NULL,
  Street NVARCHAR(50) NOT NULL,
  PostalCode CHAR(10)
);


CREATE TABLE Categories(
ID INT IDENTITY(1,1) PRIMARY KEY,
[Name] NVARCHAR(30) NOT NULL,
[Image] VARBINARY(max) NULL,
[Description] NVARCHAR(50) NOT NULL
);


CREATE TABLE Payments(
  ID INT IDENTITY(1,1) PRIMARY KEY,
  PaymentMethod NVARCHAR(20) NOT NULL,
);

CREATE TABLE Coupons(
	ID INT IDENTITY(1,1) PRIMARY KEY,
	Discount DECIMAL(8,2) NOT NULL,
	Code NVARCHAR(20) NOT NULL,
	MinCartValue DECIMAL(8,2) NOT NULL,
	Validdate DATE NOT NULL
);


CREATE TABLE Stores(
	ID INT IDENTITY(100,1) PRIMARY KEY,
	[Name] NVARCHAR(30) NOT NULL,
	[Location] NVARCHAR(50) NOT NULL
);


CREATE TABLE SubCategories(
   ID INT IDENTITY(1,1) PRIMARY KEY,
   [Name] NVARCHAR(30) NOT NULL,
   [Description] TEXT NULL,
   CategoryID  INT NOT NULL,
   CONSTRAINT FK_SubCategory_CategoryID
   FOREIGN KEY (CategoryID )
   REFERENCES Categories(ID)
);

CREATE TABLE Attributes(
	ID INT IDENTITY(1,1) PRIMARY KEY,
	Color NVARCHAR(20),
	Size CHAR(10),
	[Weight] DECIMAL(6,2),
	Quantity INT
);

CREATE TABLE Products(
    ID INT IDENTITY(1,1) PRIMARY KEY,
	[Name] NVARCHAR(50) NOT NULL,
	Price DECIMAL(8,2) NOT NULL,
	StockNumber NVARCHAR(50) NOT NULL,
	Trend NVARCHAR(30) NOT NULL,
	Quantity INT NOT NULL,
	Specifications NVARCHAR(255) NULL,
	[Description] NVARCHAR(255) NULL,
	[Image] VARBINARY(max) NULL,
	--SubCategoryID INT NOT NULL,
	--CONSTRAINT FK_Products_SUbCategoryID
    --FOREIGN KEY (SubCategoryID)
    --REFERENCES SubCategory(ID)
);

/*ALTER TABLE Products
ADD CONTRAINT
FK_Products_SubCategoryID
FOREIGN KEY(SubCategoryID) REFERENCES
SubCategory(ID); */

CREATE TABLE CategoryHasProduct(
   ID INT IDENTITY(1,1) PRIMARY KEY,
   ProductID INT NOT NULL,
   CONSTRAINT FK_CategoryHasProduct_ProductID
   FOREIGN KEY (ProductID )
   REFERENCES Products(ID),
   CategoryID INT NOT NULL
   CONSTRAINT FK_CategoryHasProduct_CategoryID
   FOREIGN KEY (CategoryID)
   REFERENCES Categories(ID)
);

CREATE TABLE Accounts(
   ID INT IDENTITY(1,1) PRIMARY KEY,
   Username NVARCHAR(35) NOT NULL,
   [Password] CHAR(30) NOT NULL,
   Email NVARCHAR(50) NOT NULL,
   Picture VARBINARY(max) NULL,
   Balance DECIMAL(8,2)  NULL,
   CreatedOn DATEtime NULL,
   PromoCodes INT NULL,
   AddressID INT NOT NULL,
   CONSTRAINT FK_Account_AddressID
   FOREIGN KEY (AddressID)
   REFERENCES Addresses(ID)
);

CREATE TABLE Users(
   ID INT IDENTITY(1,1) PRIMARY KEY,
   FName NVARCHAR(50) NOT NULL,
   LName NVARCHAR(50) NOT NULL,
   Phone CHAR(15) NULL,
   Gender CHAR(1) NULL,
   Age INT NULL,
   AccountID  INT,
   CONSTRAINT FK_Users_AccountID
   FOREIGN KEY (AccountID )
   REFERENCES Accounts(ID),
   AddressID  INT,
   CONSTRAINT FK_Users_AddressID
   FOREIGN KEY (AddressID)
   REFERENCES Addresses(ID)
);


CREATE TABLE UserHasFavStores(
     ID INT IDENTITY(1,1) PRIMARY KEY,
	 UserID INT NOT NULL,
	 CONSTRAINT FK_UserHadFavStore_UserID
     FOREIGN KEY (UserID)
     REFERENCES Users(ID),
	 StoreID INT NOT NULL,
	 CONSTRAINT FK_UserHadFavStore_StoreID
     FOREIGN KEY (StoreID)
     REFERENCES Stores(ID)
);

CREATE TABLE ShoppingCart(
   ID INT IDENTITY(1,1) PRIMARY KEY,
   UserID INT NOT NULL,
   CONSTRAINT FK_ShoppingCart_UserID
   FOREIGN KEY (UserID)
   REFERENCES Users(ID),
   ProductsCount INT,
   CreatedOn DATEtime,
   UpDATEdOn DATEtime
);

CREATE TABLE ORDERS(
    ID INT IDENTITY(1,1) PRIMARY KEY,
	Subtotal DECIMAL(8,2) NOT NULL,
	ShippingFee DECIMAL(8,2) NOT NULL,
	Total DECIMAL(8,2) NOT NULL,
	[Status] TEXT NOT NULL,
	Comment TEXT,
    UserID INT NOT NULL,
    CONSTRAINT FK_Orders_UserID
    FOREIGN KEY (UserID)
    REFERENCES Users(ID),
	CartID INT NOT NULL,
    CONSTRAINT FK_Orders_CartID
    FOREIGN KEY (CartID)
    REFERENCES ShoppingCart(ID),
	PaymentID INT NOT NULL,
    CONSTRAINT FK_Orders_PaymentID
    FOREIGN KEY (PaymentID)
    REFERENCES Payments(ID),
	CouponID INT NOT NULL,
    CONSTRAINT FK_Orders_CouponID
    FOREIGN KEY (CouponID)
    REFERENCES Coupons(ID)
);

CREATE TABLE Delivery(
  ID INT IDENTITY(1,1) PRIMARY KEY,
  [Date] DATETIME NOT NULL,
  OrderID INT NOT NULL,
  CONSTRAINT FK_Delivery_OrderID
  FOREIGN KEY (OrderID)
  REFERENCES Orders(ID),
  AddressID INT NOT NULL,
  CONSTRAINT FK_Delivery_AddressID
  FOREIGN KEY (AddressID)
  REFERENCES Addresses(ID),
  DeliveryType NVARCHAR(20) NOT NULL
);


CREATE TABLE CartHasProduct(
   ID INT IDENTITY(1,1) PRIMARY KEY,
   CartID INT NOT NULL,
   CONSTRAINT FK_CartHasProduct_CartID
   FOREIGN KEY (CartID)
   REFERENCES ShoppingCart(ID),
   ProductID INT NOT NULL,
   CONSTRAINT FK_CartHasProduct_ProductID
   FOREIGN KEY (ProductID)
   REFERENCES Products(ID),
);

CREATE TABLE WishList(
		ID INT IDENTITY(1,1) PRIMARY KEY,
		UserID INT NOT NULL,
	    CONSTRAINT FK_WishList_UserID
        FOREIGN KEY (UserID)
        REFERENCES Users(ID),
		ProductID INT NOT NULL,
		CONSTRAINT FK_WishList_ProductID
        FOREIGN KEY (ProductID)
        REFERENCES Products(ID),
		CategoryID INT NOT NULL,
		CONSTRAINT FK_WishList_CategoryID
        FOREIGN KEY (CategoryID)
        REFERENCES Categories(ID)
);

ALTER TABLE Products
ADD SubCategoryID int;

ALTER TABLE Products
ADD CONSTRAINT
FK_Products_SubCategoryID
FOREIGN KEY(SubCategoryID) REFERENCES
SubCategories(ID);

ALTER TABLE Categories
ADD ParentID int;

ALTER TABLE Categories
ADD CONSTRAINT
FK_Category_ParentID
FOREIGN KEY(ParentID) REFERENCES
Categories(ID)

ALTER TABLE Products
DROP CONSTRAINT [FK_Products_SubCategoryID];

ALTER TABLE Products
ADD ParentID int;

ALTER TABLE Products
ADD CONSTRAINT
FK_Products_ParentID
FOREIGN KEY(ParentID) REFERENCES 
Categories(ID)

CREATE TABLE StoreHasProducts
(
	StoreID INT NOT NULL,
    CONSTRAINT FK_StoreHasProducts_StoreID
    FOREIGN KEY (StoreID)
    REFERENCES Stores(ID),
	ProductID INT NOT NULL,
	CONSTRAINT FK_StoreHasProducts_ProductID
    FOREIGN KEY (ProductID)
    REFERENCES Products(ID),
);

----------------------------------------- Insert Data in database ----------------------------------------------

USE ShoppingSystemDB;

INSERT INTO Addresses
VALUES('USA','New York','Avenu','8878'),
('Bulgaria','Plovdiv','Dame Gruev','4465'),
('USA','California','Washinton Street','7775'),
('Canada','Toronto','Toronto street','9875'),
('New Zeland','New Zelanf','Avenu','2349'),
('Italy','Rim','Rim 54','8765'),
('Spain','Barcelona','Avenu 777','2347'),
('China','Pekin','Shao lin','1234'),
('Catar','Catar','Cat 86868','4330')


INSERT INTO Accounts
VALUES('test','test123','test@gmail.com',null,400.34,GETDATE(),1,1),
('admin','admin5456','admin@abv.bg',null,1200.50,GETDATE(),2,4),
('pesho','pesho12!','pesho@yahoo.com',null,2300,GETDATE(),3,6)

INSERT INTO Payments
VALUES('with cart'),('cash'),('with bank')

INSERT INTO Coupons
VALUES(12.44,'promo1',50,GETDATE()),
      (20.90,'promo2',100,GETDATE()),
	  (50.66,'promo3',200,GETDATE())

INSERT INTO Stores
VALUES('Decatlon', 'Plovdiv'),
      ('Star bucks','USA'),
	  ('Lidl','Germany')

INSERT INTO Users
VALUES('Test', 'Testov','0998789098','W',14,3,8),
('Admin','Admin','0887670809','M',24,5,4),
('Pesho', 'Peshev','0894444256','W',50,4,5)


SELECT * FROM ShoppingCart

INSERT INTO ShoppingCart
VALUES(5,3,GETDATE(), getdate() +2),
(6,1,GETDATE(), getdate() +5),
(7,2,GETDATE(), getdate() +8)

SELECT * FROM Users
Select * from ShoppingCart
SELECT * FROM ORDERS
SELECT * FROM Payments
SELECT * FROM Coupons
SELECT * FROM ORDERS

INSERT INTO ORDERS
VALUES(12.00,5.20, 17.20,'pending','good',5,1,1,1),
(40.00,5.20,45.20,'pending','bad',6,2,2,2),
(50.00,5.20,55.20,'pending','well',7,3,3,3)


INSERT INTO Delivery
VALUES('Test', 'Testov','0998789098','W',14,3,8),
('Admin','Admin','0887670809','M',24,5,4),
('Pesho', 'Peshev','0894444256','W',50,4,5)

SELECT * FROM Categories

INSERT INTO Categories
VALUES('Women Socks',null,'Women  Underwear',null),
      ('Bra & Brief Sets',null,'Women  Underwear',null),
	  ('Shapewear',null,'Women  Underwear',null),
      ('Hair Accessories',null,'Accessories',null),
      ('Sunglasses',null,'Accessories',null)

	  INSERT INTO SubCategories
      VALUES('Women Socks','Women  Underwear',4),
      ('Bra & Brief Sets','Women  Underwear',40),
	  ('Shapewear','Women  Underwear',41),
      ('Hair Accessories','Accessories',43),
      ('Sunglasses','Accessories',44)

	  SELECT * FROM Stores
	  
	  INSERT INTO UserHasFavStores
      VALUES(5,102),
      (6,100),
	  (7,101)


	  INSERT INTO Products
      VALUES('Socks',20, '5050','qwert',1, null,null,null,4, null),
      ('Sunglasses',34,'4040','fhdjdh',1,null,null,null,9,null),
	  ('Cap', 12, '2020', 'ghjgf',1, null,null,null,1,null)


	  INSERT INTO StoreHasProducts
      VALUES(102,3),
      (100,2),
	  (101,1)


	  INSERT INTO CartHasProduct
      VALUES(3,2),
      (2,1),
	  (1,3)

	    select * from Users
	  select * from Products
	  select * from Categories

	  INSERT INTO WishList
      VALUES(5,2,44),
      (6,1,6),
	  (7,3,40)

	  INSERT INTO CategoryHasProduct
      VALUES(1,44),
      (2,43),
	  (3,41)



-------------------------------------------------------------- 


USE ShoppingSystemDB
--------------------------- Procedure ---------------------------
CREATE PROCEDURE GetAllCategories
AS
BEGIN
SELECT
    c.[Name] AS Category,
   sc.[Name] AS SubCategory
FROM Categories c
JOIN SubCategories sc
ON c.ID = sc.CategoryID
END;

EXECUTE GetAllCategories;


CREATE PROCEDURE GetProductCategory
AS
BEGIN
SELECT
    p.[Name] as Product,
    sc.Name as SubCategory,
	c.Name as Category
FROM
Products p 
JOIN SubCategories sc
ON p.SubCategoryID = sc.ID
JOIN Categories c
ON c.ID = sc.CategoryID
END;

EXECUTE GetProductCategory;


CREATE PROCEDURE GetIdCategories
AS
BEGIN
SELECT
	c.ID,
    c.[Name] AS Category,
	sc.CategoryID,
   sc.[Name] AS SubCategory
FROM
Categories c
JOIN SubCategories sc
ON c.ID = sc.CategoryID
END;

EXECUTE  GetIdCategories;



CREATE PROCEDURE GetUserProduct
AS
BEGIN
SELECT a.Username AS Username ,p.[Name] AS Product,
       c.[Name] AS Category,
	   sc.[Name] AS SubCategory,
	   sd.UserID 
FROM
Products p 
JOIN SubCategories sc
ON p.SubCategoryID = sc.ID
JOIN Categories c
ON c.ID = sc.CategoryID
JOIN CartHasProduct chp 
ON chp.ProductID = p.ID
JOIN ShoppingCart sd
ON sd.ID = chp.CartID
JOIN Accounts a
ON a.ID = sd.ID
END;

----------------------------------- TRIGGER-----------------------------------------------

CREATE TABLE ShoppingCartQuantity(
  ID INT IDENTITY(1,1) PRIMARY KEY,
  CartID INT NOT NULL,
  ProductID INT NOT NULL,
  UpdatedAt DATETIME NOT NULL,
  OPERATION CHAR(3) NOT NULL,
  CHECK(OPERATION = 'INS' )
);


CREATE TRIGGER trigger_cart
ON CartHasProduct
AFTER INSERT
AS
BEGIN
  SET NOCOUNT ON;
  INSERT INTO ShoppingCartQuantity(
     CartID,
	 ProductID,
	 UpdatedAt,
	 OPERATION
  )
  SELECT 
    i.CartID,
	i.ProductID,
	GETDATE(),
	'INS'
	FROM
	inserted i

	END;


 CREATE TRIGGER deleteProductFromCart
 ON Orders
 AFTER INSERT 
 AS 
 BEGIN
 SET NOCOUNT ON
 DELETE FROM CartHasProduct 
 WHERE CartID IN(
              SELECT CartID 
			         FROM Orders 
					 WHERE CartID IN (
					       SELECT deleted.CartId from deleted)
						   )

END;

 ------------------------------------------- FUNCTION --------------------------------

CREATE FUNCTION quantityOfProducts(@CartID AS int)
RETURNS INT
AS
BEGIN
 DECLARE @quantityOfProducts int = 0;
 SELECT @quantityOfProducts =  COUNT(p.ID) 
 FROM Products p
 JOIN CartHasProduct c
 ON p.ID = c.ProductID
 JOIN ShoppingCart s 
 On s.ID = c.CartID
 WHERE c.CartID = @CartID
 RETURN(@quantityOfProducts)
 END;

 
-- SELECT dbo.quantityOfProductsInCart(1) as [Quantity of products in Cart];
