#### 1. 프로젝트 개요

- 분석 목적: 일본 애니메이션 제작사 인기 순위를 만들고, 그것을 활용하여 사용자들이 궁금해 할 만한 정보들을 시각화하기 위함
- 데이터 출처 및 설명: anilist GraphQL API 호출로 얻어낸 데이터들을 DataFrame 형식으로 저장 후 가공해 사용

#### 2. 데이터 기본 정보 확인

- 행/열 수: 2500 / 7
- 각 변수 이름, 타입, 결측치 여부 등  
  id(int) / seasonYear(float64) / popularity(int64) / favourites(int64) / genres(object) / studio(object) / PopularitySum(int64)

## 2. 데이터 기본 정보

- 총 데이터 수: 2500개
- 컬럼 수: 7개
- 주요 컬럼: `popularity`, `genres`, `studio` 등

## 3. 기술 통계 요약

### 수치형 변수

- 컬럼명: Popularity, 평균: 89011.7452, 중앙값: 51950.5, 표준편차: 101006.06, 최솟값: 21759, 최댓값: 923138
- 컬럼명: favourites, 평균: 2369.6564, 중앙값: 809, 표준편차: 5116, 최솟값: 63, 최댓값: 89649

### 범주형 변수

- 컬럼명: title, 고유값: 2500개, 최빈값: x, 분포: x
- 컬럼명: genres, 고유값: 18, 최빈값: Comedy, 분포:  
  Comedy: 13.43%, Action: 13.37%, Drama: 10.31%, Fantasy: 9.99%, Romance: 9.36%, Slice of Life: 7.38%, Adventure: 7.07%, Supernatural: 6.84%, Sci-Fi: 5.22%, Mystery: 3.69%, Ecchi: 3.6%, Psychological: 3.07%, Sports: 1.4%, Horror: 1.4%, Thriller: 1.36%, Mecha: 1.07%, Music: 0.97%, Mahou Shoujo: 0.46%

## 4. 결측치 및 이상치 탐색

- 결측치 없음, 이상치 모두 활용(전처리 필요 없음)

## 5. 변수 간 관계 분석

- 시각화: scatter, regplot, plot
- 상관분석: 수치형 변수 간 상관계수, 범주형 변수의 빈도수 등
- 그룹 비교: 상위 1~10위권과 11~20위권의 비교 등
- `PopularitySum`와 `favourites` 관계: 인기가 높은 제작사의 작품일수록 좋아요 수가 많다.
- `favourites`와 `popularity` 관계: 좋아요가 많은 작품일수록 인기가 많다.

## 6. 파생 변수 및 전처리

- 상위 2500개의 애니메이션을 만든 `studio` 중 `isMain: true`인 제작사에 그 작품의 `popularity` 값을 합한 순위
- `Studio` → 구간화

## 7. 요약 및 인사이트

- 참고: 총 제작사 수는 858사이고, 어느 정도 규모가 있는 제작사는 300사 정도다.

## 유명한 제작사가 만든 작품들은 좋아요도 많이 받았을까?  
  - scatter:  
    - x: 제작사 인기 순위 (오른쪽이 유명)  
    - y: 그 제작사가 만든 작품의 `favourites` 개수  
    
![](https://morning-sesame-d7e.notion.site/image/attachment%3A055330be-c9a6-46c7-962b-9478dbfcb24f%3Aimage.png?table=block&id=2b845ffc-66ac-8006-8fe0-f3d206512102&spaceId=9bd45ffc-66ac-8182-98f0-000382468b0b&width=770&userId=&cache=v2)
- 인기도가 높은 제작사가 만든 작품일수록 그 작품이 받은 좋아요도 많았고, 인기가 상위권이 아닌 제작사들이 만든 작품들은 좋아요를 거의 받지 못했다. 또한 순위가 높은 제작사일수록 제작하는 작품도 많다는 특징이 보여 그 규모를 짐작할 수 있다.
  

## 유명한 제작사일수록 만드는 작품들의 장르가 다양하고, 순위가 낮은 제작사는 적은 장르만 만들까?  
  - scatter, regplot(sns)
  - x축: 제작사 인기 순위  
  - y축: 해당 제작사의 유니크 장르 수 `genre_count`  
  - `ci=95`: 회귀선(단순 선형 회귀)의 신뢰구간(confidence interval) 폭을 95%로 계산  
  - 파란 띠가 회귀 추정의 불확실성이고, 데이터 퍼짐이 커질수록 밴드도 넓어지는 경향을 보게 된다.

![](https://morning-sesame-d7e.notion.site/image/attachment%3A01cb3b67-dead-4591-9194-55a87bd0685d%3Aimage.png?table=block&id=2b845ffc-66ac-80a1-933c-f9d4c1943729&spaceId=9bd45ffc-66ac-8182-98f0-000382468b0b&width=960&userId=&cache=v2)  
- 인기도가 높은 제작사일수록 장르의 가짓수도 많은 경향을 보인다. 다만 순위가 뒤로 갈수록 그 편차가 커지고, 앞선 그래프에서 표현된 제작한 작품 개수 차이를 고려하면, 순위가 낮은 제작사가 여러 장르의 작품들을 만들지 못한다는 결론을 내릴 수는 없다.

## 유명한 제작사일수록 오랫동안 활동했을까?  
  - 계산:  
    - 각 제작사에 대해:  
      - 이 제작사가 포함된 모든 작품의 `seasonYear` 목록을 모으고  
      - `max(seasonYear) - min(seasonYear)` = 활동 기간(년 단위)
  - 출력:  
    - x: 스튜디오 인기 순위 1~100 (오른쪽일수록 인기 많음)를 10개 구간별로 나눠 출력  
    - y: `groupby().agg(mean_span="mean", max_span="max")` 각 구간의 평균, 최대값을 구한다. 

![](https://morning-sesame-d7e.notion.site/image/attachment%3A20d50522-ee7a-467f-96ac-626b349d7a40%3Aimage.png?table=block&id=2b845ffc-66ac-806f-b0d1-cdc87f68581b&spaceId=9bd45ffc-66ac-8182-98f0-000382468b0b&width=860&userId=&cache=v2) 
- 순위가 높은 제작사일수록 활동 기간이 길고, 그 편차도 커지는 경향을 보인다. 또한 전체적으로 100위권(전체 중 상위권) 이상은 15년 또는 그 이상의 역사를 가진 곳이 대부분이다.