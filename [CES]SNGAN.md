# SNGAN  
## Abstract  
GAN 학습에 있어 훈련의 불안정성을 해결할 수 있는 새로운 방법 필요  
* 불균형한 학습  
  초기에 판별자와 생성자의 성능 차이가 매우 큰 경우 판별자는 쉽게 가짜와 진짜 데이터를 구별할 수 있다  
  -> 생성자는 거의 어떠한 피드백도 받지 못하고 생성하는 이미지가 제한적일 수 있다
* 모드 붕괴  
  생성자가 제한된 이미지나 패턴만 생성하도록 수렴하는 현상
  -> 판별자가 특정모드에 대해 민감하게 반응하고 나머지 모드에 대해서는 무시할 때 발생
  -> 생성자가 판별자가 약한 모드를 피하기 위해 그 모드에 대해서만 생성하는 이미지를 생성하는 문제가 발생
  
이에 discriminator의 훈련을 안정화시킬 수 있는 spectral normalization을 새롭게 제안  
이를 통해 계산량이 줄어들고 쉽게 적용할 수 있으며 학습의 안정성을 강화시킴  
더 효과적인 성능을 증명하기 위해 여러가지 실험을 토대로 증명함  

## Introduction  
Gan이 학습이 쉽지 않은데, 이 논문에서는 discriminator의 좋은 학습을 위해 stablize한 학습 방법을 제안함  
우리가 학습시키는 parameter의 weight를 normalize하는 방식 -> spectral normalization  
Lipschitz constant를 제약하는 방식으로 진행됨 -> 원래 있던 기존의 방식보다 쉬운 implementaion만으로도 접근이 용이하다는 특징이 있다  

* Lipschitz 함수란?
  <img width="659" alt="image" src="https://github.com/Artinto/2023-2_study/assets/127365253/ff77ba0a-447d-4e8f-bb1e-1734209e5494">  
  어떠한 함수 f:[a,b] -> R 가 어떠한 상수 M과 [a,b]에 속하는 모든 x,y에 대해서 이 식을 만족하게 만든다  
  다시 말해 **두 점 사이의 거리를 일정 비 이상으로 증가시키지 않는 함수** 라고 정의할 수 있다  
  -> f의 미분계수가 M을 넘어가지 않는다는 것으로 해석 가능 
## Method  
GANs is given by  

<img width="123" alt="image" src="https://github.com/Artinto/2023-2_study/assets/127365253/5be589f7-f9c5-4f07-bd6c-c543f3e76fa8">

<img width="493" alt="image" src="https://github.com/Artinto/2023-2_study/assets/127365253/fe3cb635-2846-4a20-a7ba-d2ab002cd302">  


-> derivative  
<img width="312" alt="image" src="https://github.com/Artinto/2023-2_study/assets/127365253/b1b0cce2-7dea-49f9-9680-13456a11b3f6">  
GAN에 있어 최적의 Discriminator는 위와 같이 표현할 수 있다  

$sigmoid(x)=1/1+e^{-x}$ 이기 때문에 f*를 sigmoid에 넣어 풀면 DG*와 같이 가능하다  
하지만 여기서 보듯이 generate 미분값에 대한 어떠한 제약조건이 없는 것을 알 수 있다  
새로운 제약을 걸어주면 더 좋은 학습을 시킬 수 있지 않을까 라는 아이디어 제시  

이 논문이 나오기 전 제약을 걸어주기 위한 새로운 방법들 제기  
* WGAN  
  discriminator가 아무 함수나 학습할 수 없게 Lipschitz constant를 걸어 좋은 함수를 학습시킨다는 아이디어  
  <img width="198" alt="image" src="https://github.com/Artinto/2023-2_study/assets/127365253/73d3c490-3989-4305-aa66-4524c97dae8a">  
  wight clipping  
  <img width="282" alt="image" src="https://github.com/Artinto/2023-2_study/assets/127365253/55d98b47-0ba0-49db-96f7-ed4dd17d960b">  
  각각의 parameter가 어떠한 값 이상으로 넘어가지 않게 강제적으로 막아놓는 방법  
  한계 : 위의 그래프처럼 전체적으로 좋은 함수가 학습된다기 보단 간신히 넘어가려는 함수만 급하게 막는 식으로 밖에 진행이 안됨

* GAN-GP (Gradient Panalty)
  
  <img width="318" alt="image" src="https://github.com/Artinto/2023-2_study/assets/127365253/71a83bce-f82a-499a-bacb-2ad96302d9b5">  
  
  generate의 model의 datasample 하나와 real image의 datasample 하나를 각각 뽑아 그 두 사이의 gradient를 구한다  
  이 하나의 기울기를 regulation에 넣는다  
  -> 실제의 기울기를 넣은 것이기 때문에 위에 설명한 WGAN보단 더 효과적인 학습이 가능함  
  한계 : sample 하나를 뽑아서 학습을 진행하기 때문에 그 sample에 가까운 한정적인 영역안에서는 regulation이 걸리고 나머지의 영역에서는 regulation이 제대로 걸리지 않는 문제가 발생

* Spectral Normalization  
  각 layer의 g의 spectral norm을 제어함으로써 discriminator 함수 f의 Lipschitz constant를 제어하는 것
  
  "Matrix Norm"
  
  <img width="137" alt="image" src="https://github.com/Artinto/2023-2_study/assets/127365253/fbdf60bf-382d-43f8-96d6-212ff85b65cd">  
    
  <img width="272" alt="image" src="https://github.com/Artinto/2023-2_study/assets/127365253/873d326e-df59-4f9c-aebd-a62a23c682da"> 
  
  -> 여기서 p가 2일 때 spectral norm이라고 정의할 수 있고 largistic sigular value값과 일치하는 것을 확인할 수 있다  
    
  g : h(in) -> h(out) by definition

  <img width="265" alt="image" src="https://github.com/Artinto/2023-2_study/assets/127365253/4a3090f5-cf9b-47fa-9851-d1dcdd5aa7c5">  

  g(h) = Wh 즉 linear 함수일 때

  <img width="305" alt="image" src="https://github.com/Artinto/2023-2_study/assets/127365253/d6d7f759-a685-4daa-8927-58521534144b">  

  sigmoid 함수 일 때 출력 0 or 1  
    
  <img width="188" alt="image" src="https://github.com/Artinto/2023-2_study/assets/127365253/bd0bca8b-d962-4f2a-96c4-1c71557e4b88"> 에 의거하여
  다음과 같은 수식이 나올 수 있다

  <img width="409" alt="image" src="https://github.com/Artinto/2023-2_study/assets/127365253/15ca8fbc-b043-421a-907a-fb2b76c7ce32">

  Spectral Noramzliation은 Weight matrix W의 Spectral norm을 정규화하여 <img width="52" alt="image" src="https://github.com/Artinto/2023-2_study/assets/127365253/b3b79efe-094c-4c93-8439-01c8d6b902ba"> 을 만족한다 


