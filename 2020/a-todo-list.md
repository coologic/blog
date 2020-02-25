

- 报表自动化
  - 商业智能(BI)与报表自动化
  - 数据仓库
  - 维度建模
  - 数仓建模流程
    - DW DM 分层
  - 建模经验
  - ETL 工具 Informatica
  - ETL 工具 ADF
  - ETL 工具 Informatica 与 ADF 对比
  - 增量更新的价值与业务数据库设计思考



# ETL tools

Informatica 与 Azure Data Factory 差异



mapping：



pipeline 在层次结构上 大于mapping，在内部能做的 aggregate 行为上 小于 mapping

datasets

connection

dataflow -- 中国部分区不可用  更适合做 merge 多表操作等



备份

通过 all 的 pipeline 导出得到相关的所有 json 文件



缓存

Informatica 有缓存，表结构修改有时候需要把 mapping 完整删除重建才可以重新获取 field 内容

Informatica 页面操作响应很慢

ADF 多人协作时，不要同时修改一个对象，否则以最后保存者的配置为准，不会 merge。粒度以左侧目录树形结构为准，任何打开后的操作都不会同步。甚至重命名文件夹与某人单独保存某个文件也会冲突（单独保存的文件会独立的存在旧的文件夹目录下）



# BI 商业智能化--报表

DW  DM

例子





