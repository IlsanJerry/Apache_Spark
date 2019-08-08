## Apache_SPARK

### -설치 방법

## 

1.스파크 사이트에서 https://spark.apache.org/downloads.html 

 spark-2.4.3-bin-hadoop2.7.tgz를 받는다.

```bash
# wget http://mirror.apache-kr.org/spark/spark-2.4.3/spark-2.4.3-bin-hadoop2.7.tgz

```

2.압축을 해제한 다음 spark-2.4.3 명칭으로 root 계정의 홈 디렉토리로 이동.



3.홈 디렉토리의 .bashrc 파일을 오픈 한 다음, 

export SPARK_HOME=/root/spark-2.4.3
export PATH=$SPARK_HOME/bin:$SPARK_HOME/sbin:$PATH

위의 내용을 HADOOP_HOME과 HIVE_HOME 설정 중간에 추가한다.

```bash
#
export HADOOP_HOME=/root/hadoop-2.7.7
export PATH=$HADOOP_HOME/bin:$HADOOP_HOME/sbin:$PATH
export SPARK_HOME=/root/spark-2.4.3
export PATH=$SPARK_HOME/bin:$SPARK_HOME/sbin:$PATH
export HIVE_HOME=/root/apache-hive
export PATH=$HIVE_HOME/bin:$PATH 

```



4.스파크path 설정이 되있으니, SPARK_HOME의 conf로 이동후

spark-env.sh.template 파일을 복사하여 

spark-env.sh로 파일 생성후  vi에디터로 파일 맨 마지막에 

아래의 내용을 추가한다.

```bash
#
export HADOOP_CONF_DIR=/root/hadoop-2.7.7/etc/hadoop
export YARN_CONF_DIR=/root/hadoop-2.7.7/etc/hadoop

```



5. 4번과 같은 방식으로 log4j.properties.template 을 복사해서,

   log4j.properties를 생성한다.  vi 에디터로 파일 중간의

   log4j.rootCategory="INFO "부분을 "WARN"으로 변경한다.

```bash
# Set everything to be logged to the console
log4j.rootCategory=WARN, console
log4j.appender.console=org.apache.log4j.ConsoleAppender
log4j.appender.console.target=System.err
log4j.appender.console.layout=org.apache.log4j.PatternLayout
```



6.spark - shell을 시켜서 정상적으로 접속되는지 확인한다.

```bash
[root@master ~]# spark-shell
19/08/08 19:00:31 WARN NativeCodeLoader: Unable to load native-hadoop library for your platform... using builtin-java classes where applicable
19/08/08 19:00:46 WARN Client: Neither spark.yarn.jars nor spark.yarn.archive is set, falling back to uploading libraries under SPARK_HOME.
Spark context Web UI available at http://master:4040
Spark context available as 'sc' (master = yarn, app id = application_1565230427206_0008).
Spark session available as 'spark'.
Welcome to
      ____              __
     / __/__  ___ _____/ /__
    _\ \/ _ \/ _ `/ __/  '_/
   /___/ .__/\_,_/_/ /_/\_\   version 2.4.3
      /_/
         
Using Scala version 2.11.12 (Java HotSpot(TM) 64-Bit Server VM, Java 1.8.0_131)
Type in expressions to have them evaluated.
Type :help for more information.
scala> 

```

7. 설치 완료.