# 서포트 벡터 머신

다목적 머신러닝 모델

- 매우 강력해 선형이나 비선형 분류, 회귀, 특이치 탐지에 사용 가능
- 중소규모의 비선형 데이터셋, 특히 분류 작업에 특화
- 매우 큰 데이터셋으로는 잘 확장되지 않음

## 1. 선형 SVM 분류

- SVM 분류기를 클래스 사이에 가장 폭이 넓은 도로를 찾는 것 → 라지 마진 분류(large margin classification)
- 도로 경계에 위치한 샘플에 의해 전적으로 결정 → 이러한 샘플을 서포트 벡터라고 부름
- 특성의 스케일에 민감 → 크기가 작은 특성은 무시하는 경향이 있음

### 소프트 마진 분류

- 하드 마진 분류(hard margin classificatioin): 모든 샘플이 도로 바깥쪽에 올바르게 분류되어 있음
    - 데이터가 선형적으로 구분될 수 있어야 제대로 작동하며 이상치에 민감
        
        → 도로의 폭을 가능한 한 넓게 유지하는 것과 마진 오류(margin violation, 샘플이 도로 중간이나 심지어 반대쪽에 있는 경우)사이의 적절한 균형 필요
        
        ⇒ 소프트 마진 분류 
        
- 사이킷런이 SVM 모델을 만들 때 규제 하이퍼파라미터 C를 포함해 하이퍼파라미터 지정 가능
    - 낮게 설정하면 도로가 더 커지고 더 많은 마진 오류 발생
        
        → C를 줄이면 도로를 지지하는 샘플이 많아져 과대적합의 위험이 줄어듦
        
- 로지스틱 회귀와 달리 클래스 확률을 추정하는 predict_proba() 메서드가 없으므로

## 2. 비선형 SVM 분류

다항 특성과 같은 특성을 추가해 비선형 데이터셋을 다루기

- 선형적으로 구분될 수 없는 데이터셋을 특성을 추가해 선형적으로 구분되도록 만들 수 있음
- PolynomialFeatures 변환기와 StandardScaler, LinearSVC를 연결한 파이프라인을 사용

### 다항식 커널

- SVM을 사용할 떈 커널 트릭을 사용
    
    실제로는 특성을 추가하지 않으면서 매우 높은 차수의 다항 특성을 많이 추가한 것과 같은 결과를 얻게 해주는 방법
    

### 유사도 특성

각 샘플이 특정 랜드마크와 얼마나 닮았는지 측정하는 유사도 함수로 계산한 특성을 추가하는 방법 

- 랜드마크를 선택하는 방법은 데이터셋에 있는 모든 샘플 위치에 랜드마크를 설정
    
    → 차원이 매우 커져 변환된 훈련 세트가 선형적으로 구분될 가능성이 커짐
    

### 가우스 RBF 커널

커널 트릭을 사용해 유사도 특성을 많이 추가하는 것과 비슷한 결과를 얻음

- 하이퍼파라미터 $\gamma$가 규제 역할을 함

> 다른 커널도 있지만 거의 사용되지 않고 문자열 커널이 가끔 사용됨
> 
> 
> 언제나 선형 커널을 가장 먼저 시도하고 훈련 세트가 너무 크지 않다면 가우스 RBF 커널도 시도해보기
> 

### 계산 복잡도

- LinearSVC 파이썬 클래스가 기반으로 하는 Liblinear 라이브러리
    
    선형 SVM을 위한 최적화된 알고리즘을 구현
    
    - 커널 트릭을 지원하지 않지만 훈련 샘플과 특성 수에 거의 선형적으로 증가
    
    → $O(m*n)$
    
    - 정밀도를 높이면 알고리즘의 수행 시간이 길어짐 → 오차 허용 파라미터 $\epsilon$으로 조정 (사이킷런의 매개변수 tol)
        
        대부분의 분류 문제는 기본값으로도 잘 작동
        
- SVC가 기반으로 하는 libsvm 라이브러리
    
    커널 트릭 알고리즘을 구현
    
    - 훈련 샘플 수가 커지면 매우 느려짐
        
        $O(m^2*n)$과 $O(m^3*n)$ 사이
        
    - 특성 수에 대해서, 특히 희소 특성에 대해서는 잘 확장
        
        0이 아닌 특성의 평균 수에 비례
        
- SGDClassifier 클래스는 라지 마진 분류를 수행하고 규제 파라미터를 조정해 선형 SVM과 유사한 결과를 생성
    - 확률적 경사 하강법(점진적 학습, 적은 메모리 사용)을 사용해 RAM에 맞지 않는 대규모 데이터셋을 훈련가능
        
        → $O(m*n)$
        

![Untitled](https://prod-files-secure.s3.us-west-2.amazonaws.com/4dd252a3-b085-498c-82e5-d9b17ba77910/6a620654-f6e8-4390-85bf-a1a0e09a03d0/Untitled.png)

## 3. SVM 회귀

SVM 회귀는 제한된 마진 오류 안에서 도로 안에 가능한 한 많은 샘플이 들어가도록 학습

- 도로의 폭은 하이퍼파라미터 $\epsilon$으로 조절
    - 마진 안에서는 훈련 샘플이 추가 되어도 모델의 예측에 영향이 없음
    - $\epsilon$을 줄이면 서포트 벡터의 수가 늘어나서 모델이 규제 → $\epsilon$에 민감하지 않다
- 비선형 회귀 작업을 처리할 땐 커널 SVM 모델을 사용
    
    SVR은 SVC의 회귀 버전
    
    - LinearSVR은 필요한 시간이 훈련 세트의 크기에 비례해서 선형적으로 증가
        
        SVR은 훈련 세트가 커지면 매우 느려짐
        

## 4. SVM 이론

마진 오류 횟수를 제한하면서 도로(또는 마진)를 가능한 한 넓게 만드는 가중치 벡터 w와 편향 b를 찾아야 함

- 도로의 너비
    - 도로의 너비를 더 넓히려면 w를 더 작게 만들어야 함
        
        편향 b는 마진의 크기에 영향을 미치지 않고 위치만 이동시킴
        
- 마진오류를 피하기
    - 따라서 결정 함수가 모든 양성 훈련 샘플에서는 1보다 커야 하고 음성 훈련 샘플에서는 -1보다 작아야 함
        
        앞서 말한 제약 조선을 모든 샘플에서 $t^{(i)}(w^Tx^{(i)}+b)≥1$로 표현 가능
        
    - 하드 마진 선형 SVM 분류기의 목적 함수를 제약이 있는 최적화 문제로 표현 가능
        
        $$
        minimize_{w,b} {1 \over 2}w^Tw
        $$
        
- 소프트 마진 분류기의 목적 함수를 구성하기 위해 각 샘플에 대해 슬랙 변수를 도입
    - $\zeta^{(i)}>=0$을 도입
        
        i번째 샘플이 얼마나 마진을 위반할 지 결정
        
    - 마진 오류를 최소화하기 위해 가능한 한 슬랙 변수의 값을 작게 만드는 것과 마진을 크게 하기 위해 $w^Tw*(1/2)$를 가능한 한 작게 만드는 것의 상충
        
        하이퍼파라미터가 두 목표 사이의 트레이드오프를 결정
        
        $$
        minimize_{\omega,b,\zeta} {1 \over 2}w^Tw+C\sum_{i=1}^m\zeta^{(i)}
        $$
        
        [조건] i = 1, 2, …, m일 때 $t^{(i)}(w^Tx^{(i)}+b)≥1-\zeta^{(i)}$이고 $\zeta^{(i)}≥0$
        
- 콰드라틱 프로그래밍(QP) 문제 : 하드 마진과 소프트 마진 문제는 모두 선형적인 제약 조건이 있는 볼록 함수의 이차 최적화 문제
- SVM을 훈련하는 방법
    - QP 솔버를 사용
    - 경사 하강법을 사용하여 힌지 손실 또는 제곱 힌지 손실을 최소화
        - 양성 클래스 샘플 x가 주어졌을 때 결정 함수의 출력 s가 1보다 클 경우 손실은 0
            
            → 도로에서 벗어나 양성 클래스 쪽에 있는 경우
            
        - 음성 클래스 샘플이 주어졌을 때 결정 함수의 출력 s가 -1보다 작거나 같으면 손실이 0
            
            → 도로에서 벗어나 음성 클래스 쪽에 있는 경우
            
            ⇒ 샘플이 마진에서 반대로 멀어질수록 손실이 커짐
            
            - 제곱 힌지를 사용할 경우 이상치에 더 민감하게 반응(손실이 이차식으로 증가)하지만 더 빨리 수련하는 경향이 있음
        - LinearSVC는 제곱된 힌지 손실을 사용하는 반면 SGDClassifier는 힌지 손실을 사용 → loss 하이퍼파라미터를 설정해 손실 선택 가능

## 5. 쌍대 문제

SVM 문제는 원 문제 또는 쌍대 문제 중 어느것을 선택해도 같은 해를 제공

- 선형 SVM 목적 함수의 쌍대 형식
    
    $$
    minimize_\alpha{1 \over 2}\sum_{i=1}^m\sum_{j=1}^m\alpha^{(i)}\alpha^{(j)}t^{(i)}t^{(j)}x^{(i)^T}x^{(j)}-\sum_{i=1}^m\alpha^{(i)}
    $$
    
    [조건] i=1, 2, …, m에 대해서 $\alpha^{(i)}≥0$이고 $\sum_{i=1}^m\alpha^{(i)}t^{(i)}=0$
    
    - 이 식을 최소화하는 벡터 $\hat\alpha$를 찾았다면 원 문제의 식을 최소화하는 $\hat w$과 $\hat b$를 계산 가능
- 쌍대 문제에서 구한 해로 원 문제의 해 계산하기
    
    $$
    \hat w=\sum_{i=1}^m\hat \alpha^{(i)}t^{(i)}x^{(i)}
    
    $$
    
    $$
    \hat b={1 \over n_s}\sum_{i=1}^m(t^{(i)}-\hat w^Tx^{(i)})
    $$
    
    $n_s$는 서포트 벡터의 개수
    
- 훈련 샘플 수가 특성 개수보다 작을 때 원 문제보다 쌍대 문제를 푸는 것이 더 빠름
- 원 문제에서는 적용이 안 되는 커널 트릭이 가능

### 커널 SVM

실제 훈련 샘플을 변환할 필요가 없이 벡터의 점곱을 제곱으로 바꿔 계산하는 것

- 커널: 변환 $\phi$를 계산하지 않고 원래 벡터 a와 b에 기반하여 $\phi(a)^T\phi(b)$를 계산할 수 있는 함수
- 2차 다항식 커널: $K(a,b)=(a^Tb)^2$
- 예시
    
    2차원 데이터셋에 2차 다항식 변환을 적용하고 선형 SVM 분류기를 변환된 훈련 세트에 적용할 때
    
    적용하고자 하는 다항식 매핑 함수 $\phi$
    
    $$
    \phi(x)=\phi(\begin{pmatrix}x_1\\x_2 \end{pmatrix})=\begin{pmatrix} x_i^2 \\ \sqrt{2}x_1x_2 \\ x_2^2 \end{pmatrix}
    $$
    
    변환된 벡터는 3차원이고 두 개의 2차원 벡터 a와 b에 2차 다항식 매핑을 적용한 다음 변환된 벡터로 점곱을 할 경우
    
    $$
    \phi(a)^T\phi(b)=
    \begin{pmatrix} a_1^2 \\ \sqrt{2}a_1a_2 \\ a_2^2 \end{pmatrix}^T
    \begin{pmatrix} b_1^2 \\ \sqrt{2}b_1b_2 \\ b_2^2 \end{pmatrix}
    \\=a_1^2b_1^2+2a_1b_1a_2b_2+a_2^2b_2^2
    \\=(a_1b_1+a_2b_2)^2
    \\=(\begin{pmatrix} a_1\\ a_2 \end{pmatrix}^T \begin{pmatrix} b_1\\ b_2 \end{pmatrix})^2=(a^Tb)^2
    $$
    
    변환된 벡터의 점곱이 원래 벡터의 점곱과 같음
    
    $$
    \phi(a)^T\phi(b)=(a^Tb)^2
    $$
    
    → 실제로 훈련 샘플을 어렵게 변환해서 선형 SVM 알고리즘을 적용하는 것과 같음
    
- 일반적인 커널
    - 선형: $K(a,b)=a^Tb$
    - 다항식: $K(a,b)=(\gamma a^Tb+r)^d$
    - 가우스 RBF: $K(a,b)=exp(-\gamma \lVert a-b \rVert^2)$
    - 시그모이드: $K(a,b)=tanh(\gamma a^Tb+r)$
- 커널 트릭을 사용할 경우 예측 식에 $\phi(x^{(i)})$를 포함해야 하는데,
    
    $\hat w$의 차원이 매우 크러나 무한한 $\phi (x^{(i)})$의 차원과 같아져야 하므로 계산 불가능
    
    → $\hat w$에 대한 식을 새로운 샘플 $x^{(n)}$의 결정 함수에 적용해서 입력 벡터 간의 점곱만으로 된 식을 얻을 수 있음
    
    - 커널 SVM으로 예측하기
        
        $$
        h_{\hat w \hat b}(\phi(x^{(n)}))=\hat w^T\phi(x^{(n)})+\hat b
        \\ =(\sum_{i=1}^m\hat \alpha^{(i)}t^{(i)}\phi(x^{(i)}))^T\phi(x^{(n)})+\hat b
        \\ =\sum_{i=1}^m\hat \alpha^{(i)}t^{(i)}(\phi(x^{(i)})^T\phi(x^{(n)}))+\hat b
        \\= \sum_{i=1}^m\hat \alpha^{(i)}t^{(i)}K(x^{(i)}, x^{(n)})+\hat b
        $$
        
        서포트 벡터만 $\alpha^{(i)} ≠ 0$이기 때문에 예측을 만들기 위해서는 서포트 벡터와 새 입력 벡터 간의 점곱만 계산하면 됨
        
        - 편향 $\hat b$도 커널 트릭을 사용해 계산
            
            $$
            \hat b = {1 \over n_s}\sum_{i=1}^m(t^{(i)}-\hat w^T\phi(x^{(i)}))
            \\={1 \over n_s}\sum_{i=1}^m(t^{(i)}-\sum_{j=1}^m\hat \alpha^{(j)}t^{(j)}K(x^{(i)},x^{(j)}))
            $$
            

## 연습 문제

1. 다목적 머신러닝 모델
    
    서포트 벡터 머신의 근본적인 아이디어는 클래스 사이에 가능한 한 가장 넓은 도로를 내는 것으로 두 클래스를 구분하는 결정 경계와 샘플 사이의 마진을 가장 크게 하는 것이 목적
    
    소프트 마진 분류를 수행할 때는 SVM이 두 클래스를 완벽하게 나누는 것과 가장 넓은 도로를 만드는 것 사이에 절충안을 찾음
    
    비선형 데이터셋에서 훈련할 때 커널 함수를 사용
    
    선형 및 비선형 회귀와 특이치 탑지도 수행 가능
    
2. 도로 경계에 위치한 샘플들
    
    SVM이 훈련된 후에 경계를 포함해 도로에 놓인 어떤 샘플
    
    결정 경계는 서포트 벡터에 의해 결정되고 서포트 벡터가 아닌 샘플들은 영향을 하나도 주지 못함
    
    커널 SVM으로 예측을 계산할 때는 전체 훈련 세트가 아니라 서포트 벡터만을 이용
    
3. SVM은 클래스 사이에 가능한 한 가장 큰 도로를 내는 것을 목표로 하므로 훈련 세트의 스케일이 맞지 않으면 크기가 작은 스케일을 무시하는 경향이 있음
4. decision_function을 통해 각 샘플과 결정 경계 사이의 거리를 출력할 수 있음
    
    이를 클래스 확률의 추정치로 직접 변환할 수는 없지만 SVC를 만들 때 probability=True로 지정하면 훈련이 끝날 때 5-폴드 교차 검증을 사용해 훈련 샘플에 대한 표본 외(out-og-sample) 점수를 생성하고 이 점수를 추정 확률에 매핑하기 위해 로지스틱 회귀 모델을 훈련 
    
    그 후 predict_proba() 및 predict_log_proba() 메서드를 사용 가능
    
5. LinearSVC는 선형 SVM 분류가 필요할 때 사용
    
    SVC는 비선형 SVM 분류 중에서 다항 특성을 추가해 다항식 커널을 사용할 때 사용
    
    SGDClassifier 라지 마진 분류를 사용하지만 규제 파라미터를 사용해 조정 가능
    
    세 클래스 모두 라지 마진의 선형 분류에 사용 가능하고 SVC 클래스는 커널 트릭을 지원해 비선형 작업도 처리 가능 
    
    SVC 클래스는 많은 샘플을 가진 데이터셋으로는 잘 확장되지 않지만 많은 개수의 특성으로는 잘 확장
    
    LinearSVC 클래스는 선형 SVM에 최적화된 알고리즘을 구현하지만 SGDClassifier는 확률적 경사 하강법을 사용 
    
    데이터 셋에 따라 LinearSVC가 SGDClassifier보다 약간 빠를 수 있지만 항상 그렇지는 않으며, SGDClassifier가 더 유연하고 점진적 학습을 지원
    
6. gamma와 C를 증가시켜 규제를 완화해 훈련 세트에 더 적합한 모델을 만들도록 해야 함
7. 회귀 SVM 모델은 작은 마진 내에 최대한 많은 샘플을 예측하도록 훈련
    
    마진 내에서 샘플을 추가하면 모델에 전혀 영향을 주지 않는데 이를 오차 허용 범위에 민감하지 않다고 표현
    
8. 커널 트릭은 비교적 계산이 간단해 빠르지만 비선형 변환을 사용하여 입력을 다른 공간에 매핑한 다음 이렇게 매핑된 고차원 입력에서 선형 SVM을 훈련하는 것과 똑같은 결과를 도출
    
    입력을 전혀 변환하지 않고도 동일한 결과를 제공