
# Image Classification Project


### ****📅**** 프로젝트 기간

22.08.08 ~ 22.08.19

### ****📔**** 프로젝트 주제

무신사 패션 데이터를 활용한 multi-label 이미지 분류

### ****📔**** 프로젝트 목적
- 계절별 약 1000개의 패션 이미지가 유입되지만, 현재 무신사 내 코디 팀이 직접 카테고리 분류
- 일의 효율성을 위해 패션의 스타일, 계절성, 성별을 분류하는 auto-labeling 서비스의 필요성 제시 

### ****💪****역할

- 국승용 : 데이터 전처리, 모델링, PPT
- 강효진 : 데이터 전처리, 모델링, PPT
- 김규인 : 데이터 전처리, 모델링, PPT
- 여민희 : 데이터 전처리, 모델링, PPT
- 이재혁 : 크롤링, 데이터 전처리, 모델링, PPT

### ****🗄️**** 데이터셋

- 무신사 스타일 카테고리(캐주얼, 댄디, 포멀, 골프, 스포츠) 크롤링
- 적은 데이터셋은 비슷한 카테고리 2개씩 묶음
    - 댄디 + 포멀 = 댄디
    - 골프 + 스포츠 = 스포츠

| 구분 | 스포츠 | 댄디 | 캐주얼 |
| --- | --- | --- | --- |
| 계 | 1112 | 2001 | 2000 |

### ****⚙️ Process****

- Data Preprocessing
    - 성별(남,여)과 계절(봄/가을, 여름, 겨울) 라벨 수작업
    - 불균형한 데이터로 인해 각 라벨별 증강
        - rotation, width_shift, height_shift, shear, zoom을 사용해서 증강
    
    <img width="853" alt="image" src="https://user-images.githubusercontent.com/114709620/203029298-24a06d52-a8c0-4087-9d02-a4974b1c305c.png">


    - 224 * 224로 resize
        - 옷 이미지라 작은 이미지여도 성능에 큰 영향을 안 줄것이라 가정
        - 122 * 122, 300 * 300으로 변화를 줘봤지만, 224 * 224 일때의 성능이 제일 좋았음




- Models
    - CNN기반 pretrained 모델 선정
        - ResNet50, VGG16, EfficientNetB7
- 각 라벨의 best model
    - 스타일
        - VGG16
            - node 수를 감소했을때(256->128) 성능 증가
                - why ? Overfit을 해소하기 위해서 모델과 데이터의 복잡도를 맞춰줘야 한다고 생각
                - VGG16 모델의 경우 우리가 가진 데이터보다 모델의 복잡도가 더 높고 flexible해서 Overfit이 일어난 상태
                - 따라서 node수를 줄여 모델 복잡도를 낮추고, Overfit도 해소
                - 64까지 감소시켰으면 좀 더 나아질 수 있었을 거라 판단
                - 그림 왼쪽(node 128) 그림 오른쪽(node 256)
                ![vgg_1](https://user-images.githubusercontent.com/104626180/202935973-d79e59d3-e83d-4e9c-aaa9-f551568ab576.jpg)
 
      | hidden layer | # of nodes | drop out | epochs | optimizer | learning rate|
      | --- | --- | --- | --- | --- | --- |
      | 1 | 128 | 0.5 | 30 | adam | 0.0001 |

    - 성별
        - VGG16
      
      | hidden layer | # of nodes | drop out | epochs | optimizer | learning rate|
      | --- | --- | --- | --- | --- | --- |
      | 1 | 128 | 0.5 | 20 | adam | 0.0001 |

    - 계절
        - VGG16
            - 계절에 따라 노출도가 달라진다는 점에서 아이디어를 얻어 피부색을 추출해봤지만, 성능에 대한 개선이 없었음

        | hidden layer | # of nodes | drop out | epochs | optimizer | learning rate|
        | --- | --- | --- | --- | --- | --- |
        | 1 | 256 | 0.5 | 50 | adam | 0.00001|

    - 모델에 비해 데이터의 복잡도가 단순해서 VGG16의 성능이 높았을 거라 추측

### 📊 수행 결과

**Input** :

![image](https://user-images.githubusercontent.com/114709620/203035403-b5228896-25dc-415a-b09e-82fc7b0c6569.png)

**Output** :
![chart](https://user-images.githubusercontent.com/104626180/203054974-f27a26b3-0f2e-4856-b8eb-7ea2f8b297ec.png)


⇢ **Prediction** : #캐주얼 #남성 #봄/가을


### 🙌🏻 보완점

- 더 많은 epoch을 사용해서 train 해보기
- adam 이외 최신 optimizer 사용
- 계절 라벨에서 피부색을 추출할때 배경을 검은색이 아닌 좀 더 명확한(옷의 색상으로 잘 쓰이지 않는) 색상이나 무늬를 사용하여 성능 비교
- 무신사 사이트 내 분류뿐만 아니라 일반적인 코디 사진(직접 촬영한 또는 의상 자체만의 사진)의 특징도 분류할 수 있도록 학습 데이터에 다양성 부여 
    - 예) 뒷모습/옆모습, 활동/정적인 포즈, 의상의 일부분, 브랜드 로고 
- 남, 여 공용 라벨 추가 

### 참고
- [ResNet50 논문](https://arxiv.org/pdf/1512.03385.pdf)
- [VGG16 논문](https://arxiv.org/pdf/1409.1556.pdf%20http://arxiv.org/abs/1409.1556.pdf)
- [EfficientNet 논문](https://arxiv.org/pdf/1905.11946.pdf)
