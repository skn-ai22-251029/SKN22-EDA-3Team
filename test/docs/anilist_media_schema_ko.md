# AniList Media 스키마 전체 필드 한국어 설명

아래 문서는 AniList GraphQL API의 `Media` 타입에 존재하는 모든 필드의 구조와 의미를 한국어로 상세하게 정리한 것입니다.  
EDA 관점에서 어떤 필드를 사용할지 판단하기 위한 참고 문서로 사용할 수 있습니다.

---

## 1. 기본 식별자 / 타입 관련

### **1) `id`**
- AniList 내부에서 사용하는 고유 ID  
- EDA 기본 키로 사용

### **2) `idMal`**
- MAL(MyAnimeList) 작품 ID  
- MAL 데이터와 조인할 때 활용

### **3) `title` (MediaTitle)**
여러 언어의 제목을 담은 객체:
- `romaji` — 로마자 표기
- `english` — 영어 제목
- `native` — 원어(일본어 등)

### **4) `type` (MediaType)**
- `ANIME` 또는 `MANGA`

### **5) `format` (MediaFormat)**
- `TV`, `MOVIE`, `OVA`, `ONA`, `SPECIAL`, `TV_SHORT`, `MUSIC` 등  
- 포맷별 분포/평점 비교에 유용

### **6) `status` (MediaStatus)**
- `FINISHED`, `RELEASING`, `NOT_YET_RELEASED`, `CANCELLED`, `HIATUS`

---

## 2. 설명 / 날짜 / 시즌 관련

### **7) `description`**
- 작품 줄거리/캐릭터 설명 (HTML 포함 가능)

### **8) `startDate` (FuzzyDate)**
- 방영/출시 시작일 `{year, month, day}`

### **9) `endDate` (FuzzyDate)**
- 방영 종료일

### **10) `season` (MediaSeason)**
- `WINTER`, `SPRING`, `SUMMER`, `FALL`

### **11) `seasonYear`**
- 방영 시즌 연도

### **12) `seasonInt`**
- 연도+시즌을 정수로 혼합한 값 (내부 용도)

---

## 3. 분량 / 국가 / 원작 소스

### **13) `episodes`**
- 총 에피소드 수  
- “1쿨/2쿨/장편” 분포 분석에 유용

### **14) `duration`**
- 에피소드당 러닝타임(분)

### **15) `chapters`**  
### **16) `volumes`**
- 만화(MANGA)일 때만 의미 있음

### **17) `countryOfOrigin`**
- 제작 국가 (ISO 3166-1 alpha-2, 예: JP, KR, CN)

### **18) `isLicensed`**
- 공식 라이선스 여부

### **19) `source` (MediaSource)**
- 원작 소스: `ORIGINAL`, `MANGA`, `LIGHT_NOVEL`, `GAME`, `WEB_NOVEL` 등

---

## 4. SNS / 이미지 / 미디어 정보

### **20) `hashtag`**
- 공식 트위터 해시태그

### **21) `trailer` (MediaTrailer)**
- 홍보 영상 정보 (YouTube 등)

### **22) `updatedAt`**
- 마지막 데이터 수정 시각 (UNIX time)

### **23) `coverImage`**
- 표지 이미지 (큰 사이즈, 미디엄, 썸네일, 대표 색상)

### **24) `bannerImage`**
- 애니리스트 배너 이미지 URL

---

## 5. 장르 / 동의어 / 점수 / 인기

### **25) `genres`**
- 장르 리스트 (예: Action, Fantasy)

### **26) `synonyms`**
- 대체 제목

### **27) `averageScore`**
- 가중 평균 유저 점수 (0~100)

### **28) `meanScore`**
- 단순 평균 점수

### **29) `popularity`**
- 이 작품을 리스트에 추가한 유저 수 (조회수 비슷한 개념)

### **30) `isLocked`**
- 삭제 예정/잠긴 작품 여부

### **31) `trending`**
- 최근 1시간 트렌드 지수

### **32) `favourites`**
- 즐겨찾기 등록 유저 수  
- popularity보다 더 “찐팬” 수치

---

## 6. 태그 / 관계 / 캐릭터 / 스태프 / 스튜디오

### **33) `tags` (MediaTag[])**
- 작품 속 세부 요소/테마를 나타내는 태그 집합  
- `name`, `rank`, `category`, `isAdult`, `isMediaSpoiler` 포함

### **34) `relations`**
- 프리퀄/시퀄/스핀오프 등 연결된 작품 관계

### **35) `characters`**
- 등장 캐릭터 목록

### **36) `staff`**
- 감독/작가/애니메이터 등 제작진 목록

### **37) `studios`**
- 애니메이션 스튜디오 정보

---

## 7. 인증 유저 기준 플래그

(로그인하지 않으면 거의 의미 없음)

### **38) `isFavourite`**  
### **39) `isFavouriteBlocked`**

---

## 8. 방영 정보 / 트렌드 / 외부 링크

### **40) `isAdult`**
- 성인용 여부

### **41) `nextAiringEpisode`**
- 다음 방영 예정 에피소드

### **42) `airingSchedule`**
- 전체 방영 일정

### **43) `trends`**
- 일별 인기 트렌드

### **44) `externalLinks`**
- 공식 사이트, SNS 등 외부 링크

### **45) `streamingEpisodes`**
- 스트리밍 플랫폼 에피소드 정보

---

## 9. 랭킹 / 리뷰 / 추천 / 통계

### **46) `rankings`**
- 특정 기간/포맷에서의 랭킹 정보  
- (예: “TV 애니 중 평점 1위”)

### **47) `mediaListEntry`**
- 인증된 유저의 개인 리스트 정보 (과제에서는 거의 사용 X)

### **48) `reviews`**
- 유저 리뷰 데이터

### **49) `recommendations`**
- 추천 작품 리스트

### **50) `stats` (MediaStats)**
- **점수별 분포** — scoreDistribution  
- **시청 상태 분포** — statusDistribution  
- 매우 EDA 친화적

### **51) `siteUrl`**
- AniList 공식 작품 페이지

---

# ✔ EDA에서 특히 유용한 핵심 필드

- `format`, `status`, `season`, `seasonYear`, `source`
- `episodes`, `duration`
- `genres`, `tags`
- `averageScore`, `meanScore`, `popularity`, `favourites`, `trending`
- `stats.scoreDistribution`, `stats.statusDistribution`
- `studios`, `rankings`

이 필드만으로도 강력한 분석 가능:
- 장르별 평균 점수
- 포맷별 인기/점수 차이
- 시즌별 작품 특성
- 스튜디오별 퀄리티/인기도
- 태그 기반 클러스터링
- 점수 분포 분석

