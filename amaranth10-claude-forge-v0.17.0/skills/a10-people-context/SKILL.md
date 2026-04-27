---
name: a10-people-context
description: >
  This skill should be used when the user asks to "이 사람 진행 중", "{이름} 활동",
  "내 PRJ 현황", "사람 시점 대시보드", "{ID} 종합", or 사람 시점에서 정태 메타 +
  동태 활동 종합 조회 시.
  a10-people-index(정태 ID/사번/소속/R&R) + 진행 중 PRJ + 담당 모듈 + 트래킹 노트
  자동 lookup. Phase Q-2 G50 일관 — SBUnit 한정 표현 0.
version: 0.1.0
---

# 사람 컨텍스트 (a10-people-context) — Phase Q-3 Q-15 신설

사람 시점에서 정태 메타 + 동태 활동을 종합 lookup하는 스킬. a10-people-index(정태)와 워크스페이스 동태 데이터를 결합.

## 입력

- 사용자 ID (xlsx 13번 컬럼) 또는 사번 또는 한글 이름

## 출력 (4 영역)

### 1. 정태 메타 (a10-people-index 호출)
- ID·사번·이름·직급·소속 (4계층)
- R&R (담당 모듈·역할)

### 2. 진행 중 PRJ
- 조직 _index.md "진행 중 PRJ" 섹션 lookup (Q-3 Q-15 신설)
- PM 또는 담당자 매핑 PRJ 자동 추출 (`프로젝트/_dashboard.md` + 각 PRJ `01.B 담당자` 섹션)

### 3. 담당 모듈
- module-overview.md 담당자 매핑 lookup (Amaranth10/{모듈}/)
- Cell 리더라면 본인 Cell 담당 모듈 전체

### 4. 트래킹 노트 (본인이 트래킹 대상인 경우)
- `_개인/팀트래킹/{본인_ID}/` 노트 (있으면)
- 작성자(상위 관리자)·일자·주제 요약

## 의존 자산

- `skills/a10-people-index` (G8) — 정태 메타 (필수)
- `참고자료/조직정보/*.xlsx` — SSoT
- `프로젝트/_dashboard.md` — PRJ 포트폴리오
- `조직/{...}/{이름_사번}/_index.md` — 26명 진행 중 PRJ 섹션 (Q-3 Q-15 보강)

## 실행 흐름

1. 입력 ID/사번/이름 매칭 → a10-people-index 호출
2. 4 영역 lookup 동시
3. 종합 출력 (마크다운 형식)

## 출력 예시

```
=== a10-people-context: cjrain (차민수) ===

[정태 메타]
- ID: cjrain / 사번: B2772
- 직급: 수석연구원
- 소속: DWP개발본부 / SBUnit / Unit장
- R&R: SBUnit 전체 총괄 + Forge 구축 PM

[진행 중 PRJ]
- PRJ-2026-014: Amaranth10-Claude-Forge 구축 (PM)
- PRJ-2026-013: 기술위원회 AI개발툴 TFT (UC 트랙 PM)
- PRJ-2026-020: Douzone AI Radar (협업)

[담당 모듈]
- (Unit장 — 6 모듈 전체 총괄)

[트래킹 노트]
- 본부장-정현수: _개인/팀트래킹/B2772/ 노트 (있으면)
```

## 사용 시점

| 시점 | 사용 |
|------|------|
| 본인 종합 현황 조회 | "내 PRJ 현황" |
| 부하 직원 종합 조회 | "{이름} 활동" |
| 신규 인원 R&R 파악 | "{ID} 종합" |
| Cell 리더 멤버 점검 | a10-personal-tracking 작성 전 사전 lookup |

## 갱신 정책

- 정태 메타 변경 시 → a10-people-index SSoT 갱신만
- 동태 활동 변경 시 → 조직 _index.md "진행 중 PRJ" 섹션 + dashboard.md 동시 갱신 (R3 캐스케이드)

## 관련 자산

- `skills/a10-people-index` (G8) — 정태 lookup (의존)
- `skills/a10-personal-tracking` (G10, Q-14) — 트래킹 노트 작성
- `commands/a10-personal-init` (Q-12) — 4계층 lookup + 팀트래킹 자동

## 변천사

- Phase Q-3 Q-15 (2026-04-27) — 본 스킬 신설 + 조직 _index.md 26명 "진행 중 PRJ" 섹션 보강
- 자비스 v1 §A8 일관 — 단기 정태 _index.md / 중기 a10-people-context 동태
