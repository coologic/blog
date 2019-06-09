title: " Qt数据库操作\t\t"
tags:
  - qt
  - sqlite
  - 数据库
url: 335.html
id: 335
categories:
  - Qt
date: 2017-11-25 21:59:37
---
基本操作
----

### 连接
```
if(!QSqlDatabase::contains()) {
    QSqlDatabase database = QSqlDatabase::addDatabase("QSQLITE");//第二参数不提供名字，使用默认名称，第一个参数为数据库类型名
    database.setDatabaseName(m_db_file_name);//数据库名--sqlite是文件名，sqlite不用写下面的配置
    database.setHostName("techieliang.com");    //数据库主机名   
    database.setUserName("user");        //数据库用户名   
    database.setPassword("******");        //数据库密码  
    if(!database.open()) {
        qDebug()<<database.lastError();
    }
}
```
注意addDatabase有两个参数，第二个参数是用于给此连接命名的，若不命名则为默认名称。 默认名称为：qt\_sql\_default_connection
```
static QSqlDatabase addDatabase(QSqlDriver* driver,
                                 const QString& connectionName = QLatin1String(defaultConnection));
```
### 断开连接

虽然关闭程序以后默认会断开，不会导致文件永久占用，但是建议数据库用完主动断开
```
QSqlDatabase database = QSqlDatabase::database();//根据连接名称获取数据库，不填写则为默认连接名
database.close();
```
### sql指令操作

简单一次操作：
```
const static QString mInsertCategorySql =
        "insert into category values (null,?)";//增，name名字
```
> 如果有自增量，或其他不需要输入的字段，只需要在对应位置输入null即可，注意不是字符串null 
> 
> 对于需要填充的地方可以用？代替，后续可直接用addBindValue或者bindValue指令替换，按照？出现顺序替换
```
bool SqlManager::InsertCategorySql(QString name) {
    QSqlQuery sql_query;//默认构造使用默认数据库
    sql_query.prepare(mInsertCategorySql);
    sql_query.addBindValue(name);
    if(!sql_query.exec()) {
        qDebug() << sql_query.lastError();
        return false;
    }
    return true;
}
```
多字段：
```
const static QString mInsertJournalSql =
        "insert into journal values (null,:catagory,:date,:value,:remark,:auditing)";
```
> 使用":XXXX"作为占位符，使用bindValue指令替换 
>
> 注意，只需要给出对应类型即可，Qt会自动进行转换，包括日期、时间均只给出Qt对应类型即可 
>
> 在Sqlite中日期格式AAAA-BB-CC，也可以以字符串形式输入但需要保证格式正确，不建议自行转换格式
```
bool SqlManager::InsertJournalSql(int catagory_id,
                                  QDate date,
                                  float value,
                                  QString remark,
                                  bool auditing) {
    QSqlQuery sql_query;//默认构造使用默认数据库
    sql_query.prepare(mInsertJournalSql);
    sql_query.bindValue(":catagory",catagory_id);
    sql_query.bindValue(":date",date);
    sql_query.bindValue(":value",value);
    sql_query.bindValue(":remark",remark);
    sql_query.bindValue(":auditing",auditing);
    if(!sql_query.exec()) {
        qDebug() << sql_query.lastError();
        return false;
    }
    return true;
}
```
有返回值的操作：
```
const static QString mSelectAllJournalSql =
        "select * from journal ORDER BY data DESC";//查询所有
```
```
struct JournalStruct {
    long long id;//分类id
    QString catagory;//分类
    QString date;//日期
    float value;//值
    QString remark;//备注
};
QList<JournalStruct> SqlManager::SelectUnauditJournalSql(bool is_all) {
    QSqlQuery sql_query;//默认构造使用默认数据库
    if(is_all)
        sql_query.prepare(mSelectAllJournalSql);
    else
        sql_query.prepare(mSelectUnauditJournalSql);
    QList<JournalStruct> s;
    if(!sql_query.exec()) {
        qDebug() << sql_query.lastError();
        return s;
    }
    else {
        //建立类型索引
        auto cates = SelectCategorySql();
        while(sql_query.next()) {
            s.append({sql_query.value(0).toLongLong(),
                      sql_query.value(1).toInt(),
                     sql_query.value(2).toString(),
                     sql_query.value(3).toFloat(),
                     sql_query.value(4).toString()});
        }
        return s;
    }
}
```
注意：QSqlQuery 结果默认指向空，必须先调用一次next()会指向第一个结果值，通过.value(0)取对应字段值，通过toXXX转换为对应格式。 详细操作代码请见流水账记录软件→[GitHub](https://github.com/TechieLiang/JournalAccountRecord)

其他
--

### 多数据库情况下QSqlQuery 的使用

上述所有操作，均使用了Qt默认名称数据库，故可以直接通过 QSqlQuery XX构造实例。 若指定了特定的数据库名称，或者有多个数据库需要使用

`QSqlQuery(QSqlDatabase db);`

其中db可以下面的函数获取
```
static QSqlDatabase database(const QString& connectionName = QLatin1String(defaultConnection),
                                 bool open = true);

QSqlQuery query(QSqlDatabase::database("connectionName"));
```
### Qt支持的数据库

Driver Type|Description
---|---
QDB2|IBM DB2
QIBASE|Borland InterBase Driver
QMYSQL|MySQL Driver
QOCI|Oracle Call Interface Driver
QODBC|ODBC Driver (includes Microsoft SQL Server)
QPSQL|PostgreSQL Driver
QSQLITE|[SQLite](qtsql-attribution-sqlite.html) version 3 or above
QSQLITE2|[SQLite](qtsql-attribution-sqlite.html) version 2
QTDS|Sybase Adaptive Server

上述数据取自Qt帮助文档中QSqlDatabase的描述，其中sqlite驱动默认集成，其余数据库需要安装对应驱动否则无法使用 

关于驱动的具体说明请见：[SQL Database Drivers](http://doc.qt.io/qt-5/sql-driver.html)帮助文档

### 建议使用QSqlQuery及bindValue操作

不建议使用qstring的arg()方法自行拼接字符串，使用bindValue可以有效的防止SQL注入。 

同时此方式可读性强，也对对特殊字符具有良好的支持性。