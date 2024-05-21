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

### 5. self_labeling
    
    a. pre_trained된 모델을 바탕으로 unlabeled data들을 predict하여 욕설일 확률이 큰 데이터들을 추가로 생성.

    b. self_labeled된 데이터를 기존의 labeled 데이터에 추가

### 6. activation_learning 

    a. pre_trained된 모델의 예측값이 0.3~ 0.7 사이, 즉 욕설인지 아닌지 모델이 애매하게 판단한 것들을 따로 추출.

    b. 애매한 데이터들을 모아 내가 직접 라벨링.(iffy data)

    c. 모아진 데이터들을 추가하여 학습 (labeled data, pseudo labeled data, iffy data)
    
#### 1DCNN model
Precision: 0.95820

Recall: 0.99207

F1 Score: 0.97484

Accuracy : 0.86687

ROC AUC Score: 0.77047


#### GRU model
Precision: 0.97827

Recall: 0.94158

F1 Score: 0.95957

Accuracy : 0.81203

ROC AUC Score: 0.86177

#### BiDirectional LSTM model
Precision: 0.97165

Recall: 0.97656

F1 Score: 0.97410

Accuracy : 0.84407

ROC AUC Score: 0.83978

#### soft_ensembled_model
Precision: 0.97084

Recall: 0.98449

F1 Score: 0.97762

Accuracy : 0.85085

ROC AUC Score: 0.83811

#### hard_ensembled_model
Precision: 0.97014

Recall: 0.98413

F1 Score: 0.97709

Accuracy : 0.85112

ROC AUC Score: 0.83417

### 문제점들
1. 데이터 라벨링이 쉽지 않음. > 욕설 데이터 생성을 고려중.
2. 욕설 채팅과 정상 채팅의 비중이 5:5가 아니라서 accuracy가 높아져도 모델의 성능이 높다고 볼 수 없음 (ROC AUC score가 낮음)
3. GridSearchCV로 최적의 하이퍼파라미터를 찾는 과정은 너무 많은 컴퓨팅파워를 요구함.
(괜찮은 해결책이 있다면 kimybj123@naver.com로 알려주시면 정말 감사하겠습니다.)

### 2024/5/20
데이터를 추가할 수록 성능이 좋아지고 있음. 계속 추가 라벨링하고 훈련하며 더 지켜볼 예정.

### 2024/5/21
pseudo labeling 기법을 활용하여 self supervised learning을 적용하니 상당한 성능 향상이 있었다.
또, 확률이 애매한 데이터를 뽑는 것 까진 했지만, 아직 라벨링을 하지 않았음.
라벨링까지 하여 다시 학습하면 성능 향상이 기대됨.


