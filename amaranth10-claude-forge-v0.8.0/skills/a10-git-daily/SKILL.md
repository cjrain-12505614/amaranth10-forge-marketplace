---
name: a10-git-daily
description: >
  Workspace_a10의 17개 SBUnit 관리 레포에서 전일(또는 지정 기간) git 커밋을
  수집하여 douzone-forge의 프로젝트 포트폴리오(_projects/PRJ-*.md)와 모듈
  히스토리(history/_timeline.md)에 자동 반영하는 브릿지 스킬.
  "개발 현황 업데이트해줘", "어제 커밋 뭐 들어왔어?", "깃 일일분석 돌려줘",
  "PRJ-2026-010 진행현황 업데이트", "모듈별 이번 주 커밋 요약" 요청 시 사용.
  커밋 메시지의 [PRJ-NNNN/TASK-CODE] 프리픽스를 파싱하여 해당 프로젝트 파일의
  "03. 상세 진행현황" 섹션에 일자별 로그를 append하고, 미태깅 커밋은 경고로
  분리 보고한다.
version: 0.1.0
---

# Git 일일 분석 브릿지 (A10 Git Daily Bridge)

Workspace_a10(소스) ↔ douzone-forge(기획·설계) 맥락 연계를 유지하기 위한
일일 git 분석 자동화 스킬. 공통 식별자 `[PRJ-NNNN/TASK]`를 축으로 두 워크스페이스를
양방향으로 연결한다.

## 전제 조건

- **듀얼 마운트 환경**: `/Users/cjrain/Workspace_a10/`(소스)와
  `/Users/cjrain/Workspace/douzone-forge/`(기획·설계)가 모두 접근 가능해야 함
- **커밋 메시지 규약**: `[PRJ-NNNN/TASK-CODE] 내용` 또는 `[PRJ-NNNN] 내용` 프리픽스
  - 예: `[PRJ-2026-010/개발-P1-03] 문서 검색 API 추가`
  - 예: `[PRJ-2026-006] 영업활동 조회 수정`
- **브랜치 범위**: 기본적으로 `develop`와 `devqa`만 분석 (개인 `devqa-{사번}`·`dev-{사번}`는 제외)

## 실행 흐름

### 1단계: 수집 범위 결정

- 인수 없음: 어제 00:00 ~ 오늘 실행 시점
- `--since 2026-04-15`: 지정일부터
- `--module 법무관리`: 특정 모듈 레포만
- `--repos amaranth10-lte,klago-ui-lte-micro`: 개별 레포 지정
- `--weekly`: 직전 7일
- `--include-personal`: `devqa-{사번}`·`dev-{사번}` 브랜치 포함 (기본 제외)

### 2단계: 레포별 git fetch + log

17개 SBUnit 관리 레포 순회 (모듈별 그룹):

| 모듈 | BE | FE | MCP | 기타 |
|---|---|---|---|---|
| 법무관리 | `amaranth10-lte` | `klago-ui-lte-micro` | `amaranth10-law-mcp` | — |
| CRM | `amaranth10-crm`, `amaranth10-crmgw` | `klago-ui-crm-micro` | `amaranth10-crm-mcp` | `klago-crm-mobile` |
| 업무관리 | `amaranth10-kiss` | `klago-ui-kiss-micro`, `klago-ui-kiss-universal`, `klago-pub-kiss-universal` | — | — |
| 게시판 | `amaranth10-board` | `klago-ui-board-micro` | `amaranth10-board-mcp` | — |
| 통합연락처 | `amaranth10-ab` | `klago-ui-ab-micro` | `amaranth10-ab-mcp` | — |
| 퍼블리싱 | — | `klago-ui-micro`, `klago-pub-gw`, `klago-pub-www`, `klago-pub-poc` | — | — |

각 레포에서 실행:
```bash
cd /Users/cjrain/Workspace_a10/{레포}
git fetch --all --prune
git log origin/develop --since="{start}" --until="{end}" \
  --pretty=format:'%H%x09%an%x09%ai%x09%s' --no-merges
git log origin/devqa --since="{start}" --until="{end}" \
  --pretty=format:'%H%x09%an%x09%ai%x09%s' --no-merges
```

머지 커밋은 `Merge branch 'devqa' into 'sqa'` 패턴을 별도 탐지하여 "승급 이벤트"로 분류.

### 3단계: 커밋 파싱

각 커밋 메시지에서 정규식으로 식별자 추출:
```
^\[PRJ-(\d{4}-\d{3})(?:/([^\]]+))?\]\s*(.+)$
```

- Group 1: PRJ ID (예: `2026-010`)
- Group 2: TASK 코드 (선택, 예: `개발-P1-03`)
- Group 3: 커밋 제목

매칭 실패 시 → **미태깅 커밋 리스트**로 별도 분류(경고 대상).

### 4단계: 레포트 생성

`/Users/cjrain/Workspace_a10/_doc/git-daily/YYYYMMDD.md` 생성 (덮어쓰기):

```markdown
# Git 일일 분석 — 2026-04-18

> 범위: 2026-04-17 ~ 2026-04-18 (어제)
> 분석 레포: 17개 / 총 커밋: 42건 (태깅 38 / 미태깅 4)

## 모듈별 활동

### 법무관리 (12건)
- [PRJ-2025-001/개발-24] 사건등록 팝업 유효성 추가 (박현경, amaranth10-lte, develop) — a1b2c3d
- [PRJ-2026-005/기획-07] WBS 재작성 반영 (유지수, klago-ui-lte-micro, devqa) — e4f5g6h
- ...

### CRM (9건)
...

## 브랜치 승급 이벤트
- amaranth10-lte: devqa → sqa (3건 포함) @ 2026-04-17 18:42
- ...

## ⚠️ 미태깅 커밋 (4건)

다음 커밋은 `[PRJ-NNNN/TASK]` 프리픽스가 없어 PRJ 파일에 반영되지 못했습니다:

| 레포 | 브랜치 | 저자 | 시각 | 메시지 | SHA |
|---|---|---|---|---|---|
| amaranth10-kiss | develop | 김승현C | 17:22 | 메일 본문 노출 임시 수정 | 7h8i9j0 |
| ... |

**조치 필요**: 해당 커밋의 저자에게 TASK 매핑 확인 필요.
```

### 5단계: douzone-forge 크로스포스트

**A. PRJ 파일 진행현황 append**

PRJ별로 커밋을 그룹화하여 `_projects/PRJ-{ID}.md`의 **`## 03. 상세 진행현황`** 섹션
맨 위에 일자별 블록으로 append (최신순 유지):

```markdown
### 2026-04-18 (git-daily 자동 반영)
- **[개발-24]** 사건등록 팝업 유효성 추가 — 박현경 @ amaranth10-lte develop
- **[개발-25]** 상담목록 정렬 버그 수정 — 김용훈 @ amaranth10-lte devqa ✅ 설계검수 진입
```

**B. 모듈 history/_timeline.md append**

각 모듈 `{모듈}/history/_timeline.md`에 일자별 섹션 추가 (해당 모듈 커밋이 있는 경우만):

```markdown
## 2026-04-18 (git-daily)
- amaranth10-lte develop: 3건 (박현경 2, 김용훈 1)
- klago-ui-lte-micro devqa: 1건 (유지수) ✅ 설계검수
- 상세: [Workspace_a10/_doc/git-daily/20260418.md](...)
```

**C. sessions/_current.md 업데이트**

모듈별 `sessions/_current.md`가 존재하면 "최근 git 활동" 섹션에 요약 갱신.

### 6단계: 최종 보고

사용자에게 한 화면 요약:
```
📊 Git 일일 분석 완료 (2026-04-18)
- 분석 레포 17개 / 커밋 42건
- PRJ 반영: 8개 프로젝트 진행현황 갱신
  · PRJ-2025-001(가온): +2건 / PRJ-2026-010(법무MCP): +5건 / ...
- ⚠️ 미태깅 커밋 4건 → 저자 확인 필요 (위 레포트 참조)
- 브랜치 승급: devqa→sqa 1건 (amaranth10-lte)
```

## 출력 규칙

- **"예정"/"대기" 표현 금지**: 실제 커밋된 것만 "반영 완료"로 표기
- **추측 금지**: 메시지 프리픽스 없으면 미태깅으로 분리. TASK 임의 매핑 ❌
- **시제 정확**: 커밋 날짜 기준 과거형. 머지만 된 경우 "승급" / 실제 코드 변경은 "반영"으로 구분
- **중복 방지**: PRJ 파일에 이미 같은 커밋 SHA가 기록되어 있으면 스킵 (멱등성)

## 에러 처리

- `git fetch` 실패 (네트워크·인증) → 해당 레포 스킵, 경고 리스트에 추가
- PRJ 파일 부재 (커밋이 신규 PRJ를 참조) → 미태깅과 별도로 "**⚠️ 누락된 PRJ 파일**" 섹션에 기록
- 저자명이 조직도에 없는 사람 → `_참조자료/조직/더존비즈온-조직구조.md` 업데이트 권고

## 보조 스킬 연계

- 결과를 바탕으로 `/a10-status` 실행 시 "미태깅 커밋 N건 경고" 자동 포함
- `/a10-start-session` 훅에서 전일 git-daily 파일 자동 로드 (있는 경우)
- 주간 합산은 `--weekly` 옵션으로 생성 → PRJ별 주간 리포트 소스

## 규약 명문화

이 스킬이 전제로 하는 커밋 메시지 규약은 별도 가이드에 명시한다:
- 개발 가이드: `_참조자료/프로세스/깃-커밋-메시지-규약.md` (신규 작성 권고)
- 브랜치 명명 규약: `feature/PRJ-NNNN-TASK-CODE-short-desc`
- MR 제목 규약: `[PRJ-NNNN/TASK-CODE] 제목`
