## postgresql

### PostgreSQL的优势
支持存储一些特殊的数据类型，比如：array、json、jsonb
对地理信息的存储与处理有更好的支持，所以它可以成为一个空间数据库，更好的管理数据测量和几何拓扑分析
可以快速构建REST API，通过PostgREST可以方便的为任何PostgreSQL数据库提供RESTful API的服务
支持树状结构，可以更方便的处理具备此类特性的数据存储
外部数据源支持，可以把MySQL、Oracle、CSV、Hadoop等当成自己数据库中的表来进行查询
对索引的支持更强，PostgreSQL支持 B-树、哈希、R-树和 Gist 索引。而MySQL取决于存储引擎。MyISAM：BTREE，InnoDB：BTREE。
事务隔离更好，MySQL 的事务隔离级别repeatable read并不能阻止常见的并发更新，得加锁才可以，但悲观锁会影响性能，手动实现乐观锁又复杂。而 PostgreSQL 的列里有隐藏的乐观锁 version 字段，默认的 repeatable read 级别就能保证并发更新的正确性，并且又有乐观锁的性能。
时间精度更高，可以精确到秒以下
字符支持更好，MySQL里需要utf8mb4才能显示emoji，PostgreSQL没这个坑
存储方式支持更大的数据量，PostgreSQL主表采用堆表存放，MySQL采用索引组织表，能够支持比MySQL更大的数据量。
序列支持更好，MySQL不支持多个表从同一个序列中取id，而PostgreSQL可以
增加列更简单，MySQL表增加列，基本上是重建表和索引，会花很长时间。PostgreSQL表增加列，只是在数据字典中增加表定义，不会重建表。

### mac安装
```
# 安装指定版本（推荐）
brew install postgresql@15

# 安装默认版本
brew install postgresql

# 检查
psql -V 或者 psql --version

# 初始化数据库
initdb --locale=C -E UTF-8 /usr/local/var/postgres

# 添加环境变量
echo 'export PATH="/opt/homebrew/opt/postgresql@15/bin:$PATH"' >> ~/.zshrc
source ~/.zshrc


# 配置并启动PostgreSQL服务
brew services start postgresql@15
```

### 数据类型
类型名称    存储长度    描述  范围

smallint 2 bytes 小范围整数类型 -32768 to +32767
integer 4 bytes 整数类型 -2147483648 to +2147483647
bigint 8 bytes 大范围数据类型 -9223372036854775808 to 9223372036854775807
decimal 可变 用户指定精度 up to 131072 digits before the decimal point; up to 16383 digits after the decimal point
numeric 可变 用户指定精度 up to 131072 digits before the decimal point; up to 16383 digits after the decimal point
real 4 bytes 变长，不精确 6位十进制精度
double precision 8 bytes 变长，不精确 15位十进制精度
smallserial 2 bytes small自增序列 1 to 32767                等效serial2
serial 4 bytes integer 自增序列 1 to 2147483647             等效serial4
bigserial 8 bytes bigint自增序列 1 to 9223372036854775807   等效serial8

### 查看postgresql版本
查看postgresql版本：SELECT version();

### postgresql查看pid，杀进程
查看所有进程：SELECT pid, usename, datname, query, state FROM pg_stat_activity;

查看所有被锁的进程：select * from pg_stat_activity where datname='wuhan' and wait_event_type='Lock';
或
select oid from pg_class where relname='可能锁表了的表' --oid是每个表隐藏的id
select pid from pg_locks where relation='上面查出的oid'

kill进程：SELECT pg_terminate_backend(pid);

### postgresql自增主键
* 建立自增主键序列：CREATE SEQUENCE table_name_id_seq START 1;，设置id字段的默认值为nextval('t_user_id_seq')
```
创建自增序列 CREATE SEQUENCE table_name_id_seq START 1;
字段默认值中设置 nextval('table_name_id_seq')
获取当前序列值：SELECT currval('sequence_name');
获取下一个序列值：SELECT nextval('sequence_name');

-- 设置序列所有者
ALTER SEQUENCE example_table_id_seq OWNED BY example_table.id;
-- 创建表
CREATE TABLE example_table (
    id INT PRIMARY KEY DEFAULT nextval('example_table_id_seq'),
    name TEXT NOT NULL
);
```

* 建立自增序列，同时指定最大最小值
```
-- 每次递增1 从1开始，最大值为2147483647，缓存1（只有内存中的序列号使用完后才会重新获取sequence）
CREATE SEQUENCE "public"."sys_test_id_seq" INCREMENT 1 MINVALUE  1 MAXVALUE 2147483647 START 1 CACHE 1;

alter sequence sys_test_id_seq start with 1 ; --只能增大 不能减小（start_value重置，但是nextval没有变化）
alter sequence sys_test_id_seq restart with 1 ; --可以增大也可以减小（start_value重置，nextval有变化，会自动从1开始）
SELECT setval('"public"."table_name_id_seq"', 1, true);  --从1开始（start_value重置，nextval有变化）
ALTER SEQUENCE "public"."sys_test_id_seq" OWNED BY "public"."sys_test"."id";  --设置序列所属空间及表和字段
ALTER SEQUENCE "public"."sys_test_id_seq" OWNER TO "root";  --设置序列所有者
```

* 通过指定字段为serial/bigserial类型达到递增的效果(PostgreSQL 10以前的版本)
其实就是自动创建一个序列，同时将列设置为INT，默认值设置为nextval('序列')。
```
CREATE TABLE example_table (
    id SERIAL PRIMARY KEY,
    name TEXT NOT NULL
);
```
* 使用identity column：CREATE TABLE uses_identity ( id bigint GENERATED ALWAYS AS IDENTITY PRIMARY KEY,
原理：实际上identify列，也使用了序列，如下： drop sequence test1_id_seq;(错误:  无法删除 序列 test1_id_seq, 因为 表 test1 字段 id 需要它)
```
create table test (id int GENERATED ALWAYS AS IDENTITY (cache 100), info text);  
create table test1 (id int GENERATED BY DEFAULT AS IDENTITY (cache 100), info text);  

-- ALWAYS，表示优先使用系统列生成的自增值。用户传值会报错。
-- BY DEFAULT，表示优先使用用户输入的值。如果用户没有输入值，会使用系统列生成的自增值。
-- cache 100，表示每次获取100个自增值，减少序列的访问次数，提高性能。

insert into test (id, info) values (1,'test');  -- 会报错，不能插入自增列的值
insert into test (id, info) OVERRIDING SYSTEM VALUE values (1,'test'); -- 覆盖系统列定义的自增值
insert into test1 (id, info) OVERRIDING user VALUE values (1000,'test');  -- 覆盖用户输入的值（使用系统列定义的自增值）  
```

查看所有序列：
```
--获取数据库中所有序列的列表：
SELECT sequence_schema, sequence_name FROM information_schema.sequences;

--同上
SELECT c.relname AS sequence_name
FROM pg_class c
JOIN pg_namespace n ON n.oid = c.relnamespace
WHERE c.relkind = 'S';
```

查看更详细的信息，比如序列的起始值、增量等
```
SELECT c.relname AS sequence_name,
       pg_get_expr(c.relminimumvalue, c.relowner) AS min_value,
       pg_get_expr(c.relmaximumvalue, c.relowner) AS max_value,
       pg_get_expr(c.relincrement, c.relowner) AS increment,
       c.relstart AS start_value
FROM pg_class c
JOIN pg_namespace n ON n.oid = c.relnamespace
WHERE c.relkind = 'S';

如果查询报错就使用 *：
SELECT *
FROM pg_class c
JOIN pg_namespace n ON n.oid = c.relnamespace
WHERE c.relkind = 'S';
```

### 时序数据库TimescaleDB
TimescaleDB是一个在PostgreSQL之上构建的时序数据库，它利用了关系型数据库的成熟性和灵活性，并针对时序数据进行了优化。
TimescaleDB通过使用分区表（hypertable）和连续聚集表（continuous aggregate）来处理时序数据，使得数据的存储和查询更加高效。

TimescaleDB具有以下特点：
- 与PostgreSQL兼容：TimescaleDB是一个PostgreSQL扩展，因此它兼容PostgreSQL，这意味着您可以使用标准的SQL语法来查询和管理时序数据，同时还能够利用PostgreSQL的强大功能。
- 水平扩展：TimescaleDB支持水平扩展，允许在需要时添加更多的节点，以处理大规模的时序数据，这使得它非常适合在云环境中构建弹性和高可用性的时序数据存储。
- 优化的查询性能：TimescaleDB使用了分区和数据分片技术，将数据分散到多个分区中，从而可以并行处理查询操作，提供优化的查询性能。
- 连续聚合：这是TimescaleDB的一个强大特性，它允许在数据插入的同时计算和维护聚合数据，从而大大减少了后续查询的计算成本。
- 自动数据分层：TimescaleDB支持数据分层，可以将历史数据分为不同的层级，从而更有效地管理长期存储的数据。这有助于在保持查询性能的同时控制存储成本。
- 高可用性和容错性：TimescaleDB支持在集群中复制数据以实现高可用性和容错性，确保数据的可靠性和持久性。
- 丰富的时间序列函数和操作：TimescaleDB提供了许多针对时间序列数据的内置函数和操作，使您可以轻松地进行时间序列分析和操作。

1. 安装TimescaleDB插件
安装TimescaleDB可以通过在PostgreSQL上加载TimescaleDB插件来完成。首先，确保已经安装了PostgreSQL数据库，并且具有管理员权限。然后，在命令行中执行以下步骤：
```
# 添加TimescaleDB的APT源
curl https://packagecloud.io/timescale/timescaledb/gpgkey | sudo apt-key add -
echo "deb https://packagecloud.io/timescale/timescaledb/ubuntu/ focal main" | sudo tee /etc/apt/sources.list.d/timescale_timescaledb.list
# 更新软件源
sudo apt-get update
# 安装TimescaleDB插件
sudo apt-get install timescaledb-postgresql-13
```

2. 创建TimescaleDB数据库
TimescaleDB的工作方式与传统的PostgreSQL数据库相似。我们可以使用createdb命令创建一个新的数据库，并在该数据库上加载TimescaleDB扩展：
```
# 创建一个新的数据库
createdb mydatabase
# 连接到该数据库
psql mydatabase
# 在数据库中加载TimescaleDB扩展
CREATE EXTENSION IF NOT EXISTS timescaledb CASCADE;
```

3. 扩展超级表功能
TimescaleDB中的基本数据管理单元称为超级表。超级表是基于普通表的一种特殊类型，它将时间序列数据根据时间进行分区和组织，从而实现更高效的查询性能。以下示例演示了如何创建一个超级表：
```
-- 创建超级表
CREATE TABLE conditions (
time        TIMESTAMPTZ       NOT NULL,
location    TEXT              NOT NULL,
temperature DOUBLE PRECISION  NULL,
humidity    DOUBLE PRECISION  NULL
);
-- 对超级表进行分区
SELECT create_hypertable('table_name', 'time_column');
```
创建Hypertable：TimescaleDB中的核心概念是Hypertable，它是一个逻辑表，负责将普通表划分成不同的时间段。其中，'table_name'是原始表的名称，'time_column'是存储时间信息的列。

三、数据写入和查询
1. 创建超级表

超级表的创建与普通表类似，但需要指定时间列。以下示例创建了一个包含时间列的超级表：
```
CREATE TABLE conditions (
time        TIMESTAMPTZ       NOT NULL,
location    TEXT              NOT NULL,
temperature DOUBLE PRECISION  NULL,
humidity    DOUBLE PRECISION  NULL
);
```
2. 超级表的分区
超级表的分区是TimescaleDB的一个重要特性，它可以根据时间将数据分散到不同的物理表中。通过分区，查询操作仅需要在相关的物理表上执行，从而提高查询性能。以下示例演示了如何为超级表添加分区：
```
-- 对超级表conditions按月份进行分区
SELECT create_hypertable('conditions', 'time', chunk_time_interval => INTERVAL '1 month');
```

3. 超级表的复制和副本
TimescaleDB支持复制和副本功能，可以在多个节点上创建超级表的副本，实现数据冗余和高可用性。以下示例展示了如何创建一个超级表的副本：
```
-- 在节点2上创建conditions超级表的副本
SELECT add_data_node('conditions', '2');
```

4. 插入数据
向超级表中插入数据与向普通表中插入数据类似。以下示例向超级表conditions插入一行数据：
```
INSERT INTO conditions (time, location, temperature, humidity)
VALUES ('2023-06-29 06:00:00', 'New York', 25.4, 60.2);
```

5. 更新和删除数据
在超级表中更新和删除数据与普通表相同。以下示例演示了如何更新和删除符合特定条件的数据：
```
-- 更新温度大于30的记录
UPDATE conditions SET temperature = 30.0 WHERE temperature > 30.0;
-- 删除湿度小于50的记录
DELETE FROM conditions WHERE humidity < 50.0;
```

6. 时间序列聚合函数
TimescaleDB提供了一系列内置的时间序列聚合函数，用于计算给定时间范围内的统计信息，如平均值、最大值、最小值等。以下示例展示了如何使用时间序列聚合函数：
```
-- 计算最近一小时的平均温度
SELECT time_bucket('1 hour', time) AS hour,
AVG(temperature) AS average_temperature
FROM conditions
WHERE time > NOW() - INTERVAL '1 hour'
GROUP BY hour;
```

7. 查询和过滤数据
查询和过滤数据时，可以使用常规的SQL查询语句。以下示例展示了如何查询超级表中的数据：
```
-- 查询所有温度大于25的记录
SELECT *
FROM conditions
WHERE temperature > 25.0;
```

8. 其他数据查询
TimescaleDB提供了许多用于查询时序数据的功能，例如：
查询最近一小时的数据：
```
SELECT * FROM table_name WHERE time_column > NOW() - INTERVAL '1 hour';
聚合查询：
SELECT time_bucket('5 minutes', time_column) AS bucket, AVG(value_column) AS avg_value
FROM table_name GROUP BY bucket ORDER BY bucket;
以上查询将结果按5分钟为一个时间段进行聚合，并计算每个时间段内的平均值。
```

四、连续聚集表
连续聚集表是TimescaleDB的一个重要特性，它可以在后台自动维护预定义的聚合数据。通过使用连续聚集表，可以极大地提高大规模时序数据的查询性能。以下是创建和使用连续聚集表的示例：

1. 创建连续聚集表：
```
SELECT create_continuous_aggregate('ca_table', 'SELECT time_bucket('5 minutes', time_column) AS bucket, AVG(value_column) AS avg_value FROM table_name GROUP BY bucket');
```

2. 查询连续聚集表：
```
SELECT * FROM ca_table;
```

五、分区管理
TimescaleDB使用分区（partitioning）来管理大规模的时序数据。它可以将表按照时间范围进行自动划分，提高查询性能和数据的可维护性。以下是创建和管理分区的示例：

1. 创建分区：
```
SELECT add_hypertable_partition('table_name', TIMESTAMP '2023-06-01', TIMESTAMP '2023-07-01');
```

2. 管理分区：
查询所有分区：
```
SELECT show_partitions('table_name');
```
删除分区：
```
SELECT drop_partition('table_name', TIMESTAMP '2023-06-01');
```

3. 数据连续性和存储优化
TimescaleDB通过数据连续性来优化存储空间和查询性能。数据连续性指的是调整超级表的分区和存储策略，以确保相邻时间段的数据存储在一起，从而提高查询性能。

4. 数据保留策略
TimescaleDB允许定义数据的保留策略，以自动删除过时的数据。通过设置保留策略，可以控制超级表中数据的保存时长，以及是否自动删除过期数据。

六. 综合使用示例
1. 创建超级表并插入数据
假设我们有一个名为conditions的超级表，包含时间、地点、温度和湿度字段。以下示例演示了如何创建该超级表，并向其中插入一些数据：
```
CREATE TABLE conditions (
time        TIMESTAMPTZ       NOT NULL,
location    TEXT              NOT NULL,
temperature DOUBLE PRECISION  NULL,
humidity    DOUBLE PRECISION  NULL
);
SELECT create_hypertable('conditions', 'time');
INSERT INTO conditions (time, location, temperature, humidity)
VALUES
('2023-06-29 06:00:00', 'New York', 25.4, 60.2),
('2023-06-29 07:00:00', 'New York', 26.8, 58.9),
('2023-06-29 08:00:00', 'New York', 28.3, 57.1);
```

2. 查询最新的N条数据
假设我们想要查询最新的3条温度数据。以下示例演示了如何使用LIMIT子句和ORDER BY子句进行查询：
```
SELECT *
FROM conditions
ORDER BY time DESC
LIMIT 3;
```

3. 执行时间范围内的聚合查询
假设我们想要计算过去一小时内每分钟的平均温度。以下示例展示了如何使用时间序列聚合函数和时间戳桶函数进行查询：
```
SELECT time_bucket('1 minute', time) AS minute,
AVG(temperature) AS average_temperature
FROM conditions
WHERE time > NOW() - INTERVAL '1 hour'
GROUP BY minute;
```

### postgresql使用地图数据库
参考文档：


postgresql type geography
PostgreSQL的geography类型是一种用于存储和查询地理空间数据的数据类型。它使用一种地理空间参考系统（通常是WGS84），允许执行如距离计算、方向计算等地理空间运算。

要使用geography类型，首先需要确保PostGIS扩展已经安装并被启用。以下是如何在PostgreSQL中创建一个包含geography类型列的表的示例：
```
-- 确保PostGIS扩展已安装并启用
CREATE EXTENSION IF NOT EXISTS postgis;
 
-- 创建一个包含geography类型列的表
CREATE TABLE locations (
    id SERIAL PRIMARY KEY,
    name VARCHAR(255),
    geog GEOGRAPHY(Point, 4326) -- 使用点类型，SRID为4326（WGS84）
);
 
-- 插入一个地点数据
INSERT INTO locations (name, geog) VALUES ('New York', 'POINT(-74.006 40.7128)');
 
-- 查询特定距离内的地点
SELECT * FROM locations WHERE ST_DWithin(geog, ST_GeogFromText('POINT(-74.006 40.7128)'), 1000);
```
如需要查看postgresql安装了哪些拓展
```
SELECT * FROM pg_extension;

结果示例：
oid,    extname,extowner,extnamespace,extrelocatable,extversion

14381	plpgsql	10	11	f	1.0
16979	postgis	10	2200	f	3.0.0
17982	postgis_topology	10	17981	f	3.0.0
18125	fuzzystrmatch	10	2200	t	1.1
18137	postgis_tiger_geocoder	10	18136	f	3.0.0
18565	address_standardizer	10	2200	t	3.0.0
18572	address_standardizer_data_us	10	2200	t	3.0.0
18615	postgis_raster	10	2200	f	3.0.0
16445	timescaledb	10	2200	f	1.7.0
```

使用示例：计算与给定点位的距离（参数为字符串，需要自行转化为geography格式）
```
    select
    t.ID as id,
    t.AREA_CODE as areaCode,
    t.NAME as name,
    t.SHORTNAME as shortname,
    t.apprise as apprise,
    t.train_gps as trainGps,
    t.video_intro_url as videoIntroUrl,
    t.school_img as schoolImg,
    t.is_open_timing as isOpenTiming,
    t.consult_phone as consultPhone,
    t.stars as stars,
   
    round((ST_Distance(
--      ST_SetSRID(ST_MakePoint(114.55750231062069::NUMERIC,30.488116978027715::NUMERIC),4326)::geography,
        ST_SetSRID(ST_MakePoint(split_part('114.55750231062069,30.488116978027715',',',1)::NUMERIC,split_part('114.55750231062069,30.488116978027715',',',1)::NUMERIC),4326)::geography,
        ST_SetSRID(ST_MakePoint(split_part(t.train_gps,',',1)::NUMERIC,split_part(t.train_gps,',',2)::NUMERIC),4326)::geography
    )/1000)::NUMERIC ,3)  as distance
    from school t
    where t.id = '2144751407927662'
```
函数说明：
- split_part：分隔字符串，参数：字符串，分割字符，取位数
- ST_MakePoint：创建一个2D XY、3D XYZ 或 4D XYZM 的点几何对象，参数为2-4个：ST_MakePoint(float x, float y, float z, float m);
- ST_SetSRID：为创建的点指定一个空间参考标识码
- ST_Distance：计算距离，传入两个点位
- ::geography:指定为地理空间数据类型
