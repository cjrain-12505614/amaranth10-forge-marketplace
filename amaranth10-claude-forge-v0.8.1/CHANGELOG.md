# Changelog

## v0.8.1 (2026-04-18)

### `a10-git-daily` 규약 보강 — 유지보수 분기 룰

초기 dry-run 결과 미태깅 100% + 박수연 `2682f550` 건(JIRA CSA10-40261)을
계기로 "유지보수 vs 프로젝트" 분류 룰을 명문화.

- `[MAINT]` / `[MAINT/CSA번호]` 프리픽스 추가 → 모듈 `history/_timeline.md` "유지보수 로그" 누적 테이블에 기록, PRJ 파일 미건드림
- 휴리스틱: JIRA CSA 티켓·운영성 키워드·유지보수 라인 담당자 → ⚠️ 유지보수 후보 라벨
- 규약 문서 보강: `_참조자료/프로세스/깃-커밋-메시지-규약.md` 3.4 "유지보수 vs 프로젝트 판단 기준"
- 스킬 파싱·출력 로직에 유지보수 분기 추가

---

## v0.8.0 (2026-04-18)

### `a10-git-daily` 스킬 + `/a10-git-daily` 커맨드 추가

Workspace_a10(소스) ↔ douzone-forge(기획·설계) 맥락 연계 자동화 스킬.
공통 식별자 `[PRJ-NNNN/TASK-CODE]`를 축으로 일일 git 커밋을 수집하여
PRJ 포트폴리오 `03. 상세 진행현황`과 모듈 `history/_timeline.md`에 자동 반영.
미태깅 커밋은 경고 리스트로 분리 보고(멱등성 보장, 추측 반영 금지).

- 스킬: `skills/a10-git-daily/SKILL.md`
- 커맨드: `commands/a10-git-daily.md`
- 지원 옵션: `--since`, `--weekly`, `--module`, `--repos`, `--dry-run`, `--include-personal`
- 보조 문서(douzone-forge): `_참조자료/프로세스/깃-커밋-메시지-규약.md` (팀 공지용)

**배경**: 2026-04-18 연계 체계 감사 결과 25칸 매트릭스 중 "TASK↔git" 고리가
5/5 전면 미구축으로 드러남. 이 스킬이 해당 고리를 자동화하고, 검수시나리오 ↔ 소스,
실시간 history 반영 등 후속 연계의 기초가 됨.

---

## v0.7.0 (2026-04-18)

### `a10-jira-query` 스킬 + `/a10-jira` 커맨드 추가

PRJ-2026-012 JIRA 연동 PoC (`amaranth10-jira-collector`, 김경엽 책임 배포 2026-04-17)
를 래핑한 조회 전용 스킬. Claude 세션 중 애드혹 JQL, 8종 프리셋, 일일 배치 결과
로드를 지원한다. 정식 JIRA MCP 전환(05/08 기획-15 이후) 전까지의 실증 단계.

- 스킬: `skills/a10-jira-query/` (SKILL.md + examples 3종 + lib/jira-wrapper.sh)
- 커맨드: `commands/a10-jira.md`
- 6가지 모드: default / 프리셋 / `--list` / `--jql` / `--read` / `--module` / `--ping`
- 조회 전용 (이슈 생성·수정·코멘트 금지), `.env` 비공개, 한글 JQL 필드 쌍따옴표 선검증

---

## v0.6.2 (2026-04-17)

### `a10-oneffice-comment` 스킬 + `/a10-post-comment` 커맨드 추가

ONEFFICE 문서에 멘션(@이름) 포함 댓글을 자동 게시하는 스킬. 멘션 자동완성 팝업이
신뢰된 네이티브 키 이벤트로만 뜨는 제약을 `type("@이름 ")` → `Backspace` 2회
패턴으로 우회한다. 2026-04-17 PRJ-005 분할 안내 댓글 작성에서 검증된 절차 표준화.

- 스킬: `skills/a10-oneffice-comment/SKILL.md`
- 커맨드: `commands/a10-post-comment.md`

---

## v0.6.1 (2026-04-16)

### 공개 마켓플레이스 배포 파이프라인 추가

- `build.sh --deploy` 시 `amaranth10-forge-marketplace` GitHub 레포에 자동 push
- 외부 사용자 설치: `claude plugin marketplace add --github cjrain-12505614/amaranth10-forge-marketplace`
- 버전업 테스트 릴리즈

---

## v0.6.0 (2026-04-16)

### `a10-oneffice-writer` ONEFFICE outline 자동 주입 대응 (0.3.0 유지, 수정 내장)

ONEFFICE(dzeditor) 가 블록 요소(`.doc-header`, `.section`, `.footer` 등)에
`outline: 1px dashed` 를 자동 주입하여 읽기모드에서 점선이 보이는 문제 대응.
Step 4 Python 전처리, Step 8 JS 주입, 디버깅 체크리스트, HTML 가공 원칙에 수정 내장.

- **Step 4 Python 스니펫** — `<style>` 내부 추출 후 `* { outline: none !important; }`
  자동 삽입. `style.group(0)` (태그 전체) vs `style.group(1)` (내부만) 혼동 방지를
  위해 `style_inner` 변수 분리.
- **Step 8 JS 주입** — `* { outline: none !important; }` 포함 `<style>` 태그를
  dzeditor `<head>` 에 동적 삽입. 편집모드 중 즉시 효과, 재저장 필요.
- **디버깅 체크리스트** — "읽기모드에서 점선이 보임" 증상·원인·즉시 수정법 추가.
- **HTML 가공 원칙** — "★ ONEFFICE 전용 필수 CSS" 섹션 신설 (outline, 다크→라이트,
  슬라이드형 min-height). 처음부터 HTML 작성 시 포함 권장.

### `/a10-oneffice-write` 커맨드 보강

- "치명적 주의" 에 outline 점선 증상 + 즉시 수정법 항목 추가.
- `.noext` 방지 규칙 항목 추가 (opener 경유, Step 10.5 확장자 육안 확인).

---

## v0.5.9 (2026-04-16)

### 플러그인 배포 인프라 전면 수정 + `/a10-plugin-save` 커맨드 추가

2026-04-16 배포 시스템 삽질 세션에서 발견한 구조적 문제 4건 수정.
`bash build.sh --deploy` 한 줄로 CoWork 자동 반영까지 완전 자동화 달성.

- **`/a10-plugin-save` 커맨드 신설** — CoWork에서 "플러그인 저장해줘",
  "업데이트해줘" 등 자연어 요청 시 `present_files` 로 저장 UI를 항상 띄움.
  빌드 → `present_files` 제시까지 원스톱.

### `build.sh` 개선

- **`claude plugin install` 자동 실행 추가** — `--deploy` 시 마켓플레이스
  갱신 후 `claude plugin install` 까지 실행. CoWork가 별도 조작 없이
  즉시 새 버전을 반영함 (UI "업데이트" 버튼 클릭 불필요).
- **`.claude-plugin/marketplace.json` zip 제외** — 플러그인 번들 내부에
  stale marketplace.json 이 패키징되던 문제 수정.
- **marketplace 갱신 경로 수정** — `_플러그인/marketplace.json` (top-level,
  EISDIR 에러) → `_플러그인/.claude-plugin/marketplace.json` (표준 위치).

---

## v0.5.6 (2026-04-15)

### `a10-oneffice-writer` 실전 교훈 반영 (0.2.0 → 0.3.0)

2026-04-15 로폼 5차 미팅 회의록 원피스 생성 세션에서 발생한 3대 문제
(`.noext` 확장자, 꺾쇠 1.3 배 오차, 탭 간 HTML 전달 블로킹) 재발 방지.
약 6회 왕복 재작업의 실전 교훈을 스킬 본문에 내장.

- **`.onex` 확장자 강제** — Step 0 을 "아마링크 navigate" 에서 "ONEFFICE 워드
  템플릿 버튼 클릭 경유" 로 교체. 상단 치명적 함정 목록에 0번 항목 추가.
  Step 10.5 `.onex` 홈 화면 육안 검증 + `.noext` 복구 절차 신설 (삭제 금지,
  localStorage 로 복제).
- **zoom 보정 필수화** — Step 7 에 `.dze_document_container` 의 `transform:
  matrix(zoom)` 직접 감지 추가. BCR(viewport px) vs CSS(unzoomed px) 단위
  혼동 경고 박스. "1.3 배 오차 무한 누적" 경고.
- **컨테이너 보존 주입 (모드 B) 을 기본으로 승격** — `<style>` + `.container`
  통째 보존, `.container` 하나에만 inline style. 원본 CSS 전부 보존.
  기존 플랫 주입은 `.container` 없는 HTML 전용 레거시 (모드 A) 로 강등.
- **Step 3.5 신설: 탭 간 HTML 복제 `localStorage['__doc_extract__']` 우회**
  — Chrome MCP 의 `[BLOCKED: Cookie/query string data]` 차단 회피. 기존
  원피스 문서 innerHTML 을 새 `.onex` 에 즉시 복제. `.slice()`, `btoa()`
  실패 사례 명시.
- **디버깅 체크리스트 4 항목 추가** — 1.3 배 오차 / 탭 간 블로킹 /
  `.noext` 증상 / 모드 A 주입 시 `.container` CSS 깨짐.
- **검증된 상수 요약 테이블 확장** — zoom 감지, 기본 zoom, 탭 간 전달,
  확장자 검증 방법 행 추가.

### `a10-oneffice-new-doc-opener` 보강 (0.1.0 유지)

- **`.noext` 방지 경고** — Step 4 하단에 "버튼 텍스트 정확히 `'ONEFFICE 워드'`
  매칭 유지, 느슨하게 바꾸지 말 것" 박스 추가.
- **Simple path** — Step 3 상단에 "빈 `.onex` 만 필요하면 payload/XHR swap
  스킵하고 버튼 클릭만" 경로 추가. writer Step 0 의 기본 경로로 권장.

---

## v0.5.5 (2026-04-15)

### 원피스(ONEFFICE) 쓰기 워크플로우 end-to-end 화

2026-04-15 법무관리 작업 중 확립된 실전 노하우를 스킬·커맨드로 내장.
**모든 스킬은 자체 완결적이며 외부 메모리 파일에 의존하지 않는다** — 검증된
셀렉터·좌표·프리셋 값·코드 스니펫을 SKILL.md 본문에 전부 임베드했다.

**변경: `a10-oneffice-writer` (0.1.0 → 0.2.0)**
- **Step 0 추가** — 새 문서 자동 생성 (`a10-oneffice-new-doc-opener` 호출)
- **Step 1.5 추가** — 단일페이지 모드 전환 (웹페이지형 긴 문서 필수)
- **Step 1.9 추가** — 편집모드 가드 (`main.isContentEditable === true`). 읽기모드 저장 silent no-op 함정 차단
- **Step 2 개선** — A4 세로/여백 보통/줌 130% 프리셋 캐시(`-1px / 644px`) 기본 적용, 실측은 fallback
- **Step 6 추가** — 문서명 변경 (native setter + dispatchEvent, React controlled input 대응)
- **Step 7 경고** — 저장 전 새로고침 금지 / 순서 엄수 명시
- **HTML 가공 원칙 명문화** — `.container` 강제 대신 `<body>` 전체 보존, script/nav/button/form만 제거 ("원본 그대로 주입해야 이쁘다" 원칙)
- description에 자동 인식 패턴 확장

**추가: 스킬 `a10-oneffice-new-doc-opener`**
- ONEFFICE HOME에서 새 워드 문서를 XHR body swap으로 생성
- 기존 빈 탭 재사용 체크 포함
- writer 스킬 Step 0에서 호출되며 단독 사용도 가능

**변경: `a10-oneffice-reader` (0.1.0 → 0.2.0)**
- 외부 워크스페이스 파일 의존 제거 (douzone-forge `CLAUDE.md`, `_참조자료/프로세스/아마링크-참조링크-운영규칙.md` 참조 삭제)
- 아마링크 URL 구조·모듈별 타이틀 규칙 13종 테이블 내장
- Chrome MCP 보안 차단 정리 테이블 내장 (쿼리스트링 차단 이유·회피 방법)
- 이미지 추출 방법 3종 (Canvas base64 / 메타데이터 분리 / overflow+스크린샷 폴백)
- 회수/삭제된 아마링크 처리, 조회 권한 정책 요약 포함
- 자체 완결 선언 — 이제 Peekly·SCU 등 다른 워크스페이스에서도 그대로 작동

**추가: 커맨드 `/a10-oneffice-write`**
- HTML/Markdown/자연어 → 원피스 워드 문서 end-to-end
- 옵션: `--long`/`--webpage`, `--title`, `--from`
- 자동 인식 패턴: "원피스로 ~작성해줘", "ONEFFICE 문서로 만들어줘", "웹페이지처럼 긴 원피스 문서", "~를 원피스 워드로 뽑아줘"

---

## v0.5.3 (2026-04-14)

### 추가: 원피스(ONEFFICE) 읽기/쓰기 스킬 2종

원피스 문서를 정확히 읽고 쓰는 표준 절차를 스킬로 내장. 2026-04-14 법무관리 4/15 미팅 준비
HTML 리포트를 원피스 빈 문서에 주입하는 과정에서 확립한 기법을 재사용 가능하게 일반화.

| 스킬 | 설명 |
|------|------|
| `a10-oneffice-reader` | 원피스 아마링크 본문 텍스트·표·이미지·내부 아마링크 추출 (javascript_tool DOM 접근 방식) |
| `a10-oneffice-writer` | 완성된 HTML을 원피스 편집모드 빈 문서에 주입 (꺾쇠 기반 폭 측정 + 인라인 style 주입) |

**핵심 원칙 (쓰기)**
1. `.dze_page_main` 구조 절대 건드리지 않음 (새로고침 시 리셋)
2. `dze_page_margin_indicator_*` 꺾쇠 좌표로 실제 컨텐츠 폭 측정
3. 클래스·`<style>` 태그 대신 **인라인 style 속성**만 사용 (저장 시 보존되는 유일한 경로)
4. 40KB+ HTML은 로컬 CORS 서버(`127.0.0.1:8765`)로 서빙 후 fetch 주입

**핵심 원칙 (읽기)**
1. 스크롤·스크린샷 반복 금지 → `innerText` 슬라이싱
2. 중첩 iframe: `#open_oneffice_body_iframe → #dzeditor_0 → body`
3. 이미지 추출은 Canvas → `toDataURL()` (img.src 직접 접근 불가)
4. 내부 아마링크는 자동 재귀 금지 — 사용자 확인 후 확장

---

## v0.5.2 (2026-03-29)

### 패치: 세션/현황 스킬 경로 수정 — solo-forge 잔재 제거

세션 시작/종료/현황 보고 기능이 solo-forge 시절의 `docs/00_관리/` 중앙화 구조를 참조하고 있어
douzone-forge의 모듈별 분산 구조와 불일치하는 문제를 수정.

### 수정된 커맨드 (3개)

| 커맨드 | 변경 내용 |
|--------|----------|
| `a10-start-session` | `docs/00_관리/sessions/` → `[모듈]/sessions/_current.md` + `_projects/_dashboard.md` |
| `a10-end-session` | `docs/00_관리/sessions/` → `[모듈]/sessions/_current.md` + `_projects/PRJ-*.md` 연동 |
| `a10-status` | `docs/00_관리/team_plan.md` → `_projects/_dashboard.md` + 모듈별 교차 조회 |

### 수정된 스킬 (2개)

| 스킬 | 변경 내용 |
|------|----------|
| `a10-session-protocol` | 전면 재작성 — 모듈별 `sessions/_current.md` 기반 시작/종료 프로토콜 |
| `a10-status-reporter` | 전면 재작성 — `_dashboard.md` + `PRJ-*.md` + 모듈별 교차 조회 |

---

## v0.5.1 (2026-03-29)

### 패치: 연쇄 업데이트 규칙(Cascade) 스킬 내장

맥락 구조 종합 점검(진단보고서 20260329)에서 발견된 "계층 간 맥락 단절" 문제를 해소하기 위해,
3개 핵심 스킬에 Cascade Update 규칙을 내장.

### 변경된 스킬 (3개)

| 스킬 | 변경 내용 |
|------|----------|
| `a10-context-analyzer` | 7단계 "연쇄 업데이트 (Cascade R1 + R2)" 추가 — context 생성 후 module-overview.md 갱신, PRJ 연결 정보 확인, 문서/INDEX.md 반영 |
| `a10-project-tracker` | 새 프로젝트 등록 9단계 "Cascade R2" 추가 — PRJ 04.연결정보에 context/, history/, INDEX.md, _tech-reference.md, Google Sheets 링크 |
| `a10-context-manager` | "연쇄 업데이트 규칙 (Cascade)" 섹션 신설 — R1(context→module-overview), R3(담당자 5파일 동시 갱신), R4(소스분석→douzone-forge) |

### 배경

douzone-forge CLAUDE.md에 R1~R5 연쇄 업데이트 규칙이 추가되었으나(v0.5.0 이후),
스킬 실행 시 자동으로 cascade를 수행하려면 각 스킬 SKILL.md에도 규칙이 내장되어야 함.

---

## v0.5.0 (2026-03-29)

### 주요 변경: 프로젝트 포트폴리오 관리 + 마크다운 링크 규칙

프로젝트를 포트폴리오 레벨에서 관리하는 체계를 추가.
모듈별 고도화, 크로스 모듈, 수명업무 등 모든 프로젝트 유형을 `_projects/` 폴더에서 통합 추적.

### 추가된 스킬 (+1)

| 스킬 | 설명 |
|------|------|
| `a10-project-tracker` | 프로젝트 포트폴리오 관리 — 대시보드, 프로젝트 등록/업데이트/완료, TASK↔상세진행현황 관계 |

### 추가된 커맨드 (+3)

| 커맨드 | 설명 |
|--------|------|
| `a10-projects` | 프로젝트 포트폴리오 대시보드 조회 (ID 지정 시 상세) |
| `a10-add-project` | 새 프로젝트 등록 (템플릿 기반) |
| `a10-update-project` | 프로젝트 진행현황 업데이트 (TASK + 일자별 로그) |

### 전체 스킬 공통 변경

- **마크다운 링크 규칙** 추가: 모든 스킬에서 경로·파일 참조 시 클릭 가능한 상대 링크 필수
- douzone-forge CLAUDE.md에 `## 마크다운 링크 표기 규칙` 섹션 추가
- douzone-forge CLAUDE.md에 `## 프로젝트 포트폴리오 관리` 섹션 추가
- 세션 시작 체크리스트에 `_projects/_dashboard.md` 확인 단계 추가

### 추가된 douzone-forge 파일

```
_projects/
├── _dashboard.md            ← 전체 프로젝트 포트폴리오 대시보드
├── _templates/
│   └── project-detail.md    ← 프로젝트 상세 템플릿
├── _archive/                ← 완료된 프로젝트 보관
└── PRJ-2025-001_가온-요구사항.md  ← 샘플 (가온 프로젝트)
```

---

## v0.4.0 (2026-03-23)

### 주요 변경: a10-project 플러그인 통합 + Claude Code 개발 지원

D-31 의사결정에 따라 a10-project 플러그인을 amaranth10-claude-forge에 통합.
회사 업무(Amaranth 10 모듈 운영 + 프로젝트 관리)를 단일 플러그인으로 지원.

### 추가된 스킬 (+8, 기존 a10-project에서 이관)

| 스킬 | 설명 |
|------|------|
| `a10-agent-dispatch` | 서브에이전트 팀 투입 (병렬/순차 작업 분배) |
| `a10-decision-tracker` | 의사결정 3단계 심각도 추적 및 관리 |
| `a10-deliverable-management` | 산출물 버전 관리, 명명 규칙, 품질 게이트 |
| `a10-figma-make-reviewer` | Figma Make ↔ 화면설계서 교차 검증 |
| `a10-lessons-learned` | 교훈·패턴 기록 및 재발 방지 |
| `a10-project-bootstrap` | CLAUDE.md 스캐폴드 + 워크스페이스 초기 설정 |
| `a10-session-protocol` | 세션 시작/종료 프로토콜 (컨텍스트 복원·저장) |
| `a10-status-reporter` | 프로젝트 현황 종합 보고 |

### 추가된 커맨드 (+13)

| 커맨드 | 설명 | 출처 |
|--------|------|------|
| `a10-decision` | 의사결정 기록/컨펌 요청 | a10-project |
| `a10-dev-instructions` | 현재 스프린트 구현 지시서 확인 | a10-project |
| `a10-dispatch` | 에이전트 투입 | a10-project |
| `a10-end-session` | 세션 종료 + 로그 저장 | a10-project |
| `a10-extract-design-tokens` | Figma Make 디자인 토큰 추출 | a10-project |
| `a10-guide` | 현재 상태 기반 다음 단계 안내 | a10-project |
| `a10-init-project` | 프로젝트 초기 설정 | a10-project |
| `a10-lesson` | 교훈 기록 | a10-project |
| `a10-review-figma` | Figma Make 검증 실행 | a10-project |
| `a10-start-session` | 세션 시작 프로토콜 | a10-project |
| `a10-status` | 프로젝트 현황 보고 | a10-project |
| `a10-tdd` | RED→GREEN→REFACTOR TDD 워크플로우 | 신규 |
| `a10-verify-step` | Step 완료 빌드/테스트/Enum 검증 | 신규 |

### 추가된 Hooks (+4, Claude Code 개발 품질 지원)

| Hook | 트리거 | 설명 |
|------|--------|------|
| `code-quality-reminder` | PostToolUse (Write/Edit) | Java/TS 파일 수정 시 품질 체크리스트 알림 |
| `security-auto-trigger` | PostToolUse (Write/Edit) | Security/JWT/Auth 파일 감지 시 보안 리뷰 유도 |
| `db-migration-guard` | PreToolUse (Write/Edit) | Flyway SQL에서 DROP TABLE/TRUNCATE 차단 |
| `build-verify-reminder` | PostToolUse (Write/Edit) | 5회 편집마다 빌드 검증 리마인더 |

### 추가된 Rules (+3, Claude Code 코딩 표준)

| Rule | 설명 |
|------|------|
| `verification` | 검증 없이 완료 선언 금지 — 실행 증거 필수 |
| `coding-standards` | Surgical Changes, Small Functions, Immutability 원칙 |
| `enum-consistency` | D-28 기반 Enum 5레이어 일관성 (DDL↔Entity↔DTO↔FE↔OpenAPI) |

### 네이밍 변경 (전체)

모든 스킬(18개)과 커맨드(27개)에 `a10-` 접두어 추가.
다른 플러그인(solo-forge, study-assistant 등)과의 네임스페이스 충돌 방지.

- 스킬: `context-manager` → `a10-context-manager` 등
- 커맨드: `load-context` → `a10-load-context` 등

### 통계

| 항목 | v0.3.0 | v0.4.0 | 변화 |
|------|--------|--------|------|
| 스킬 | 10 | 18 | +8 |
| 커맨드 | 14 | 27 | +13 |
| Hooks | 0 | 4 | +4 |
| Rules | 0 | 3 | +3 |
| Scripts | 3 | 3 | 0 |

---

## v0.3.0 (2026-03-21)

초기 안정화 버전. 모듈 맥락 관리(GNB/LNB), Google Sheets 연동, 문서 큐레이션, 세션 관리 기능 제공.
