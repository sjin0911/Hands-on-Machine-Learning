# 연습문제 #3

## 데이터 다운로드 받기

- kaggle에 있는 데이터를 바로 다운로드 받을 수 있는 방법으로 데이터 가져옥

### 데이터 분석

- titanic
    
    이미 test set와 train set가 분리되어 있는 데이터
    
    - train
        - 총 12개의 특성과 891개의 데이터를 가지고 있음
        - 5개의 범주형 특성과 7개의 숫자형 특성으로 이루어져있음
        
        → Survived 열을 타깃 열로 함
        
    - test
- 특성
    - Pclass: int, 1,2,3으로 이루어진 Ticket class
    - Name: object, 승객 이름
    - Sex: object, 승객 성별, 1은 남자 0은 여자
    - Age: float, 승객 나이
    - SilSp: int, 함께 탑승한 가족(형제자매, 배우자) 수
    - Parch: int, 함께 탑승한 가족(부모, 자식) 수
    - Ticket: object, 티켓 이름
    - Fare: float, 승객 요금
    - Cabin: object, 객실 넘버
    - Embarked: object, 탑승한 장소 (C=Cherbourg, Q= QUeenstrown, S=Southampton)
- PassengerId는 고유한 숫자이기 때문에 이 열을 인덱스 열로 설정

## 파이프라인

- train set의 타깃 열(’Survived’ 열)을 label로 분리
- 특성을 분리
    - num_missing_attirbs: 값이 누락되었으면서 int나 float형인 특성들
        
        SimpleImputer를 통해 누락된 값을 중앙값으로 채움
        
        StandardScaler로 값을 스케일
        
    - cat_attirbs_to_num: object형 특성 중 숫자로 변환시킬 특성들
        
        OrdinalEncoder를 통해 문자형 데이터를 숫자형 데이터로 변환
        
        SimpleImputer를 통해 누락된 값을 가장 많이 등장하는 값으로 채움
        
        OnHotEncoder를 통해 범주형 데이터를 사용하기 편하게 변환
        
    - cat_attribs_orig: 누락된 값을 채우거나 타입 변환이 필요없는 특성들
- train_set을 만든 파이프라인으로 사용 가능하게 변환

## 훈련

- RandomForestClassifier로 훈련
    - n_estimaotrs=100, random_state=42로 설정
- SVC로 훈련

## 평가

- cross_val_score를 통해 만든 분류기의 성능 평가
- 각 모델에 대한 모든 10개의 점수를 하위 및 상위 사분위수를 강조하는 박스 플롯과 점수의 범위 그리기

## 여러 가지 방법 시도해보기

- 숫자형 특성을 범주형 특성으로 변환: 범주형 특성으로 변환해 특성간 영향을 눈에 띄게 확인 가능
- 특성 합치기: 개별로는 큰 의미를 가지지 않는 특성을 합쳐 유용한 특성으로 변환시키기