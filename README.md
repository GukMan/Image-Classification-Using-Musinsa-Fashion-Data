
# Image Classification Project


### ****📅**** 프로젝트 기간

22.08.08 ~ 22.08.19

### ****📔**** 프로젝트 주제

무신사 패션 데이터를 활용한 multi-label 이미지 분류

### ****📔**** 프로젝트 목적
- 계절별 약 1000개의 이미지가 유입되지만, 현재 코디팀이 직접 스타일 분류
- 일의 효율성을 위해 이를 자동으로 해주는 auto-labeling의 필요성 제시

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
        - ~로 증강
<img width="846" alt="Screenshot 2022-11-08 at 5 33 45 PM" src="https://user-images.githubusercontent.com/104626180/202327384-5682e8cb-69b1-41d1-a667-de8698842ff8.png">

    - 224*224로 resize
        - 옷 이미지라 작은 이미지여도 성능에 큰 영향을 안 줄것이라 가정
        - 122*122, 300*300으로 변화를 줘봤지만, 224*224 일때의 성능이 제일 좋았음




- Models
    - CNN기반 pretrained 모델 선정
        - ResNet50, VGG16, EfficientNetB7
- 각 라벨의 best model
    - 스타일
        - VGG16
            - node 수를 감소했을때(256->128) 성능 증가
                - why ? 좀 더 flexible해서 data에 대해 overfit (model 복잡도 어쩌고) 이므로 node수를 줄이면 overfit도 감소(다시 정리)
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

### 🙌🏻 보완점

- 더 많은 epoch을 사용해서 train 해보기
- 데이터 획일화 방지를 위한 variation 부여
- adam 말고 다른 최신의 optimizer 사용
- 계절 라벨링에서 피부색을 추출할때 배경을 검은색이 아닌 좀 더 명확한(옷의 색상으로 잘 쓰이지 않는) 색상이나 무늬를 사용하여 성능 비교

### 참고
- [ResNet50 논문](https://arxiv.org/pdf/1512.03385.pdf)
- [VGG16 논문](https://arxiv.org/pdf/1409.1556.pdf%20http://arxiv.org/abs/1409.1556.pdf)
- [EfficientNet 논문](https://arxiv.org/pdf/1905.11946.pdf)
