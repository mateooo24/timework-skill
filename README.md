# Forced Feedback Loop

AI 에이전트가 주어진 시간 동안 절대 멈추지 않고, 스스로 피드백 루프를 돌며 작업 품질을 끌어올리는 스킬.

## 이런 문제를 해결합니다

- AI가 대충 한 번 훑고 "끝났습니다" 하고 멈추는 현상
- "다음에 이렇게 해줄게요" 같은 말만 번지르르한 제안
- 작업 내역이 컨텍스트에만 남아서 세션이 끝나면 증발하는 문제
- 자기가 내린 결론을 검증 없이 확정하는 확증 편향

## 핵심 원리

1. **시간이 끝날 때까지 절대 멈추지 않는다** — `min_required_minutes` 동안 능동적 작업만 수행
2. **매 루프마다 새로운 가설을 만든다** — 기존 정보를 조합해서 아직 없던 제안을 생성
3. **모든 것을 파일에 기록한다** — `work-log.md`에 역순 리포트로 누적, 세션 간 연속성 보장

## 파일 구조

```
forced-feedback-loop/
├── SKILL.md                   # 핵심 실행 정책 (에이전트가 활성화 시 로드)
└── references/
    ├── DOMAIN-TABLES.md       # 도메인별 상세 테이블 (필요 시 lazy load)
    └── PROHIBITIONS.md        # 24개 금지 항목 (필요 시 lazy load)
```

## 지원 도메인

| task_type | 용도 |
|---|---|
| `research` | 조사, 리서치 |
| `code` | 개발, 엔지니어링 |
| `design` | UI/UX 디자인 |
| `document` | 문서 작성 |
| `analysis` | 데이터 분석 |
| `project` | 프로젝트 관리 |
| `other` | 기타 |

도메인에 따라 증거 유형, 소스 계층, 태그 체계, 테스트 방법이 자동으로 적응합니다.
상세 매핑은 `references/DOMAIN-TABLES.md`에 정의되어 있습니다.

## 사용법

### 설치

다운로드한 zip을 해제하고, 사용하는 에이전트의 스킬 디렉터리에 배치합니다.

| 에이전트 | 경로 |
|---|---|
| Perplexity | User Settings에서 업로드 |
| OpenAI Codex | `~/.codex/skills/forced-feedback-loop/` |
| Claude Code | `~/.claude/skills/forced-feedback-loop/` |
| Augment | `~/.augment/skills/forced-feedback-loop/` |
| VS Code (Copilot) | `.agents/skills/forced-feedback-loop/` (프로젝트 루트) |

### 실행 예시

**리서치 작업:**
```
30분 동안 "한국 전기차 배터리 시장 동향"을 조사해줘.
```

**코드 개발:**
```
20분 동안 이 API 서버의 응답 속도를 개선해. 현재 p99 latency가 500ms야.
```

**디자인:**
```
15분 동안 이 대시보드 레이아웃의 사용성을 개선해줘.
```

**문서 작성:**
```
25분 동안 이 기술 제안서의 논증 구조를 강화해줘.
```

시간을 명시하면 에이전트가 자동으로 스킬을 활성화하고, 해당 시간이 끝날 때까지
피드백 루프를 돌립니다.

## 에이전트가 생성하는 산출물

실행이 끝나면 다음 파일들이 작업 디렉터리에 생성됩니다:

| 파일 | 내용 |
|---|---|
| `work-log.md` | 전체 작업 이력. 역순 리포트 스택, 줄 수 기반 점진적 읽기 지원 |
| `working-raw.md` | 아직 검증되지 않은 아이디어, 초안, 브레인스토밍 |
| `proved-archive.md` | 테스트 후 기각된 항목 (기각 사유 포함) |
| `proved-curated.md` | 검증 완료된 항목, 불확실성/갭 레지스터 포함 |

### work-log.md 읽는 법

리포트가 위에서부터 최신순으로 쌓입니다. 각 리포트의 첫 줄에 해당 리포트의 줄 수가 적혀 있어서,
필요한 만큼만 단계적으로 읽을 수 있습니다.

```
=== Report #3 | lines: 12 | elapsed: 14:30 | type: feedback ===
(이 리포트는 12줄)
=== Report #2 | lines: 8 | elapsed: 10:00 | type: milestone ===
(이 리포트는 8줄)
...
```

과거 리포트를 참조할 때는 상대좌표(`K-reports-below`, `line N below`)를 사용하므로,
로그가 정리되거나 압축되어도 참조가 깨지지 않습니다.

## 멀티 세션 사용

이전 세션의 `work-log.md`가 남아 있으면, 다음 세션에서 이어서 작업할 수 있습니다.
에이전트가 점진적 읽기 프로토콜로 이전 기록을 복원하고, 이어서 피드백 루프를 돌립니다.

```
어제 작업하던 배터리 시장 리서치를 이어서 20분 더 해줘.
```

## 호환성

[Agent Skills 오픈 스펙](https://agentskills.io/specification)을 따르며,
이 스펙을 지원하는 모든 에이전트에서 동작합니다.
