---
layout: post
title: "[DACON] 해커톤 경진대회 회고"
tags:
  - etc
---

## DACON

- 주제: 뉴스 제목과 키워드를 통한 카테고리 예측
- 평가방식: F1 Score(macro)

### 1. 데이터 파악하기
```py
# csv파일 불러오기
train = pd.read_csv('./train.csv') 
test = pd.read_csv('./test.csv')    

# 분류해야 할 클래스의 데이터 분포 수 확인하기
category_counts = train['분류'].value_counts()
```

<details>
<summary> 데이터 분포수 보기 </summary>

| 분류            | count  |
|---------------|--------|
| 지역            | 26950  |
| 경제:부동산        | 3454   |
| 사회:사건_사고      | 2568   |
| 경제:반도체        | 2318   |
| 사회:사회일반       | 1480   |
| 사회:교육_시험      | 995    |
| 정치:국회_정당      | 966    |
| 사회:의료_건강      | 950    |
| 경제:취업_창업      | 845    |
| 스포츠:올림픽_아시안게임 | 841    |
| 경제:산업_기업      | 711    |
| 문화:전시_공연      | 671    |
| 경제:자동차        | 640    |
| 경제:경제일반       | 625    |
| 사회:장애인        | 621    |
| 스포츠:골프        | 617    |
| 정치:선거         | 608    |
| 경제:유통         | 589    |
| IT_과학:모바일     | 537    |
| 사회:여성         | 536    |
| 사회:노동_복지      | 447    |
| 사회:환경         | 396    |
| 경제:서비스_쇼핑     | 387    |
| 경제:무역         | 375    |
| 정치:행정_자치      | 349    |
| 국제            | 337    |
| 문화:방송_연예      | 335    |
| 스포츠:축구        | 328    |
| 경제:금융_재테크     | 327    |
| 정치:청와대        | 279    |
| 문화:출판         | 248    |
| IT_과학:IT_과학일반 | 243    |
| IT_과학:인터넷_SNS | 238    |
| 문화:미술_건축      | 229    |
| 정치:정치일반       | 221    |
| IT_과학:과학      | 215    |
| 문화:문화일반       | 213    |
| 문화:학술_문화재     | 202    |
| 문화:요리_여행  | 190    |
| 경제:자원     | 178    |
| 문화:종교     | 173    |
| IT_과학:콘텐츠 | 160    |
| 사회:미디어    | 128    |
| 사회:날씨     | 124    |
| 스포츠:농구_배구 | 113    |
| 문화:음악     | 110    |
| 문화:생활     | 102    |
| IT_과학:보안  | 94     |
| 스포츠:월드컵   | 90     |
| 경제:증권_증시  | 76     |
| 정치:북한     | 67     |
| 정치:외교     | 31     |
| 스포츠:스포츠일반 | 29     |
| 문화:영화     | 27     |
| 스포츠:야구    | 17     |
| 경제:외환     | 9      |

</details>

데이터를 확인해본 결과 56000개의 훈련데이터가 있고, 분류해야 할 클래스는 56개이다. 또한 가장 많은 데이터를 가지고 있는 클래스는 27000개의 데이터가 있는 반면 가장 적은 데이터를 가지고 있는 클래스는 9개이다. 

1. F1 score macro을 통해 모델을 평가하기에 적은 샘플의 데이터에도 효과적으로 훈련할 수 있어야 한다.
2. 훈련데이터에서 검증 데이터로 나누는 과정에서 랜덤하게 비율을 나눌 때, 적은 샘플 수를 가진다면 적절하게 나뉠 수 있을까라는 의문이 들었다.

구축하고자 하는 모델의 핵심은 `데이터 불균형 문제를 해결`하는 문제라고 생각했다.

### 2. 데이터 전처리

샘플이 200개 미만인 데이터에 한해 단어 순서를 변경하여 증강을 실시하였다.
```py
import random

class_counts = train['분류'].value_counts()

# 단어 순서를 랜덤하게 바꾸는 함수
def random_swap(text, n):
    words = text.split()
    new_words = words.copy()
    
    for _ in range(n):
        # 무작위로 세 개의 단어 위치를 교환
        idx1, idx2, idx3 = random.choices(range(len(new_words)), k=3)
        new_words[idx1], new_words[idx2], new_words[idx3] = new_words[idx3], new_words[idx1], new_words[idx2]
    
    return ' '.join(new_words)

# 소수 클래스에 대해 데이터 증강 적용
new_data = []
for category, count in class_counts.items():
    category_data = train[train['분류'] == category]
    
    if count < 200:
        # 데이터 증강 적용하여 텍스트 변형
        augmented_texts = []
        augmented_keywords = []
        for i in range(200 - count):  # 200개가 될 때까지 증강
            text = random.choice(category_data['제목'].tolist())          
            augmented_text = random_swap(text, 2)            
            
            keywords = random.choice(category_data['키워드'].tolist())
            augmented_keyword = random_swap(keywords, 2)
            
            augmented_texts.append(augmented_text)
            augmented_keywords.append(augmented_keyword)
        
        # 증강된 데이터를 DataFrame으로 추가
        augmented_df = pd.DataFrame({
            '제목': augmented_texts,
            '키워드': augmented_keywords,
            '분류': [category] * len(augmented_texts)
        })
        category_data_resampled = pd.concat([category_data, augmented_df])
        new_data.append(category_data_resampled)
    else:
        new_data.append(category_data)

# 데이터 결합
balanced_train = pd.concat(new_data)

# 결과 확인
balanced_train['분류'].value_counts(), len(balanced_train['분류'].unique())
```

또한 `지역`이라는 분류에 너무 과적합 되지 않도록 데이터의 개수를 줄이는 작업을 병행했다.

```py
# '지역' 카테고리를 제외한 나머지 데이터
other_categories = balanced_train[balanced_train['분류'] != '지역']
# '지역' 카테고리의 데이터에서 25%만 샘플링
region_category = balanced_train[balanced_train['분류'] == '지역']
region_sample = region_category.sample(frac=0.25, random_state=42)
balanced_train = pd.concat([other_categories, region_sample])
print(balanced_train['분류'].value_counts())
```

중복된 단어와 대부분의 키워드가 1번만 사용되었고, 노이즈라고 생각하여 자주 사용되는 키워드만 추출하기 위한 전처리 작업도 진행했다.

```py
def preprocess_text(text):
    text = text.split(',')
    text = [re.sub(r'[^가-힣\s]', ' ', x) for x in text]
    return ' '.join(text)
    
balanced_train['processed_title'] = balanced_train['제목'].apply(preprocess_text) 
balanced_train['processed_keywords'] = balanced_train['키워드'].apply(preprocess_text)

test['제목_키워드'] = (test['제목'] + " " + test['키워드']).apply(preprocess_text)

from collections import Counter

keywords = balanced_train['processed_keywords']
keywords = keywords.tolist()
keyword_counts = Counter(keywords)

sorted_keyword_dict = sorted(keyword_dict.items(), key = lambda x:x[1])
sorted_keyword_dict_len = len(sorted_keyword_dict)

# 자주 사용되는 상위 30% 키워드만 추출하기 위한 배열
sorted_keyword = sorted_keyword_dict[int(sorted_keyword_dict_len * 0.7) :]
sorted_keyword = dict(sorted_keyword_dict).keys()
```

### 3. 모델 구성

#### 3-1. 하이퍼파라미터
모델의 하이퍼파라미터를 구성한다.
```py
config = {
    "learning_rate": 1e-5, 
    "epoch": 50,
    "batch_size": 64,
    "weight_decay": 0.01,
    "tokenizer_max_len": 128,
}

CFG = SimpleNamespace(**config)
```

#### 3-2. 사전 학습된 모델 부르기
```py
device = torch.device('cuda' if torch.cuda.is_available() else 'cpu')
tokenizer = AutoTokenizer.from_pretrained("monologg/kobigbird-bert-base")  # BertTokenizer
model = BigBirdForSequenceClassification.from_pretrained(
    'monologg/kobigbird-bert-base', 
    num_labels=len(train['분류'].unique())
).to(device)
```

#### 3-3. 모델 인풋 데이터 구성
TextDataSet이라는 Dacon에서 샘플 코드로 작성된 클래스를 통해 데이터를 구성하였다.

```py
# 레이블 인코딩
label_encoder = {label: i for i, label in enumerate(balanced_train['분류'].unique())}
balanced_train['label'] = balanced_train['분류'].map(label_encoder)

# 데이터 분할 (train -> train + validation)
train_df, val_df = train_test_split(balanced_train, test_size=0.2, stratify=balanced_train['분류'], random_state=42)

# 데이터셋 생성
train_dataset = TextDataset(train_df.제목_키워드.tolist(), train_df.label.tolist(), tokenizer, max_len=CFG.tokenizer_max_len, preprocess_datarows=True)
val_dataset = TextDataset(val_df.제목_키워드.tolist(), val_df.label.tolist(), tokenizer,  max_len=CFG.tokenizer_max_len, preprocess_datarows=True)
test_dataset = TextDataset(test.제목_키워드.tolist(), None, tokenizer) 

# 데이터 로더 생성
train_loader = DataLoader(train_dataset, batch_size=CFG.batch_size, shuffle=True)
val_loader = DataLoader(val_dataset, batch_size=CFG.batch_size, shuffle=False)
test_loader = DataLoader(test_dataset, batch_size=CFG.batch_size, shuffle=False)
```

검증 데이터에 대한 F1 스코어는 훈련할수록 점수가 올랐고 대부분 0.72에서 수렴하였다. 하지만 테스트 데이터에 대한 예측 후 제출하였을 땐 0.55, 0.49 등 생각보다 모델의 성능이 좋지 않았다.

뭐가 문제였던건지 잘못 생각한 것 같다.
