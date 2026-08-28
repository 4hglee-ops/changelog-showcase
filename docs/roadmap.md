# Roadmap

CHANGELOG의 로드맵은 기능 수를 인위적으로 제한하기보다 **사용자 가치, 선행 의존성, 구현 품질, 검증 가능성**에 따라 순서를 정합니다.

---

## P0 — Connected Context

목표: 흩어진 작업 기록을 프로젝트 맥락 안에서 연결합니다.

```text
Project
→ Source
→ Activity
→ Timeline
→ Decision
→ Evidence
→ Original Source
→ Context Graph
```

현재 상태:

- GitHub API Sync 구현
- Notion Snapshot 기반 Prototype ingestion
- Timeline / Decision / Evidence 구현
- Connected Evidence Graph 구현
- Provenance / Original Source 구현

---

## P1 — Evidence Intelligence

목표: 많은 raw record에서 의미 있는 판단 근거를 더 잘 찾고 연결합니다.

```text
Semantic Classification
→ Change Set
→ Relation Candidate
→ Human Confirmation
→ Evidence Chain Quality
```

현재 검증 중:

- Source와 Semantic Role 분리
- 규칙 기반 Candidate scoring
- Candidate grouping / dedupe
- PR 기반 Change Set
- Conservative auto-linking

다음 후보:

- changed file / module signal
- Decision 주변 이웃 Evidence 활용
- 의미 기반 Change Set clustering
- Relation confidence / explanation
- 잘못된 Relation 수정/해제 UX

---

## P2 — Personal Evidence AI

목표: 축적된 Evidence Chain을 자연어로 탐색하고 장기 Context Recovery에 사용합니다.

```text
Ask CHANGELOG
→ Evidence Retrieval
→ Hybrid Search
→ RAG
→ Evidence-grounded Answer
```

예상 질문:

- 왜 이 구조를 선택했지?
- 이 기능을 만들 때 어떤 대안을 검토했지?
- 이 결정은 어떤 PR에서 구현됐지?
- 테스트/CI로 검증되지 않은 결정은 무엇이지?
- 지난달 이 프로젝트에서 가장 중요한 변화는 무엇이었지?

AI 답변은 가능한 경우 Original Evidence를 함께 제시하는 것을 기본 원칙으로 합니다.

---

## P3 — Evidence Reuse & Output

목표: 축적한 개발 Evidence를 다양한 결과물로 재사용합니다.

```text
Connected Evidence
→ Narrative
→ Impact
→ Pattern
→ Reusable Output
```

예시:

- Project Story
- 주간/월간 회고
- 기술 의사결정 기록
- Handoff
- Portfolio
- Resume bullet
- Interview story
- 개인 CHANGELOG

---

## 정식 데이터 모델 전환

Connected Evidence Alpha의 실제 사용 결과를 기반으로 Final ERD를 확정한 뒤 정식 저장소로 전환할 계획입니다.

예상 방향:

- Supabase PostgreSQL
- Supabase Auth / RLS
- Source Adapter
- provenance-aware ingestion
- background sync
- pgvector / retrieval layer
- AI service abstraction

하지만 이 구성은 Alpha에서 검증되지 않은 요구를 미리 고정하지 않기 위해 현재는 계획 단계로 구분합니다.

---

## 현재 Milestone

### Connected Evidence Alpha

완료 기준은 단순 기능 개수가 아닙니다.

- 실제 raw record가 충분히 들어오는가
- Decision 주변 Evidence를 의미 있게 압축할 수 있는가
- Relation Candidate의 오탐/누락이 감당 가능한가
- Change Set이 실제 기억 단위에 가까운가
- Evidence Chain만 보고 판단 맥락이 복원되는가
- Original Source / provenance를 신뢰할 수 있는가

이 조건을 실제 사용으로 확인한 뒤 다음 단계로 이동합니다.
