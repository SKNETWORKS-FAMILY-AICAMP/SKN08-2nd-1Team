# SKN08-2nd-1Team
SK Networks AI 8기 2번째 단위 프로젝트입니다.

# OPJP-Data-Analysis
 > 팀장: 오민수 https://github.com/yooodleee

 > 팀원: 정현서 https://github.com/jungs0914
--------------------------------------------------------------

# 📍넷플릭스 영화 및 TV 쇼 클러스터링

## 프로젝트 개요

* 넷플릭스는 2022년 기준, 2억 2천만 명 이상의 가입자를 보유한 세계 최대의 스트리밍 플랫폼입니다. 사용자 경험을 개선하고 구독자 이탈을 방지하기 위해, 넷플릭스에 등록된 영화와 TV 쇼를 클러스터링하여 콘텐츠를 분석하고 추천하는 시스템을 구축했습니다.

* 이 프로젝트는 넷플릭스 콘텐츠를 그룹화(클러스터링)하여 개인화된 추천 시스템을 구현하는 데 초점을 맞추고 있습니다.

## 💡 주요 목표

1. 넷플릭스 콘텐츠를 유사한 항목끼리 클러스터링.
2. 클러스터 내부에서는 유사성이 높고, 클러스터 간에는 차별성이 유지되도록 설계.
3. 데이터를 바탕으로 개인화된 콘텐츠 추천 시스템(Content-Based Recommender System) 구축.


## 데이터셋 정보

### 데이터 출처

* Kaggle 데이터셋: Netflix Movies and TV Shows Clustering

### 데이터 구조

데이터셋은 넷플릭스 영화 및 TV 쇼에 대한 7787개의 레코드와 11개의 속성으로 구성되어 있습니다.

#### 주요 컬럼 설명

* show_id: 영화 또는 TV 쇼의 고유 ID
* type: 콘텐츠 유형 (Movie 또는 TV Show)
* title: 제목
* director: 감독 이름
* cast: 출연진 목록
* country: 제작 국가
* date_added: 넷플릭스에 추가된 날짜
* release_year: 실제 출시 연도
* rating: 연령 등급 (예: TV-MA, TV-14 등)
* duration: 콘텐츠 길이 (분 단위 또는 시즌 수)
* listed_in: 장르 정보 (예: 드라마, 다큐멘터리 등)
* description: 간략한 콘텐츠 설명

## 프로젝트 작업 흐름

### 🔧 1. 데이터 전처리
#### 1.1. 결측값 처리
* 데이터셋에 결측값이 다수 포함되어 있으며, 이에 대한 처리가 필요했습니다.
  
  ![Untitled](IMG/nat.png)
  
  * director, cast, country 컬럼: 결측값을 'Unknown'으로 대체.
  * rating 컬럼: 최빈값(Mode)을 사용하여 결측값 대체.
  * date_added 컬럼: 결측값이 포함된 10개의 레코드 삭제.

#### 1.2. 데이터 변환
* date_added를 날짜 형식으로 변환 후, 연도(year_added)와 월(month_added)로 분리.
* duration 컬럼은 분(min) 또는 시즌(season) 데이터를 정수로 변환.
  
![Untitled](IMG/df.png)        ➟        ![Untitled](IMG/df_after.png)


#### 1.3. 문자열 처리
* 다중 국가 또는 다중 장르의 데이터를 단순화하기 위해 주요 값만 추출.
  * 예: country와 listed_in의 첫 번째 값만 유지.

### 🔎 2. 탐색적 데이터 분석 (EDA)
EDA를 통해 데이터셋의 주요 특성과 패턴을 분석했습니다.

#### 2.1. 데이터 분포
* 영화는 데이터셋의 약 **69.14%**를 차지하며, TV 쇼는 약 **30.86%**로 나타남.
  
  ![Untitled](IMG/movies&tv_data.png)
* 넷플릭스 콘텐츠는 미국, 인도, 영국 순으로 제작된 비중이 높음.
  
  ![Untitled](IMG/top_nation.png)
* 장르별로는 드라마, 코미디, 다큐멘터리가 상위권에 위치.
  
  ![Untitled](IMG/top_genre.png)
* 대부분의 콘텐츠는 성인 및 젊은 성인 연령대를 대상으로 제작.
  
  ![Untitled](IMG/age_groups.png)

#### 2.2. 연도별 콘텐츠 분석
* 콘텐츠 추가 수는 매년 증가했으며, 특히 10월~1월 사이에 많은 콘텐츠가 추가됨.
  
  ![Untitled](IMG/add_months.png)
* COVID-19로 인해 2020년에 추가된 영화 수가 감소했으나, TV 쇼는 증가세를 보임.
  
  ![Untitled](IMG/2008_netflix.png)
  
### 📝 3. 텍스트 기반 데이터 처리
텍스트 데이터를 전처리하고 분석 가능한 형식으로 변환하는 작업을 수행했습니다.

#### 3.1. 데이터 결합
* director, cast, country, listed_in, description 데이터를 결합해 클러스터링에 사용할 단일 컬럼 생성.

#### 3.2. 텍스트 정제
* 모든 텍스트 데이터를 소문자로 변환.
* 불용어(Stopwords) 제거.
* 구두점(Punctuation) 제거.
* 비ASCII 문자 제거.
* 표제어 추출(Lemmatization)을 통해 단어를 기본 형태로 변환.

#### 3.3. 벡터화
* TF-IDF 벡터화(Term Frequency-Inverse Document Frequency)를 통해 텍스트 데이터를 벡터 형태로 변환.
  *최대 20000개의 속성을 생성하여 정보 손실 최소화.

#### 3.4. 차원 축소
* 주성분 분석(PCA)을 사용하여 데이터의 차원을 축소.
* 약 4000개의 구성 요소만으로도 분산의 80% 이상을 설명.
  ![Untitled](IMG/pca.png)


### 🧐 4. 클러스터링
넷플릭스 콘텐츠를 유사성에 따라 그룹화하기 위해 두 가지 주요 클러스터링 알고리즘을 사용했습니다: **K-평균 클러스터링(K-Means)**과 계층적 클러스터링(Hierarchical Clustering).

#### 4.1. K-평균 클러스터링

* 최적 클러스터 수 결정:
  * 엘보우(Elbow) 기법과 **실루엣 점수(Silhouette Score)**를 통해 클러스터 수를 결정.
    ![Untitled](IMG/elbow.png) ![Untitled](IMG/silhouette.png)
  * 최적의 클러스터 수: 6개.
    
* 결과 시각화:
  * 각 클러스터의 콘텐츠 수를 시각화하여 클러스터의 분포를 분석.
  * 클러스터별 주요 키워드를 워드클라우드로 시각화하여 각 클러스터의 주제를 도출.
    ![Untitled](IMG/kmeans_1.png)
    ![Untitled](IMG/kmeans_2.png)
    ![Untitled](IMG/kmeans_3.png)
    
클러스터별 주요 키워드

1. 클러스터 0: "Life, Family, Help, Teen, Discover"
2. 클러스터 1: "Love, Family, Father, Daughter, Romance"
3. 클러스터 2: "Young, Battle, World, School, Demon"
4. 클러스터 3: "Crime, Murder, Adventure, Secret"
5. 클러스터 4: "Comedian, Stage, Joke, Comedy Special"
6. 클러스터 5: "Life, Find, New, take, young"
   ![Untitled](IMG/6_word.png)
   
#### 4.2. 계층적 클러스터링

* **덴드로그램(Dendrogram)**을 사용하여 최적의 클러스터 수를 도출.
  * 최적의 클러스터 수: 12개.
* 클러스터 특성 분석:
  * 각 클러스터의 주제를 나타내는 워드클라우드를 생성.
    
클러스터별 주요 키워드

1. 클러스터 0: "Adventure, Family, Teen, Save"
2. 클러스터 1: "Love, Romance, School, Family"
3. 클러스터 2: "Crime, Mystery, World"
4. 클러스터 3: "Wedding, Secret, Young, Teen"
5. 클러스터 4: "Documentary, History, Battle"
6. 클러스터 5: "Mexico, Singer, Spanish"
7. 클러스터 6: "Young, Demon, Student, School"
8. 클러스터 7: "Korean, Detective, Love"
9. 클러스터 8: "Egypt, Money, Revolution"
10. 클러스터 9: "Comedian, Stage, Show"
11. 클러스터 10: "Nature, Planet, Animal"
12. 클러스터 11: "Korean, Years, South, Man, Past"
    ![Untitled](IMG/12_word.png)

### ⚖️ 5. 콘텐츠 기반 추천 시스템

#### 5.1. 추천 알고리즘
* **코사인 유사성(Cosine Similarity)**을 사용하여 텍스트 데이터를 비교.
* 사용자가 특정 콘텐츠를 시청한 경우, 유사한 쇼를 추천.

#### 5.2. 추천 기능 구현
* 각 콘텐츠에 대해 TF-IDF로 벡터화된 데이터를 사용해 유사성 행렬 생성.
* 사용자가 입력한 콘텐츠 제목에 따라 상위 10개의 유사 콘텐츠를 추천.

추천 예제
* 입력: "Stranger Things"
* 추천 콘텐츠:
  1. "Dark"
  2. "The OA"
  3. "Chilling Adventures of Sabrina"
  4. "The Umbrella Academy"
  5. "Black Mirror"
  6. "The Society"
  7. "The Haunting of Hill House"
  8. "The Witcher"
  9. "Locke & Key"
  10. "Teen Wolf"

### 😇 6. 프로젝트 결과 및 결론
* 넷플릭스 데이터셋의 EDA를 통해 중요한 통찰을 발견했습니다:
  * 영화의 비율이 TV 쇼보다 높으며, 대부분의 콘텐츠는 미국에서 제작.
  * 넷플릭스는 성인 및 젊은 성인 연령대를 타겟으로 한 콘텐츠가 주를 이룸.
* K-평균 클러스터링과 계층적 클러스터링을 활용해 클러스터링을 성공적으로 수행.
* 클러스터별 주요 키워드를 도출하여 넷플릭스 콘텐츠의 주제별 분포를 분석.
* 코사인 유사성을 기반으로 한 콘텐츠 기반 추천 시스템을 구현하여, 사용자 선호에 맞춘 콘텐츠 추천 가능.

-------------------------------------------------------------------
## 🫠 기술 스택
* 프로그래밍 언어: ![Untitled](IMG/python.png)
* 라이브러리:
  * 데이터 처리:  ![Untitled](IMG/pandas.png), ![Untitled](IMG/numpy.png)
  * 시각화:  Matplotlib, Seaborn, WordCloud
  * 머신러닝:  ![Untitled](IMG/scikitlearn.png)
  * 텍스트 처리: NLTK, TF-IDF Vectorizer
 









참고 자료
* 데이터 출처: Netflix Movies and TV Shows Clustering - Kaggle
