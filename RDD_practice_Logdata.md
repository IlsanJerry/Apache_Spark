# RDD개념을 활용한 상품 로그 데이터 분석

1. Version1.0

   product_click.log 데이터를 불러와 누적횟수를 조회한다.

```scala
val textRDD = sc.textFile("/edudata/product_click.log")
val textArray = textRDD.collect
textArray.foreach(println)
scala> textArray.foreach(println)
201612120944 p001
201612120944 p003
201612120944 p011
201612120945 p008


val productClickLog = textRDD.flatMap(_.split(" "))
productClickLog.first
scala> productClickLog.first
res5: String = 201612120944


// (예시)201612120945 p008에서 p008의 횟수를 누적해야 
// 인기있는 품목의 로그데이터를 파악 할 수 있다.
// 따라서 정규 표현식으로 p000이하 내용들은 제외시켜야한다.
val productClickLogArray = productClickLog.collect
val pClickLog = productClickLog.filter(_.matches("p[a-z0-9]*"))
pClickLog.first
scala> pClickLog.first
res6: String = p001

val pClickLogOnePairRDD = pClickLog.map((_, 1))
pClickLogOnePairRDD.first
scala> pClickLogOnePairRDD.first
res7: (String, Int) = (p001,1)

val pClickLogCountRDD = pClickLogOnePairRDD.reduceByKey(_ +_)
scala> pClickLogCountRDD.first
res9: (String, Int) = (p004,50)

val pClickLogCountArray = pClickLogCountRDD.collect
scala> pClickLogCountArray.foreach(println)
(p004,50)
(p006,51)
(p011,49)
(p013,4)
(p008,80)
(p015,7)
(p002,66)
(p012,39)
(p001,84)
(p005,43)
(p003,54)
(p007,71)
(p009,80)
(p014,10)
(p010,58)

-end-!

```

```

```

2. Version2.0

​		product_click.log 데이터를 불러와 누적횟수를 조회한다.

​		정렬이 안된 누적횟수들을 `sortByKey ` 메서드를 이용해 정렬한다. 

```scala
// 단어의 등장횟수로 정렬하기 위해서, (상품아이디,클릭누적횟수)형의 튜플을 요소로 갖는 RDD에서
// `map` 메소드를 사용해서 
// 클릭누적횟수가 키가 되도록, (클릭누적횟수, 상품아이디)형의 튜플을 요소로 가지는 RDD생성을 함.
val pClickCountLogRDD = pClickLogCountRDD.map{LogAndCount =>(LogAndCount._2,LogAndCount._1)}
pClickCountLogRDD: org.apache.spark.rdd.RDD[(Int, String)] = MapPartitionsRDD[22] at map at <console>:25

scala> pClickCountLogRDD.first
res15: (Int, String) = (39,p012)


// sortByKey에서 매개변수 생략시 자동으로 true가 되어 
// 오름차순으로 정렬 된다.
// 스파크는 예외적으로 액션 호출 이외에 sortByKey에 의해서도 잡이 실행된다. 
val sortedCountLogRDD = pClickCountLogRDD.sortByKey(false)
sortedCountLogRDD: org.apache.spark.rdd.RDD[(Int, String)] = ShuffledRDD[23] at sortByKey at <console>:25

scala> sortedCountLogRDD.first
res16: (Int, String) = (84,p001)


val sortedLogCountRDD = sortedCountLogRDD.map{LogAndCount =>(LogAndCount._2,LogAndCount._1)}
sortedLogCountRDD: org.apache.spark.rdd.RDD[(String, Int)] = MapPartitionsRDD[24] at map at <console>:25

scala> sortedLogCountRDD.first
res17: (String, Int) = (p001,84)


val sortedLCArray=sortedLogCountRDD.collect
sortedLCArray: Array[(String, Int)] = Array((p001,84), (p009,80), (p008,80), (p007,71), (p002,66), (p010,58), (p003,54), (p006,51), (p004,50), (p011,49), (p005,43), (p012,39), (p014,10), (p015,7), (p013,4))

scala> sortedLCArray.foreach(println)
(p001,84)
(p009,80)
(p008,80)
(p007,71)
(p002,66)
(p010,58)
(p003,54)
(p006,51)
(p004,50)
(p011,49)
(p005,43)
(p012,39)
(p014,10)
(p015,7)
(p013,4)

scala> val sortedLogCountRDD = sortedCountLogRDD.map{case(count,word)=>(word,count)}
sortedLogCountRDD: org.apache.spark.rdd.RDD[(String, Int)] = MapPartitionsRDD[25] at map at <console>:25

scala> sortedLogCountRDD.first
res19: (String, Int) = (p001,84)


// `take` 메소드를 사용해서 
// 상품아이디의 클릭누적횟수가 많은 상위 5개의 요소를 
// RDD에서 꺼낸 후 출력한다.
scala> val top5LogArray = sortedLogCountRDD.take(5)
top5LogArray: Array[(String, Int)] = Array((p001,84), (p009,80), (p008,80))

scala> top5LogArray.foreach(println)
(p001,84)
(p009,80)
(p008,80)
(p007,71)
(p002,66)

-end-!!

```

