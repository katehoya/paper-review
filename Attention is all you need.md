# Attention is all you need  
***
## Abstract  

 - 가장 좋은 모델도 어텐션 메커니즘으로 encoder와 decoder를 연결하는 것이다.  
 - 우리는 Transformer라는 새로운 구조를 제안한다.  
   Transformer는 어텐션 메커니즘에만 기반하며 순환 및 콘볼루션구조는 사용하지 않는다.  
 - 두 기계번역 task에서의 실험은 이 모델이 더 높은 quality를 내면서도 병렬 연산이 가능하며 훈련에도 시간이 적게 걸린다.  
   우리 모델은 WMT 2014 영어-독일어 번역 task에서 28.4 BLEU를 달성하여 기존 최고 점수(앙상블 포함)보다 2BLEU 이상을 향상시켰다.  
   또한 WMT 2014 영어-프랑스어 번역 task에서 단일모델 기준 최고점수인 41.8 BLEU를 기록했으며 8개의 GPU로 3.5일동안 훈련하여 기존 모델보다 적은 훈련 비용이 들었다.  
 - 마지막으로 English constituency parsing task에서 큰 training data와 작은 training data에서 성공적으로 적용하여 다른 task에서도 Transformer가 일반화 가능하다는 것도 입증하였다.
***
## Introduction  
 - RNN, LSTM, GRU는 시퀀스 모델링과 기계번역, 언어 모델링과 같은 시퀀스 변환 문제에서 최신의 접근법으로 자리잡았습니다.  
 - 순환 모델과, 인코더 - 디코더 구조의 한계를 뛰어넘으려는 노력을 지속해왔습니다.
 
 - 순환 모델은 일반적으로 인풋, 아웃풋 시퀀스의 symbol 위치를 따라 연산을 수행한다.  
   이 모델은 이전 hidden state h(t-1)과 t에서의 input을 받아 hidden state h(t)를 생성하는 함수이다.  
   그러나 이러한 순차적인 특성이 training example들을 병렬처리를 못하게 하고, 시퀀스가 길 때는 메모리 제한으로 인해배치 크기를 증가시키는 것이 어렵기 때문에 치명적이다.
 - 그래도 최근의 연구는 인수분해 기법과 조건부 연산을 통해 계산 효율성을 크게 향상시켰고 모델의 성능도 개선했다.
   그러나 순차적 연산의 근본적인 한계는 여전히 남아있다.

  - 어텐션 메커니즘은 인풋, 아웃풋 시퀀스 내에서 거리와 상관없이 의존성을 모델링할 수 있게 하여 compelling 시퀀스 모델링과 변환 모델의 필수적인 부분이 되었다.
  - 그러나 대부분의 기존 연구에서는 어텐션 메코니즘을 RNN과 결합하여 사용하였다.

  - 이 연구에서 **Transformer**라는 모델 구조를 제안한다.
  - Transformer는 순환 구조를 완전히 제거하고 입력과 출력간의 global한 의존성을 이끌어 내는 데 오직 어텐션 메커니즘만을 사용한다.
  - Transformer는 병렬 처리를 대폭 향상시키며 단 12시간, 8개의 P100 GPU만 학습해도 최신 번역 모델보다 높은 성능을 달성할 수 있다.
  ***

 ## Background  
 - 순차 연산을 줄이는 것은 Extended Neural GPU, ByteNet, ConvS2S등의 모델에서도 목표로 설정되었다.
   이 모델들은 기본 building block으로서 CNN을 사용하며 모든 input, output의 위치에 대해 hidden representation을 병렬적으로 계산한다.
 - 그러나 이런 모델들에서 임의의 두 입출력 위치로부터 신호를 연결하는 데 필요한 연산량이 증가한다는 문제가 있다.
   ConvS2S에서는 선형적으로, ByteNet에서는 로그적으로 증가한다.
 - 이 연산량이 증가하는 문제는 시퀀스 내에서 멀리 떨어진 단어들 간의 의존성을 학습하기 어렵게 한다.
 - 반면 Transformer에선 임의의 두 위치 간 관계를 학습하는 연산량을 상수값으로 유지할 수 있다.
   하지만 어텐션 가중치를 평균내는 과정에서 정보의 손실이 발생할 수 있다.
   이를 해결하기 위해 Multi-Head Attention을 사용한다.

 - intra-attention이라고도 불리는 Self attention은 하나의 시퀀스 내에서 다른 위치의 토큰들 간 관계를 학습해 해당 시퀀스의 표현을 계산한다.
 - Self attetion은 독해, 추상적 요약, 텍스트 함의, task-independent한 문장표현학습 등의 다양한 작업에서 성공적으로 활용되었다.

 - End-to-End 메모리 네트워크는 순차적으로 정렬된 순환구조(RNN) 대신 순환적인 어텐션 메커니즘에 기반한다.
   그리고 단순한 언어 기반의 질의응답과 언어 모델링 작업에서 잘 작동한다.

 - 우리가 알기론 Transformer가 RNN과 convolution을 사용하지 않고 오직 self-attention만을 사용하여 input,output의 표현을 연산하는 최초의 순환모델이다.
 -  이후의 section에서 Transformer에 대해 설명할 것이며 self-attention을 사용하는 이유 및 기존 모델 대비 Transformer의 장점을 논의할 것이다.
***
## Model Architecture
   
