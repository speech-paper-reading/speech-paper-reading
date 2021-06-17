# GPT Understands, Too

- Xiao Liu et al.
- Tsinghua University etc.
- arXiv pre-print

​

## Abstract

- GPT를 파인튜닝하는 방법은 Narural Language Understanding (NLU) 태스크쪽에서 좋지 않은 결과를 보임
- P-tuning을 이용해서 GPT로 비슷한 사이즈의 BERT와 비슷 or 웃도는 성능을 기록 (Vanilla 대비 20%이상 성능 향상)
- P-tuning은 BERT 성능도 향상시킴

​

## Introduction

- Language model pre-training은 contextualized text reporesentation 뿐만 아니라 grammar, syntactic, commonsense, world knowledge까지 학습이 된다고 주장하는 연구 결과들이 있음.

- Language model pre-training은 다음 3가지 (uni-directional language models (e.g., GPT), bidirectional language models (e.g., BERT), hybrid language models (e.g., XLNet))로 나눌 수 있음

- 오래동안 GPT 스타일은 NLU 태스크에 적합하지 않다고 여겨져 왔음

- GPT3는 적절한 프롬프트를 이용해서 NLU 태스크를 풀 수 있지만, 매번 좋은 프롬프트를 바로바로 찾는건 현실적으로 힘들다.

- automatic prompt searching (retrieval 등) 같이 discrete prompts를 찾는 방법들이 등장했지만, discrete prompt를 찾는건 차선책일 뿐이다.

- 그래서 continuous space에서 prompt를 찾는 P-tuning을 제안

- P-tuning은 여러 NLU 태스크에서 상당한 성능 향상을 이룸.

​

## Motivation

- Big-model은 많은 문제를 해결해줬지만 poor-transferability 문제가 있음

- Big-model은 fine-tuning을 제대로 하기가 어려움.

- prompt가 조금만 바뀌어도 큰 성능 차이가 있음


## Method: P-tuning


- 예시 문제: "The capital of Britain is [MASK]"

- Prmopt: "The capital of ... is ..."

- Context: "Britain"

- Target: "[MASK]"

- 기존 Inputs: e(token 0), e(token 1), ..., e(token n) (e는 embedding)

- P-tuning Inputs: h(P[0]), ..., h(P[i]), e(x), h(P[i+1]), ..., h(P[m]), e([MASK]) (h는 lstm hidden state, x는 context)

  - P = [0, 1, 2, 3, 4, 5] 이런 식으로 정의

  - Anchor tokens: (b)의 "capital" 같이 태스크와 관련된 토큰을 추가로 임베딩 레이어에 넣어줄 수도 있음 (성능 개선을 위해)

​

- Prompt Encoder 구성
```python
PromptEncoder(
  (lstm): LSTM(hidden_size, hidden_size // 2, num_layers=2, bidirectional=True)
  (mlp): Sequential(
    (0): Linear(in_features=hidden_size, out_features=hidden_size)
    (1): ReLU()
    (2): Linear(in_features=hidden_size, out_features=hidden_size)
  )
)
```
- Bi-directional LSTM + MLP 구조

​

## Experiments

- Knowledge Probing (LAMA Dataset)
<img src="https://user-images.githubusercontent.com/42150335/119778212-3120b900-bf02-11eb-91b0-896d355c901e.png">

- SuperGLUE

<img src="https://user-images.githubusercontent.com/42150335/119778331-56152c00-bf02-11eb-92fc-05acf14e8027.png">
​

## Conclusion

- GPT, BERT에 상대적으로 작은 사이즈의 prompt-encoder를 도입해서 큰 성능 향상을 얻음

- 같은 방법으로 GPT3 같은 모델에도 p-tuning을 도입해서 성능 향상을 기대해 볼 수 있음

- Megatron-LM 사이즈까지는 실험을 했으나, 그 이상의 사이즈는 실험 X
