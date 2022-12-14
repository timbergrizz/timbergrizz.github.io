---
title: GCP BigQuery
feed: hide
---

- 출처
	- [구글 빅데이타 플랫폼 빅쿼리 소개](https://bcho.tistory.com/1116)
	- [빅쿼리 #2 - 아키텍처](빅쿼리 #2-아키텍쳐)
	- [Snowflake, BigQuery, Redshift 비교](https://giljae.medium.com/snowflake-bigquery-redshift-%EB%B9%84%EA%B5%90-5c585df450b7)

## What is BigQuery?
- 빅쿼리는 페타바이트 급의 데이터 저장 및 분석용 클라우드 서비스이다.
	- [[AWS Redshift]] 같은 일종의 Data Warehouse 형식이라고 생각하면 좋을 것 같다.
### 특징
- 클라우드 설치로 서비스에 따로 설정이 필요 없다
- 기존의 SQL 언어를 그대로 사용한다.
- 클라우드 스케일의 인프라를 통한 대용량 지원과 빠른 성능을 확보할 수 있다.
	- 4TB 용량이 핸들링되는데 30초가 소요되며 이 쿼리를 수행하는데 20불로 사용할 수 있다.
- 데이터가 3개의 복제본이 서로 다른 3개의 데이터 센터로 분산되어 유실 위험이 적다.
- 한번에 데이터를 처리하는 배치와 REST API등을 통해 실시간으로 데이터를 입력하는 스트리밍 모두 지원한다.
- 저장되는 데이터 사이즈와 쿼리시 발생하는 트랜젝션 비용만큼만 과금이 이루어진다.

### 기존 빅데이터 플랫폼과의 다른점?
- Hadoop / Spark 등의 빅데이터 분석 플랫폼과 다른 점은 다음과 같다.
	- Hadoop의 Map & Reduce 구조나 SparkSQL에 비해 난이도가 쉽다.
	- Hadoop과 Spark와 달리따로 운영을 필요로 하지 않는 클라우드 관리형 서비스이다.
	- 또한, 인프라에 대한 투자 없이 컴퓨팅 자원을 사용할 수 있다.

## Architecture of BigQuery
- 일반적인 DBMS는 로우 단위로 데이터가 구성되게 된다.
- 빅쿼리는 컬럼 기반 저장소로 구성된다.
	- 이 경우, 컬럼에 따라 다른 파일에 나누어 저장이 이루어지게 된다.
	- 특정 컬럼만 읽어서 개수를 세거나 통계를 내는 분석용 데이터 베이스 작업에 유리하다.
- 트리 구조의 데이터 처리 구조를 사용한다.
- 빅쿼리에는 키나 인덱스의 개념 없이, 풀 스캔을 사용한다.
- 성능을 위해 데이터를 추가하는것만 지원하며, update나 delete가 불가능하다.
- 데이터를 입력하여도 3개의 분산 데이터 센터로 복제하는 시간이 걸려 데이터를 입력한 후 바로 조회할 수 없다.

## Redshift, BigQuery, SnowFlake
- 모든 솔루션이 일반적인 DBMS와 달리, 엄청나게 큰 데이터셋에 대한 쿼리를 수행할 수 있게 된다.
- 모두 데이터 웨어하우스 솔루션이지만, 목적에 따라 사용이 달라지게 된다.
	- Redshift의 경우, 노드 그룹을 통해 클러스터를 미리 설정해야 한다.
		- 따라서 일정한 계산이 필요한 시나리오에 적합하게 된다.
		- 클러스터를 프로비저닝 한 후 데이터 분석 쿼리를 실행하기 위해 데이터 세트를 업로드해야 한다.
			- 데이터세트의 크기에 관계 없이 동일한 SQL 기반 도구 및 Bi 솔루션을 사용하여 빠른 쿼리 성능을 얻을 수 있다.
		- 생성한 클러스터에서 실행된다.

	- BigQuery의 경우, Dremel을 이용해 빠른 시간 내에 정확한 결과를 얻을 수 있는 쿼리 서비스이다.
		- 워크로드가 급증하는 시나리오에 가장 적합하다.
			- 전자 상거래 어플리케이션을 위한 실행등이 적절하다.
		- 분산 컴퓨팅에서 실행되며, Google 데이터센터의 Borg에서 실행된다.

	- SnowFlake는 관계형 DB 관리 시스템이다.
		- 구조화된 데이터와 반구조화 데이터 모두를 지원하는 데이터웨어 하우스이다.
		- 기존 DBMS 또는 빅데이터 플랫폼에 **구축되지 않으며**, 클라우드용으로 설계된 고유한 아키텍처가 존재하는 SQL 엔진을 사용한다.
		- 안정적이고 지속적인 사용 패턴에 적합하다.
