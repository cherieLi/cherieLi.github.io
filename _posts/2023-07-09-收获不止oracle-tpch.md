#### 收获不止oracle
https://github.com/liangjingbin99/shouhuo/tree/master    


identity列  
https://www.gpsos.es/2022/02/columnas-identity-oracle-12/    
https://www.cnblogs.com/xqzt/p/4455149.html  

权限：三权分立    
角色，系统，对象    

#### 命名规范
表以t_为前缀；  
视图以v_为前缀；  
同义词以s_为前缀；  
簇表以c_为前缀；  
序列以seq_为前缀；  
存储过程以p_为前缀；  
函数以f_为前缀；  
包以pkg_为前缀；  
类以type_为前缀；  
外键以fk_为前缀；  
主键以pk_为前缀；  
唯一索引以ux_为前缀；  
普通索引以idx_为前缀；  
位图索引以bx_为前缀；  
函数索引以fx_为前缀  

#### 小技巧
```
只取一行：https://www.jianshu.com/p/7f5f4098a2d0

```
#### TPC-H
1、官网下载最新的TPC-H的zip包

http://tpc.org/tpch/default.asp

2、解压配置与安装

解压后，进入dbgen目录

cp makefile.suite Makefile

vi Makefile

修改配置参数：
```
CC=gcc
DATABASE=ORACLE
MACHINE=LINUX
WORKLOAD=TPCH
```
编译
```
make
```
3、TPC-H数据生成  
```
./dbgen -s 100 -f  生成100G的数据，ll -h *.tbl可查看
./dbgen -vf -s 1  生成标准1G数据
./dbgen -s 0.01 -f 生成少量数据  
```
4、转csv数据  
```
for i in `ls *.tbl`; do sed 's/|$//' $i > ${i/tbl/csv}; done
```
将tbl文件转成csv格式并去掉行尾|分割符

转成.csv格式的文件在 ../dbgen目录下，可以通过ll -h *.csv查到
```
  24M  customer.csv
 719M  lineitem.csv
 2.2K  nation.csv
 163M  orders.csv
 23M   part.csv
113M   partsupp.csv
 384   region.csv
1.4M   supplier.csv

CREATE TABLE region (
          R_REGIONKEY       INTEGER         NOT NULL,
          R_NAME            CHAR(25)        NOT NULL,
          R_COMMENT         VARCHAR(152)
        ) ORGANIZATION LSC
ORDER BY (R_REGIONKEY);
      
CREATE TABLE nation (
          N_NATIONKEY       INTEGER         NOT NULL,
          N_NAME            CHAR(25)        NOT NULL,
          N_REGIONKEY       INTEGER         NOT NULL,
          N_COMMENT         VARCHAR(152)
        ) ORGANIZATION LSC
        ORDER BY (N_NATIONKEY);
CREATE TABLE supplier (
          S_SUPPKEY         INTEGER         NOT NULL,
          S_NAME            CHAR(25)        NOT NULL,
          S_ADDRESS         VARCHAR(40)     NOT NULL,
          S_NATIONKEY       INTEGER         NOT NULL,
          S_PHONE           CHAR(15)        NOT NULL,
          S_ACCTBAL         DECIMAL(15,2)   NOT NULL,
          S_COMMENT         VARCHAR(101)    NOT NULL
        ) ORGANIZATION LSC
ORDER BY (S_SUPPKEY)
PARTITION BY HASH(S_SUPPKEY) PARTITIONS 16;

CREATE TABLE part (
          P_PARTKEY         INTEGER         NOT NULL,
          P_NAME            VARCHAR(55)     NOT NULL,
          P_MFGR            CHAR(25)        NOT NULL ENCODING DICTIONARY(RLE),
          P_BRAND           CHAR(10)        NOT NULL ENCODING DICTIONARY(RLE),
          P_TYPE            VARCHAR(25)     NOT NULL ENCODING DICTIONARY(RLE),
          P_SIZE            INTEGER         NOT NULL,
          P_CONTAINER       CHAR(10)        NOT NULL ENCODING DICTIONARY(RLE),
          P_RETAILPRICE     DECIMAL(15,2)   NOT NULL,
          P_COMMENT         VARCHAR(23)     NOT NULL
        ) ORGANIZATION LSC
        ORDER BY (P_PARTKEY)
        PARTITION BY HASH(P_PARTKEY) PARTITIONS 16;

        CREATE TABLE partsupp (
          PS_PARTKEY        INTEGER         NOT NULL,
          PS_SUPPKEY        INTEGER         NOT NULL,
          PS_AVAILQTY       INTEGER         NOT NULL,
          PS_SUPPLYCOST     DECIMAL(15,2)   NOT NULL,
          PS_COMMENT        VARCHAR(199)    NOT NULL
        ) ORGANIZATION LSC
        ORDER BY (PS_SUPPKEY, PS_PARTKEY)
        PARTITION BY HASH(PS_PARTKEY) PARTITIONS 16;
        CREATE TABLE customer (
          C_CUSTKEY         INTEGER         NOT NULL,
          C_NAME            VARCHAR(25)     NOT NULL,
          C_ADDRESS         VARCHAR(40)     NOT NULL,
          C_NATIONKEY       INTEGER         NOT NULL,
          C_PHONE           CHAR(15)        NOT NULL,
          C_ACCTBAL         DECIMAL(15,2)   NOT NULL,
          C_MKTSEGMENT      CHAR(10)        NOT NULL ENCODING DICTIONARY(RLE),
          C_COMMENT         VARCHAR(117)    NOT NULL
        ) ORGANIZATION LSC
        ORDER BY (C_CUSTKEY)
        PARTITION BY HASH(C_CUSTKEY) PARTITIONS 16;

        CREATE TABLE orders (
          O_ORDERKEY        INTEGER          NOT NULL,
          O_CUSTKEY         INTEGER         NOT NULL,
          O_ORDERSTATUS     CHAR(1)         NOT NULL,
          O_TOTALPRICE      DECIMAL(15,2)   NOT NULL,
          O_ORDERDATE       DATE            NOT NULL,
          O_ORDERPRIORITY   CHAR(15)        NOT NULL ENCODING DICTIONARY(RLE),
          O_CLERK           CHAR(15)        NOT NULL ENCODING DICTIONARY(RLE),
          O_SHIPPRIORITY    INTEGER         NOT NULL,
          O_COMMENT         VARCHAR(79)     NOT NULL
        ) ORGANIZATION LSC
        ORDER BY(O_ORDERDATE, O_ORDERKEY)
        PARTITION BY HASH(O_ORDERKEY) PARTITIONS 16;
        CREATE TABLE lineitem (
          L_ORDERKEY       INTEGER          NOT NULL,
          L_PARTKEY         INTEGER         NOT NULL,
          L_SUPPKEY         INTEGER         NOT NULL,
          L_LINENUMBER      INTEGER         NOT NULL,
          L_QUANTITY        DECIMAL(15,2)   NOT NULL,
          L_EXTENDEDPRICE   DECIMAL(15,2)   NOT NULL,
          L_DISCOUNT        DECIMAL(15,2)   NOT NULL,
          L_TAX             DECIMAL(15,2)   NOT NULL,
          L_RETURNFLAG      CHAR(1)         NOT NULL,
          L_LINESTATUS      CHAR(1)         NOT NULL,
          L_SHIPDATE        DATE            NOT NULL,
          L_COMMITDATE      DATE            NOT NULL,
          L_RECEIPTDATE     DATE            NOT NULL,
          L_SHIPINSTRUCT    CHAR(25)        NOT NULL ENCODING DICTIONARY(RLE),
          L_SHIPMODE        CHAR(10)        NOT NULL ENCODING DICTIONARY(RLE),
          L_COMMENT         VARCHAR(44)     NOT NULL
        ) ORGANIZATION LSC
        ORDER BY(L_SHIPDATE, L_ORDERKEY)
        PARTITION BY HASH(L_ORDERKEY) PARTITIONS 16;
load data
        options(DEGREE_OF_PARALLELISM=2,TRIM=NOTRIM,ENABLE_BULK=TRUE,DECODER_THREAD_TIMES=1)
        infile 'region.csv' without embedded fields terminated by '|'
        append into table region(R_REGIONKEY, R_NAME, R_COMMENT);

        load data
        options(DEGREE_OF_PARALLELISM=2,TRIM=NOTRIM,ENABLE_BULK=TRUE,DECODER_THREAD_TIMES=1)
        infile 'nation.csv' without embedded fields terminated by '|'
        append into table nation(N_NATIONKEY, N_NAME, N_REGIONKEY, N_COMMENT);

        load data
        options(DEGREE_OF_PARALLELISM=2,TRIM=NOTRIM,ENABLE_BULK=TRUE,DECODER_THREAD_TIMES=1)
        infile 'supplier.csv' without embedded fields terminated by '|'
        append into table supplier(S_SUPPKEY, S_NAME, S_ADDRESS, S_NATIONKEY, S_PHONE, S_ACCTBAL, S_COMMENT);

        load data
        options(DEGREE_OF_PARALLELISM=2,TRIM=NOTRIM,ENABLE_BULK=TRUE,DECODER_THREAD_TIMES=1)
        infile 'part.csv' without embedded fields terminated by '|'
        append into table part(P_PARTKEY, P_NAME, P_MFGR, P_BRAND, P_TYPE, P_SIZE, P_CONTAINER, P_RETAILPRICE, P_COMMENT);

        load data
        options(DEGREE_OF_PARALLELISM=2,TRIM=NOTRIM,ENABLE_BULK=TRUE,DECODER_THREAD_TIMES=1)
        infile 'partsupp.csv' without embedded fields terminated by '|'
        append into table partsupp(PS_PARTKEY, PS_SUPPKEY, PS_AVAILQTY, PS_SUPPLYCOST, PS_COMMENT);

        load data
        options(DEGREE_OF_PARALLELISM=2,TRIM=NOTRIM,ENABLE_BULK=TRUE,DECODER_THREAD_TIMES=1)
        infile 'customer.csv' without embedded fields terminated by '|'
        append into table customer(C_CUSTKEY, C_NAME, C_ADDRESS, C_NATIONKEY, C_PHONE, C_ACCTBAL, C_MKTSEGMENT, C_COMMENT);

        load data
        options(DEGREE_OF_PARALLELISM=2,TRIM=NOTRIM,ENABLE_BULK=TRUE,DECODER_THREAD_TIMES=1)
        infile 'orders.csv' without embedded fields terminated by '|'
        append into table orders(O_ORDERKEY, O_CUSTKEY, O_ORDERSTATUS, O_TOTALPRICE, O_ORDERDATE, O_ORDERPRIORITY, O_CLERK, O_SHIPPRIORITY, O_COMMENT);
        load data
        options(DEGREE_OF_PARALLELISM=2,TRIM=NOTRIM,ENABLE_BULK=TRUE,DECODER_THREAD_TIMES=1)
        infile 'lineitem.csv' without embedded fields terminated by '|'
        append into table lineitem(L_ORDERKEY, L_PARTKEY, L_SUPPKEY, L_LINENUMBER, L_QUANTITY, L_EXTENDEDPRICE, L_DISCOUNT, L_TAX, L_RETURNFLAG, L_LINESTATUS, L_SHIPDATE, L_COMMITDATE, L_RECEIPTDATE, L_SHIPINSTRUCT, L_SHIPMODE, L_COMMENT
```

创建ctrl文件 eg:tpch_lineitem.ctl
```
LOAD DATA
INFILE '/data/tpch/lineitem.csv'
fields terminated by '|'  optionally enclosed by '"'
APPEND INTO TABLE LINEITEM
(
L_ORDERKEY,
L_PARTKEY,
L_SUPPKEY,
L_LINENUMBER,
L_QUANTITY,
L_EXTENDEDPRICE,
L_DISCOUNT,
L_TAX   ,
L_RETURNFLAG,
L_LINESTATUS,
L_SHIPDATE,
L_COMMITDATE,
L_RECEIPTDATE,
L_SHIPINSTRUCT,
L_SHIPMODE,
L_COMMENT
)
```

ORA - 错误码 完整版：https://docs.oracle.com/en/error-help/db/ora-index.html  

###### 安装oracle
```
1、准备工作
linux版本：CentOS Linux release 7.9.2009
Oracle版本：oracle-19c
拷包
注：下文 # 开头表示当前用户为 root、$ 开头表示当前用户为 oracle.
创建用户和用户组
# useradd oracle
# passwd oracle （修改用户 oracle 的密码为 oracle）
# groupadd oinstall
# groupadd dba
# groupadd oper
# usermod oracle -g oinstall -G dba,oper
关闭防火墙
# systemctl stop firewalld.service
# systemctl status firewalld.service
2、安装步骤
创建 oracle 相关目录
# mkdir -p /home/oracle/oracle/product/19.3.0/db_1
# mkdir -p /home/oracle/oracle/oraInventory
解压安装包
# unzip db.zip -d /home/oracle/oracle/product/19.3.0/db_1
目录授权
# chown -R oracle:oinstall /home/oracle/oracle/
配置环境变量
# su - oracle
$ cat /home/oracle/.bash_profile
PATH=$PATH:$HOME/.local/bin:$HOME/bin
export PATH
export LANG=en_US.UTF-8
export ORACLE_BASE=/home/oracle/oracle
export ORACLE_HOME=/home/oracle/oracle/product/19.3.0/db_1
export ORACLE_SID=oracledb
export NLS_DATA_FORMAT="yyyy-mm-dd HH24:MI:SS"
export NLS_LANG=AMERICAN_AMERICA.UTF8
export PATH=.:$PATH:$HOME/.local/bin:$HOME/bin:$ORACLE_HOME/bin
$ source /home/oracle/.bash_profile
配置响应变量
$ cd $ORACLE_HOME/install/response
$ cp db_install.rsp db_install.rsp.bak
编辑文件
$ cat db_install.rsp
oracle.install.option=INSTALL_DB_SWONLY
UNIX_GROUP_NAME=oinstall
INVENTORY_LOCATION=/home/oracle/oracle/oraInventory
ORACLE_BASE=/home/oracle/oracle
oracle.install.db.InstallEdition=EE
oracle.install.db.OSDBA_GROUP=dba
oracle.install.db.OSOPER_GROUP=oper
oracle.install.db.OSBACKUPDBA_GROUP=dba
oracle.install.db.OSDGDBA_GROUP=dba
oracle.install.db.OSKMDBA_GROUP=dba
oracle.install.db.OSRACDBA_GROUP=dba
oracle.install.db.rootconfig.executeRootScript=false
安装 oracle 所需的依赖库，通过 yum install {包名} 来验证是否安装，例如 yum install binutils
binutils-2.23.52.0.1-12.el7.x86_64
compat-libcap1-1.10-3.el7.x86_64
gcc-4.8.2-3.el7.x86_64
gcc-c++-4.8.2-3.el7.x86_64
glibc-2.17-36.el7.i686
glibc-2.17-36.el7.x86_64
glibc-devel-2.17-36.el7.i686
glibc-devel-2.17-36.el7.x86_64
ksh
libaio-0.3.109-9.el7.i686
libaio-0.3.109-9.el7.x86_64
libaio-devel-0.3.109-9.el7.i686
libaio-devel-0.3.109-9.el7.x86_64
libgcc-4.8.2-3.el7.i686
libgcc-4.8.2-3.el7.x86_64
libstdc++-4.8.2-3.el7.i686
libstdc++-4.8.2-3.el7.x86_64
libstdc++-devel-4.8.2-3.el7.i686
libstdc++-devel-4.8.2-3.el7.x86_64
libXi-1.7.2-1.el7.i686
libXi-1.7.2-1.el7.x86_64
libXtst-1.2.2-1.el7.i686
libXtst-1.2.2-1.el7.x86_64
make-3.82-19.el7.x86_64
sysstat-10.1.5-1.el7.x86_64
使用下面指令，检查依赖软件包
# yum install binutils-2.* compat-libstdc++-33* elfutils-libelf-0.* elfutils-libelf-devel-* gcc-4.* gcc-c++-4.*
glibc-2.* glibc-common-2.* glibc-devel-2.* glibc-headers-2.* ksh-2* libaio-0.* libaio-devel-0.* libgcc-4.*
libstdc++-4.* libstdc++-devel-4.* make-3.* sysstat-7.* unixODBC-2.* unixODBC-devel-2.* pdksh*
修改内核参数
# cat /etc/sysctl.conf
net.ipv4.icmp_echo_ignore_broadcasts = 1
net.ipv4.conf.all.rp_filter = 1
fs.file-max = 6815744 # 设置最大打开文件数
fs.aio-max-nr = 1048576
kernel.shmall = 2097152 # 共享内存的总量，8G内存设置：2097152*4k/1024/1024
kernel.shmmax = 10737418240 # 最大共享内存的段大小
kernel.shmmni = 4096 # 整个系统共享内存端的最大数
kernel.sem = 250 32000 100 128
net.ipv4.ip_local_port_range = 9000 65535 # 可使用的IPv4端口范围
net.core.rmem_default = 262144
net.core.rmem_max= 4194304
net.core.wmem_default= 262144
net.core.wmem_max= 1048576
# sysctl -p # 执行该命令以上配置生效
配置 limit.conf
对oracle用户设置限制，提高软件运行性能
# cat /etc/security/limits.conf
oracle soft nproc 2047
oracle hard nproc 16384
oracle soft nofile 1024
oracle hard nofile 65536
执行安装
$ cd $ORACLE_HOME
$ ./runInstaller -silent -responseFile $ORACLE_HOME/install/response/db_install.rsp
出现 Successfully Setup Software(Warnings). 表示执行成功；否则如果执行失败，打屏会提示详情可见 log 日志，请自行去看 log
根据提示执行 (需要切成 root 权限)
# chown root:root /home/oracle/oracle/oraInventory/orainstRoot.sh
# chown root:root $ORACLE_HOME/root.sh
# /home/oracle/oracle/oraInventory/orainstRoot.sh
# ORACLE_HOME/root.sh
没有报错即 oracle 软件安装成功
配置监听
$ cd $ORACLE_HOME/assistants/netca/ # 必须切换到该目录下执行创建监听，否则注册监听失败
$ netca /silent /responseFile $ORACLE_HOME/assistants/netca/netca.rsp
创建数据库
$ cd $ORACLE_HOME/assistants/dbca/
$ cp dbca.rsp dbca.rsp.bak
$ cat dbca.rsp # 修改如下内容：
gdbName=oracledb
sid=oracledb
databaseConfigType=SI
templateName=General_Purpose.dbc
sysPassword=oracle
systemPassword=oracle
emConfiguration=DBEXPRESS
dbsnmpPassword=oracle
characterSet=UTF8
执行安装
$ dbca -silent -createDatabase -responseFile $ORACLE_HOME/assistants/dbca/dbca.rsp
（注意，建库过程中时间比较长，需要耐心等待）
完成100%进度后，表示安装完成。
登录连接数据库

本地连接：
su - oracle                                     
sqlplus / as sysdba
startup;
shutdown immediate;
远程连接：sqlplus system/oracle@192.168.*.*:端口号/oracledb

重启数据库：
shutdown immediate
startup
```
oracle 连接sys用户：rlwrap sqlplus / as sysdba  
oracle unwrap解密工具： https://www.codecrete.net/UnwrapIt/  

date format设置显示时分秒
alter session set date_format = 'yyyy-mm-dd hh24:mi:ss'  

https://www.cnblogs.com/wcwen1990/p/6656618.html  oracle执行计划  


###### tpcc
```
TPCC  是由 TPC（Transaction Processing Performance Council） 非盈利组织推出的一套基准测试程序，主要用于OLTP类应用的测试；

       它由一系列的 OLTP 工作流组成，包括查询，更新及队列式小批量事务在内的广泛数据库功能。它模拟了一个典型的 OLTP 应用环境中的活动，这些活动由一系列复杂的事务组成。TPC-C 工作流应该具备以下特性：

适当复杂的 OLTP 事务
在线和离线事务执行模型
多用户
适当的系统和应用执行时间
大量的磁盘输入和输出
事务完整性（ACID）
随机的数据访问
数据库由各种大小，属性和关系的表组成。

TPC-C 系统需要处理的交易有以下五种：

New-Order： 客户输入一笔新的订货交易
Payment：更新客户账户余额以反应其支付状况
Delivery：发货（批处理交易）
Order-Status：查询客户最近交易的状态
Stock-Level：查询仓库库存状况，以便能够及时补货。
各个类型交易默认配置比例参数（共100%）

newOrderWeight = 45
paymentWeight = 43
orderStatusWeight = 4
deliveryWeight = 4
stockLevelWeight = 4
流量指标（Throughput,简称tpmC)：按照TPC组织的定义，流量指标描述了系统在执行支付操作、订单状态查询、发货和库存状态查询这4种交易的同时，每分钟可以处理多少个新订单交易。所有交易的响应时间必须满足TPC-C测试规范的要求，且各种交易数量所占的比例也应该满足TPC-C测试规范的要求。在这种情况下，流量指标值越大说明系统的联机事务处理能力越高。
按照TPC组织的定义，流量指标描述了系统在执行支付操作、订单状态查询、发货和库存状态查询这4种交易的同时，每分钟可以处理多少个新订单交易。所有交易的响应时间必须满足TPC-C测试规范的要求，且各种交易数量所占的比例也应该满足TPC-C测试规范的要求。在这种情况下，流量指标值越大说明系统的联机事务处理能力越高。
```

#### oracle其他
```
Oracle 里面有个叫做spfile的东西，就是动态参数文件，里面设置了Oracle 的各种参数。所谓的动态，就是说你可以在不关闭数据库的情况下，
更改数据库参数，记录在spfile里面。更改参数的时候，有4种scope选项。scope就是范围
scope=spfile 仅仅更改spfile里面的记载，不更改内存，也就是不立即生效，而是等下次数据库启动生效。有一些参数只允许用这种方法更改
修改sysdate的格式就只能使用这种方式。
scope=memory 仅仅更改内存，不改spfile。也就是下次启动就失效了
scope=both 内存和spfile都更改
不指定scope参数，等同于scope=both.

三权分立：

内置admin用户（DBA，SECURITY_ADMIN, AUDIT_ADMIN)，内置对应角色。

**！搜索时，按以下顺序：**

0. 搜索用户是否有ALL权限

1. 搜索用户的system role

2. 搜索用户的object role

3. 搜索在此session上缓存的用户的normal role

	- 搜索normal role具有的system privilege

	- 搜索normal role具有的object privilege

4. 搜索用户的normal role被级联授权的其他role，其他role也按步骤3进行搜索

 **任一步骤找到权限后，终止，如果搜索完所有的role仍无，则认定没有权限**
```
