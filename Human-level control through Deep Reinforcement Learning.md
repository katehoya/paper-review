# Human-Level Control through Deep Reinforcement Learning  
***
## Abstract  
강화학습의 이론은 환경 내에서 에이전트가 어떻게 최적의 컨트롤을 할 수 있는지를 심리학적, 신경과학적인 관점에서 설명한다.  
그러나 강화학습을 실제 환경만큼의 복잡도에서 쓰기엔 어려움이 있었다.   
실제 환경은 고차원 sensory input이라서 이걸 효율적으로 표현할 수 있어야 하며, 과거의 경험에서 새 상황으로 일반화해야 한다.  
이를 강화학습과 계층적인 센서 처리 시스템의 조화를 통해 해결한 것으로 보인다.  
이런 조화는 실제 뉴런과 강화학습 간의 유사성을 보여주는 다양한 신경 데이터에서도 확인할 수도 있다.  

그러나 여전히 강화학습 에이전트는 feature를 수동으로 다룰 수 있거나, 저차원이고 모든 상태를 완전히 알 수 있는 그런 도메인에서만 적용이 한정되었다.  
여기서 우리는 최근 큰 발전을 이룬 DNN을 사용하여 deep-Q-network이라 불리는 인공 agent가 고차원 입력을 직접 받아서 정책을 학습할 수 있게 했다.  
이 agent를 Atari 2600이라는 게임이라는 꽤 challenging한 환경에서 테스트했다.  
이 deep-Q-network agent를 사용하여 게임 픽셀과 점수만을 input으로 받고 기존의 모든 알고리즘의 성능을 뛰어넘었다.  
게다가 동일한 알고리즘, 뉴럴 네트워크, 하이퍼파라미터를 사용하여 49개의 다양한 게임에서 인간 프로 선수들의 수준과 비슷한 수준의 성능을 달성하였다.  

우리는 다양한 부류의 어려운 작업에서도 경쟁력을 갖춘 알고리즘을 만들고자 했다. 이는 일반적인 인공지능의 주요 목표이며, 아직 성공하지 못했다.  
이를 위해 강화학습과 DNN으로 알려진 인공 신경망을 결합한 DQN이라는 새 agent를 개발했다.  
데이터의 점점 추상적인 표현들을 학습하도록 사용된 노드의 층이 이용된 최근 DNN의 발전은 인공신경망이 raw sensory data로부터 바로 object category와 같은 개념을 학습할 수 있게 되었다.  
우리는 인간 수용장의 구조를 모방하기 위해 겹쳐진 convolutional 필터를 계층적으로 쌓은 층 사용한 deep convolutional network 구조를 사용한다.  
이로 인해 이미지에 나타나는 local spatial correlation을 추출하고 관점, 크기의변화와 같은 자연스러운 변화에 대한 강건성도 얻을 수 있다.  

우리는 agent가 관찰, 행동, 보상의 과정을 통해 환경과 상호작용하는 것을 고려한다.  
agent의 목표는 미래의 reward를 최대화하는 방향으로 action을 선택하는 것이다.  
정확히는 optimal한 action-value function을 근사하기 위해 deep CNN을 이용한다.![image](https://github.com/user-attachments/assets/f92cc330-19c7-4879-8fc5-91df9f729d3a)  
action-value function을 표현할 때 neural network와 같은 비선형 함수가 approximator로 쓰이면 강화학습은 발산하거나 불안정하다.  
여기에는 여러 이유가 있다.  


  
