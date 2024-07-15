# 연습문제 #4

## 데이터 가져오기

- 햄과 스팸 파일로 분류 되어 있는 데이터 세트 가져오기
- ham_filenames, spam_filenames: 리스트에 이메일의 경로를 담은 PosixPath가 저장되어 있는 형태
- ham_emails, spam_emails: ham_filenames와 spam_filenames에 있는 이메일 내용 모두 추출
    - .get_content()를 통해 이메일 내용을 가져올 수 있음

## 데이터 분석하기

- 이메일 구성 형태 살펴보기
    
    이메일은 이미지나 첨부파일을 가진 multipart로 구성되어있을 수 있음
    
    - 햄 이메일들은 plain text 형태가 대부분을 차지하고 스팸 이메일은 html과 plain text가 차지하는 비율이 비슷
    - pgp-signature가 포함된 형태는 햄 이메일에만 존재
- 이메일 헤더 살펴보기
    - 메일에 대한 다양한 정보를 답고 있음

## 훈련 세트와 테스트 세트로 분리하기

- 분리하기 전
    - 분리되어 있는 ham과 spam 이메일을 object 타입으로 합쳐 array 형태로 바꿔주시
    - ham 이메일은 0으로 spam 이메일은 1로 표시하는 label array 만들어주기
- sklearn의 train_test_split 함수를 이용해 10%의 테스트 세트를 분리하기
- 훈련 세트에 들어있는 이메일 중 html로만 이루어진 이메일을 추출해 html을 plain text로 변환하기
    
    → 이메일의 포맷 상관없이 이메일을 모두 일반 텍스트로 바꿔야 함
    

## 변환 파이프라인 작성

- 이메일을 모두 단어 카운트로 변환하는 변환기
    
    이메일을 모두 텍스트로 변환 후 원형으로 바꾼 단어들을 카운트 해 dictionary에 저장
    
- 단어 카운트를 벡터로 변환하는 변환기
    
    자주 나타나는 단어 순으로 정렬된 언어 목록을 생성해 각 단어에 해당하는 카운트를 나타내는 벡터를 생성
    

## 전체 데이터셋 변환

- 위 두 변환기를 모두 하나의 파이프라인으로 연결
- 로지스틱 회귀 모델을 사용해 예측기를 훈련

## 정확도 평가하기

- 정밀도와 재현율을 계산해 정확도 계산