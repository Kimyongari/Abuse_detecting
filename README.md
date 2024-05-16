LCK 채팅 데이터 욕설탐지 딥러닝 모델

### 1. 전처리

    a. @로 시작하는 채팅들을 Null로 설정 (봇 제거)
   
    b. Null들을 제거
   
    c. :로 시작하는 이모티콘들 제거
   
    d. 메세지에서 한국어와 영어만 남긴다.
   
    e. 텍스트에서 겹친 글자를 줄인다. (ㅋㅋㅋㅋㅋ > ㅋ)
   
    f. 텍스트를 초성으로 바꾼다.
   
    g. 바꾼 텍스트에서 겹모음을 바꾼다.
    
### 2. fasttest 모델을 비지도학습하여 임베딩 모델을 구축

    a. 초성을 n = 5 로 설정한 n_gram으로 쪼개어 bow를 만든다.
    
    b. bow를 바탕으로 fasttext 모델을 비지도 학습하여 임베딩 모델을 훈련시킨다. 
    
    ex ) ::green_smiling::faker는 신이다. 
     를 바꾸면,  faker akerㄴ kerㄴㅡ / erㄴㅡㄴ / rㄴㅡㄴㅅ / ㄴㅡㄴㅅㅣ / ㅡㄴㅅㅣㄴ / ㄴㅅㅣㄴㅇ /
     ㅅㅣㄴㅇㅣ / ㅣㄴㅇㅣ- / ㄴㅇㅣ-ㄷ / ㅇㅣ-ㄷ
     ㅏ / ㅣ-ㄷㅏ-
### 3. 전처리 된 데이터를 설정한 단어길이에 맞게 벡터로 변환

    a. 단어 길이를 설정하고, 데이터가 단어 길이보다 작으면 0으로 채우고, 초과하면 설정한 단어 길이만큼만 벡터로 변환한다.
    
    b. 데이터를 훈련 데이터로 바꿀 수 있도록 함수화 하였음.
    
### 4. 훈련

    a. 1DCNN, GRU, BiDirectionalLSTM 모델을 활용
    
    b. GridSearchCV를 활용하여 모델의 Dropout 비율과 epochs 비율에 대한 최적의 하이퍼 파라미터를 찾음.
    
    c. 찾은 하이퍼파라미터를 바탕으로 모델을 훈련.
    
    d. 각 모델의 성능을 출력한다.
    
    e. 세 모델을 softvoting 방식으로 앙상블 한 모델을 만들고, 결과를 반환.
    
#### 1DCNN model
Precision: 0.89409

Recall: 1.00000

F1 Score: 0.94408

Accuracy : 0.84180

ROC AUC Score: 0.61947


#### GRU model
Precision: 0.92468

Recall: 0.98072

F1 Score: 0.95187

Accuracy : 0.80523

ROC AUC Score: 0.73372

#### BiDirectional LSTM model
Precision: 0.93127

Recall: 0.95179

F1 Score: 0.94142

Accuracy : 0.78084

ROC AUC Score: 0.75023

#### ensembeld_model
Precision: 0.91487

Recall: 0.99174

F1 Score: 0.95175

Accuracy : 0.82003

ROC AUC Score: 0.69941

### 문제점들
1. 데이터 라벨링이 쉽지 않음. > 욕설 데이터 생성을 고려중.
2. 욕설 채팅과 정상 채팅의 비중이 5:5가 아니라서 accuracy가 높아져도 모델의 성능이 높다고 볼 수 없음 (ROC AUC score가 낮음)
3. GridSearchCV로 최적의 하이퍼파라미터를 찾는 과정은 너무 많은 컴퓨팅파워를 요구함.
(괜찮은 해결책이 있다면 kimybj123@naver.com로 알려주시면 정말 감사하겠습니다.)




