# RDD개념을 활용한 WordCount

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


//(예시)201612120945 p008에서 p008의 횟수를 누적해야 
//인기있는 품목의 로그데이터를 파악 할 수 있다.
//따라서 정규 표현식으로 p000이하 내용들은 제외시켜야한다.
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

-end-!!








```

