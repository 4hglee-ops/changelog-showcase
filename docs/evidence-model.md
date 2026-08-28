# Evidence Model

## 핵심 개념

CHANGELOG의 데이터 모델은 단순 Activity 수집보다 **Decision을 설명하는 Evidence Chain**을 중심으로 설계합니다.

```text
Activity
  ↓
Evidence
  ↓
Decision
  ↓
Relation
  ↓
Change Set / Verification
```

현재 Alpha Schema는 최종 ERD가 아니라 실제 사용을 통해 필요한 필드를 검증하기 위한 Prototype Schema입니다.

> **Prototype Schema ≠ Final ERD**

---

## Source와 Semantic Role 분리

실제 데이터를 적용하면서 가장 중요하게 발견한 점 중 하나입니다.

Source는 기록이 **어디에서 왔는지**를 나타냅니다.

```text
GitHub
Notion
AI Conversation
```

Semantic Role은 기록이 **Decision을 설명하는 데 어떤 역할을 하는지**를 나타냅니다.

```text
Discussion
Documentation
Implementation
Verification
```

따라서 같은 GitHub 기록도 역할이 다를 수 있습니다.

| Source | 예시 | Semantic Role |
| --- | --- | --- |
| GitHub | Issue discussion | Discussion |
| GitHub | `docs:` commit | Documentation |
| GitHub | source code commit | Implementation |
| GitHub Actions | successful workflow | Verification |
| Notion | Decision Log | Documentation |
| AI Conversation | 설계 논의 | Discussion |

이 분리는 향후 AI 분류·검색·Graph 표현에서 중요한 기반이 됩니다.

---

## Evidence Level

Alpha에서는 Evidence Chain의 역할을 빠르게 확인하기 위해 L1~L4 표현을 사용합니다.

| Level | 의미 | 대표 예시 |
| --- | --- | --- |
| L1 | Discussion | AI conversation, Issue discussion |
| L2 | Documentation | Notion decision, docs commit |
| L3 | Implementation | PR, code commit, migration |
| L4 | Verification | CI success, test result |

Evidence Level은 Source의 신뢰도 순위가 아니라 **현재 Decision Chain에서의 역할 단계**에 가깝습니다.

---

## Decision

Decision은 단순 Activity와 분리해서 관리합니다.

대표 필드:

```text
Decision
├─ title
├─ why
├─ alternative
├─ summary
├─ status
├─ occurred_at
├─ original_url
└─ provenance
```

중요한 점은 결과만 저장하지 않고 `왜`, `어떤 대안`, `어떤 근거`를 함께 보존하는 것입니다.

---

## Relation

현재 검증 중인 대표 Relation은 다음과 같습니다.

```text
Discussion   ─ informed_by ─→ Decision
Documentation ─ documented_by → Decision
Decision ─ implemented_by → Implementation
Decision ─ verified_by → Verification
Evidence ─ supports → Decision
```

실제 제품에서는 방향과 명칭을 Final ERD 단계에서 다시 정규화할 수 있습니다.

Alpha에서는 사용자가 Relation을 이해하고 수정할 수 있는지 먼저 검증합니다.

---

## Provenance

CHANGELOG에서 AI가 만든 요약과 실제 원본을 혼동하지 않도록 provenance를 핵심 필드로 둡니다.

예시:

```text
provenance = real
import_mode = github_api
original_url = GitHub URL
external_id = commit SHA / PR ID / workflow ID
imported_at = timestamp
```

Notion Snapshot, Discussion Snapshot도 가져온 방식과 한계를 구분해 표시합니다.

```text
REAL · GITHUB API
REAL · NOTION SNAPSHOT
REAL · DISCUSSION SNAPSHOT
```

특히 Discussion Snapshot에 원본 대화 URL이 없으면 이를 실제 원본처럼 표현하지 않습니다.

---

## Change Set

raw GitHub record는 너무 세밀할 수 있습니다.

```text
commit A
commit B
commit C
PR
CI run 1
CI run 2
```

사용자가 기억하는 변화 단위는 다음과 더 가깝습니다.

```text
Change Set
"Connected Evidence Candidate Engine"
├─ commits
├─ PR container
└─ verification runs
```

현재 Alpha는 PR 기반 grouping에서 시작해 이 개념을 검증합니다.

향후 고려할 신호:

- PR membership
- Commit message semantics
- changed file/module
- Decision keyword/context
- 시간적 근접성
- CI 관계

---

## Relation Candidate Review

자동 Relation을 바로 확정하지 않고 후보 상태를 별도로 관리합니다.

```text
Candidate
├─ decision_id
├─ evidence_id
├─ score
├─ suggested_relation_type
├─ reasons
└─ review action
    ├─ accepted
    └─ ignored
```

이 구조는 향후 규칙 기반 추천을 Embedding/LLM 기반 추천으로 바꿔도 사용자 확인 흐름을 유지할 수 있게 합니다.

---

## Final ERD 전에 검증할 것

1. Activity와 Evidence를 분리할 가치가 실제로 있는가
2. Source와 Semantic Role 분리가 충분한가
3. Change Set을 별도 Entity로 유지해야 하는가
4. Decision ↔ Evidence Relation 방향과 타입이 자연스러운가
5. Provenance에 어떤 필드가 필수인가
6. AI 추천과 사용자 확정을 어떻게 구분할 것인가
7. 삭제/Source 연결 해제 시 파생 Evidence를 어떻게 처리할 것인가

이 질문에 답한 후 정식 Supabase PostgreSQL 모델로 이동할 계획입니다.
