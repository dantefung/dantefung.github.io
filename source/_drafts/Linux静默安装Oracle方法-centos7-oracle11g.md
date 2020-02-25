title: Linux静默安装Oracle方法(centos7+oracle11g)
author: dantefung
date: 2018-11-21 16:24:04
tags:
---
oracle软件包下载地址：http://www.oracle.com/technetwork/database/enterprise-edition/downloads/112010-linx8664soft-100572.html

\begin{cases} 3x + 5y + z \\ 7x - 2y + 4z \\ -6x + 3y + 2z \end{cases}

1、 增加虚拟内存

dd if=/dev/zero of=/swapadd bs=1024 count=2006424

mkswap /swapadd

swapon /swapadd


2、 检查依赖包
rpm -q binutils compat-libstdc++-33 elfutils-libelf elfutils-libelf-devel gcc gcc-c++ glibc-2.5 glibc-common glibc-devel glibc-headers ksh libaio libaio-devel libgcc libstdc++ libstdc++-devel make sysstat unixODBC unixODBC-devel

注：通过命令 ldd --version 查看glibc安装版本，如果安装了且版本为2.17也是可以安装oracle的

3、 安装依赖包

yum install binutils compat-libstdc++-33 elfutils-libelf elfutils-libelf-devel expat gcc gcc-c++ glibc glibc-common glibc-devel glibc-headers libaio libaio-devel libgcc libstdc++ libstdc++-devel make pdksh sysstat unixODBC unixODBC-devel

或

yum install compat-libstdc++-33 
yum install elfutils-libelf-devel 
yum install gcc-c++ 
yum install glibc-2.5 // 如果已安装了glibc2.17也是可以的
yum install ksh 
yum install libaio 
yum install libaio-devel 
yum install libstdc++-devel 
yum install unixODBC
yum install unixODBC-devel

4、 创建用户和组

groupadd oinstall  
groupadd dba  
groupadd asmadmin  
groupadd asmdba  
useradd -g oinstall -G dba,asmdba oracle -d /home/oracle 
passwd  oracle // 然后会让你输入密码，密码任意输入2次，但必须保持一致，回车确认

5、 修改内核参数

vim /etc/sysctl.conf  增加或者修改
fs.aio-max-nr = 1048576
fs.file-max = 6815744
kernel.shmall = 2097152
kernel.shmmax = 536870912
kernel.shmmni = 4096
kernel.sem = 250 32000 100 128
net.ipv4.ip_local_port_range = 9000 65500
net.core.rmem_default = 262144
net.core.rmem_max = 4194304
net.core.wmem_default = 262144
net.core.wmem_max = 1048576

6、 修改用户限制
vim /etc/security/limits.conf


oracle soft nproc 2047oracle hard nproc 16384oracle soft nofile 1024oracle hard nofile 65536oracle soft stack 10240
vim /etc/pam.d/login 
session  required   pam_limits.so
vim /etc/profile
if [ $USER = "oracle" ]; then   if [ $SHELL = "/bin/ksh" ]; then      ulimit -p 16384      ulimit -n 65536      else      ulimit -u 16384 -n 65536   fifi
source /etc/profile
7、 创建安装目录
mkdir -p /u01/app/
chown -R oracle:oinstall /home/oracle  
chmod -R 775 /home/oracle  
chown -R oracle:oinstall /u01/app/ 
chmod -R 775 /u01/app/
vim /etc/oraInst.loc

inventory_loc=/u01/app/oracle/oraInventoryinst_group=oinstallchown oracle:oinstall /etc/oraInst.loc

chmod 664 /etc/oraInst.loc

8、 设置oracle环境变量
su – oracle
vim ~/.bash_profile

export ORACLE_BASE=/u01/app/oracleexport ORACLE_SID=orclsource /home/oracle/.bash_profile

env
9、 解压
上传linux.x64_11gR2_database_1of2.zip，linux.x64_11gR2_database_2of2.zip到/home/oracle目录下
cd /home/oracle/
unzip linux.x64_11gR2_database_1of2.zip
unzip linux.x64_11gR2_database_2of2.zip

10、复制响应文件模板
su – root
cd /home/oracle/ 
mkdir etc
cp /home/oracle/database/response/* /home/oracle/etc/
chmod 777 /home/oracle/etc/*.rsp

11、静默安装Oracle软件
su – oracle
vim /home/oracle/etc/db_install.rsp

oracle.install.option=INSTALL_DB_SWONLYORACLE_HOSTNAME=ruoUNIX_GROUP_NAME=oinstallINVENTORY_LOCATION=/u01/app/oracle/oraInventorySELECTED_LANGUAGES=en,zh_CN,zh_TWORACLE_HOME=/u01/app/oracle/product/11.2.0/db_1ORACLE_BASE=/u01/app/oracleoracle.install.db.InstallEdition=EEoracle.install.db.isCustomInstall=trueoracle.install.db.DBA_GROUP=dbaoracle.install.db.OPER_GROUP=oinstalloracle.install.db.config.starterdb.type=GENERAL_PURPOSEoracle.install.db.config.starterdb.globalDBName=orcloracle.install.db.config.starterdb.SID=orcloracle.install.db.config.starterdb.memoryLimit=512oracle.install.db.config.starterdb.password.ALL=oracleDECLINE_SECURITY_UPDATES=true
12、安装

cd /home/oracle/database

./runInstaller -silent -force -responseFile /home/oracle/etc/db_install.rsp -ignorePrereq
cd $ORACLE_BASE/oraInventory/logs


13、环境变量
su - oracle
vi ~/.bash_profile

export ORACLE_HOME=$ORACLE_BASE/product/11.2.0/db_1export TNS_ADMIN=$ORACLE_HOME/network/adminexport PATH=.:${PATH}:$HOME/bin:$ORACLE_HOME/binexport PATH=${PATH}:/usr/bin:/bin:/usr/bin/X11:/usr/local/binexport LD_LIBRARY_PATH=${LD_LIBRARY_PATH}:$ORACLE_HOME/libexport LD_LIBRARY_PATH=${LD_LIBRARY_PATH}:$ORACLE_HOME/oracm/libexport LD_LIBRARY_PATH=${LD_LIBRARY_PATH}:/lib:/usr/lib:/usr/local/libexport CLASSPATH=${CLASSPATH}:$ORACLE_HOME/JREexport CLASSPATH=${CLASSPATH}:$ORACLE_HOME/JRE/libexport CLASSPATH=${CLASSPATH}:$ORACLE_HOME/jlibexport CLASSPATH=${CLASSPATH}:$ORACLE_HOME/rdbms/jlibexport CLASSPATH=${CLASSPATH}:$ORACLE_HOME/network/jlibexport LIBPATH=${CLASSPATH}:$ORACLE_HOME/lib:$ORACLE_HOME/ctx/libexport ORACLE_OWNER=oracleexport SPFILE_PATH=$ORACLE_HOME/dbsexport ORA_NLS10=$ORACLE_HOME/nls/data

source /home/oracle/.bash_profile
14、配置网络
$ORACLE_HOME/bin/netca /silent /responseFile /home/oracle/etc/netca.rsp

15、安装数据库
vim /home/oracle/etc/dbca.rsp

GDBNAME="orcl.ruo" SID="orcl"SYSPASSWORD = "oracle" SYSTEMPASSWORD = "oracle" CHARACTERSET="AL32UTF8" NATIONALCHARACTERSET="UTF8"

$ORACLE_HOME/bin/dbca -silent -responseFile /home/oracle/etc/dbca.rsp



16、建库后实例检查
ps -ef | grep ora_ | grep -v grep | wc -l
ps -ef | grep ora_ | grep -v grep

17、建库后监听检查
lsnrctl status

18、修改oracle启动配置文件
vi /etc/oratab

orcl:/u01/app/oracle/product/11.2.0/db_1:Y


dbstart
dbshut 
注意：可能会遇到如下问题：

分别打开两个文件找到：ORACLE_HOME_LISTNER=$1,修改为 ORACLE_HOME_LISTNER=$ORACLE_HOME

vi $ORACLE_HOME/bin/dbstart

vi $ORACLE_HOME/bin/dbshut

19、sqlplus / as sysdba

conn /as sysdba  连接到数据本地数据
alter user system identified by password;   修改System 密码  为password
已连接。 
SQL> alter user system identified by password;
用户已更改。
SQL> alter user sys identified by password;
用户已更改。
SQL> alter user system identified by manger;
解锁方法 
alter user system account unlock;