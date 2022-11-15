---
title: Apache Airflow
feed: hide
---

- 출처
	- [Apache Airflow](https://zzsza.github.io/data/2018/01/04/airflow-1/)
	- [What is ETL - ORACLE](https://www.oracle.com/kr/integration/what-is-etl/)
	- [버킷플레이스 Airflow 도입기](https://www.bucketplace.com/post/2021-04-13-%EB%B2%84%ED%82%B7%ED%94%8C%EB%A0%88%EC%9D%B4%EC%8A%A4-airflow-%EB%8F%84%EC%9E%85%EA%B8%B0/)

## What is Airflow
- AirBnB에서 만든 Workflow Managaement Tool
	- 워크플로우에 대한 모니터링, 관리를 지원한다.
	- MLOps, Data Engineering에서 주로 사용된다.
		- 주로 데이터 파이프라이닝에서 사용된다.
- 다음과 같은 부분에서 활용할 수 있다.
	- 데이터 엔지니어링 : ETL 파이프라인 구성
	- 머신러닝 엔지니어링
		- 머신 러닝 모델의 주기적인 학습 및 예측
		- 실시간 API가 아닌 일괄적(Batch) 예측
	- 여러 작업들의 관리
		- 선후 관계가 설정된 시퀀셜한 작업의 연결성 관리
		- 시각화 등의 작업

### ETL
- 다음과 같은 과정으로 구성된다.
	- 소스 시스템 등 데이터 소스에서 정형 / 비정형 데이터를 가져온다 (Extract)
	- 데이터를 요구사항에 맞춘 형식으로 변환한다 (Transform)
	- 추출 및 변환된 데이터를 타겟 데이터베이스로 보낸다. (Load)
- 여러 곳에 저장되어 있는 데이터를 필요사항에 맞도록 다른 과정으로 이동시키는 프로세스라고 생각하면 될 듯 하다.
	- 이러한 전처리 및 이동 이후 데이터 마트, 데이터 웨어하우스 등에 저장된다. [[분산형 데이터 분석 아키텍처]]
- 주로 엔터프라이즈 데이터 웨어하우스에 데이터를 제공하기 위해 사용된다.
	- 이러한 웨어하우스의 데이터는 데이터 검증을 제공하는 엄격한 스키마, 메타데이터, 규칙을 통해 구조화된다.
	- 대용량 고성능 일괄 처리 로드, 이벤트 구동, 세류 공급 통합 프로세스, 프로그래밍 가능한 변환, 조정과 같은 데이터 통합 요구를 충족할 수 있어야 한다.

## Airflow의 장점
- Python 기반으로 구성되어, 데이터 분석가들도 쉽게 다룰 수 있다.
- Airflow Console을 통해 태스크 관리를 서버에서 직접 하지 않아도 되며, 각 작업별 소요 시간이 나와 병목을 찾을때도 유용함
- GCP의 BigQuery, Dataflow를 쉽게 사용할 수 있도록 제공함
	- GCP에서는 Cloud Composer라는 이름으로 Airflow에 대한 Managed Service를 제공함

## Airflow의 구조
- 다음과 같은 구성 요소로 이루어져 있다.
- Airflow Webserver
	- 웹 UI를 표기
	- workflow의 상태를 표기하고, 실행 / 재시작 / 수동 조작 / 로그 확인 등의 동작을 수행한다.
- Meta Database
	- Airflow에 존재하는 DAG와 Task들의 메타데이터를 저장하는 데이터베이스이다.
- Airflow Scheduler
	- 작업 기준이 충족되는지 여부를 확인한다.
		- 종속 작업이 성공적으로 완료되었고, 예약 간격이 주어지면 실행할 수 있는 작업인지, 실행 조건이 충족되는지 여부를 확인한다.
		- 충족 여부가 DB에 기록되면, task들이 worker들에게 선택되어 작업을 수행한다.
- Worker
	- task를 수행하는 역할을 한다.
	- Executor의 종류에 따라 동작하는 방식이 달라진다.
- Airflow는 Python DAG를 읽고, 이에 맞춰 Scheduler가  Task를 스케줄링하면, 워커 노드가 Task를 가져가 실행한다
	- Task의 실행 상태는 데이터베이스에 저장되며, 사용자는 UI를 통해 실행 상태, 성공 여부를 확인할 수 있다.

### DAG
- DAG는 Directed Acyclic Graph로 사이클이 없는 단방향성 그래프를 의미한다.
- Airflow는 workflow를 DAG 형태로 구성할 수 있다.
	- Scheduler는 이러한 순차적인 작업들의 구성을 확인하고, 작업을 실행하도록 하는 것이다.
- [공식 깃허브](https://github.com/apache/airflow/tree/main/airflow/example_dags) 에서 예제 워크플로우를 확인할 수 있다.

### Operator
- 각 Airflow는 여러가지 Task로 구성되어 있다.
	- operator나 sensor가 하나의 task로 만들어진다.
- Airflow는 기본적인 Task를 위해 다양한 operator를 제공한다.
	- BashOperator : bash command 실행
	- PythonOperator : Python 함수를 실행
	- EmailOperator : Email을 발송한다.
	- MySqlOperator : SQL 쿼리를 수행한다.
	- Sensor : 시간, 파일, DB row 등을 기다리는 센서이다.
	- 이외에도 커뮤니티에서 만든 다양한 operator가 존재한다.

### Executor
- Executor의 종류에 따라 Worker의 동작이 달라질 수 있다.
- 대표적인 Executor로 Celery와 Kubernetes가 존재한다.
	- 이런거 보면 진짜 클러스터링 필요한 작업이면 요즘은 다 쿠베 넣고 시작하는 것 같다.
		- Spark도 요즘 쿠베 기반으로 옮기고 있다던데
	- Celery Executer
		- Task를 메시지 브로커에 전달하고, Worker가 Task를 가져가 실행한다.
		- Worker 수를 스케일 아웃으로 처리할 수 있는 것이 장점이다.
		- 메시지 브로커를 따로 관리해야 하며, 워크 프로세스에 대한 모니터링도 필요하다는 단점이 있다.
	- Kubernetes Executer
		- Task를 스케줄러가 실행 가능 상태로 변경하면 Airflow 워커를 Pod 형태로 실행시킨다
		- 매 Task마다 pod를 생성하여 가볍고, Worker에 대한 유지보수가 필요 없다는 장점이 있다.
		- 지속적으로 자원을 점유하지 않아 효율적으로 자원을 사용할 수 있다.
		- 짧은 Task에도 Pod를 사용하는 오버헤드가 있으며, celery에 비해 자료가 적고 구성이 복잡하다는 단점이 있다.
		- Python뿐만이 아닌, 쿠버네티스 Pod을 이용해 다른 Task도 구성할 수 있어 Operation의 다양성을 구축할 수 있다.
- 