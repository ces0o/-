### Abstract
* 레이어가 깊어질수록 학습하는 것이 어려움
* Residual Learning Framework를 통해 모델의 깊이가 깊어져도 정확도 상승
* 152개의 Layer를 쌓았음에도 VGG보다 복잡도가 낮았고 ImageNet Dataset에 대해 3.57% Error를 달성
### 1. Introduction  
딥러닝에서 neural networks가 깊어질수록 성능은 더 좋아지지만 train이 어렵다는 한계가 있음  
두 가지 문제점  
1. Convergence Problem (수렴 문제)
2. Degradation Problem (기울기 문제)

위와 같은 문제를 해결하기 위해 이 논문은 residual를 이용한 잔차학습(residual learning framework)를 이용해 깊은 신경망에서도 training이 쉽게 이루어질 수 있음을 증명  

<img width="381" alt="image" src="https://github.com/ces0o/Paper-Review/assets/127365253/89c3bbde-a1c8-440f-a44a-33e8b9ba574a">
 
  
Resnet은 수학적으로 어려운 개념이 적용되었다기보다는 방법론적으로 신박한 개념이 도입됨. 이것이 Residual이라는 개념  

<img width="344" alt="image" src="https://github.com/ces0o/Paper-Review/assets/127365253/1c820fec-3ba7-4597-9e63-9ef0d92c4e61">

 ### 2. Residual Learning  

<img width="384" alt="image" src="https://github.com/ces0o/Paper-Review/assets/127365253/00cfd536-79c8-4bec-bef9-9c6f1f9816a7">
 

VGG 네트워크는 작은 크기의 3x3 컨볼루션 필터(filter)를 이용해 레이어의 깊이를 늘려 우수한 성능을 보임  
하지만 단순히 레이어만 깊게한다고 해서 성능이 좋아지는 건 아님  
parameter의 수가 여전히 많았기 때문에 이를 해결하기 위한 다른 접근법 필요   


<img width="372" alt="image" src="https://github.com/ces0o/Paper-Review/assets/127365253/02a53eaa-ed87-4f63-a7cd-b3db2eb0c1e6">
  

* x : 입력값  

잔여블록(residual block)을 이용해 네트워크의 최적화(optimization)난이도를 낮춤
* 실제로 내재한 mapping인 H(x)를 곧바로 학습하는 것은 어려우므로 대신 F(x) = H(x) - x를 학습  
* Mapping을 따로 설정해서 이와 같이 H(x)대신 F(x)를 사용  

일반적 레이어  
*   하나의 입력 -> convolution연산 -> relu -> nonlinear -> 컨볼루션 연산 -> ...
* H(x)가 정답값 y에 정확히 매핑이 되는 함수를 찾는 것을 목적 -> 즉 신경망은 학습을 하면서 H(x) - y의 값을 최소화 시키면서 H(x) = x 가 되는 것을 목표로 함  
* 가중치 값을 개별적으로 학습하기 때문에 수렴 난이도가 높아지는 문제 발생

residual 레이어  
*   쉽게하기 위해 input값을 여러번 거친 값들에 x를 더해주는 것으로  끝남  
*   앞선 레이어에서 학습된 정보는 그대로 가져옴  
*   추가적으로 잔여 정보인 F(x)를 더해주면 끝나서 비교적 단순  
*  기존 신경망이 H(x)-x=0을 만들려 했다면 ResNet은 H(x)-x=F(x)로 두어 F(x)를 최소화 시키는게 목적 -> 즉 F(x)=0이라는 목표를 두고 학습 시작, 이렇게 되면 F(x)라는 목표값이 주어지기 때문에 학습이 더 쉬워짐  
수식적으로 접근   

<img width="356" alt="image" src="https://github.com/ces0o/Paper-Review/assets/127365253/559edb1a-09a2-482d-9774-3c8fccbf4d2c">
 

F함수를 다음과 같이 정의  
first weight 값을 곱하도록 만들고 activation function, 여기서는 relu를 사용해서 그 값을 씌워주고 그 다음값을 이어서 곱해주는 형식  
추가적으로 x가 더해지는 것을 확인할 수 있음  
F값을 정할 때 weight를 단순히 2번만 쓰는게 아니라 더 많은 값을 사용할 수 있기 때문에 이러한 식으로 표현  
따라서 multiple convolution layers라 정의  
shortcut을 이용해 기존의 입력 값을 그대로 가져와서 그 결과에다가 더해줄 수 있도록 만든다

<img width="270" alt="image" src="https://github.com/ces0o/Paper-Review/assets/127365253/fe3c97a7-1856-4a9d-8d68-fd34ae5acd0d">

residual이 성능이 더 좋고,  residual 중에서도 깊이가 깊은 net이 성능이 더 좋음  
### 4. 정리
resnet


*   기존의 VGG 네트워크보다 더 깊지만 residual block을 활용해 복잡도와 성능 개선
*   구현이 매우 간단하며, 학습 난이도가 매우 낮아짐  
*   깊이가 깊어질수록 높은 정확도 향상을 보임  
