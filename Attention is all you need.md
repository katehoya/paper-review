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
 - 
   
