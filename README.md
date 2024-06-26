**Note: debezium has been upgraded and the repository is no longer maintained. Please see the [latest official documentation](https://debezium.io/releases/)**

[![Debezium architecture](https://debezium.io/documentation/reference/1.4/_images/debezium-architecture.png)](https://debezium.io/documentation/reference/1.4/connectors/mysql.html)

[![Build Status(https://github.com/holmofy/debezium-datetime-converter/actions/workflows/release.yml/badge.svg)](https://github.com/holmofy/debezium-datetime-converter/actions/workflows/release.yml/badge.svg)](https://github.com/holmofy/debezium-datetime-converter/releases)

# debezium-datetime-converter

Debezium [custom converter](https://debezium.io/documentation/reference/development/converters.html) is used to deal with mysql [datetime type problems](https://debezium.io/documentation/reference/1.5/connectors/mysql.html#mysql-temporal-types).

| mysql                               | binlog-connector                         | debezium                          | schema                 |
| ----------------------------------- | ---------------------------------------- | --------------------------------- | ----------------------------------- |
| date<br>(2021-01-28)                | LocalDate<br/>(2021-01-28)               | Integer<br/>(18655)               | io.debezium.time.Date               |
| time<br/>(17:29:04)                 | Duration<br/>(PT17H29M4S)                | Long<br/>(62944000000)            | io.debezium.time.Time               |
| timestamp<br/>(2021-01-28 17:29:04) | ZonedDateTime<br/>(2021-01-28T09:29:04Z) | String<br/>(2021-01-28T09:29:04Z) | io.debezium.time.ZonedTimestamp     |
| Datetime<br/>(2021-01-28 17:29:04)  | LocalDateTime<br/>(2021-01-28T17:29:04)  | Long<br/>(1611854944000)          | io.debezium.time.Timestamp          |

> For details, please refer to [this article](https://blog.hufeifei.cn/2021/03/DB/mysql-binlog-parser/)

# Usage

1. [Download](https://github.com/holmofy/debezium-datetime-converter/releases) the extended jar package and put it in the same level directory of the debezium plugin.
创建目录存放这个类
2. In debezium-connector, Add the following configuration: 
然后添加配置
```properties
Properties debeziumProperties = new Properties();
// 自定义解释器
// 设置解释器及解释器参数
debeziumProperties.put("converters", "MySqlDateTimeConverter");
debeziumProperties.put("MySqlDateTimeConverter.type", "io.debezium.connector.mysql.MySqlDateTimeConverter");
debeziumProperties.put("MySqlDateTimeConverter.database.type", "mysql");
// 自定义格式，可选，解析时间格式
debeziumProperties.put("MySqlDateTimeConverter.format.datetime", "yyyy-MM-dd HH:mm:ss");
debeziumProperties.put("MySqlDateTimeConverter.format.date", "yyyy-MM-dd");
debeziumProperties.put("MySqlDateTimeConverter.format.time", "HH:mm:ss");
// 解析数字类型格式
debeziumProperties.setProperty("bigint.unsigned.handling.mode", "long");
debeziumProperties.setProperty("decimal.handling.mode", "double");
```
或者
```properties
connector.class=io.debezium.connector.mysql.MySqlConnector
# ...
converters=datetime
datetime.type=com.darcytech.debezium.converter.MySqlDateTimeConverter
datetime.format.date=yyyy-MM-dd
datetime.format.time=HH:mm:ss
datetime.format.datetime=yyyy-MM-dd HH:mm:ss
datetime.format.timestamp=yyyy-MM-dd HH:mm:ss
datetime.format.timestamp.zone=UTC+8
```
