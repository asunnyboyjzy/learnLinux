db2
1.索引
底层:b+树 
本身占据空间，在增删改时会有额外维护动作，因此不能太多
data page/index page
索引扫描 匹配索引扫描 不匹配索引扫描 只扫描索引
可索引谓词 = 
order by /group by 可以考虑建立索引
复合索引 考虑好列的顺序{索引顺序}过滤因子
索引3-5个
避免索引谓词的列上使用复杂表达式可能{}无法索引  索引的列尽可能不同的值



2.游标
只读游标
数据传输更快
可更新游标

3.notice
可以使用一条就不要使用两条语句
配合使用fetch first n rows only/optimize for n rows
查看执行计划
db2expIn -d sample -q "SQL" -t -g -i
db2exfmt诊断工具
查看某个package的执行计划


4.日常操作
reorg减少表空间的碎片化
离线reorg 只读速度快
在线reorg 可读写速度慢
查看表状态 load query table tabname
查看统计信息db2 reorgchk current statistics on table tabname (or all)
runstats
大量数据装载 创建新索引 reorg
语法 runstats on table db2admin.department
load 装载数据 
export 导出
import 导入
单事务控制在5万条内
db2 drop nickname <youmk>
表空间和大字段
数据和索引表空间分开存储
分区索引 
全局索引


