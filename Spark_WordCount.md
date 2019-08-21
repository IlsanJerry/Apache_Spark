## Apache_SPARK

### -자바7 및 자바8(람다)환경에서 워드카운트 수행.



-searchFestival Repository에서 발생한 

Tomcat_access_log.txt를 가져와서 워드 카운트 수행.

cf. yarn을 통해서 파티션이 2개로 나뉘어서 저장됨, 특히 최소2개로 저장됨.



## 1.워드카운트(자바 7)

```java
package sparkexam;

import java.util.Arrays;
import java.util.Iterator;

import org.apache.spark.SparkConf;
import org.apache.spark.api.java.JavaPairRDD;
import org.apache.spark.api.java.JavaRDD;
import org.apache.spark.api.java.JavaSparkContext;
import org.apache.spark.api.java.function.FlatMapFunction;
import org.apache.spark.api.java.function.Function2;
import org.apache.spark.api.java.function.PairFunction;

import scala.Tuple2;

public class WordCount7 {

	public static void main(String[] args) {
		// http://master:4040/ 확인
		SparkConf conf = new SparkConf().setAppName("WordCount_version7").setMaster("yarn");
		JavaSparkContext sc = new JavaSparkContext(conf);

JavaRDD<String> rdd1 = sc.textFile("hdfs://192.168.111.120:9000/edudata/tomcat_access_log.txt");

		JavaRDD<String> rdd2 = rdd1.flatMap(new FlatMapFunction<String, String>() {
			@Override
			public Iterator<String> call(String v) throws Exception {
				return Arrays.asList(v.split("\\s+")).iterator();
			}
		});

		JavaPairRDD<String, Long> rdd3 = rdd2.mapToPair(new PairFunction<String, String, Long>() {
			@Override
			public Tuple2<String, Long> call(String s) throws Exception {
				return new Tuple2<String, Long>(s, 1L);
			}
		});

		JavaPairRDD<String, Long> rdd4 = rdd3.reduceByKey(new Function2<Long, Long, Long>() {
			@Override
			public Long call(Long v1, Long v2) throws Exception {
				return v1 + v2;
			}
		});

		System.out.println(rdd4.collect());
		rdd4.saveAsTextFile("hdfs://192.168.111.120:9000/output/java7");
		sc.close();
	}

}

```



## 2.워드카운트(자바 8, 람다 활용)

```java
package sparkexam;

import java.util.Arrays;
import java.util.Iterator;

import org.apache.spark.SparkConf;
import org.apache.spark.api.java.JavaPairRDD;
import org.apache.spark.api.java.JavaRDD;
import org.apache.spark.api.java.JavaSparkContext;
import org.apache.spark.api.java.function.FlatMapFunction;
import org.apache.spark.api.java.function.Function2;
import org.apache.spark.api.java.function.PairFunction;

import scala.Tuple2;

public class WordCount8 {

	public static void main(String[] args) {
//리소스매니저가 yarn이라고 설정.
SparkConf conf = new SparkConf().setAppName("WordCount_version8").setMaster("local");
		JavaSparkContext sc = new JavaSparkContext(conf);
		JavaRDD<String> rdd1 = sc.textFile("hdfs://192.168.111.120:9000/edudata/tomcat_access_log.txt");
//9000=네임노드한테 접속하는 정보임
        
JavaRDD<String> rdd2 = rdd1.flatMap(line ->Arrays.asList(line.split("\\s+")).iterator());

JavaPairRDD<String, Long> rdd3 = rdd2.mapToPair(w -> new Tuple2<String, Long>(w, 1L));

JavaPairRDD<String, Long> rdd4 = rdd3.reduceByKey((x, y) -> x + y);
		
//action 연산을 2개 호출했음 ,collect 랑 saveAsTextFile로 하둡파일시스템에 저장함.
System.out.println(rdd4.collect());
rdd4.saveAsTextFile("hdfs://192.168.111.120:9000/output/java8");
sc.close();
	}
}

```





## 3.실행결과



```bash
#
(/festival/MyPage?action=revDelete&check=109&check=114&check=104&check=106&check=108&check=169,1)
([22/Jul/2019:10:49:43,1)
([23/Jul/2019:14:49:16,4)
(/festival/notice/ncontent?nid=23,3)
([11/Jul/2019:10:47:58,1)
(/festival/detail?fid=24,1)
([19/Jul/2019:12:43:11,5)
(/festival/resources/images/23/2.jpg,1)
([17/Jul/2019:09:24:50,1)
([01/Aug/2019:15:58:49,1)
(/festival/resources/images/75/4.jpg,2)
([23/Jul/2019:11:09:49,1)
(/festival/resources/images/76/4.jpg,4)
(/festival/festivalmain?city=c1&district+=d1&month=m1&ftype+=f1,1)
([22/Jul/2019:14:05:53,4)
(/festival/changelatlng?lat=37.4893989&lng=126.9865735&fid=59,3)

```

이하 생략.. 



## 4.결론.

추후에

1.이용자의 인기 검색 도시, 축제, 월 등의 데이터를 수집하고,

2.이용자가 조회한 인기 있는 축제 사진 데이터들을 수집하고,

3.공지 사항 및 MyPage에서 어느 기능을 이용했는지 수집 할 수 있을거라 예상함...

