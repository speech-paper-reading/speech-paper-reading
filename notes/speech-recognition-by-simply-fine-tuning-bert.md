# Speech Recogntion By Simply Fine-Tuning BERT
Wen-Chin Huang et al. \
NagoyaUniversity
## Summary
- MLM으로 pretraining된 BERT를 기반 단순 Finetuning만으로 asr의 가능성을 보여줌 
- 성능은 좋지 않지만 앞으로 더 발전 가능성이 있어 보임 
- Token과 Audio간의 alighnment를 할수 있는 방법을 강구
- 더 좋은 encoding (embedding을 추출할떄) 방법이 필요

## Reference
- [Attention is All you Need](https://arxiv.org/abs/1706.03762)
- [BERT](https://arxiv.org/abs/1810.04805) 

## BERT
<img src="https://user-images.githubusercontent.com/47840814/108331259-f0821700-7211-11eb-9c5a-93e47a837798.png">

- Bidirectional Encoder Representations from Transformers
- Pretrain deep bidirectional representations from unlabeled text by jointly conditioning on both left and right context in all layers
- The input embeddings are the sum of the token embeddings, the segmentation embeddings and the position embeddings

## BERT-ASR

<img width="321" alt="Screen Shot 2021-02-18 at 5 56 44 PM" src="https://user-images.githubusercontent.com/47840814/108333944-cc740500-7214-11eb-946f-cc8da5e86110.png">


<img width="515" alt="Screen Shot 2021-02-18 at 5 56 49 PM" src="https://user-images.githubusercontent.com/47840814/108333911-c1b97000-7214-11eb-8b62-96532b470db3.png">

### Training a probabilistic LM with BERT
<img width="400" alt="Screen Shot 2021-02-18 at 5 56 56 PM" src="https://user-images.githubusercontent.com/47840814/108338541-b87ed200-7219-11eb-8606-1b9dc1748a87.png">

- Simply iterate through each traing data to predict (classify) next token

### Training objective

<img width="427" alt="Screen Shot 2021-02-18 at 6 10 52 PM" src="https://user-images.githubusercontent.com/47840814/108333801-a9e1ec00-7214-11eb-889b-eaf3e7c55637.png">

1. Pretrain a BERT using a large-scale text dataset
2. fine-tune a BERT-LM on the transcriptions of the ASR traing dataset, as described in [Training a probabilistic LM with BERT]

## Acoustic encoder
<img width="617" alt="Screen Shot 2021-02-18 at 6 12 31 PM" src="https://user-images.githubusercontent.com/47840814/108334018-e281c580-7214-11eb-80de-f82fb5d306dd.png">

### Average encoder
- after segmentation each F will bee average on the time axis, and resulting vector will be go through linear layer which transform the vector into the dimension of the model.  

### Conv1d resnet encoder
- Same as the average encoder
- add L learnable residual block that operates on whole sequence

## Experiment
<img width="343" alt="Screen Shot 2021-02-18 at 5 58 17 PM" src="https://user-images.githubusercontent.com/47840814/108333561-6edfb880-7214-11eb-86ed-0dd1a4e96044.png">

- Two decoding scenarios (alignment strategy):
    1. the oracle decoding is where we assumed that alignment is accessible
    2. the practical decoding seeting where assuming that the alignment between each utterance and the underlying text is linear (dividing the acoustic frames into segments of equal lengths) 
### Dataset
- AISHELL-1: 170 hr Mandarin speech
- 80-dim log Mel + 3-dim pitch features and normalized them
- Have to follow the LM training stratege above and make it into 1.7M training samples
- Used whole word maksing (not token masking)
- HMM/DNN which trained with AISHELL-1 traing set was used for the alignment 
### Result
<img width="1023" alt="Screen Shot 2021-02-18 at 6 08 52 PM" src="https://user-images.githubusercontent.com/47840814/108333532-62f3f680-7214-11eb-81ec-5cf8c46f2f2f.png">

### Error Analysis
- Polyphone in Mandarin
    - Mandarin is a character-based language (pronunciation can be mapped to different charactes)
    - The expereiment was performed on the character based BERT so it is possible for the model to learn same pronounciation on different characters.
    - See the SER (Sy)
## Conclusion
- Simply finetuned BERT for the ASR 
- Alighnment/Segmentation is important
- Room for a lot of improvement

