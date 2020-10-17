# sql

[![hackmd-github-sync-badge](https://hackmd.io/_hX0TVpHQ7mZbS0yGXAffg/badge)](https://hackmd.io/_hX0TVpHQ7mZbS0yGXAffg)


## 一次insert多筆資料
```
insert into @member
values('jacky','m')
,('ada','w')
,('able','m')
,('colbel','m')
```

## 宣告一個資料表，但不會實際存任何資料到資料庫裡。
```
declare @member table(
	name nvarchar(50)
	,sex varchar(1)
)
insert into @member
values('jacky','m')
,('ada','w')
,('able','m')
,('colbel','m')

declare @Txt nvarchar(max) = ''
select @Txt = (
	select name AS '@ABC'
	,sex
	from @member 
	FOR XML RAW('RAW')
	,ROOT('ROOT')
	,ELEMENTS
)
select Replace(@Txt,'_x0040_','@') XML_Txt
```
## GO	GO只是SQL Server管理器（SSMS）中用來提交T-SQL語句的一個標誌，若GO後面有數字，則為執行的次數。

## SET ANSI_NULLS ON	
還沒找到

## SET QUOTED_IDENTIFIER ON	
還沒找到

## 宣告變數
```
DECLARE @count int, @x int, @y nvarchar(10)

-- 檢查變數的初始值
SELECT [@count] = @count, [@x] = @x, [@y] = @y

-- 使用 SET 指派值
SET @count = 1

-- 使用 SELECT 指派值
SELECT @x = 0, @y = 'alexc'

-- 檢查變數的設定值
SELECT [@count] = @count, [@x] = @x, [@y] = @y
```
https://ithelp.ithome.com.tw/articles/10009411

## SQL While迴圈
```
--定義迴圈參數
DECLARE  
@TotalNum INT, --執行次數
@Num INT       --目前次數

--設定迴圈參數
SET @TotalNum = 10 --執行次數
SET @Num =1        --目前次數 

--執行WHILE迴圈
WHILE @Num <= @TotalNum  --當目前次數小於等於執行次數
BEGIN

    /*
    這裡放要執行的SQL
    */

    --設定目前次數+1
    SET @Num = @Num + 1
END
```
https://dotblogs.com.tw/berrynote/2016/08/06/120741

## 資料怎麼分批匯出及分批匯入?
不知道

## 產生1000筆或3000筆insert的資料

- REPLICATE ( 字串 , 重複幾次)   --將字串值重複指定的次數
- RIGHT(字串,取幾個字元)
- select  cast(c1  as  integer)  from t1  //將欄位值取出並轉換為數值型態
```
--定義迴圈參數
DECLARE  
@TotalNum INT, --執行次數
@Num INT     , --目前次數
@ProductNumber [nvarchar](100) 

DECLARE @Result nvarchar(9)  
  
--設定迴圈參數
SET @TotalNum = 10000 --執行次數
SET @Num =1        --目前次數 

--執行WHILE迴圈
WHILE @Num <= @TotalNum  --當目前次數小於等於執行次數
BEGIN

    SELECT @Result = 'G'+RIGHT(REPLICATE('0', 7) + CAST(@Num as VARCHAR),8)  
	INSERT INTO [dbo].[pricebooks]([ProductNumber],[Price]) VALUES (@Result,@Num)

    SET @Num = @Num + 1
END
```

## 可以練習下SQL指令的地方，新北市垃車。

![](https://i.imgur.com/iWwVGqy.png)

https://sheethub.com/data.ntpc.gov.tw/%E6%96%B0%E5%8C%97%E5%B8%82%E5%9E%83%E5%9C%BE%E8%BB%8A%E8%B7%AF%E7%B7%9A/sql?sql=SELECT+*+FROM+this+ORDER+BY+_id_+ASC

## T-SQL是什麼?
Transact-SQL（又稱T-SQL），是在Microsoft SQL Server和Sybase SQL Server上的ANSI SQL實作

### 控制流語法
Transact-SQL可支援下列的控制流程語法（control-flow）：

- BEGIN ... END，標示SQL指令區塊，使用BEGIN ... END包裝的指令會被視為同一個指令區塊。
- IF ... ELSE的條件式，並可支援巢狀式的IF判斷式，若IF或ELSE中的指令包含兩個以上，則必須要使用BEGIN ... END來標示區塊，否則會發生語法檢查錯誤。
- WHILE迴圈，這也是Transact-SQL中唯一支援的迴圈，迴圈中的指令要用BEGIN...END包裝。
- RETURN，可強制終止區塊的執行。
- WAITFOR，可強制讓語句等待指定時間後才繼續執行。
- GOTO，可導向執行指令到指定的位置。
- 自訂變數
### 在Transact-SQL中，可以利用DECLARE來宣告變數，用SET來設定變數值，用SELECT @var = column的方式，由一個語句的回傳值中來取得變數值。

- DECLARE @v int -- declare a variable
- SET @v = 50 -- set variable directly.
- SELECT @v = SUM(Qty) FROM SaleItemRecords  WHERE SaleID = 53928 -- set variable from a result of statement

資料來源:https://zh.wikipedia.org/wiki/Transact-SQL