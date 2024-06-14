# 분류

> 사용하는 데이터: 고등학생과 미국 인구 조사국 직원들이 손으로 쓴 70,000개의 작은 숫자 이미지
> 

## 1. MNIST

- MNIST 데이터셋 내려받기
    - sklearn.datasets 패키지에 있는 함수
        1. 실전 데이터셋을 다운로드하기 위한 fetch_* 함수: fetch_openml()(→ 입력을 판다스 데이터프레임, 레이블을 판다스 시리즈로 반환)
        2. 사이킷런에 번들로 포함된 소규모 데이터셋을 로드하기 위한 load_* 함수
        3. 테스트에 유용한 가짜 데이터셋을 생성하기 위한 make_* 함수
        
        → 넘파이 배열이고 입력과 타깃 데이터를 담은 (X,y) 튜플로 반환 (sklearn.utils.Bunch 객체로 반환될 경우 DESCR, data, target 등 사용 가능)
        
- 총 70,000개의 이미지(28x28 특성)에 784개의 특성이 있고 각 특성은 0부터 255 (흰색부터 검은색)까지의 픽셀 강도를 나타냄
- 데이터를 자세히 조사하기 전 항상 테스트 세트를 분리해 놓아야 함 → fetch_openml()은 이미 훈련 세트(앞쪽 60,000개 이미지)와 테스트 세트(뒤쪽 10,000개 이미지)로 나뉘어있음
    - 훈련 세트는 이미 섞여 있어 모든 교차 검증 폴드를 비슷하게 만듦
    - 어떤 학습 알고리즘은 훈련 샘플 순서에 민감하고 비슷한 샘플이 연이어 나타나면 성능이 나빠지기 때문에 데이터셋을 섞어주어야 함

## 2. 이진 분류기 훈련

- 이진 분류기: 두 개의 클래스를 구분할 수 있음
    - 하나의 숫자 5만 분류 → ‘5-감지기’는 ‘5’와 ‘5 아님’ 두 개의 클래스를 구분
- 확률적 경사 하강법 (Stochastic Gardient Descent) 분류기: 매우 큰 데이터셋을 효율적으로 처리하고 한 번에 하나씩 훈련 샘플을 독립적으로 처리

## 3. 성능 측정

- 다양한 정확도 측정 방법
    - 교차 검증
        
        cross_val_score() 함수로 폴드가 3개인 k-폴드 교차 검증 사용
        
        - 교차 검증을 구현해야하는 경우
            
            ```python
            from sklearn.model_selection import StratifiedKFold
            from sklearn.base import clone
            
            skfolds=StratifiedKFold(n_splits=3) #데이터셋이 섞여 있지 않을 경우 shuffle=True 사용
            
            for train_index, test_index in skfolds.split(X_train, y_train_5):
            	clone_clf=clone(sgd_clf)
            	X_train_folds=X_train[train_index]
            	y_train_folds=y_train_5[train_index]
            	X_test_fold=X_train[test_index]
            	y_test_fold=y_train_5[test_index]
            	clone_clf.fit(X_train_folds, y_train_folds)
            	y_pred=clone_clf.predict(X_test_fold)
            	n_correct=sum(y_pred==y_test_fold)
            	print(n_correct/len(y_pred))
            ```
            
            - 클래별 비율이 유지되도록 폴드를 만들기 위해 계층적 샘플링을 수행
            - 매 반복에서 분류기 객체를 복제해 훈련 폴드로 훈련시키고 테스트 폴드로 예측을 만듦
            - 올바른 예측의 수를 세어 정확한 예측의 비율을 출력
        - 모든 이미지를 가장 많이 등장하는 클래스(음성 클래스)로 분류하는 더미 분류기로도 정확도 측정 가능
        
        → 특히 불균형한 데이터셋을 다룰 경우, 분류기의 성능 측정 지표로 정확도를 선호하지 않음 
        
    - 오차 행렬
        - 모든 A/B 쌍에 대해 클래스 A의 샘플이 클래스 B로 분류된 횟수를 세는 것
            
            분류기가 숫자 8의 이미지를 0으로 잘못 분류한 횟수를 오차 행렬의 8번 행 0번 열에 저장
            
        - 오차 행렬 만들기
            
            실제 타깃과 비교할 수 있도록 예측 값을 만듦
            
            → 테스트 세트를 사용하는 대신 cross_val_predict() 함수를 사용
            
            - cross_val_predict(): k-폴드 교차 검증을 수행하지만 평가 점수 대신 각 테스트 폴드에서 얻은 예측을 반환
                
                훈련 세트의 모든 샘플에 대한 깨끗한(훈련에 사용되지 않은 데이터에 대한) 예측을 얻음
                
            
            confusion_matrix() 함수를 사용해 오차 행렬 생성
            
        - 행은 실제 클래스를 나타내고 열은 예측한 클래스를 나타냄
            - 좋은 분류기일수록 주대각선의 값이 큼 → 진짜 양성과 진짜 음성 값
    - 정밀도(양성 예측의 정확도)
        
        $$
        정밀도=TP/(TP+FP)
        $$
        
        - TP: True Positive, 진짜 양성
        - FP: False Positive, 가짜 양성
        - 가장 간단한 방법
            - 제일 확신이 높은 샘플에 대해 양성 예측을 하고 나머지는 모두 음성 예측을 하는 분류기를 만들기 → 양성 예측이 맞을 경우 분류기의 정확도는 100%
        - 재현율(recall): 분류기가 정확하게 감지한 양성 샘플의 비율, 민감도(sensitivity) 또는 진짜 양성 비율(true positive rate, TPR)
            
            $$
            재현율=TP/(TP+FN)
            
            $$
            
            - FN: False Negative, 거짓 음성
- 정밀도와 재현율
    - 사이킷런은 정밀도와 재현율을 포함해 분류기의 지표를 계산하는 함수 제공
    - F1 점수: 정밀도와 재현율의 조화 평균(낮은 값에 훨씬 더 높은 비중을 두는 평균)
        
        $$
        F_1 = 2/(1/정밀도 + 1/재현율) = 2*(정밀도 * 재현율)/(정밀도 + 재현율) = TP/(TP+(FN+FP)/2)
        $$
        
        → F1 점수는 정밀도와 재현율이 비슷해야 높은 점수를 가짐
        
    - 정밀도/재현율 트레이드오프: 정밀도를 올리면 재현율이 줄고 재현율을 올리면 정밀도가 주는 현상

## 4. 정밀도/재현율 트레이드오프

SGDClassifier 분류기의 작동을 통해 정밀도/재현율 트레이드오프를 이해하기

- 결정 함수(decision function)를 사용해 각 샘플의 점수를 계산
    - 점수가 임곗값보다 크면 샘플을 양성 클래스에 할당하고 그렇지 않으면 음성 클래스에 할당
    - 결정 임곗값(decision threshold)