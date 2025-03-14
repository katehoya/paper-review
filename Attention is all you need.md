residual connection, position-wise fully connected feed-forward network  
 
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
## 1. Introduction  
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

 ## 2. Background  
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
## 3. Model Architecture  
 - 가장 성능이 좋은 신경망 기반 시퀀스 변환 모델은 인코더-디코더 구조를 이용한다.  
   인코더는 입력 시퀀스를 연속적인 표현으로 변환한다.  
   그거를 받아서 디코더는 하나씩 출력 시퀀스를 생성한다.
 - 각 step에서 해당 모델은 이 전에 생성된 symbol을 그 다음 symbol을 생성하는 데 추가적인 input으로 사용함으로써 자기회귀적이다.
 - Transformer는 이러한 기본적인 인코더-디코더 구조를 따르지만, 인코더와 디코더에서 self-attention과 point-wise fully connected layers를 여러 겹으로 쌓는다.  
   사진에서 왼 쪽이 인코더, 오른 쪽이 디코더이다.  
   ![image](https://github.com/user-attachments/assets/8bf2d8d2-f0a7-4b55-8c17-30cd111e9c3c)

### 3.1 Encoder and Decoder Stacks  
인코더 :   
 - 인코더는 6개의 동일한 층을 쌓아올린 형태로 구성되고, 각 층은 2개의 sub층을 갖는다.
 - sub layer의 첫 번째는 self-attention 메커니즘이고, 두 번째는 간단한, position-wise fully connected feed-forward network이다.
 - 각 sub layer에는 residual connection이 적용되며 이후 layer 정규화가 진행된다.
 - 결국 각 sub-layer의 output은 다음과 같이 계산된다.
   ==> LayerNorm(x + Sublayer(x)).       (Sublayer(x)는 해당 sub layer에서 수행하는 연산.)
 - 이러한 residual connection을 원활하게 하기 위해, embedding layer를 포함한 모든 sub-layer들의 출력은 512차원으로 생성된다.

디코더 :   
 - 디코더 또한 동일한 6개의 층으로 이루어져 있다.
 - 다만 인코딩 층에서 있었던 2개의 sub layer에다가 + 디코더는 인코더의 출력을 받아서 multi-head attention을 수행하는 3번째 sub layer를 추가한다.
 - 인코더와 비슷하게, 각 sub-layer에 residual connection을 적용한 다음 layer 정규화를 수행한다.
 - 우리는 또한 디코더에서 position이 후속 position을 이용하는 걸 막기 위하여 sub layer의 self attention을 수정했다.(masking 처리를 하였다.)
  이 masking은 출력 임베딩을 한 칸 이동하여 해당 position에 있는 단어를 예측할 때 오직 그 이전에 나온 output만 가지고 예측할 수 있게 한다.

### 3.2 Attention  
 - Attention 함수는 query와 key-value쌍을 output으로 mapping하는 것이다.(q,k,v,o은 모두 벡터.)
 - 출력은 value들의 가중치 합으로 계산되며, 각 value에 할당되는 가중치는 query와 해당 key간의 유사도를 나타내는 함수에 의해 결정된다.


#### 3.2.1 Scaled Dot-Product Attention  
![image](https://github.com/user-attachments/assets/6a8b4e48-fbbc-4426-b5d1-c932ed4c2c0e)  
 - input으로는 d_k차원의 query와 key, d_v차원의 value가 들어간다.
 - query, key, value들은 행렬 Q,K,V로서 동시에 계산된다.
 - Output계산은 다음과 같다.  
![image](https://github.com/user-attachments/assets/4c5d4597-be7d-44f0-ba23-0343c8c6c809)

이와 비슷한 성능을 하는 Attention이 원래 dot-product attention과 additive attention이 있다.  
 - dot-product attention은 우리가 차용한 방식이고,
 - additive attention은 하나의 hidden layer에서 feed-forward network를 이용하여 Q와 K 간 연관성을 계산한다.
 - 둘 다 이론적으로는 비슷하지만, dot product attention이 행렬 곱셈으로 구현가능하기 때문에 속도가 빠르고 메모리에도 효율적이다.
 - 작은 차원(d_k)에서는 두 메커니즘 성능이 비슷하되, 큰 차원에서는 dot product attention이 additive attention보다 성능이 별로다.
 - 우리는 큰 차원의 d_k를 가정하였고 그렇다면 dot product attention을 사용할 경우 output크기가 너무 커져서 softmax에 넣으면 출력이 0 또는 1에 가까운 극단적인 값으로 변한다.
   즉, softmax의 gradient가 너무 작아져 학습이 제대로 진행되지 않는 문제가 발생한다.
 - 그래서 ![image](https://github.com/user-attachments/assets/3a1a1af4-4604-4092-a857-0dba5317741f) 이걸로 scaling을 해줬다.

#### 3.2.2 Multi-Head Attention  
![image](https://github.com/user-attachments/assets/0b6959bd-a1b5-401d-8828-9a2e468ad941)  

 - 기존의 attention에서는 d_model 차원의 key, value, query를 한 attention function에 넣는다.
  그러나 multi-head attention에선 각각을 h개의 부분으로 나누어 각각 다르게 학습된 차원 d_k,d_k,d_v으로 변환한다.
 - 이렇게 다른 차원으로 투영된 q,k,v들을 가지고 attention을 병렬로 연산하고, 결과적으로 d_v차원의 output value가 나온다.
 - 이들은 다시 병합되어 최종적으로 투영되어(원래 차원으로 복원) final value가 된다.

 - Multi-head attention은 모델이 서로 다른 representation subspace에 있는 정보를 다른 position으로 배정한다.
  ===> 각 위치마다 다른 어텐션을 적용해 각기 다른 위치에서의 의미를 다르게 해석한다.(각 헤드가 문장에서 서로 다른 부분을 집중적으로 학습한다.)
ex) 어떤 헤드는 문장에서 주어를 잘 학습하고, 다른 헤드는 동사를 잘 학습한다던가.  
![image](https://github.com/user-attachments/assets/8f03930a-053e-46c8-9e37-6a32dc71e20a)
![image](https://github.com/user-attachments/assets/a4e6afe8-0edd-4a2b-b0d1-123ceb8b194c)![image](https://github.com/user-attachments/assets/13a4554e-59eb-4e5b-b92a-a76bb6e7c2ed)

 - 이 논문에선 h=8, d_k = d_v = d_model/h = 64로 설정했다.
 - 각 head에서의 차원이 줄었기 때문에, 차원을 안 줄이고 하나의 attention으로 계산했을 때와 연산량은 비슷하다.

#### 3.2.3 Applications of Attention in our Model  
### 3.3 Position-wise Feed-Forward Networks  
### 3.4 Embeddins and Softmax  
### 3.5 Positional Encoding  
## 4. Why Self - Attention  
## 5. Training  
## 6. Results  
## 7. Conclusion  










