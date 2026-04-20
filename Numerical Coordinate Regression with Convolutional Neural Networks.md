# [Paper Title]

## Numerical Coordinate Regression with Convolutional Neural Networks

# [Summary]

기존의 수치 좌표 예측 Task 의 문제점은 다음과 같음.
1. FC Layer 방식: 1차원 배열로 바꾸면서 공간적 정보가 없어짐, 과적합에 취약해짐
2. Heatmap + argmax 방식: FCN 으로 히트맵을 주출하여 가장 큰 값의 위치를 argmax 로 구함, 미분 불가능 학습 불가

---------------------------------------

논문에서 제안하는 DSNT(Differentiable Spatial to Numerical Transform) 모듈은 미분 가능, 공간 정보 보존에 우수함.
더불어 훈련 매개변수가 증가하지 않아 정확도, 추론시간 측면 모두에서 우수함.

---------------------------------------

DSNT 의 방식은 아래와 같음.

---------------------------------------

- 사전 설정
<img width="900" height="385" alt="image" src="https://github.com/user-attachments/assets/6d21f813-81b1-49e1-9821-53c9d7a20298" />

---------------------------------------

Step 1. 확률맵 P 추출

특징맵 Z 에서 모든 픽셀값을 0~1 사이의 확률 값으로 변환. 이때 총합이 1이 되도록 Spatial Softmax 적용.
최종 P의 픽셀값은 X, Y 좌표가 존재할 확률을 나타냄.

<img width="340" height="96" alt="image" src="https://github.com/user-attachments/assets/d143994c-650b-4e67-9d9d-f7b83fe5a79e" />

---------------------------------------

Step 2. 좌표 그리드 X, Y 생성

X 그리드 행렬은 행렬 좌측이 -1, 우측이 1로하여 같은 열의 값이 같도록 설계.
Y 그리드 행렬은 행렬 상단이 -1, 하단이 1로하여 같은 행의 값이 같도록 설계.

- 좌표 그리드 생성 조건 수식
<img width="215" height="168" alt="image" src="https://github.com/user-attachments/assets/7d7c95e1-73cb-40cb-84f8-2f7d4486a769" />

- 좌표 그리드 예시
<img width="668" height="465" alt="image" src="https://github.com/user-attachments/assets/2d3b464a-1948-413f-9afc-d30af485e4d2" />

Step 3. 좌표 예측 $\mu$

---------------------------------------

앞서 구한 P 와 X, Y 그리드의 곱을 통해 $\mu$ 값을 각각 x,y 에 대해 구함.
이는 정규화된 좌표값으로 실제 좌표값으로의 변환이 필요.

- 기댓값 연산 수식
<img width="221" height="216" alt="image" src="https://github.com/user-attachments/assets/37c7e491-4b0b-470b-b4f9-d26548e2546d" />

- 예시 연산
<img width="788" height="241" alt="image" src="https://github.com/user-attachments/assets/22375bb7-4b98-4fc2-b597-7ce2ce53b502" />

- 실제 좌표 변환
<img width="421" height="447" alt="image" src="https://github.com/user-attachments/assets/a96ba18b-ddff-47a0-9fff-ff1a21afce61" />

---------------------------------------

Step 4. 손실 함수
1. 실제 좌표랑 모델이 예측한 좌표를 비교하는 유클리디안 거리 계산
2. 예측 값의 분산이 좁은 가우시안 형태가 되도록 설계 (Regularization loss) -> 모델 꼼수 방지
최종 loss = 1 + 2
