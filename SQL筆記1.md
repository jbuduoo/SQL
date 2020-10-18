# SQL FOR XML RAW 相關資料
---

## 1.SQL語法:在輸出XML中加上首尾兩段文字。
### 解法一:
```
DECLARE @mybin1 varchar(1000),@mybin2 varchar(1000)
SET @mybin1='<?xml version="1.0" encoding="UTF-8"?><inventory xmlns="http://www.demandware.com/xml/impex/inventory/2007-05-31">'
SET @mybin2='</inventory>'

SELECT @mybin1 +(SELECT name,sex from kl.dbo.member  FOR XML RAW('bcd'),ROOT('asdfas'))+@mybin2
```

```
<?xml version="1.0" encoding="UTF-8"?>
<inventory xmlns="http://www.demandware.com/xml/impex/inventory/2007-05-31"><asdfas>
    <bcd name="jacky     " sex="m         "/>
    <bcd name="ada       " sex="w         "/>
    <bcd name="able      " sex="m         "/>
    <bcd name="colbel    " sex="m         "/>
</asdfas>
</inventory>
```

### 解法二:
```
declare @top as varchar(200) = 'inventory'
declare @xmlns as varchar(200) = 'http://www.demandware.com/xml/impex/inventory/2007-05-31'
declare @header1 as varchar(200) = '<?xml version="1.0" encoding="UTF-8"?>'
declare @header2 as varchar(200) = '<'+@top+' xmlns="'+@xmlns+'"'
declare @end2 as varchar(200) = '</'+@top+'>'

select @header1 = '<?xml version="1.0" encoding="UTF-8"?>'
select @header1 + @header2 + @end2
```
![](https://i.imgur.com/ZSLv0mw.png)






## 2.SQL語法:
a.使用@將欄位值加入屬性，
b.使用'x-default' as [display-name/@xml:lang]製造一組屬性。
c.使用case when [Published] = 0 then 'false' when [Published] = 1 then 'true' end as [onlineflag]，將0轉成false及1轉成true
```
select ID as [@category-id] ,'x-default' as [display-name/@xml:lang], [Name] as [display-name]
, case when [Published] = 0 then 'false' when [Published] = 1 then 'true' end as [onlineflag]
from Catalog
FOR XML PATH('category'),ROOT('catalog
```

![](https://i.imgur.com/Tqhjxhi.png)


## 3.sql xml attribute field name
a.''很重要，沒有的話，就會報錯。

```
select 
'name' as [custom-attribute/@attribute-id],
name as[custom-attribute],'',
'sex' as [custom-attribute/@attribute-id],
sex as[custom-attribute] 

from kl.dbo.member
FOR XML PATH('category'),ROOT('catalog')
```
![](https://i.imgur.com/RMIwRuH.png)

參考資料:https://stackoverflow.com/questions/39635878/sql-server-specify-column-name-as-attribute-and-node-value-for-xml

## 4.每一段xml前面都加header
```

DECLARE @mybin1 varchar(1000)
SET @mybin1='        <header pricebook-id="NTD-testpricebook">            <currency>TWD</currency>            <display-name xml:lang="x-default">testpricebook</display-name>            <description xml:lang="x-default">Just test NTD</description>            <online-flag>true</online-flag>            <online-from>2020-10-01T15:20:00.000Z</online-from>            <online-to>2020-10-06T01:01:00.000Z</online-to>        </header>'

declare @Txt nvarchar(max)=''

select @Txt=(
select 
@mybin1,
name as [price-tables/price-table],
sex as [price-tables/amount]

from kl.dbo.member
FOR XML PATH(''),root('pricebook')
)
select @Txt=(select Replace(@Txt,'&lt;','<'))
select Replace(@Txt,'&gt;','>')XML_Txt
```
![](https://i.imgur.com/Z3Ur6Ng.png)


## 4.只加一個header
```
DECLARE @mybin1 varchar(1000)
SET @mybin1='        <header pricebook-id="NTD-testpricebook">            <currency>TWD</currency>            <display-name xml:lang="x-default">testpricebook</display-name>            <description xml:lang="x-default">Just test NTD</description>            <online-flag>true</online-flag>            <online-from>2020-10-01T15:20:00.000Z</online-from>            <online-to>2020-10-06T01:01:00.000Z</online-to>        </header>'

declare @Txt nvarchar(max)=''

select @Txt=(select @mybin1,(
select 
name as [price-tables/price-table],
sex as [price-tables/amount]
from kl.dbo.member
FOR XML PATH('')
)
FOR XML PATH,root('pricebook')
)
select @Txt=(select Replace(@Txt,'&lt;','<'))
select Replace(@Txt,'&gt;','>')XML_Txt
```
![](https://i.imgur.com/OmkMdwh.png)


## SQL 取代二個空白變一個，
```

略


--去除空格
while charindex('  ',@Txt  ) > 0
begin
   set @Txt = replace(@Txt, '  ', ' ')
end

--取代
SET @Txt= Replace(@Txt,'&lt;','<')
select Replace(@Txt,'&gt;','>')_xml
```
 
原本的畫面:
![](https://i.imgur.com/PSuBbuB.png)
畫面上的字旁邊的空白就只剩一個了:
![](https://i.imgur.com/5rjMx1h.png)

## 一個TAG兩個屬性
```
select name as [@name],sex as [@sex],name,sex from member FOR XML PATH('product'), Root('Catalog')
```
![](https://i.imgur.com/RXVjESd.png)


### 多層 select 產生的問題
第二層select 會造成< 變成@lt; 及 >變成&gt;
第三層select 會造成@lt; 變成@amp;lt; 及 &gt變成&amp;gt;

處理方式:
```
SET @Txt= Replace(@Txt,'&amp;lt;','<')
SET @Txt= Replace(@Txt,'&amp;gt;','>')

SET @Txt= Replace(@Txt,'&lt;','<')
SELECT Replace(@Txt,'&gt;','>')_XML
```

## 這個TAG沒有辦法合起來
![](https://i.imgur.com/tYI2kEz.png)

處理的方式
```
SELECT 'hello' AS Node1,    
    (SELECT TOP 2 SiteId
     FROM [dbo].[Sites]
       FOR XML PATH('Site')) AS Sites
FOR XML PATH('ResultDetails')
```
結果
```
<ResultDetails>
  <row>
    <Node1>hello</Node1>
    <Sites>&lt;Site&gt;&lt;siteId&gt;102&lt;/siteId&gt;&lt;/Site&gt;&lt;Site&gt;&lt;siteId&gt;1&lt;/siteId&gt;&lt;/Site&gt;</Sites>
  </row>
</ResultDetails>
```

## XML中會有&lt;及&gt;之類的字

加個TYPE就行了
```
SELECT
    'hello' AS Node1
    , (
        SELECT TOP 2 SiteId 
            FROM [dbo].[Sites] 
        FOR XML PATH('Site'), TYPE
    ) AS Sites 
FOR XML PATH('ResultDetails') 
```