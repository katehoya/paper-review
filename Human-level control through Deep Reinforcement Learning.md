# Human-Level Control through Deep Reinforcement Learning  
***
## Abstract  
 - 강화학습의 이론은 환경 내에서 에이전트가 어떻게 최적의 컨트롤을 할 수 있는지를 심리학적, 신경과학적인 관점에서 설명한다.  
 - 그러나 강화학습을 실제 환경만큼의 복잡도에서 쓰기엔 어려움이 있었다.   
   실제 환경은 고차원 sensory input이라서 이걸 효율적으로 표현할 수 있어야 하며, 과거의 경험에서 새 상황으로 일반화해야 한다.  
 - 이를 강화학습과 계층적인 센서 처리 시스템의 조화를 통해 해결한 것으로 보인다.  
 이런 조화는 실제 뉴런과 강화학습 간의 유사성을 보여주는 다양한 신경 데이터에서도 확인할 수도 있다.  

 - 그러나 여전히 강화학습 에이전트는 feature를 수동으로 다룰 수 있거나, 저차원이고 모든 상태를 완전히 알 수 있는 그런 도메인에서만 적용이 한정되었다.  
 - 여기서 우리는 최근 큰 발전을 이룬 DNN을 사용하여 deep-Q-network이라 불리는 인공 agent가 고차원 입력을 직접 받아서 정책을 학습할 수 있게 했다.  
  이 agent를 Atari 2600이라는 게임이라는 꽤 challenging한 환경에서 테스트했다.  
 - 이 deep-Q-network agent를 사용하여 게임 픽셀과 점수만을 input으로 받고 기존의 모든 알고리즘의 성능을 뛰어넘었다.  
  게다가 동일한 알고리즘, 뉴럴 네트워크, 하이퍼파라미터를 사용하여 49개의 다양한 게임에서 인간 프로 선수들의 수준과 비슷한 수준의 성능을 달성하였다.  

 - 우리는 다양한 부류의 어려운 작업에서도 경쟁력을 갖춘 알고리즘을 만들고자 했다. 이는 일반적인 인공지능의 주요 목표이며, 아직 성공하지 못했다.  
  이를 위해 강화학습과 DNN으로 알려진 인공 신경망을 결합한 DQN이라는 새 agent를 개발했다.  
 - 데이터의 점점 추상적인 표현들을 학습하도록 사용된 노드의 층이 이용된 최근 DNN의 발전은 인공신경망이 raw sensory data로부터 바로 object category와 같은 개념을 학습할 수 있게 되었다.  
  우리는 인간 수용장의 구조를 모방하기 위해 겹쳐진 convolutional 필터를 계층적으로 쌓은 층 사용한 deep convolutional network 구조를 사용한다.  
  이로 인해 이미지에 나타나는 local spatial correlation을 추출하고 관점, 크기의변화와 같은 자연스러운 변화에 대한 강건성도 얻을 수 있다.  

 - 우리는 agent가 관찰, 행동, 보상의 과정을 통해 환경과 상호작용하는 것을 고려한다.  
  agent의 목표는 미래의 reward를 최대화하는 방향으로 action을 선택하는 것이다.  
  정확히는 optimal한 action-value function을 근사하기 위해 deep CNN을 이용한다.![image](https://github.com/user-attachments/assets/f92cc330-19c7-4879-8fc5-91df9f729d3a)  
 - action-value function을 표현할 때 neural network와 같은 비선형 함수가 approximator로 쓰이면 강화학습은 발산하거나 불안정하다.  
  여기에는 여러 이유가 있다.  
  1) 연속된 관찰 내의 상관관계
  2) Q값이 조금만 update되어도 policy를 크게 변화시킬 수 있다. 연쇄적으로 data 분포도 크게 바뀐다.   
  3) action-value(Q)와 target value 간의 상관관계  

 - 2가지 idea를 첨가해 새로운, 변형된 Q-learning으로 이러한 불완정성을 해결했다.
  1) 생물학에서 영감을 받은 **experience replay**라는 걸 사용했다.
     이는 data들을 랜덤화함으로써 일련의 관찰들 간의 상관관계를 없애고 데이터 분포 변화를 완화한다.
  2) update의 반복을 통하여 Q값을 target에 점진적으로 맞춰가도록 조정했다. 단, 목표 값은 일정한 주기마다만 갱신되도록 하여 학습중인 Q값과 target간의 상관관계를 줄였다.  

 - 사실 이미 neural fitted Q-iteration과 같이 강화학습 세팅에서 신경망을 훈련시킬 수 있는 안정적인 방식이 존재하긴 하지만, 이 방법은 네트워크를 수백번의 반복으로 훈련시킨다.
   그러나 이런 방법은 대규모의 신경망에서 쓰이기는 너무 비효율적이다.
 - 우리는 value funation ![image](https://github.com/user-attachments/assets/7215148a-c8e7-46bc-a971-219558bd85ba)의 근사치 를 deep CNN으로 매개변수화한다.(theta는 가중치, i는 반복 횟수)
 -  
   
