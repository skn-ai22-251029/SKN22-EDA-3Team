# Anilist Top-10000 EDA 프로젝트
SKN22-EDA-3Team, 최정환, 정세환, 안민제, 엄형은, 임도형

- 본 프로젝트는 AniList Top 10,000 데이터셋을 기반으로,
작품 특성, 장르·태그 메타데이터, 제작사 정보, 시청 통계 등 주요 도메인을 세분화하여
애니메이션 작품의 평가·인기도·제작사 특성·완주율(drop rate) 패턴을 분석했습니다.

## 주요 분석 insight
- 최정환: `인기도(popularity) vs. format, averageScore / 장르 개수, 제작사 작품 수 vs. averageScore` 
```
Target-like variable: 작품 인기 또는 점수 상위권 여부
Features: 장르, 스튜디오, 시즌
→ “어떤 특성이 상위권 작품을 만드는가?”
```
    * 인기도가 증가할 수록 평균 점수가 전반적으로 상승됨
    * 장르 개수와 평균 점수는 관계가 드러나지 않음, 
    * 제작사 작품 수가 많을수록, 평균 점수는 떨어진다.
---
- 정세환: `작품, 장르, 스튜디오, 시즌별 점수, 인기 패턴`
```
Target-like variable: 작품 인기 또는 점수 상위권 여부
Features: 장르, 스튜디오, 시즌
→ “어떤 특성이 상위권 작품을 만드는가?”
```
    * 데이터 전체의 상위 인기작·점수작 분포 정리
    * 시즌/스튜디오/장르 조합에 따라 점수 분포가 달라지는 양상을 탐색
	* 장르와 스튜디오 특성별 인기 분포 비교
---

- 안민제: `인기 순 제작사 vs. favourites, 장르 수, 활동 시기`
```
Target-like variable: 제작사 인기 순위
Features: favourites, genre diversity, 활동 기간
→ “인기 제작사의 특징은 무엇인가?”
```

    * 인기가 높은 제작사(보조지표)일 수록 좋아요가 많고, 제작사의 인기와 장르 수는 관계를 보이기 힘들고, 순위가 높은 제작사일 수록 활동 기간이 길고, 그에 따라 순위 편차도 심해진다.
---
- 엄형은: `genres, tags 조합별 대중성 분석` 
```
Target-like variable: 조합별 대중성 점수
Features: 장르, 태그
→ “어떤 조합이 대중적 패턴을 만드는가?”
```

    * 장르와 태그를 조합하여, 그 조합 점수 별 Top-N 리스트를 시각화
---
- 임도형: `애니메이션 시청 데이터 vs. 중도 하차(drop rate)` 
```
Target-like variable: drop_rate
Features: averageScore, episodes
→ “어떤 요인이 시청 중도 이탈을 만드는가?”
```
    * 많이 찾는 작품이라도 중도 이탈은 점수(averageScore)·회차(episodes) 영향이 더 크다.
    * 점수가 낮을수록, 드랍률이 급증하며, 회차수가 길어질수록 드랍률이 더 증가한다.
---
## 주요 컬럼 소개

| Column | Description |
| --- | --- |
| id | 작품 고유 ID |
| title_english | 작품명(영문) |
| format | TV / MOVIE / OVA / ONA 등 매체 형식 |
| status | FINISHED / RELEASING |
| season | SPRING / SUMMER / FALL / WINTER |
| seasonYear | 방영 연도 (예: 2013) |
| **seasonInt** | 방영 분기 정수값 (예: 2013 + 분기코드 → 시계열 정렬용)<br>시즌코드(Spring=1, Summer=2, Fall=3, Winter=4) |
| episodes | 총 에피소드 수 |
| duration | 1화의 평균 러닝타임 (분) |
| **averageScore** | 사용자 평균 평점 |
| **meanScore** | 정규화된 평균 점수 |
| **popularity** | 인기 지표 |
| **favourites** | 좋아요 수 |
| genre_list | 1개 이상의 장르 문자열(예: Action|Drama) |
| tag_list | 세부 태그(예: Coming of Age, Shounen 등) |
| main_studio_name | 메인 제작 스튜디오 |
| studio_list | 참여한 전체 스튜디오 목록 |
| directors | 감독진 정보 |

## 공통 데이터 전처리 요약

본 프로젝트의 모델링 및 EDA는 AniList Top 10,000 JSON 원시 데이터에서 시작하여, 제목·장르·태그·스튜디오·시청 통계·방영일수 등을 정규화하고, 장르/주요 태그를 One-Hot Encoding으로 확장한 뒤, 가중평균 평점(weighted_score), 중도 하차율(drop_rate), 방영일수(airing_days) 등의
파생변수를 포함한 anilist_top10000_eda.csv/parquet 전처리 데이터셋을 기반으로 수행했습니다.

## 공통 데이터 전처리 프로세스

1.	JSON 로드
    - AniList Top 10,000 구조화 데이터 로드
2.	Title 컬럼 구조화
    - title 딕셔너리를 전개: title_romaji, title_english, title_native
3.	Tags 전처리
    - 태그 객체 → 문자열 리스트로 변환, tag_list 생성
4.	Genres 전처리
    - 장르 explode(작품 1:N 구조 분해)
    - 장르 빈도 계산(genre_counts)
    - 장르 OHE 준비
5.	Genres OHE
    - id 기준 concat + groupby sum
    - 장르별 OHE 데이터프레임 생성
6.	Tags OHE
    - 태그는 희소도가 높기 때문에 TOP 200 태그만 사용
    - 동일 방식으로 id 그룹화 OHE 수행
7.	시청 통계 기반 파생 변수 생성
    - weighted_score: 평점 분포(scoreDistribution) 기반 가중 평균
    - drop_rate: statusDistribution 기반 중도 하차 비율
8.	제작사 데이터 가공
    - main_studio: 스튜디오 nodes/edges에서 isMain=True인 제작사 추출
    - studio_list 전체 참여 스튜디오 추출
9.	방영일수 파생 변수
    - startDate, endDate → 날짜 정규화
    - airing_days = 종료일 – 시작일 (FINISHED에 한해 계산)

## 팀원별 분석 상세

### 작품 단위 TOP 분석 (세환)
- 실제 데이터 TOP을 확인하기 위해 시각화. 수치적 데이터를 포함함
- Score와 Popularity를 결합한 **종합 지표(total_rank_score)** 생성.
- 이 지표는 다음 작품을 식별하는 데 효과적:
    - **평가 + 대중성 모두 잡은 작품 → “전천후(all-rounder) 작품”**
- 시각화 결과:
    - 점수/인기도 중 하나만 높은 작품과 확실히 구분됨.

![](images/sh3.png)
![](images/sh5.png)

### 수치적 데이터 Overview (세환)
- 인기도(Popularity) vs. 평점(Score)
    -> 대중성과 작품성의 상관관계는 낮음
- Popularity(인기도)와 Score(평점)을 비교한 결과, 대중적으로 인기 많은 작품이 반드시 높은 평점을 받지는 않음.
- 반대로, **평점이 매우 높아도 대중적 인기는 낮은 작품도 존재.**

![](images/sh7.png)

### 형식(format)별 평균 점수 분석 (정환)
- 시각화: 형식(Format)별 작품 수 및 평균 점수 (Bar Chart) 
- 상관분석: (N/A - 범주형 변수 간의 그룹 비교)
- 그룹 비교:
    * TV 시리즈: 가장 많은 작품 수를 차지하며, 평균 점수(약 73점대)가 안정적입니다. 이는 시장의 중심축으로서 일관된 품질 관리가 이루어지고 있음을 시사합니다.
    * Movie / OVA: TV 대비 평균 점수가 유사하거나 약간 높은 수준을 보이며, 한정된 기간에 집중 투자되는 콘텐츠의 높은 품질 기대치를 충족시킵니다.
    * ONA (Original Net Animation): TV와 비슷한 수준의 높은 점수를 기록하며 비중이 증가하고 있습니다. OTT 플랫폼 확산에 따라 고품질 ONA가 새로운 주류 콘텐츠 형식으로 부상하고 있습니다.

![image.png](images/jh.png)

### 스튜디오별 작품 수 분포 (세환)
- Toei Animation, Sunrise, MADHOUSE → 다작 스튜디오
- Kyoto Animation, Ufotable → 소수 정예 + 고평가

- 데이터 분석 결과:
    - **작품 수가 많다고 평가가 좋은 스튜디오가 아님**
    - 오히려 **작품 수는 적지만 ‘퀄리티 중심’의 스튜디오가 더 높은 평가**를 받음.
- 즉,
    - ‘생산량 중심의 스튜디오’ vs ‘퀄리티 중심의 스튜디오’의 차이가 명확하게 드러남.
    - 이는 애니메이션 산업의 제작 전략 차이를 보여주는 대표적인 패턴임.

**스튜디오 지표 해석 방식**

- **평균 종합 점수**
    
    → 스튜디오가 제작한 모든 작품의 *total_rank_score 평균*
    
- **대표작**
    
    → 해당 스튜디오 작품 중 **가장 높은 종합 점수를 기록한 작품**

![alt text](images/sh6.png)

### 제작사 작품수 와 평균 점수 (정환)
- 데이터 분석 결과 
    1) 제작사의 작품수가 증가할 수록 평균 점수는 약하게 감소하는 경향을 보임 
    2) 회귀선 역시 오른쪽으로 갈수록 완만하게 내려가는 형태를 띰 
    3) 제작사는 1편~20편 정도의 소규모 포트폴리오를 가지고 있으며, 구간에는 점수가 폭 넓게 분포
    4) 일부 대형 제작사들은 수백편 단위의 작품을 내고 있음에도 평균 점수는 오히려 중간 수중에 머무는 모습 보임 
    5) 작품수가 증가 할수록 다양한 장르/ 형태/ 제작 환경 등의 변화가 발생 하면서 품질을 일정하게 유지 하는게 어려울 것으로 판단 됨

![](images/jh2.png)
![](images/jh3.png)

### 유명한 제작사가 만든 작품들은 좋아요도 많이 받았을까?  (민제)

    `Studio`: 상위 2500개의 작품의, `main_studio`. studio obj의 edge가 isMain == true인 스튜디오. 

    `PopularitySum`: `Studio` 중 `“isMain”: true` 인 각 작품의 `popularity` 값을 합한 순위

    `WorkCount` :  각 `PopularitySum` 값을 제공한 `title` 개수. 즉, 제작사의 애니메이션 작업 참여 수를 의미함.

    이 세가지 컬럼을 포함한 새로운 데이터프레임, studio_popularity_top2500.csv를 따로 생성 후 사용

  - scatter:  
    - x: 제작사 인기 순위 (오른쪽이 유명), PopularitySum.
    - y: 그 제작사가 만든 작품의 `favourites` 개수
    
![](https://morning-sesame-d7e.notion.site/image/attachment%3A055330be-c9a6-46c7-962b-9478dbfcb24f%3Aimage.png?table=block&id=2b845ffc-66ac-8006-8fe0-f3d206512102&spaceId=9bd45ffc-66ac-8182-98f0-000382468b0b&width=770&userId=&cache=v2)
    - 인기도가 높은 제작사가 만든 작품일수록 그 작품이 받은 좋아요도 많았고, 인기가 상위권이 아닌 제작사들이 만든 작품들은 좋아요를 거의 받지 못했다. 또한 순위가 높은 제작사일수록 제작하는 작품도 많다는 특징이 보여 그 규모를 짐작할 수 있다.

### 유명한 제작사일수록 만드는 작품들의 장르가 다양하고, 순위가 낮은 제작사는 적은 장르만 만들까?  (민제)
  - scatter, regplot(sns)
  - x축: 제작사 인기 순위  
  - y축: 해당 제작사의 유니크 장르 수 `genre_count`  
  - `ci=95`: 회귀선(단순 선형 회귀)의 신뢰구간(confidence interval) 폭을 95%로 계산  
  - 파란 띠가 회귀 추정의 불확실성이고, 데이터 퍼짐이 커질수록 밴드도 넓어지는 경향을 보게 된다.

![](https://morning-sesame-d7e.notion.site/image/attachment%3A01cb3b67-dead-4591-9194-55a87bd0685d%3Aimage.png?table=block&id=2b845ffc-66ac-80a1-933c-f9d4c1943729&spaceId=9bd45ffc-66ac-8182-98f0-000382468b0b&width=960&userId=&cache=v2)  
    - 인기도가 높은 제작사일수록 장르의 가짓수도 많은 경향을 보인다. 다만 순위가 뒤로 갈수록 그 편차가 커지고, 앞선 그래프에서 표현된 제작한 작품 개수 차이를 고려하면, 순위가 낮은 제작사가 여러 장르의 작품들을 만들지 못한다는 결론을 내릴 수는 없다.

### 유명한 제작사일수록 오랫동안 활동했을까? (민제)
  - 계산:  
    - 각 제작사에 대해:  
      - 이 제작사가 포함된 모든 작품의 `seasonYear` 목록을 모으고  
      - `max(seasonYear) - min(seasonYear)` = 활동 기간(년 단위)
  - 출력:  
    - x: 스튜디오 인기 순위 1~100 (오른쪽일수록 인기 많음)를 10개 구간별로 나눠 출력  
    - y: `groupby().agg(mean_span="mean", max_span="max")` 각 구간의 평균, 최대값을 구한다. 

![](https://morning-sesame-d7e.notion.site/image/attachment%3A20d50522-ee7a-467f-96ac-626b349d7a40%3Aimage.png?table=block&id=2b845ffc-66ac-806f-b0d1-cdc87f68581b&spaceId=9bd45ffc-66ac-8182-98f0-000382468b0b&width=860&userId=&cache=v2) 
    - 순위가 높은 제작사일수록 활동 기간이 길고, 그 편차도 커지는 경향을 보인다. 또한 전체적으로 100위권(전체 중 상위권) 이상은 15년 또는 그 이상의 역사를 가진 곳이 대부분이다.

### [장르 X 태그] 조합으로 대중성 분석 (형은)

- 장르와 태그 간의 공통 출현(Co-occurrence) 패턴을 상관계수로 확인합니다.
- **임계값(Threshold)**을 적용하여 의미 있는 관계만 시각화합니다.

- 상관관계 심층 분석 (순위화 및 텍스트 요약)
    - **Centrality (중요도)**: 해당 장르/태그가 다른 항목과 얼마나 강하게 연결되는지 (최대 상관계수).
    - **Popularity (인기도)**: 인기 상위 100개 작품에서 얼마나 자주 등장하는지.
    - **Normalization (정규화)**: 두 지표의 스케일(0~1)을 맞춰 공정한 비교가 가능하게 함.

![](images/he.png)
![](images/he2.png)
![](images/he3.png)
![](images/he4.png)
![](images/he5.png)
![](images/he6.png)

### 애니메이션 시청 데이터 vs. 중도 하차(drop rate) (도형)

#### stats_drop_rate
```python
stats = {
  "statusDistribution": [
      {"status": "completed", "amount": ...},
      {"status": "watching", "amount": ...},
      {"status": "dropped",  "amount": ...},
      {"status": "paused",   "amount": ...},
      ...
  ]
}
```

-> `drop_rate = dropped / total_users`

- **평균 점수(averageScore)는 Drop Rate와 뚜렷한 음의 상관**
   - 점수가 낮을수록 드랍률이 빠르게 상승
   - 점수는 가장 영향력 있는 예측 변수로 확인됨.

![](images/output3.png)

- **회차 수(episodes)는 Drop Rate를 강하게 증가시키는 요인**
   - 회차가 많을수록 드랍률 상승
   - 특히 100화 이상 장편에서 중도 이탈이 크게 증가.

![](images/output4.png)
