# Luna: Linear Unified Nested Attention
  
- USC + CMU + Facebook AI
- 2021.06
- [code](https://github.com/XuezheMax/fairseq-apollo)
  
## Abstract
  
- 트랜스포머의 Multi Headed Self Attention은 시퀀스가 길어질수록 메모리, 시간복잡도가 quadratic함
- 이를 linear하게 바꾸기 위한 시도로 Luna (Linear Unified Nested Attention) 을 제안
- 핵심은 인풋을 고정 길이로 변환한다는 점과 attention을 2개로 분리한다는 점.
- Positional Embedding을 별도의 고정 길이 query로 뺌.
- 속도는 Performer랑 비슷한데 시퀀스가 길어질수록 좀 더 효과적이고 메모리도 적게 쓴다고 함.
- 정확도 성능은 경쟁력이 높은 편.

  
## Attention
  
### Traditional attention mechanism:
    
<img src="https://user-images.githubusercontent.com/42150335/127510622-5af08d8d-771c-4bb9-8e9e-53bb3f4cf85e.png" height=70>
  
- X: Query sequence, C: Context sequence
- ω: activation function (usually softmax)
- Q = XW<sub>Q</sub>, K = CW<sub>K</sub>, V = CW<sub>V</sub>
- Self attention에서는 X == C
- 공간 & 시간복잡도: O(nm) (n: X의 시퀀스 길이, m: C의 시퀀스 길이)
  
## Linear Unified Nested Attention (LUNA)
  
<img src="https://user-images.githubusercontent.com/42150335/127514004-0ca02332-8ef5-4563-b9b4-bd9bf6bb72b9.png" height=400>

- Goal: Attention mechanism's complexity **quadratic => linear**
  
### Luna (Pack and Unpack Attention)
  
- 이 어텐션의 핵심은 어텐션을 2개로 쪼개는 것.  
- O(ln) (l은 P의 길이)
- Pack Attention: 
  - Query를 learnable paramter인 P로 대체 (고정 길이, 길이에 대한 실험 있음) 
  - 상대적으로 더 짧은 Query를 놓음으로써 complexity를 줄임
  - P-contextual: 첫 레이어의 P는 learnable parameter이며 다음 레이어로 전달 
  - P-non-contextual: 각 레이어마다 P를 학습하고 다음 레이어로 넘기지 않음 
  - contextual & non-contextual에 대한 실험은 뒤에 있음

<img src="https://user-images.githubusercontent.com/42150335/127603299-44234ff8-1735-4dc2-95b0-bc8f66bfbbde.png" height=60>

- Unpack Attention:
  - Pack Attention의 결과인 `packed context`를 Key와 Value로 사용. Query는 기존 query.

<img src="https://user-images.githubusercontent.com/42150335/127603352-dfb741df-ec40-4b49-8bdf-b158e55b771e.png" height=70>

- Luna Attention:

<img src="https://user-images.githubusercontent.com/42150335/127603409-554fa551-e89d-46ee-aec4-9a471a5cdc8f.png" height=70>
  
- Luna Layer

<img src="https://user-images.githubusercontent.com/42150335/127603246-192380b0-fde6-4941-b060-1bf639d3b8e7.png" height=100>

## Discussion

### Relation to Linformer

- Linformer와 비슷한 포지션. 그럼 뭐가 더 나은가?
  - Linformer는 인풋 시퀀스가 모두 고정 길이를 가져야하는데, Luna는 various length가 가능 (projection matrix 때문에 linformer는 길이가 고정이여야함)
  - 결정적으로 Linformer보다 성능이 좋음


## Experiment
  
### Long-Context Sequence Modeling
  
#### **Score**

<img src="https://user-images.githubusercontent.com/42150335/127603947-40c6b9f0-63d7-475c-8b6d-cefdfbe5ab59.png" height=500>

#### **Training Speed & Memory**

<img src="https://user-images.githubusercontent.com/42150335/127604072-79facf8c-7f84-4e9d-bd1e-38b3322458d0.png" height=500>
  
- 인풋 길이가 길어지면 Luna가 Linformer보다 더 빠름
- 메모리 사용량에서 Luna가 Linformer를 포함한 다른 모델들보다 경쟁력이 있음

### NLU Task (Masked Language Modeling for Large-Scale Pretraining)

<img src="https://user-images.githubusercontent.com/42150335/127604344-7074edf7-ede2-4140-a6e8-320ed711d5f3.png" height=300>

- BERT 방식으로 pre-training 후 파인튜닝 했을 때 성능 비교
- RoBERTa와도 비견될만큼 좋은 성능을 보임

### Machine Translation
  
<img src="https://user-images.githubusercontent.com/42150335/127604608-822e1fd6-00a1-472b-801e-24cda16efa5f.png" height=250>

### Abbrebiation Study (contextual <-> non-contextual)

<img src="https://user-images.githubusercontent.com/42150335/127604743-ae8289cc-d420-41b0-9ff1-5604ef770b4c.png" height=150>

- P를 각 레이어의 파라미터로 둘지, 위층으로 넘김으로써 context 정보를 넘겨줄지에 대한 실험
- 결론: 다음 층으로 넘겨주는게 좋더라.
