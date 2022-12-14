---
title : Spark - What is Big Data
feed : hide
date : 19-11-2022
---

- 출처 
	- 한기용 멘토님 멘토링
	- [Apache Spark #1 - 아키텍쳐 및 기본 개념](https://bcho.tistory.com/1387)

- Spark : 빅데이터 분석 프레임워크, 빅데이터 분석의 사실상 표준.
	- 하둡 생태계를 보완하는 기술
	- 실제로 기동할땐 하둡의 기능들을 사용하게 된다.
- 스파크는 디스크나 기타 다른 저장소에 저장된 데이터를 메모리로 올려 분석하는 방식으로 동작한다.
	- 배치 분석 뿐만 아니라, 스트리밍 데이터 양쪽 분석을 모두 지원한다.
- 스파크를 만들 때 판다스 데이터프레임을 스케일만 키워 똑같은 컨셉으로 프로세싱할 수 있도록 하자는게 목적이었고, 따라서 판다스를 이해하고 있다면 쉽게 사용할 수 있다.
- 머신 러닝 모델 등을 빌딩할 수도 있고, 데이터를 배치 형태로 처리하는것이 아닌 스트리밍 형태로 real time으로 들어오는 데이터를 처리하는 기능도 있다.
	- 요즘은 GraphDB도 처리해준다.

## 빅데이터 처리란 무엇인가
- 큰 데이터 처리의 특징
	- 시간이 오래 걸린다.
	- 큰 데이터를 손실없이 보관할 방법을 필요로 한다.
	- 메모리에 모두 올리고 처리하는것이 불가능하다.
	- 비구조화된 데이터일 가능성이 높다.
	- 웹 로그 파일등이 될 수 있다.
- 해결방안
	- 시간이 오래 걸림 → 병렬 처리로 수행한다.
	- 큰 데이터를 손실 없이 보관할 방법 → 큰 데이터 처리가 가능한 파일 시스템을 제공
	- 메모리에 모두 올리고 처리 불가능 → 다수의 컴퓨터로 구성된 프레임워크를 필요로 한다.
	- 비구조화된 데이터 → 비구조화를 처리할 방법이 필요
- 다수의 컴퓨터로 구성된 프레임워크 하둡에서 스파크로 발전함.
	- 하둡에는 데이터 처리 시스템, 파일 시스템으로 구성된다.
	- 하둡에서 사용되던 map reduce가 생산성이 떨어졌고, 따라서 spark가 등장하게 된다.
		- 패러다임에 맞지 않는 문제가 나오게 되면 골때려지기 시작한다.
	- map reduce가 아닌 더 상위 레벨로 procedual하게 변환해 나간다.
		- 일반적인 개발자가 사용하기 더 편해졌다.
		- 빅데이터 batch system, 머신러닝, 그래프 처리가 가능해진다.
- spark 자체가 하둡 위에서 돌아간다.
    - 분산 처리 자체가 general하고 따라서 쉽게 올릴 수 있다.
    - 새로운 버전에서는 쿠버네티스로의 배포를 준비하고 있다.

## Hadoop/Spark의 구성
- 다수의 노드로 구성된 클러스터 시스템이 있다.
	- 마치 하나의 거대한 컴퓨터처럼 동작하게 된다.
		- 다수의 컴퓨터들이 복잡한 소프트웨어로 통제된다.
	- 대용량 파일 시스템 (Disk)
		- HDFS : Hadoop에서 사용하는 시스템
		- S3(AWS)
	- 대용량 컴퓨팅 시스템
		- Hadoop → MapReduce
		- Spark : RDD, Dataset, Dataframe
- 스파크 클러스터의 구조는 Master Node와 Worker Node로 구성된다.
	- Master Node는 전체 클러스터를 관리하고 분석 프로그램을 수행하는 역할을 한다.
	- 분석 프로그램을 스파크 클러스터에 실행하게 되면 Job이 생성된다.
		- Job이 외부 저장소로부터 데이터를 로딩하는 경우, 데이터는 Worker Node로 로딩되고, 로딩된 데이터는 여러 서버의 메모리에 분산되어 로딩이 된다.
	- 이렇게 스파크 메모리에 저장된 데이터 객체를 RDD라고 한다.

### Cluster Manager
- 스파크는 데이터를 분산 처리하기 위해 하나의 클러스터에 여러대의 워커로 구성된다.
	- 하나의 Job이 여러개의 워커에 분산되어 처리되기 위해서는 하나의 Job을 여러개의 Task로 나눈 후, 적절하게 이 Task를 여러 서버에 분산해 배치해야 한다.
- 클러스터 내의 워크 노드 리소스를 관리하고 Task를 배치하는 역할을 하는 것이 클러스터 매니저이다.
	- 클러스터 매니저는 일종의 스파크 런타임이라고 생각하면 된다.

### 데이터는 어떻게 저장되는가? / Storage
- 논리적으로 엄청나게 큰 파일이 있다고 하자.
	- 여러개의 block으로 나뉘여 구성된다.
- 시스템에 문제가 생겨 데이터 유실이 발생하면?
	- 똑같은 블락을 3군데에 중복해서 복제한다.
- 중앙에 파일과 이들의 블록 단위 정보를 관리하는 서비스들이 존재한다.
	- 하둡에는 name node와 data node
		- name node HA라 한다.
- 스파크는 자체 파일시스템이 없고, 다른 파일 시스템을 이용한다.
	- 클러스터 내에 노드들에다 로드하여 처리하고, 다시 HDFS, S3로 저장한다.
	- spark는 데이터를 지속적으로 내부에 저장하지 않고, 외부의 데이터를 로드하여 작업하여 다시 저장하는 형태로 동작한다.
- 코드를 병렬적으로 실행시키면, 여러 서버로 코드가 전송이 되고 여러 서버에서 나뉘어 동작하게 된다.
	- 로컬에서 실행을 시키면, 내 컴퓨터에서 실행되는 것이 아니라 worker node로 보내고, 그 결과를 처리가 끝나면 받아온다.
	- python, scala를 일반적으로 사용한다.
	- 입력 데이터, 출력 저장소가 있어야 한다.
		- 스파크는 그 자체로 스토리지는 아니다. 외부에서 동작시키고 그 결과를 저장할 수 있어야 한다.
- 기본적으로 스파크 / 하둡은 데이터가 있는 곳으로 보낸다.
	- 빅데이터 프로세싱 시스템은 데이터를 로드하고, 이 데이터가 로드된 곳에 보내 여기서 코드를 실행시키는 형태가 된다.
	- 이러한 과정은 스파크 시스템에서 추상화되고, 따라서 최적화 문제가 아니면 생각할 필요가 없다.
	- 데이터가 여러 서버에 나뉘어 있으면, join을 하려면 조인하려는 노드들이 한 컴퓨터에 있어야 한다.
		- 만약 따로 떨어져있으면 오버헤드가 발생하고 data shuffling이라 한다.
		- 하나보다 큰 데이터셋을 처리하면서 데이터 간의 분포가 다른 경우 등에서 문제가 발생할 수 있다.
			- 이럴 때 spark의 내부 동작을 이해할 필요가 생긴다.
- 스파크는 별도의 외부 스토리지를 사용해야 한다.
	- 대표적으로 HDFS가 사용되며, 클라우드의 경우 S3나 GCS등이 사용될 수 있다.
	- DB로는 분산 노드에서 데이터를 동시에 읽어야 하기 땜누에 분산처리를 지원하는 NoSQL인 HBase등이 널리 사용된다.

### Partitioning : 데이터가 Spark node들로 어떻게 나눠지는가?
- 파티션은 스파크 노드들로 나눠진 데이터 블록들을 말한다. 이를 RDD라고 한다.
- Spark 코드는 기본적으로 RDD에 다양한 작업을 통해 원하는 결과를 이루는 것이다.
	- Group By, Shuffle, map, map partition등의 동작을 수행할 수 있다.
- 어떤 노드는 작고, 어떤 노드는 큰 상황이 발생할 수 있다.
	- partition에 문제가 생기면, 가장 큰 노드가 끝날때까지 처리가 끝나지 않게 되고, 분산 처리의 이점이 사라지게 된다.
	- 병렬 처리가 항상 빠른 것은 아니다.

### Map Reduce란?
- 기본적으로 데이터 셋을 Key, Value의 집합으로 간주한다.
- 두개의 오퍼레이션만을 지원한다.    
	- Map : (k, v) → [(k’, v’)*]
	- Reduce : {k’, [v1’, v2’, …]} → (k’’, v’’)
		- Group By와 동일하다.
		- HIVE → SQL과 동일하다.
	- 생산성이 박살이 난다.
		- 모든 데이터를 2개로만 처리해야 한다.
		- HIVE를 통해 map, reduce로 컴파일 해주는 도구가 나오게 되었다.
		- operation set을 단순화하여 유연하진 않지만 병렬 처리를 극대화 할 수 있었다.
		- 다시 또 SQL로 돌아갔다가 스파크로 간다.
	- 예시 : 단어 카운트
		- 맵으로 로컬에서 처리한 후, 리듀스를 통해 로컬에 처리된 데이터들을 종합해 처리한다.
		- map-reduce의 패러다임과 부합하면 잘 작동할 수 있으나, 달라지면 골때려진다.
    
## 빅데이터의 정의

1. 빅데이터 → 서버 한 대로 처리할 수 없는 규모의 데이터
	-   다수의 서버로 처리해야 하는 데이터
2. 빅데이터 → 기존의 소프트웨어로는 처리할 수 없는 규모의 데이터
	- 기존 DBMS는 용량이 부족하면 서버의 성능 / 용량을 향상시키는 방법밖에 없다.
	- 작은 스타트업의 실수가 관계형 DB를 abuse하는 것이다.
		- 불필요한 데이터도 여기에 저장하는 것이다.
			- 빅데이터 시스템이 아닌 서버 한대짜리 시스템에서 불필요한 데이터를 빼내는 것을 주로 했다.
		- 성격에 맞춰 필요 없는 데이터는 밖으로 빼내야 한다.
	- 서버를 붙이면 붙이는만큼 좋아지도록 하면 된다.
		- 저사양의 많은 서버를 붙여 클러스터의 용량을 키우는 방법이 있다.

## 하둡의 등장과 소개
- 대용량 처리 기술
	- 분산 환경 기반 (1대 이상의 서버로 구성된다)
		- 분산 컴퓨팅과 분산 파일 시스템을 필요로 한다.
	-   HDFS - 분산 파일 시스템
		- 데이터를 블록 단위로 저장한다.
			- 블록의 크기는 기본 128MB
		- 블록 복제 방식 (Replication)
			- 각 블록은 3곳에 중복되어 저장된다.
	- 분산 컴퓨팅 시스템	
		- 잡 트래커가 프로그램을 나눠 테스트 트래커에 분배, 이를 통해 통신하여 남겨둔다.
		- 하둡 2.0부터 분산 처리를 간단하게 사용할 수 있도록 했다.
			- 더 general한 프레임워크를 만들고, 원하는 대로 분산 처리를 할 수 있는 프레임워크를 만들도록 했다
	- Spark의 등장	
		- 버클리 대학의 아파치 오픈소스 프로젝트로 시작되었다.
		- 하둡의 뒤를 잇는 2세대 빅데이터 기술이다.
			- 하둡 2.0을 분산 환경으로 사용할 수 있고, 자체 분산환경도 지원된다.
			- 스칼라로 작성되었다.
		- MapReduce의 단점을 대폭 개선했다.
			- Pandas와 굉장히 유사하지만, 규모가 다르다.
		- 최신 버전은 3.0이다.
		- 파이썬이나 스칼라로 사용할 수 있다.
			- 머신러닝과 관련해서 많은 개선이 있었다.
- spark의 코드 첫번째는 데이터를 가져오고, 처리를 한 뒤 마지막으로 저장하는 과정을 필요로 한다.
- 여러가지 컴포넌트로 구성되고, 이중 핵심은 spark core가 된다.
	- spark의 데이터를 sql처럼 조작할 수 있도록 하는 spark sql이 존재한다.
	- spark단에서 ml도 자동으로 수행할 수 있다.
		- 데이터 크기 관계 없이 처리될 수 있다.
	- 스트리밍, 그래프 데이터도 문제 없이 처리할 수 있다.
	- 쿠버네티스도 지원한다.
- Spark의 기본은 RDD로 수행하는 것이다.
	-   로우레벨 API로 세밀하게 조정할 수 있으나, 코딩의 복잡도가 올라간다.
	-   Dataframe, Dataset으로 조정할 수 있다. (판다스의 데이터프레임과 유사하다.)
		- 하이레벨 API라 편하게 사용할 수 있다.
		- SparkSQL을 사용하면 이를 사용한다.
		- 파이썬에서 데이터셋으로 사용할 수는 없다.
	        - 타입을 알아야 프로그램을 쓸 수 있는 것은 아니다.

## 판다스와 스파크의 비교
- 판다스는 파이썬 데이터 분석 기본 모듈 중 하나
	- 시각화, 머신 러닝과 같은 다른 모듈과 사용된다.
	- 엑셀에서 하는 일을 파이썬에서 가능하게 된다.
- 소규모의 구조화된 데이터를 다루는데 최적화되어있다.
	- 한 대의 서버에서 다룰 수 있는 데이터로 크기가 제약된다.
	- 병렬 처리를 지원하지 않는다.
	- 큰 데이터를 다룰 때 spark를 다루게 된다.
- 판다스로 뭘 할수 있을까?
	- 데이터를 읽어올 수 있다.
        - csv, json등의 데이터를 가져올 수 있다.
        - 웹, RDBMS에서 데이터를 뽑아올 수 있다.
	- 이를 통해 다양한 통계를 가져올 수 있다.
		- 컬럼별로 통계적 데이터를 가져올 수 있다.
	- matplot등을 통해 시각화 할 수 있다.
	- 기본으로 Series, DataFrame으로 구성된다

- Pandas 데이터 변환
	- numpy_array에서 pandas dataframe으로 변환할 수 있다.
		- 컬럼 헤더를 지정해주어야 한다.
	- spark dataframe으로 변환할 수 있다.
		- 스파크 데이터프레임에서 판다스 프레임으로 변환할 때 데이터가 충분히 작아야 한다.
		- 너무 크면 에러 터지겠죠?
			- 일반적으로 요약 통계정보나 일부 작은 데이터를 검증용으로 확인할 때 사용한다.
	        - parallelize, collect

## 데이터프레임, 데이터셋, RDD

### Spark 데이터 구조
- RDD (Resilient Distributed Dataset)
	- 로우레벨 데이터로 클러스터 내의 서버의 분산된 데이터를 지칭한다.
	- 레코드별로 존재하며 구조화된 데이터나 비구조화된 데이터를 모두 제공한다.
-   Dataframe과 Dataset
	- RDD 위에 만들어지는 하이레벨 데이터
		- RDD와 달리 필드 정보를 갖고 있다.
	- Dataset은 필드의 타입 정보가 존재하여 컴파일언어에서 사용할 수 있다.
	- PySpark에선 Dataframe을 사용하게 된다.
		- SparkSQL을 사용하는게 편한 경우도 있다.

#### RDD
- 변경이 불가능한 분산 저장된 데이터
	- RDD는 다수의 파티션, 블록으로 구성되고 스파크 클러스터 내 서버들에 나누어 저장된다.
	- 로우레벨의 함수형 변환을 지원한다.
-   일반 파이썬 데이터는 parallelize 함수로 RDD로 변환한다.
	- Spark에 존재하는 데이터는 collect로 파이썬 데이터로 변환할 수 있다.
		- list를 spark cluster의 RDD로 로딩하는 것이 왼쪽 코드가 된다.
		- 이를 통해 추가적인 작업을 수행할 수 있다.
		- collect를 이용해 크기가 너무 크지 않는 한 파이썬으로 다시 돌아올 수 있다.

#### 데이터 프레임
- RDD처럼 변경이 불가능한 분산 저장 데이터
- RDD와는 다르게 관계형 데이터베이스 테이블처럼 컬럼으로 나누어 저장한다.
	- 판다스의 데이터 프레임 혹은 관계형 데이터로 저장된다.

#### 데이터 셋
- 컴파일 언어에서 사용된다.

## PySpark의 구조
-   SparkSession을 만들어야 한다.
	- 컨텍스트를 만들어 데이터를 로드하고, 처리하고 저장한다.
	- 데이터를 어디서 로드할것인지부터가 중요하다.
-   데이터를 로딩하고, 조작한 후, 최종 결과를 저장한다.
	- 데이터가 스파크 클러스터에 계속 남아있을 수 없다.
	- MongoDB, cassandra등에 저장될 수 있다.

### Spark 세션
- 스파크 프로그램의 시작은 스파크 세션을 만드는 것
- 스파크 세션을 통해 스파크의 기능을 사용할 수 있다.    
	- Spark 컨텍스트, Hive 컨텍스트, SQL 컨텍스트.
	- 예전엔 기능 따라 다른 컨텍스트 생성해야 했다

## 스파크 데이터 구조
- 데이터 프레임 생성 방법
	- RDD를 변환하여 생성한다.
		- RDD의 toDF 함수를 이용한다.
	- SQL 쿼리를 기반으로 생성한다. 아래 예를 참조하자.
	- 외부 데이터를 로딩하여 생성한다.
	- 접근 권한이 있다는 가정 하에 DB에서 데이터를 읽어와 spark의 df라는 이름으로 로딩된다.
		- 이후 판다스의 dataframe처럼 사용할 수 있다.
		- 이후 다시 dbms에 작성할 수도 있다.
- 기본적으로 대부분은 pandas와 동일하다.
- 다음번엔 collabolate approach 한번 써봅시다.