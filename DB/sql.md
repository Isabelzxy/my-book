## 如何优化SQL

### 避免全表扫描
1. 首先应考虑在Where 以及 order by 设计的列上建立索引
2. 避免在Where子句中对字段进行null值判断，否则会放弃索引
3. 在索引列上建立默认值
4. 避免在where子句中使用!=或<>操作符，否则会放弃索引
5. in，not in 慎用，用exists代替in，对连续的值可以用between
6. 避免在where中对字段列进行表达式及函数操作，避免在“=”左边进行函数、算术运算或其他表达式运算
7. 使用索引字段作为条件时，如果是复合索引，必须使用改索引的第一个字段作为条件，并且尽可能的让字段顺序与索引顺序一致
8. 索引列如果有大量重复的数据，查询将放弃索引
9. 一般索引数不要查过6个
10. 尽量使用数值型字段，若只含数值尽量不要设计成字符型，会降低查询和连接性能，增加存储开销（查询时会比较字符串中的每一个字符，和数值型只比较一次）
11. 尽量使用varchcar/nvarchar 代替char/nchar, 变长字段存储空间小
12. 任何查询都不要用select * , 只返回需要的列
13. 如果你的数据只有你所知的少量的几个, 最好使用 ENUM 类型
14. 用union all替换union
15. 把IP地址存成 UNSIGNED INT
    可以使用 INET_ATON() 来把一个字符串IP转成一个整形，并使用 INET_NTOA() 把一个整形转成一个字符串IP
14. 定期分析表和检查表
    分析表的语法：ANALYZE [LOCAL | NO_WRITE_TO_BINLOG] TABLE tb1_name[, tbl_name]...
15. 定期优化表
    优化表的语法：OPTIMIZE [LOCAL | NO_WRITE_TO_BINLOG] TABLE tb1_name [,tbl_name]...
    这个命令可以将表中的空间碎片进行合并，并且可以消除由于删除或者更新造成的空间浪费，但OPTIMIZE TABLE 命令只对MyISAM、 BDB 和InnoDB表起作用
    注意： analyze、check、optimize执行期间将对表进行锁定，因此一定注意要在MySQL数据库不繁忙的时候执行相关的操作。

