---
title : NLP - BERT
feed : hide
date : 11-11-2022
---

- 출처 : [BERT: Pre-training of Deep Bidirectional Transformers for Language Understanding](https://aclanthology.org/N19-1423/)


## Abstract
- 기존 언어 모델과 다르게, BERT는 라벨링되지 않은 텍스트에서 모든 레이어에 대해 문맥을 bidirectional representation을 pre-train하도록 설계되었다.
	- 결과적으로, pre-trained 모델은 추가적인 파인 튜닝 레이어만으로 많은 언어적 영역에서 SOTA 모델을 만들 수 있다.

## Introduction
- 언어 모델에 대한 프리 트레이닝은 많은 NLP 프로세싱에 대해 효과적으로 작동한다.
	- 이러한 과정은 다음과 같은 문장에 대한 작업들에도 적용된다.
		- Natural Language Inference, Paraphrasing
		- 또한, token-level의 작업에서도 이러한 성과를 얻을 수 있다.
	- 전인적으로 문장간의 관계를 예측함으로서 이러한 성과를 얻을 수 있다.
- 두가지의 pre-trained language representation을 적용하기 위한 전략이 존재한다.
	- feature-based
		- pre-trained representation을 추가적인 feature로 가져, task-specific한 아키텍처를 만든다.
	- fine-tuning
		- 모든 pre-trained 파라미터에 대해 fine-tuning을 수행하여 downstream task에 대해 학습된다.
		- minimal-task specific parameter를 가진다.
	- 두 접근 방식 모두 pre-train동안 동일한 objective function을 가진다.
	- 현재 이러한 기술들은 pre-trained representation의 능력에 제약을 걸고 있다.
		- 특히 fine-tuning 방식에 대해서는 제약이 발생한다.
		- 가장 큰 제약사항은, 표준적인 언어 모델은 단방향으로만 작동한다는 것이다.
			- 이것은 프리트레이닝에서의 학습에 제약을 만든다.
			- 이러한 제한은 문장단위에서 최적된 결과를 가질 수 없게 하고, 파인튜닝에서 문장의 양방향으로 문맥을 파악해야 하는 토큰 단위 처리가 발생할 때 큰 문제가 생긴다.
	- 파인 튜닝을 양방향으로 적용하는 Bidirectional Encoder Representations from Transformers를 제안한다.

## Masked Languaged Model
- BERT는 Masked Languaged Model을 통해 단방향의 제약을 완화한다.
	- Masked Languaged Model은 랜덤으로 입력의 일부를 가리고, 원래 단어를 문맥으로 예측하도록 한다.
- 일반적인 left-to-right 언어 모델 pre-training과 다르게, 왼쪽과 오른쪽의 문맥을 결합하여 학습하는 것이 가능하다.
	- 이를 통해 bidirectional Transformer를 더 깊이 학습시킬 수 있다.
- MLM에 더해, 다음 문장 예측 작업도 수행한다.
	- 이는 문장간의 관계를 묘사하도록 한다.

# Related Work
- pre-trained language representation은 긴 역사를 가지고 있고, 간단하게 리뷰하면 다음과 같다.

## Unsupervised Feature-based Approaches
- 단어 임베딩 벡터에 대해 scratch에서 pre-train을 위해 left-to-right 언어 모델링이 사용되었다.
	- 왼쪽과 오른쪽의 문맥에 따라 correct와 incorrect word를 구분하면서 학습되었다.
- 이러한 접근은 coarser 접근으로 일반화되었다.
	- 문장에 대한 임베딩과, 문단에 대한 임베딩으로 이어진다.
	- 문장 representation을 학습하기 위해서, 이전에는 다음 문장 후보들 중 최적을 선택하는 방법이 사용되었다.
		- 이전 문장이 주어졌을 때 left-to-right를 구하는 것과 denoising auto-encoder가 사용되었다.
- ELMo와 후속 연구들은 기존의 단어 임베딩 연구를 다른 차원으로 일반화했다.
	- 문맥 특화적인 feature를 left-to-right와 right-to-left를 통해 뽑아내었다.
		- 각 토큰의 문맥적인 representation은 ltr과 rtl representation의 concatenation이다.
	- 문맥적인 단어 임베딩을 기존의 작업 특화된 아키텍처에 적용했을 때 SOTA급 성능을 냈다.
		- 질문에 대한 답변과 감성 분석, 객체명 인식에서에서 LSTM을 이용한 문맥을 통한 양방향 단어 예측 모델을 사용했을 때 좋은 성능을 거두었다.
	- ELMo도 feature-based 모델이고 deeply bidirectional하지 않다.
- Feduset al. 연구에서 빈칸 채우기 식의 학습이 텍스트 생성 모델의 강건함을 강화시킬 수 있을 것임을 보였다.

## Unsupervised Fine-tuning Approaches
- feature-based 연구와 유사하게, 첫 번째 연구는 레이블 되지 않은 텍스트에 대한 단어 임베딩 pre-train이었다.
- 최근에는, 문맥적인 representation을 만드는 문장 / 문서 인코더가 레이블되지 않은 텍스트에서 pre-train되고, 지도 학습을 통한 파인 튜닝이 이루어지고 있다.
	- 이러한 접근에 대한 이점으로, scratch에서 적은 파라미터만 학습되어도 된다는 점이 있다.
	- 이러한 장점으로 인해, GPT는 GLUE에서 이전의 SOTA급 성능을 낼 수 있었다.
- LtR 언어 모델링과 오토인코더가 이러한 모델을 사전 학습하기 위해 사용되었다.

## Transfer Learning from Supervised Data
- 대형 데이터에 대한 지도 학습을 통한 모델 생과 이를 transfer함으로서 효율적인 학습을 하는 모델도 존재한다.
	- Natural Language Inference와 Machine Translation에 대한 연구가 그렇다.
- 컴퓨터 비전 연구들도 거대한 pre-train 모델에 대한 transfer learning이 효율적인 파인 튜닝 모델을 만들 수 있음을 보였다.

# BERT
- BERT에는 두가지 스텝이 존재한다.
	- Pre-training
		- 모델이 레이블되지 않은 데이터에 대해 학습이 진행된다.
	- Fine-tuning
		- BERT 모델이 pre-trained 파라미터로 초기화된 후, downstream task로부터 레이블된 데이터로 학습이 진행된다.
		- 각 downstream task는 같은 pre-train 파라미터로 초기화된 각각의 fine-tuned 모델을 갖고 있다.
- 다른 모델과 BERT의 차이점은 다른 작업에 대한 unified task이다.
	- pre-train 아키텍처와 최종 downstream architecture간의 차이점을 최소한으로 가져간다.

## Model Architecture
- BERT는 멀티 레이어 양방향 트랜스포머 인코더를 기반으로 아키텍처가 구성되었다.
	- 트렌스포머의 기본 아키텍처와 거의 유사하게 사용한다.
- 레이어의 개수를 $L$, Hidden size $H$, self-attention head의 개수를 $A$ 라 하자.
	- 두 모델 사이즈에 대한 결과를 낼 수 있었다.
		- BASE 모델은 $L=12, H=768, A=12$. Total Parameters = 110M을 가진다.
		- LARGE 모델은 $L = 24, H=1024, A=16$, Total Parameters = 340M을 가진다.
	- $BERT_{BASE}$ 모델은 비교를 위해 GPT와 동일한 사이즈로 설계되었다.

### Input / Output Representations
- BERT를 다양한 down-stream task에 적용하기 위해서, 우리의 입력 representation은 단일 문장과 문장의 셋을 하나의 토큰 시퀀스로 넣도록 할 수 있다.
	- 이러한 작업을 통해 문장을 실제 문장이 아닌, 인접한 텍스트의 임의의 공간으로 설정할 수 있었다.
- sequence는 BERT의 입력 토큰 시퀀스이다.
	- 하나의 문장이나 두개의 문장이 묶여 들어갈 수 있다.
- 모델에 3만개의 토큰 vocabulary를 가진 WordPiece 임베딩을 사용했다.
	- 모든 시퀀스의 첫번째 토큰은 특수한 분류 토큰(\[CLS\]) 이다.
	- 이 토큰에 대응하는 마지막 hidden state는 sequence representation을 통합하기 위해 사용된다.
- 문장 한 쌍은 싱글 시퀀스로 묶여서 처리한다.
	-  이러한 문장을 두가지 방법으로 처리했다.
		- 두 문장을 특수한 토큰 (\[SEP\]) 토큰으로 구분했다.
		- 모든 토큰에 learned embeding을 추가하여, 어떤 문장에 속하는지 표시하도록 하였다.
- 주어진 토큰에 대해, 입력 representation은 corresponding token, segment, position embedding을 합하는 것으로 설계될 수 있다.

## Pre-Training BERT
- 일반적인 left-to-right / right-to-left 모델 대신, 두가지 unsupervised task를 진행하여 BERT를 학습시킨다.

### Task #1 : Masked Language Model
- 직관적으로 deep 양방향 모델이 left-to-right 모델이나 ltr / rtl 모델의 결합보다 강력하다.
- 표준적인 언어 모델은 left-to-right나 right-to-left 모델로만 학습 가능하다.
	- 양방향 컨디셔닝은 각 단어를 예측하는게 아닌, 직접 보도록 할 수 있기 때문이다.
	- 그리고 멀티레이어 상황에서 trivially하게 단어를 예측할 수 있기 때문이다.
- deep 양방향 모델을 학습하기 위해서, 입력의 일부를 임의로 변경하는 방법을 사용할 수 있다.
	- 이러한 방법을 Masked LM이라 명명한다.
		- 기존에 문학에서는 이를 Cloze task라 했다.
	- 이러한 경우, 마스크 토큰과 대응되는 최종 hidden layer는 일반적인 언어 모델과 동일하게, vocabulary를 거친 출력 softmax가 된다.
- 모든 WordPiece 토큰에서 15%를 마스킹 하여 실험을 진행했다.
	- 오토 인코더를 denoising하는것과 다르게, 전체 입력을 재구성하는 것이 아닌 masked된 단어들을 채우는 과정만 수행했다.
	- 이를 통해 양방향 pre-trained 모델을 얻을 수 있었지만, pre-train 모델과 fine-tuning간의 미스매치가 발생했다.
		- \[MASK\] 토큰이 파인튜닝동안 나타나지 않기 떄문이다.
		- 이를 해결하기 위해서, masked된 단어들을 모두 토큰으로 변환하지 않았다.
			- 훈련 데이터 생성기가 15%의 토큰 포지션을 임의로 선택하도록 했다.
				- i번째 토큰이 선택하면, 이걸 80%는 \[MASK\]로, 10%는 랜덤한 토큰으로, 남은 10%는 바뀌지 않은 상태로 두었다.
			- $T_i$ 이 크로스 엔트로피 로스와 함께 원래 토큰을 추측하기 위해 사용되었다.

### Task #2 : Next Sentence Prediction
- 많은 중요한 downstream task는 두 문장간의 관계를 이해하는 것에 기반한다.
	- 이러한 과정은 단순 언어 모델링으로는 알아낼 수 없다.
- 문장 관계를 이해하도록 모델을 학습하기 위해, 단일 문장 코퍼스에서 다음 문장 예측 task를 pre-train에 수행했다.
	- 특히 각 pre-training 예시에서 문장 A와 B를 선택할 때, 50%의 확률로 B는 A의 다음 문장으로 왔다.
	- 남은 50%는 그냥 코퍼스에 있는 랜덤한 문장이었다.
- 이러한 과정의 간단함에 비해, Question Answering 등의 작업에서 굉장히 잘 작동했다.
- 이전 연구에서는 문장 임베딩을 downstream task에서만 사용했다.
	- BERT는 모든 파라미터를 초기화하고 end-task model parameter에도 사용한다.

### Pre-training
- Pre-training 과정은 기존에 존재하는 언어 모델의 프리 트레이닝과 동이랗다.
	- BooksCorpus(800M Words), English Wikipedia(2,500M Words) 코퍼스를 사용하였다.
- 길고 연속된 시퀀스를 추출하기 위해서 문서 단위의 코퍼스를 사용하는 것이 문장 레벨 코퍼스를 사용하는 것보다 좋았다.

## Fine-tuning BERT
- 파인 튜닝은 트랜스포머의 self-attention 매커니즘이 BERT를 다른 downstream task에 적용할 수 있도록 하여 굉장히 직관적이다.
	- 적절한 입력과 출력을 바꿔서 이렇게 할 수 있다.
- 입-출력 텍스트 페어에 대한 적용을 위해, 일반적인 패턴은 텍스트의 페어를 양방향 크로스 어텐션 이전에 인코딩하는 것이었다.
	- BERT는 두개의 stage를 통합하기 위해 셀프 어텐션 매커니즘을 사용한다.
		- 텍스트의 페어를 셀프어텐션으로 결합한 후 인코딩하였다.
		- 이렇게 함으로서 두 문장의 양 방향 크로스 어텐션을 포함할 수 있었다.
- 각 태스크에 대해, 단순하게 입력과 출력을 BERT에 집어넣어 fine-tune을 사용할 수 있었다.
	- 입력으로는 pre-training의 문장 A, 문장 B는 다음에 유사하다.
		- paraphasing의 문장 페어
		- 가설-전제 페어
		- 질문-답변 페어
		- degenerate text - $\emptyset$  페어로 구성된다.
	- 출력으로는 토큰 representation이 출력 레이어에서 토큰 레이어로 들어간다.
		- 이때 감정 분석 등의 분류는 \[CLS \] 태그로 들어간다.
- pre-training에 비교하여, 파인튜닝은 비용이 적게 소모된다.
	- 연구 속 모든 결과가 구글 클라우드 TPU에서 1시간 이내로 수행될 수 있었다.
