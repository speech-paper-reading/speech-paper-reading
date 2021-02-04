Contrastive Predictive Coding of Audio with an Adversary
==

Luyu Wang, Kazuya Kawakamu, and Aaron van den Oord (Deep Mind)

INTERSPEECH, 2020

Reference
--
* [paper](https://www.isca-speech.org/archive/Interspeech_2020/pdfs/1891.pdf)

* [wav2vec](https://arxiv.org/pdf/1904.05862.pdf)

* [FGSM](https://arxiv.org/pdf/1412.6572.pdf)

* [SimCLR](https://arxiv.org/pdf/2002.05709.pdf) 

* [AudioSet](https://research.google.com/audioset/)

* [DCASE2013](http://dcase.community/challenge2013/task-acoustic-scene-classification)

Summary
--
* raw audio data에 CPC를 적용하여 general audio representation을 학습
* adversarial perturbations을 통해 data augmentation를 수행
* similarity를 구하는 데에 있어서 projection head 개념 사용


The Contrastive Predictive Coding Approach
--
![image](https://user-images.githubusercontent.com/33409264/106697553-4c4d7d00-6622-11eb-8bd8-a60292814ec6.png)

* self-supervised proxy task : context로부터 future latents를 예측

* <b>Encoder</b> : raw audio를 latent space에 매핑
    * CNN with kernel sizes (10, 4, 4, 4, 4, 4, 4, 4, 1, 1) and strides (5, 2, 2, 2, 2, 2, 2, 2, 1, 1)
    * encode 40ms for every 20ms

* <b>Context network</b> : 이전 정보 요약
    * CNN with kernel sizes (2, 3, 4, 5, 6, 7, 8, 9, 10, 11, 12, 13) and strides (1)
    * total receptive field : 1.62s
    * bidirectional



***

* <b> Loss</b>
 ![image](https://user-images.githubusercontent.com/33409264/106563597-5d898180-656f-11eb-9cde-f959dd10c811.png)

     * loss 자체는 wav2vec과 동일한 형태



* <b>Similarity</b>
 ![image](https://user-images.githubusercontent.com/33409264/106564526-ab52b980-6570-11eb-909c-9b8ec38b3c15.png)

     *  p(·) : a MLP with a single hidden layer of size 512.

***
❖ <b>SimCLR</b>

![image](https://user-images.githubusercontent.com/33409264/106864791-b0993b00-670d-11eb-917d-9b01e5639fab.png)

* projection head(g)는 다운스트림 작업에 유용할 수 있는 정보를 제거할 수 있음
* 따라서 h에서 더 많은 정보를 형성하고 유지


 Adversarial perturbations Augmentations
 --
 * k-th future latent step 예측은 의미없거나 사소한 정보를 사용할 수도 있음
 * Augmentation을 통해 이를 방지 (prior knowledge)

* <b>FGSM (Fast Gradient Sign Method)</b>
![image](https://img1.daumcdn.net/thumb/R800x0/?scode=mtistory2&fname=https%3A%2F%2Ft1.daumcdn.net%2Fcfile%2Ftistory%2F99540C3A5D81D9B032)

 


Datasets
--
- AudioSet(public) : 2 million 10s samples, 527 classes (audio tagging)
- DCASE2013 : 100 30s samples, 10 classes (acoustic scene classification)



Results
--
* <b>Effects of the adversarial perturbations</b>
![image](https://user-images.githubusercontent.com/33409264/106713798-4d8ca300-663e-11eb-80c1-ce3134b6b140.png)

***

* <b>Negative sampling distributions</b>
![image](https://user-images.githubusercontent.com/33409264/106710899-08667200-663a-11eb-8df4-627c015a361b.png)

     1) local sampling : 같은 샘플 내에서만 샘플링
    2) cross sampling :같은 샘플 + 다른 샘플 샘플링
    3) exclusive sampling : 다른 샘플에서만 샘플링



***


* <b>Comparison to the state of the art</b>

![image](https://user-images.githubusercontent.com/33409264/106709022-35fdec00-6637-11eb-8122-f10d2d33eea1.png)

- raw audio를 직접 사용
- [3]과 [2]보다 1.4 million 적은 train data 사용


