# 실제 데이터 스스로 사용해보기

- 배운 내용을 바탕으로 캐글의 데이터셋을 활용해 머신러닝 프로젝트를 실행해보기

## 캐글 데이터를 직접 다운로드하지 않고 가져오는 방법

1. 주피터 노트북에서 kaggle을 다운로드하고 kaggle 홈페이지 방문해 내 계정에서 API 다운로드
2. 다운로드 받은 kaggle.json 파일을 user → PC(컴퓨터 명) → .kaggle 폴더로 이동시키기
3. 주피터 노트북에서 kaggle 정보 및 config 확인
4. 캐글에서 희망하는 데이터 API 주소를 복사해 다운로드 
5. 다운로드된 파일 압축해제 후 사용

### 사용하는 데이터에 대한 정보

https://www.kaggle.com/datasets/edumagalhaes/quality-prediction-in-a-mining-process

- 광석 농측물에 존재하는 불순물의 정도를
- 데이터셋의 주어진 정보를 활용해 맨 마지막 열인 % Silica Concentrate 을 예측하기 위해 만들어진 데이터셋
- 각 열에 대한 정보
    
    1: 날짜
    
    2-3: 부양 공장에 투입되기 전에 iron ore pulp를 측정한 값
    
    4-8: 최종 결과물의 품질에 가장 많은 영향을 끼치는 요소 
    
    9-22: 부양 공장에 투입되 불순물 제거가 진행되는 동안의 순차적인 데이터
    
    23-24: 최종적인 iron ore pulp quality 특정에 대한 정보
    
- 최종적인 문제
    - % Silica Concentrate 를 매 분마다 예측 가능한가?
    - 몇 단계를 거쳐야 % Silica Concentrate를 예측할 수 있는가?
    - % Iron Concentrate를 사용하지 않고 % Silica Concentrate를 예측할 수 있는가?
