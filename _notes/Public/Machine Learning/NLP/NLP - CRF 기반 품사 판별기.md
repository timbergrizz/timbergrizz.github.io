- 출처 : [Conditional Random Field (CRF) 기반 품사 판별기의 원리와 HMM 기반 품사 판별기와의 차이점](https://lovit.github.io/nlp/2018/09/13/crf_based_tagger/)
- 품사 판별은 string 형식의 문장으로부터 (단어, 품사) 형식으로 문장을 구성하는 단어와 품사를 인식하는 문제
	- 한 문장은 여러 개의 단어 / 품사열의 후보가 만들어질 수 있다.
- 품사 판별 과정에서는 가능한 단어 / 품사열 후보 중 가장 적절한 것을 선택해야 한다.
	- 길이가 n인 입력 시퀀스에 대해 가장 적절한 출력 시퀀스를 찾는 문제이기도 하다.

# Sequential Labeling
- 일반적인 classification은 하나의 입력 벡터 x에 대해 하나의 label y를 반환하는 과정이다.
	- 입력 벡터 x가 벡터가 아닌 sequence일 수 있다.
- labeling은 출력 가능한 레이블 중 적절한 것을 선택하는 것이기 때문에 일종의 분류이다.
	- 데이터의 형식이 벡터가 아닌 시퀀스이기 때문에, sequential data에 대한 분류라는 의미로 Sequential Labeling이라 한다.
		- 띄어쓰기 문제, 품사 판별이 대표적인 시퀀셜 라벨링 문제이다.

## Conditional Random Field
- CRF는 sequential labeling을 위해 potential function을 이용하는 소프트맥스 회귀이다.
	- RNN이 sequential labeling에 이용되기 전에 사용되었다.
- Sequential Labeling은 길이가 n인 sequence 향태의 입력에 대해 길이가 n인 적절한 label sequence를 출력한다.
- 소프트맥스 회귀는 벡터 x에 대하여 label y를 출력하는 함수이다.
	- 입력되는 sequence data가 단어열과 같이 벡터가 아닐 경우에는 이를 벡터로 변환해야 한다.
		- Potential Function이 categorical value를 포함하여 sequence로 입력된 다양한 형태의 값을 벡터로 변환한다.
- Potential function은 boolean 필터처럼 작동한다.
	- 