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
***
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
 - 우리는 value funation ![image](https://github.com/user-attachments/assets/7215148a-c8e7-46bc-a971-219558bd85ba)의 근사를 deep CNN으로 매개변수화한다.(theta는 가중치, i는 반복 횟수)
 - 여기서 experience replay를 하기 위해 agent의 experience를 dataset에 넣는다. et = (st,at,rt,s(t+1))  -->  D{e1,e2,...et}
 - 그런다음 학습할 때 이 dataset에서 random하게 하나(minibatch )를 뽑아서 Q-learning의 update할 때 사용한다.
 - 각 iteration마다 loss function은 이걸 사용한다. ![image](https://github.com/user-attachments/assets/c3fd5d0c-8d43-4909-a4f0-775265b5d24a)
   여기서 세타i는 각 iteration에서 Q-network에서의 parameter이고 세타i-는 iteration i에서의 target을 계산할 때 쓰이는 parameter이다.
   target network의 parameter인 세타i-는 일정한 time step C마다만 업데이트된다. 그동안은 고정. --> 매 time step마다 target이 변화하는 문제점 해결.

 - 이 DQN agent를 평가하기 위해 인간에게 어려운 다양한 task들을 제공하는 Atari 2600 게임을 이용했다.  
 - 우리는 모두 똑같은 네트워크 구조와, 하이퍼파라미터, 학습 과정(60Hz에서의 210x160의 컬러 비디오라는 고차원 데이터를 바로 input으로 받아들이는 것)을 사용했다. 
   우리의 접근이 아주 적은 사전정보(visual image가 input이라는 것, 게임 내에서 가능한 action의 수. 그러나 각 행동이 어떤 의미를 가지는 지는 알려주지 않았다.)와 감각 입력만으로도 다양한 종류의 게임에서 강건하게 성공적인 정책을 학습한다는 것을 증명하기 위해서.
 - 우리의 방법은 강화학습 신호와 SGD를 이용하여 대규모 신경망을 안정적으로 훈련시킬 수 있었다.

 - 이러한 안정적인 학습은 agent의 'episode당 평균 점수'와 '예상 Q-value의 평균'으로 확인할 수 있었다.
![image](https://github.com/user-attachments/assets/d1f43fec-1463-4dee-b147-214f43c66765)

 - 우리는 DQN을 기존 강화학습 방법 중 Atark 2000에서 가장 성능이 좋은 방법과 비교를 했다.  
 - 추가로 인간 프로게이머의 점수와 랜덤으로 action을 고르는 policy를 조사했다.
 - 우리 DQN방법은 49개 중 43개의 게임에서 다른 method에서 받아들이는 추가적인 사전정보 없이도 최고의 성능을 내었다.
 - 뿐만 아니라 49게임 전체에서 프로게이머와 비등비등한 성능을 내었고 절반 이상의 게임에서는 인간 점수의 75% 이상의 점수를 내었다.
![image](https://github.com/user-attachments/assets/f237dc04-7741-4ee9-a766-5e0b87a63d17)
 - 또한 우리는 추가적인 시뮬레이션에서 (replay memory, target Q-network를 분리시키는 것, Deep CNN과 같은)DQN agent의 주요 요인들을 비활성화시킴으로써 이들의 중요성을 입증하였다.

***
![image](https://github.com/user-attachments/assets/3c81926f-5e08-4c4e-88fd-65c70251ca00)  
 - DQN agent를 2시간 동안 훈련시키고 t-SNE 알고리즘을 DQN의 last hidden layer에 적용했을 때의 그래프이다.  
 - 각 점들은 state value(maximum expected reward of a state)에 따라 dark blue(젤 낮은 V)부터 dark red(젤 높은 V)까지 분포해있다.

 - 적들로 꽉 차 있는 화면(오른쪽 위)와 아예 적들이 없는 화면(왼쪽 아래)에서는 높은 state value를 예측했다.
   왜냐면 적들을 잠재적인 reward로 보기때문에 적이 많으면 많은 reward로 보고, 아예 적이 없어도 새로운 stage의 시작을 의미하기 때문에 높은 state value로써 파악하는 것이다.
(절반만 차 있는 화면보다 아예 빈 화면을 더 높은 state value로 예측하는 게 인상적이다)
 - 또한 orange bunker는 별로 중요하지 않기 때문에 낮은 state value로 파악한다.  
***
 - 그다음 Space Invaders에서 성공적인 성능을 보인 DQN의 representation을 (고차원 데이터 시각화를 하기 위해 개발된)t-SNE기법을 이용하여 분석했다.  
 - 예상대로, t-SNE 알고리즘은 시각적으로 유사한 상태들을 서로 가까운 지점에 배치했다.
 - 흥미로운 것은 t-SNE가 DQN representation을 보고 expected reward 측면에서 비슷하다고 생각해서 비슷한 embedding을 한 것들이 시각적으로 딱 봤을 때는 비슷하지 않은 것들도 있었다.  
   이 것은 네트워크가 고차원 sensort input들로부터 adaptive한 행동을 지원하는 표현을 학습할 수 있다는 걸 말한다.
   (단순한 표면적인 것이 아닌, 실제로 큰 reward를 얻을 수 있는 상황을 구별할 수 있다는 것을 의미한다.)  
 - 나아가, DQN에서 학습한 표현이 자신이 아닌 다른 Policy에 일반화될 수 있다는 것을 보였다.  
   이를 확인하기 위해 시뮬레이션에서 인간과 에이전트가 플레이하는 동안 경험한 게임 상태를 네트워크의 입력으로 제공하였고, 마지막 hidden layer에서의 표현을 기록한 후, t-SNE 알고리즘을 사용하여 시각화하였다.  
 - Extended Data Fig.2는 어떻게 DQN으로부터 학습한 표현이 상태와 action value를 정확히 예측할 수 있는지 보여준다.  
***
![image](https://github.com/user-attachments/assets/1560f27e-8433-41e5-929f-9850faf80967)  
***
 - 주목할만한 점은 DQN이 뛰어난 성능을 보인 게임들의 특징이 매우 다양했다.  
   side-scrolling shooters(River Raid)부터 복싱 게임, 3D car racing 게임까지 매우 다양한 게임에서 뛰어났다.  
 - 실제로 특정 게임에서 DQN은 상대적으로 장기적인 전략을 찾는 것도 성공했다.
 - 그럼에도 불구하고 더 장기적인 계획을 필요로 하는 게임에서는 DQN을 포함한 모든 agent가 여전히 어려움을 겪는다
***
 - 이 연구에서 우리는 단일 신경망 구조가 아주 적은 사전지식으로, input으론 오직 pixel과 game score만 받으며, 동일한 알고리즘 네트워크 하이퍼파라미터를 사용하여 다양한 환경에서 control policy를 잘 학습할 수 있다는 걸 증명했다.
   이는 마치 인간과도 같다.
 - 기존의 선행연구와는 달리, 우리는 end-to-end 강화학습 방식을 적용했다.
   이는 reward를 활용하여 convolutional network에서의 representation이 환경의 중요한 특징을 학습하게 하며
   이를 통해 value estimation이 효과적으로 이루어진다.  
 - 이러한 접근법은 인지 학습 도중의 reward signal이 주요 시각 피질의 표현 특성에 영향을 미칠 수 있다는 신경생물학적인 근거에 기반한다.
 - 또 주목할만한 것은 RL과 deep network 구조의 통합이 우리의 replay algorithm에 주요하게 영향을 받았다는 것이다.
   (replay algorithm은 최근 경험한 전환을 저장하고 이를 반복적으로 활용하는 방식이다.)
 - 흥미롭게도 다양한 연구에서 포유류의 해마가 이와 유사한 메커니즘ㅇ르 수행할 수 있다는 증거가 발견되었다.
   즉 해마는 offline 기간동안 최근 경험한 경험한 경로를 시간 압축하여 재활성화하며, 이를 통해 기저핵과의 상호작용을 통해 value function을 효과적으로 업데이트할 수 있는 가능성이 제시되었다.
 - 미래엔 experience replay를 중요한 event에 편향시키는 방법을 찾는 것이 중요할 것이다.
   이러한 방식은 해마에서 경험적으로 관찰된 replay와도 같으며 RL에서의 '우선 탐색' 개념과도 연괸이 있다.
 - 종합하자면 우리의 연구는 다양한 challenging한 task에서 완벽하게 학습할 수 잇는 agent를 만들기 위하여 최신 머신러닝 기술을 생물학에서 영감을 받은 메커니즘과 결합하는 것의 강력함을 설명하였다.
   
 - 

   
   
   





     
