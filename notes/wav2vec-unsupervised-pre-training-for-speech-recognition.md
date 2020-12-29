# WAV2VEC: Unsupervised Pre-Training for Speech Recognition
Steffn Schneider, Alexei Baevski, Ronan Collobert Michael Auli  \
Facebook AI Research

- NLP & Computer Vision already adopted unsupervised pre-training on there domains and achieved SOTA.
- [BERT](https://arxiv.org/pdf/1810.04805.pdf) uses unsupervised pretraining approaches.
    - Input으로 재공된 문장/지문의 특정단어를 Masking한 후, 그 Masking된 단어를 기준 앞 뒤의 단어들을 이용해 Masking된 단어를 예측 하는 방식으로 pre-training함 

## Pre-Training Approach

- 그럼 오디오는 어떤식으로 pre-training을 해야할까?
    > "Given an audio signal as input, we optimize our model to predict future samples from a give signal context."
- [Representation Learning with Contrastive Predictive Coding](https://arxiv.org/pdf/1807.03748.pdf)

## Mdoel

![Screenshot from 2020-12-28 02-14-43](https://user-images.githubusercontent.com/47840814/103176236-72f5f680-48b3-11eb-8375-b7fa28911c26.png)

- Encoder network embeds the audio signal in a **latent space**
    - CNN with kernel sizes (10,8,4,4,4) and strides (5,4,2,2,2)
    - Output is **low frequency feature** which encodes about 30,s of 16kHz os audio (striding results z<sub>i</sub> in every 10ms)

- Context network combines multiple time-steps of the encoder to obtain contextualized representations

## Objective
![Screenshot from 2020-12-28 02-15-16](https://user-images.githubusercontent.com/47840814/103176226-6a052500-48b3-11eb-92e0-3e79c1900215.png)

- Minimizing contrastive loss for each step k = 1,...,K
- The model basically has to tell which on is the future sample z<sub>i+k</sub> from negative samples (randomly picked sample from audio)
- &lambda; is the number of negatives

## Experimental Setup

### Data

- TIMIT: 3 hours of audio data 
- Wall Street Journal (WSJ): 81 hours of transcribed audio data
    - trained on si284
    - validate on nov93dev
    - test on nov92
- Librispeech: 960 hours of clean and noisy speech for training

- For pretrained model training: 
    1. 81 hours of the WSJ
    2. 80 hours of lcean Librispeech
    3. Full 960 hour Libirispeech
    4. Combination of all of 3.

### Acoustic Model

- All of the Acoustic models were implemented with wav2letter++ toolkit
- Baseline Mdel: **80 log-mel filterbank coefficients** for a 25ms sliding window with stride 10ms
- trained on 8 NVIDIA V100 GPUs
    
### Decoding
![Screenshot from 2020-12-29 21-24-02](https://user-images.githubusercontent.com/47840814/103283554-385b9d80-4a1c-11eb-87ff-91bc6ee35860.png)
- 4 gram KenLM language model
- Word-based convolutional language model
character based convolutional language model


### Results
- pre-training with larger dataset brings better result
- fine-tunning doesn't have a great effect on the pre-training embedding but only increases acoustic model training time 
- Also pretraining with more than K=12 steps doesn't result in better performance 

**with WSJ** 
- pre-trained model reduce WET by **36%** on nov92 \

![Screenshot from 2020-12-28 02-45-51](https://user-images.githubusercontent.com/47840814/103283504-13ffc100-4a1c-11eb-9fb7-263b548f153c.png) \

**With TIMIT** \
![Screenshot from 2020-12-29 22-02-53](https://user-images.githubusercontent.com/47840814/103285495-a2c30c80-4a21-11eb-8aa7-f437295ea921.png)

