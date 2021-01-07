Data-Driven Harmonic Filters for Audio Representation Learning
===
Minz Won, Sanghyuk Chun, Oriol Nieto, and Xavier Serra

ICASSP, 2020

Reference
--
* [Paper](https://ccrma.stanford.edu/~urinieto/MARL/publications/ICASSP2020_Won.pdf) 
* [Source code](https://github.com/minzwon/data-driven-harmonic-filters)

Summary
--
* 다방면에서 활용 가능한 audio representation front-end 모듈
* 배음 구조를 적용한 data-driven 방식의 필터 뱅크
* music tagging, keyword spotting, sound event tagging에서 SOTA 달성

배음
--
![image](https://user-images.githubusercontent.com/33409264/103764441-d4118d00-505e-11eb-859e-f175e1aa1932.png)
* 배음이란 특정 진동수(기음)의 정수배를 의미
* 배음의 세기에 따라서 음색 결정

Approach
--
* feature extraction에 있어서 hand-crafted보다 data-driven 방식이 좋은 성능을 내고 있음
* data-driven 방식으로 모듈을 구성하되, 음향악적 지식을 가미하면 더 좋은 성능을 낼 수 있을 것으로 기대

Architecture
--
![image](https://user-images.githubusercontent.com/33409264/103475478-c3f47600-4df0-11eb-98d2-9a9598948f3e.png)

* **front-end** : STFT를 취해 Spectrogram으로 만든 후, harmnoic filters를 거침
    ![image](https://user-images.githubusercontent.com/33409264/103597462-a4ba2d80-4f43-11eb-8ffc-20c14a21a25e.png)
    * f는 frequency bin, f_c는 mel-scale로 초기화 된 center frequency, BW는 대역폭
    * BW는 0.1079f_c + 24.7로 근사 가능 [link](https://www.sciencedirect.com/science/article/pii/037859559090170T)
    
![image](https://user-images.githubusercontent.com/33409264/103597853-83a60c80-4f44-11eb-960a-d5d6bf928bb8.png)
    
(필터가 3개, 4배음 harmonic filters의 예시, 실제로는 128개의 필터, 6배음까지 사용)
    
* **back-end** : 7개의 resudial하게 연결된 CNN 블럭 적용
    * conv(3, 3) -> batch_norm -> activation(relu) -> max_pool(2)
    * 5개의 블럭은 128 채널, 나머지 두 개의 블럭은 256 채널 사용
    
Datasets
--
* Automatic music tagging : Magna tag a tune dataset, 약 26k개의 샘플, 자주 쓰인 상위 50개의 태그
* Keyword spotting : Speech commands dataset, 약 106k개의 샘플, 35개의 커맨드
* Sound event tagging : DCASE 2017, task 4 : 'large-scale weakly supervised sound event detection for smart cars', 약 53k개의 샘플, 17개의 이벤트

Experiments
--
![image](https://user-images.githubusercontent.com/33409264/103481390-1bf5a180-4e1e-11eb-997a-f5bfbaa38985.png)
* 3개 분야 모두 SOTA 성능을 보임
* 특히, keyword spotting에서 매우 빠른 수렴 속도 보임
* 6배음까지 학습할 때 제일 좋은 성능을 냄 -> 너무 많은 배음을 보면 기음의 범위가 줄어들기 때문
* f_c는 mel-scale로 fix하든지 learnable하게 하든지 뚜렷한 차이 존재하지 않음
![image](https://user-images.githubusercontent.com/33409264/103598632-6eca7880-4f46-11eb-93b6-19721f314559.png)
* Q는 fft-bin, 데이터 셋, tone scale에 따라서 달라지는 의미있는 파라미터
