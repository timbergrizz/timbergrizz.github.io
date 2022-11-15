---
title: AWS Redshift
feed: hide
---
- 출처
	- [Redshift란? 다른 DB들과 차이점은?](https://pearlluck.tistory.com/648)
	- [Redshift의 특징들. 다른 DB와 뭐가 다른가?](https://jaemunbro.medium.com/aws-redshift-%EA%B8%B0%EC%B4%88%EC%A7%80%EC%8B%9D-987aedcb2830)

## What is Redshift?
- AWS에서 완전 관리형으로 제공되는 클라우드 데이터 웨어하우스
	- 데이터 웨어하우스는 여러가지 소스에서 데이터를 추출한 후 변환하여 적재하는 관계형 데이터베이스
		- ETL에서 Load가 이러한 데이터 웨어하우스를 타겟으로 이루어지게 된다.
		- [[Apache Airflow]], [[대형 서비스 아키텍처]] 참고
- 클러스터 (노드 집합)를 생성하고, 클러스터를 사용할 준비가 되면 데이터를 적재하고 분석을 수행할 수 있다.
	- 이때 PostgreSQL을 기반으로 설계해 표준 SQL을 이용한 데이터 처리를 지원한다.
	- 여러가지 Business Intelligence 도구도 사용할 수 있다.

## Architecture of Redshift
- 클러스터
	- 리더 노드와 하나 이상의 컴퓨팅 노드로 구성되어 있다.
	- Leader Node
		- SQL 연결 엔드포인트로서, 외부 어플리케이션은 리더 노드와 통신하게 된다.
		- 또한 노드간의 통신을 관리하며 메타 데이터를 저장하고, 클러스터의 모든 쿼리 수행을 관리한다.
	- Compute Node
		- 실제 Task를 수행하는 노드
		- 각 노드마다 여러개의 슬라이스를 보유하고 있다.
			- Slice는 하나의 컨테이너같은 개념으로 이해할 수 있다.
				- 별도의 메모리, CPU, 디스크 공간이 할당되어 슬라이스별로 독립적인 워크로드를 병렬적으로 실행할 수 있다.
				- 데이터를 포함하고 있다.
		- 병렬로 쿼리를 처리하고, S3 기반으로 백업 및 복구를 진행할 수 있다.

## Features of Redshift

### Massive Parallel Processing
- 분산형 시스템을 제공한다.
	- 구조에서 확인하였듯이 여러가지 node와 slice로 구성되어 있다.
- 데이터를 병렬적으로 처리할 수 있도록 테이블의 row를 compute node로 배포한다.
	- 이후 각 테이블마다 Distribute Key / All / Even 중 하나의 방식으로 슬라이스를 선택한다.
		- Distribute Key
			- Key-Value 방식으로 slice에 배치한다.
			- 명시적으로 지정한 컬럼을 기준으로 각 레코드의 slice 배치가 결정된다.
			- column cardinality에 따라 slice간 편차가 발생할 수 있다.
		- All
			- 모든 레코드가 각 computing node에 동일하게 복제된다.
		- Even
			- 각 레코드가 슬라이스의 round robin 방식으로 분산되어 균등하게 데이터를 저장한다.
- 쿼리들은 모든 슬라이스에 걸쳐 병렬 수행을 한다.

### Columnar Data Storage
- DB에 Row가 아닌 Column 기반으로 테이블 정보를 저장한다.
	- 컬럼으로 압축하여 데이터를 읽는 크기를 줄여 디스크 i/o를 줄이고, 쿼리를 향상시킨다.
	- 쿼리를 실행하면 압축된 데이터를 메모리로 읽어온 후, 쿼리 실행 도중에 압축이 실행되도록 한다.
		- 압축 기능의 자동 적용을 위해 copy 명령으로 redshift의 데이터를 로딩하면서 데이터 분석을 수행하도록 압축을 수행할 수 있다.

## Advantages of Redshift
- Copy 명령
	- S3, DynamoDB, EMR에 있는 데이터를 병렬적으로 로딩할 수 있다.
	- 근데 이게 ETL과 뭐가 다른지 모르겠다.
- 신속한 확장과 백업
	- 필요할 때 마다 설정을 변경하여 DB의 스케일을 조정할 수 있다.
	- S3에 자동으로 백업 데이터가 저장된다.
		- 필요에 따라 수등으로 스냅샷을 생성할 수 있다.
- OLTP 기능
	- 데이터 삽입 / 삭제 등의 온라인 트랜젝션 기능을 일반적인 RDBMS와 같이 제공한다.
	- 특히 매우 큰 데이터세트의 분석을 위해 최적화 되어 있다.

## Difference

### from Previous DW
- 확장성
	- 기존 DW 솔루션은 온프레미스 시스템으로, 사전 할당된 리소스만 사용할 수 있었다.
	- Redshift는 클라우드 기반으로 확장성이 뛰어나다.
- 데이터 집합적
	- 기존 DW는 데이터가 여러 장소에 분산되어 원본 데이터를 알 수 없었다.
	- Redshift는 모든 데이터를 S3에 저장한다.
- 비용
	- 기존 DW는 개발 비용이 크지만, Redshift는 온-디맨드로 사용하는 만큼만 비용이 부과된다.
- 데이터의 종류
	- 기존 DW는 정형화된 데이터만 분석할 수 있다.
	- Redshift는 정형데이터 뿐만 아니라 반정형, 비정형 데이터도 분석할 수 있다.
		- raw data를 그대로 저장하고, Redshift가 분석할 때 필요한 형태로 가공할 수 있다.

### from Athena
- Athena는 Serverless 서비스이다.
	- S3의 데이터셋의 쿼리를 올릴 수 있도록 해준다.
- Athena는 대용량 쿼리나 장기적 배치 처리에 적합하지 않다.
	- 리소스를 사용자가 독점하는 방식이 아닌, Region별로 리소스를 공유한다.
	- 트래픽이 몰리는 시간대에 메모리 부족 오류 등이 발생할 수 있다.
- Redshift는 대용량 데이터에 대한 Batch 처리를 위한 아키텍처이다.