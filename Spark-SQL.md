# Spark-SQL

SPARK-SQL을 위해서는 `hive-site.xml`을 `/root/spark-2.4.3/conf` 에 복사 한 후,

745행에 `hive.metastore.schema.verification` 을 아래와 같이 수정한다.

```xml
<property>
    <name>hive.metastore.schema.verification</name>
    <value>false</value> //745행 
						//verification을 true에서 false로 변경.
  </property>
```

```

```

1.SPARK-SQL을 활용한 상품 클릭 로그데이터 분석

```scala
scala> sortedLogCountRDD.first
res0: (String, Int) = (p001,84)

// 위의 (String, Int) = (p001,84)에 대해서 
//pClickLog에 대한 클래스를 케이스 클래스로서 다음과 같이 정의함.
scala> case class pClickLog (pid: String, cnt: Int)
defined class pClickLog

//적용 실패 사례
scala>  val pClickLogDF = sortedLogCountRDD.map{ record => 
     |           val splitRecord = record.split(",") 
     |           val pid= splitRecord(0)
     |           val cnt = splitRecord(1) 
     |           pClickLog(pid,cnt)
     |               }.toDF
<console>:28: error: value split is not a member of (String, Int)
       		val splitRecord = record.split(",")
                                         ^

//적용 성공 사례
scala> val pClickLogDF = sortedLogCountRDD.map{ record => 
     |           val pid= record._1
     |           val cnt =record._2
     |           pClickLog(pid,cnt)
     |           }.toDF
pClickLogDF: org.apache.spark.sql.DataFrame = [pid: string, cnt: int]

scala> pClickLogDF.show
+----+---+
| pid|cnt|
+----+---+
|p001| 84|
|p009| 80|
|p008| 80|
|p007| 71|
|p002| 66|
|p010| 58|
|p003| 54|
|p006| 51|
|p004| 50|
|p011| 49|
|p005| 43|
|p012| 39|
|p014| 10|
|p015|  7|
|p013|  4|
+----+---+

```



