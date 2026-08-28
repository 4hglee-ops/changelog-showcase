# Decisions & Learnings

이 문서는 CHANGELOG를 실제 기록으로 검증하면서 바뀐 주요 가설과 설계 학습을 공개 가능한 수준으로 정리합니다.

---

## 1. Visual Alpha → Functional Alpha

### 관찰

화면을 코드로 재현하는 것만으로는 제품의 핵심 interaction을 검증하기 어려웠습니다.

### 결정

최소한 다음 기능이 실제로 동작하는 Functional Alpha로 확장했습니다.

- Timeline 선택
- Decision 선택
- Evidence Detail
- Relation 연결
- Decision 수정
- SQLite persistence

### 학습

프로토타입은 완성된 UI보다 **검증하려는 가설에 필요한 interaction이 실제로 동작하는 것**이 중요했습니다.

---

## 2. Sample Data → Real-data Alpha

### 관찰

샘플 데이터에서는 Context Recovery가 정말 유용한지 판단하기 어려웠습니다.

### 결정

- GitHub는 API로 실제 기록 Sync
- Notion은 필요한 문서를 Snapshot으로 가져옴
- 실제 프로젝트 Discussion을 curated snapshot으로 추가

### 학습

실제 데이터가 들어오자 샘플에서는 보이지 않던 중복, 연결 누락, 의미 분류 문제가 즉시 드러났습니다.

---

## 3. Data Aggregation → Connected Evidence

### 관찰

GitHub 기록을 수집하는 데 성공했지만 raw record가 빠르게 증가했습니다.

```text
많은 Activity
≠
좋은 Context Recovery
```

Decision과 Evidence가 연결되지 않으면 Activity Feed와 차이가 크지 않았습니다.

### 결정

다음 Chain을 핵심으로 전환했습니다.

```text
Discussion → Decision → Documentation → Implementation → Verification
```

### 학습

CHANGELOG의 핵심 가치는 Source를 한곳에 모으는 Aggregation보다 **기록 사이의 지속적인 Relation**에 더 가깝습니다.

---

## 4. Source ≠ Evidence Role

### 초기 가정

```text
GitHub → Implementation
Notion → Documentation
AI → Discussion
```

### 실제 문제

GitHub 안에서도 다음처럼 역할이 달랐습니다.

```text
docs: commit → Documentation
code commit → Implementation
Issue → Discussion
Actions → Verification
```

### 결정

Source와 Semantic Role을 별도 필드로 분리했습니다.

### 학습

서비스 이름으로 의미를 결정하면 Source가 늘어날수록 모델이 불안정해집니다.

---

## 5. PR ≠ 반드시 하나의 Change Set

### 초기 가정

PR을 구현의 대표 Change Set으로 사용하면 raw commit을 압축할 수 있다고 봤습니다.

### 실제 문제

긴 Alpha PR 하나 안에서도 여러 독립적인 제품 변화가 발생했습니다.

```text
Functional Alpha
Real-data Alpha
Connected Evidence
Candidate Engine
```

### 현재 가설

```text
PR = GitHub container
Change Set = 사람이 기억하는 의미 있는 변화 단위
```

현재는 PR grouping부터 구현하고, 향후 의미 기반 Change Set으로 확장합니다.

---

## 6. Candidate Recall보다 Relation Trust

### 관찰

후보 범위를 넓히면 관련 Evidence를 놓칠 가능성은 줄지만 무관한 Commit/PR까지 많이 노출됩니다.

### 결정

자동 연결 기준을 높이고 Human Confirmation을 유지했습니다.

```text
75+   높은 신뢰 / 제한 자동 연결
50-74 검토 권장
35-49 낮은 관련도
<35   숨김
```

### 학습

Evidence 제품에서는 "많이 맞히는 것"뿐 아니라 **틀렸을 때 사용자가 이유를 이해하고 수정할 수 있는 것**이 중요합니다.

---

## 7. 같은 날짜는 약한 신호

### 관찰

개발이 집중된 날에는 같은 프로젝트에서 많은 Commit과 CI가 발생합니다.

날짜가 가깝다는 이유만으로 높은 점수를 주면 서로 관련 없는 기록도 비슷한 후보가 됩니다.

### 결정

Candidate scoring에서 날짜 비중을 낮추고 다음 신호를 강화했습니다.

- 제목 의미 일치
- 기술 용어 일치
- Change Set 관계
- Semantic Role
- 향후 changed file/module 관계

---

## 8. Raw Evidence 보존 ≠ Raw Evidence 기본 노출

### 관찰

GitHub Actions의 동일 Workflow Run이 수십 개 존재할 수 있습니다.

DB 관점에서는 각각 실제 Evidence지만 UI에 모두 보여주면 사용성이 급격히 떨어집니다.

### 결정

- 원본 Evidence는 보존
- 동일 맥락 Candidate는 Grouping
- Graph에는 대표 Evidence 우선
- 낮은 관련도는 접기/페이지 처리

### 학습

CHANGELOG는 데이터를 줄이는 것이 아니라 **정보 손실 없이 기본 인지 부하를 줄이는 제품**이어야 합니다.

---

## 9. Seed 재실행 → Non-destructive Migration

### 관찰

Prototype code가 바뀔 때 seed를 다시 실행하면 사용자가 만든 Relation과 실제 GitHub Sync 결과가 사라집니다.

### 결정

Prototype에서도 schema migration과 curated fixture upsert를 분리했습니다.

### 학습

Dogfooding이 시작되는 순간부터 프로토타입 데이터도 단순 disposable seed가 아니라 **사용 기록 자체가 Evidence**가 됩니다.

---

## 현재 가장 중요한 검증

다음 단계에서는 기능 수를 늘리는 것보다 아래 질문의 답을 얻는 것이 중요합니다.

1. Change Set이 실제 기억 단위와 맞는가
2. Candidate 추천을 신뢰할 수 있는가
3. 대표 Evidence만으로도 충분히 과거 맥락이 복원되는가
4. Evidence가 없는 단계는 유용한 누락 신호인가
5. Ask CHANGELOG가 이 Relation을 사용했을 때 일반 검색/요약보다 유용한가

이 결과를 기반으로 Final ERD와 AI 계층을 설계합니다.
