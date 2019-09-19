# CentOS에서 R 설치 방법

## setup

1.기본적으로 R에서는 CentOS에서의 설치를 지원안함

https://ftp.harukasan.org/CRAN/ 에서 확인

따라서 yum(메이븐프로젝트같은 느낌)을 설치해야 한다.

```bash
yum update -y
#(지금 센토스에 있는 yum은 구버전이여서 업데이트부터 해야한다.)
yum -y install epel-release
yum -y install R
```



2.`/root/spark-2.4.3/conf`에서 vi 에디터로 `log4j.properties`로 접속한뒤

`:set nu`를 입력한 후, 19 Line에서 `ERROR` 인지 확인한다.

```bash
18 # Set everything to be logged to the console
19 log4j.rootCategory=ERROR, console

```

3.HDFS 및 YARN 기동 후 `sparkR`을 실행한다.

```bash
start-dfs.sh
start-yarn.sh
sparkR
```

4.성공적으로 로딩시 최초 화면

```bash
 Welcome to
    ____              __ 
   / __/__  ___ _____/ /__ 
  _\ \/ _ \/ _ `/ __/  '_/ 
 /___/ .__/\_,_/_/ /_/\_\   version  2.4.3 
    /_/ 
    SparkSession available as 'spark'.

```



## practice

https://spark.apache.org/docs/2.4.3/api/R/ 참고

1.RDD에서 사용한 `product_click.log`파일을 가져와 동일한 작업 수행.

```bash
> product_path <- "/edudata/product_click.log"
> product_df <- read.df(product_path,source="csv",sep=" ")
> colnames(product_df) <- c("date","pid")                                       
> product_g <- groupBy(product_df,"pid")
> product_cnt <- count(product_g)
> showDF(arrange(product_cnt,desc(product_cnt$count)))
+----+-----+                                                                    
| pid|count|
+----+-----+
|p001|   84|
|p008|   80|
|p009|   80|
|p007|   71|
|p002|   66|
|p010|   58|
|p003|   54|
|p006|   51|
|p004|   50|
|p011|   49|
|p005|   43|
|p012|   39|
|p014|   10|
|p015|    7|
|p013|    4|
+----+-----+

-end-
> 

```

