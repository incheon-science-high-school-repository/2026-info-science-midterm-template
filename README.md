# [정보과학과제연구] 중간 수행평가

## 1. 제출자 정보
* **학번 / 이름**: 
* **제출일시**: 2026년 9월 15일
* **OBS 녹화 영상 링크**: (구글 드라이브 또는 제출 링크 기재)

---

## 2. 프로젝트 개요
스마트 교실 환경 데이터 및 학생 학습 데이터를 통합하여 결측치 정제, NumPy 기반 집중도 지수 산출, 위험 교실 추출을 수행하는 분석 파이프라인입니다.

---

## 3. 주요 구현 내용
* **Task 1 (Pandas 정제)**: `co2` 전향 채우기(ffill), `quiz_score` 교실별 중앙값 대체, `temperature` 이상치 제거 후 선형 보간 적용
* **Task 2 (NumPy 연산)**: `for` 문 없이 pure NumPy 브로드캐스팅 기반 Z-Score 표준화 및 행렬 곱(`@`) 연산 수행
* **Task 3 (집계 및 출력)**: `np.where` 조건부 로직 적용, `groupby` 집계 후 상위 2개 위험 교실 및 정제 데이터 CSV 저장

---

## 4. 실행 방법
```bash
python solution.py
```

---

## 5. 생성 결과물 확인
* `cleaned_smart_classroom.csv`: 정제 및 집중도 지수/상태가 추가된 전체 데이터셋
* `classroom_summary.csv`: 위험 건수 상위 2개 교실 요약 데이터

<br>

---
---

<br>

# [시험 문제지] 스마트 교실 센서 및 학습 데이터 통합 분석 파이프라인 구축

* **시험 시간**: 50분
* **제출 방식**: 
  1. 본인 개인 GitHub 저장소(반드시 Private 설정할 것)에 커밋 및 푸시
  2. 교사 계정을 Collaborator로 등록
  3. OBS 전체 화면 녹화 영상 제출
* **허용 범위**: Python/Pandas/NumPy 공식 문서 및 치트시트, 기존 작성 코드 참고 가능 (단, 타인과의 실시간 소통 및 대리 작성 금지), AI 사용금지

---

### 1. 배경 및 시나리오
스마트스쿨 시스템 구축을 위해 교실 실내 환경 데이터(CO2, 온도)와 학생들의 정보과학 과목 학습 수행 데이터(학습시간, 과제제출률, 퀴즈점수)가 통합된 데이터셋(`smart_classroom.csv`)이 수집되었습니다. 

Pandas와 NumPy를 활용하여 **데이터 정제 → 종합 학습 집중도 지수 계산 → 위기 교실/학생 추출 및 결과 저장**으로 이어지는 데이터 분석 파이프라인을 구축하시오.

---

### 2. 세부 요구사항 및 단계별 작업

#### **[Task 1] Pandas 기반 데이터 로드 및 시계열/결측치 정제**
1. 제공된 `smart_classroom.csv` 파일을 DataFrame으로 로드하시오.
2. `timestamp` 컬럼을 Datetime 객체로 변환하고 인덱스(Index)로 설정하시오.
3. **결측치 및 이상치 처리**:
   * `co2` 컬럼의 결측치는 직전 유효 값으로 채우시오 (Forward Fill).
   * `quiz_score` 컬럼의 결측치는 동일한 교실(`room_id`)의 **중앙값(Median)**으로 채우시오.
   * `temperature` 컬럼에서 0℃ 이하 또는 40℃ 이상의 이상치는 `np.nan`으로 변환한 후, **선형 보간(Linear Interpolation)**을 적용하시오.

#### **[Task 2] NumPy 활용 Z-score 표준화 및 행렬 곱 연산**
> **주의**: Task 2에서는 Python의 `for` 문 사용이 엄격히 금지됩니다. 반드시 **pure NumPy 연산**만 활용하시오.

1. 정제된 데이터에서 `[study_time, submission_rate, quiz_score]` 3개 학습 특성 컬럼을 추출하여 NumPy 2D Array(`features`)로 변환하시오.
2. 각 특성(열)별로 Z-Score 표준화 행렬 $Z$를 계산하시오. 
   $$Z = \frac{X - \mu}{\sigma}$$
3. 주어진 가중치 Vector $W = [0.3, 0.3, 0.4]$ 와 표준화 행렬 $Z$ 간의 **행렬 곱(Dot Product / `@` 또는 `np.dot`)**을 수행하여 학생별 `focus_score`(집중도 지수) 1차원 배열을 구하시오.
4. 구한 `focus_score`를 기존 Pandas DataFrame에 파생 컬럼으로 추가하시오.

#### **[Task 3] Conditional Indexing 및 데이터 집계/Export**
1. `np.where`를 활용하여 `focus_score`가 **-0.5 미만**이면서 동시에 `co2` 수치가 **1200 이상**인 행의 `status` 컬럼 값을 `'Warning'`, 그렇지 않은 경우 `'Normal'`로 지정하시오.
2. 교실(`room_id`)별로 그룹화(`groupby`)하여 다음 항목을 집계(Aggregate)하시오:
   * `co2`의 평균값
   * `focus_score`의 평균값
   * `status`가 `'Warning'`인 총 건수
3. `'Warning'` 건수가 가장 높은 상위 2개 교실의 정보를 `classroom_summary.csv` 파일로 export 하시오.
4. 정제 완료된 전체 데이터셋을 `cleaned_smart_classroom.csv` 파일로 export 하시오.

---

### 3. Git 커밋 메시지 준수 조건 (10분 / 진행 과정 중 필수)
작업 단계에 따라 **최소 3회 이상** 아래 규칙에 맞춰 GitHub에 commit 및 push를 수행하시오.

* **Commit 1**: `feat: preprocess missing values and outliers`
* **Commit 2**: `feat: calculate focus_score using numpy matrix multiplication`
* **Commit 3**: `feat: aggregate classroom warning status and export results`

---

### 4. 프로젝트 제출 디렉토리 구조

```text
├── README.md                     # [수험자 작성란 + 문제지 통합본]
├── smart_classroom.csv           # [제공 데이터]
├── solution.py                   # [학생 작성 코드]
├── cleaned_smart_classroom.csv   # [생성 결과물 1]
└── classroom_summary.csv         # [생성 결과물 2]
```
