## 서버의IP주소  :70.12.113.xxx

1.`putty`에`70.12.113.xxx`입력후  opanapi 를 open

1-1.AWS 서버의IP주소  :13.125.xx.x

ec2-13-125-37-0.ap-northeast-2.compute.amazonaws.com [현재 JDK 와 Spark가 설치가 되있음]

2.sampledata 폴더 생성

3.윈도우에 있는 product_click.log파일을 Filezilla를 통해서 서버에 sampledata에

전달해줄거임

4.호스트:`70.12.113.xxx`, 사용자명 : lab04 비밀번호 :password 입력 후 빠른연결 누르기 (포트입력안함)

5.vi 에디터로.bashrc파일 오픈 -> 제일마지막행에 입력하기

```bash
export PATH=$SPARK_HOME/bin:$PATH
```

6.입력후 `source .bashrc`입력해서 적용하기.

7.로컬 PC에서 원격 서버로 파일을 전송하기 위해서

`FileZilla Client`프로그램을 사용한다!

8.sparkRDD, sparkSQL, sparkR 환경이 잘 실행되는지 각각 실행해보기

```scala
[ Spark RDD 소스 ]
val productRDD = sc.textFile("/home/lab04/sampledata/product_click.log")
val productSplit = productRDD.flatMap(x => x.split(" "))
val productFilter = productSplit.filter(e => e.length==4)
val productMap = productFilter.map(x => (x,1))
val productReduce = productMap.reduceByKey((x,y) => (x+y))
productReduce.collect.foreach(println)



val RDD1 = sc.textFile("/home/lab04/sampledata/product_click.log")
val RDD2 = RDD1.map(x => x.split(" ")).map(x => x(1)).map((_,1)).reduceByKey(_+_)
val RDD3 = RDD2.map { wordAndCount=> (wordAndCount ._2, wordAndCount ._1) }
val RDD4 = RDD3.sortByKey(false)
val RDD5 = RDD4.map { wordAndCount => (wordAndCount._2, wordAndCount._1) }
RDD5.collect.foreach(println)
```



```scala
[ Spark SQL 소스 ]
case class Product(pid: String, cnt: Int)
val productRDD = sc.textFile("/home/lab04/sampledata/product_click.log")
val productDF = productRDD.map{record=>
    val splitRecord = record.split(" ")
    val pid = splitRecord(1)
    Product(pid, 0)
    }.toDF

val pidAndCntDF = productDF.groupBy($"pid").agg(count($"pid") as "cnt").orderBy($"cnt".desc)
pidAndCntDF.show

val rowRDD = pidAndCntDF.rdd

val pidAndCntRDD = rowRDD.map{row=>
      val pid = row.get(0)
      val cnt = row.get(1)
      (pid, cnt)}

pidAndCntRDD.collect.foreach(println)
```

```R

[ SparkR 소스 ]

file_path<-"/home/lab04/sampledata/product_click.log"
pcl <- read.df(path=file_path, source="csv", delimiter=" ")
first(pcl)
names(pcl) <- c("clickdt", "pid")
first(pcl)
g <- groupBy(pcl, pcl$pid)
result <- agg(g, clickcnt=count(pcl$clickdt))
result <- arrange(result, desc(result$clickcnt))
showDF(result)
```

