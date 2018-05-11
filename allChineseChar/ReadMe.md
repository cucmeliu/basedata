# 所有汉字 20902 个

Sql Server数据库汉字按字母、笔划、拼音首字母、排序

## 排序规则简介

什么叫排序规则呢？ms是这样描述的："在 microsoft sql server 2000 中，字符串的物理存储由排序规则控制。排序规则指定表示每个字符的位模式以及存储和比较字符所使用的规则。"


　　在查询分析器内执行下面语句，可以得到sql　server支持的所有排序规则。

　　　　select * from ::fn_helpcollations()

排序规则名称由两部份构成，前半部份是指本排序规则所支持的字符集。如：

　　chinese_prc_cs_ai_ws

前半部份：指unicode字符集，chinese_prc_指针对大陆简体字unicode的排序规则。
排序规则的后半部份即后缀含义：

　　_bin 二进制排序

　　_ci(cs) 是否区分大小写，ci不区分，cs区分

　　_ai(as) 是否区分重音，ai不区分，as区分　　　

　　_ki(ks) 是否区分假名类型,ki不区分，ks区分　

     _wi(ws) 是否区分宽度 wi不区分，ws区分　
	 
区分大小写:如果想让比较将大写字母和小写字母视为不等，请选择该选项。

区分重音:如果想让比较将重音和非重音字母视为不等，请选择该选项。如果选择该选项，比较还将重音不同的字母视为不等。

区分假名:如果想让比较将片假名和平假名日语音节视为不等，请选择该选项。

区分宽度:如果想让比较将半角字符和全角字符视为不等，请选择该选项

## 排序规则的应用

　　sql server提供了大量的windows和sqlserver专用的排序规则，但它的应用往往被开发人员所忽略。其实它在实践中大有用处。

　　例1:让表name列的内容按拼音排序：

create table #t(id int,name varchar(20))

insert #t select 1,中

union all select 2,国

union all select 3,人

union all select 4,阿


select * from #t order by name collate chinese_prc_cs_as_ks_ws

drop table #t

　　例2：让表name列的内容按姓氏笔划排序：

create table #t(id int,name varchar(20))

insert #t select 1,三

union all select 2,乙

union all select 3,二

union all select 4,一

union all select 5,十

select * from #t order by name collate chinese_prc_stroke_cs_as_ks_ws

drop table #t

## 在实践中排序规则应用的扩展

　　sql server汉字排序规则可以按拼音、笔划等排序，那么我们如何利用这种功能
来处理汉字的一些难题呢？我现在举个例子：

### 用排序规则的特性计算汉字笔划

　　要计算汉字笔划，我们得先做准备工作，我们知道，windows多国汉字，unicode目前
收录汉字共20902个。简体gbk码汉字unicode值从19968开始。

　　首先，我们先用sqlserver方法得到所有汉字，不用字典，我们简单利用sql语句就可以得到：

select top 20902 code=identity(int,19968,1) into #t from syscolumns a,syscolumns b

再用以下语句，我们就得到所有汉字，它是按unicode值排序的：

　　select code,nchar(code) as cnword from #t

　　然后，我们用select语句，让它按笔划排序。

select code,nchar(code) as cnword

from #t

order by nchar(code) collate chinese_prc_stroke_cs_as_ks_ws,code

结果：

code        cnword

----------- ------

19968       一

20008       丨

20022       丶

20031       丿

20032       乀

20033       乁

20057       乙

20058       乚

20059       乛

20101       亅

19969       丁
