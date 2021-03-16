# Pushing the Limits of Semi-Supervised Learning for Automatic Speech Recognition
  
Yu Zhang et al., 2020  
Google Research, Brain Team
  
***

## Reference
  
- [Wav2vec 2.0 Summary](https://github.com/kakaobrain/nlp-paper-reading/blob/master/notes/wav2vec%202.0.md)  
- [Conformer Summary](https://github.com/speech-paper-reading/speech-paper-reading/blob/main/notes/conformer.md)
  
***

## Summary
  
- 현재 Papers with Code 기준 ASR 부문 State-Of-The-Art  
- Conformer + Wav2vec 2.0 + Noisy Student Training
- 1.3%/2.6%/1.4%/2.6% on the dev/dev-other/test/test-other sets  
  
<img src="https://user-images.githubusercontent.com/42150335/111322357-2f3dac80-86ac-11eb-8a05-24be848077de.png" height=300>
  
***
  
### Wav2vec 2.0 Pre-training
  
<img src="https://user-images.githubusercontent.com/42150335/92450554-8a22b280-f1f6-11ea-8f66-0616b29d8c94.png">
  
- 53,000이라는 대량의 Unlabeled speech data로 학습
- Pre-training 과정
  - Waveform에서 CNN을 이용해서 피쳐를 뽑음 
  - 이를 Vector Quantization을 통해 one-hot-vector로 만들고 Embedding matrix를 내적하여 token화 함
  - 일정 비율로 Masking하고 다음 Token이 뭔지 알아맞추게하는 Masked Language Modeling (MLM) 학습 방식 적용
- Vector Quantization
<img src="https://camo.githubusercontent.com/4e4253817961b5bead8072739c39bd3f3daaced98e8735018c50e8a55d78fb9c/68747470733a2f2f692e696d6775722e636f6d2f7931355175355a2e706e67" height=300>
  - Z를 선형변환하여 logit을 만듦
  - 여기에 Gumbel Softmax와 argmax를 취해 one-hot vector를 만듦
  - 이후 Embedding matrix를 내적해 Z^를 만듦
  
### Conformer
  
- Self-attention 기반한 트랜스포머는 global-context 정보를 잘 표현하지만, local-context에서는 부족하다는 단점이 있음
- 반면, CNN 기반 모델은 local-context는 잘 표현하지만 global-context를 반영하기 위해서는 적당한 dilation과 깊은 구조를 가져야 함
- 이 두 방법을 결합하여 global-context와 local-context 모두 잘 표현할 수 있도록 하기 위한 transformer + CNN 결합구조인 Conformer 구조 제안
  
## Method
  
본 논문에서 실험한 모델 구조 및 트레이닝 방법

### Model Architecture
  
Conformer Encoder + LSTM decoder로 이루어진 Transducer 구조  
  
<img src="https://user-images.githubusercontent.com/42150335/111322627-77f56580-86ac-11eb-957c-d51db823e4e4.png" height=600>
  
- 기존 Conformer 구조에서 Wav2vec 2.0 Pre-training을 도입하기 위해 Masking하는 과정과 Linear Layer (Quantization 대체) 추가
- 사이즈별로 L, XL, XXL로 구분
- XXL+는 Conformer XXL에 Conformer block을 stack (XXL보다 50M 파라미터 추가)

<img src="https://user-images.githubusercontent.com/42150335/111324910-8ba1cb80-86ae-11eb-997f-12634e7b6164.png">
  
### Wav2vec 2.0 Pre-training
  
- 60k Libri-Light 데이터셋 사용
- 기존 논문과 달리, 인풋으로 log-mel spectrogram 사용
- Masking 된 인풋과 예측한 context vector 간의 contrastive loss로 학습
  
### Noisy Student Training with SpecAugment
  
<img src="https://user-images.githubusercontent.com/42150335/111328868-fa345880-86b1-11eb-925c-a76cdbfd7c8c.png" height=200>
  
## Experiiments
  
<img src="https://user-images.githubusercontent.com/42150335/111329076-2e0f7e00-86b2-11eb-8c87-17d2eca8948b.png" height=400>
  
- 결과적으로 Pre-training + NST가 좋은 성적을 냄
