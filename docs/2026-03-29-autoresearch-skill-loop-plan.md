# planning-skill / working-skill autoresearch 루프 적용

## 1. 요구사항 요약
- `planning-skill`과 `working-skill`이 `autoresearch`의 핵심 아이디어를 반영해 더 엄격한 반복 루프와 명확한 keep/discard 기준으로 계획 작성과 구현을 수행하도록 개선한다.
- 성공 기준은 두 스킬이 각각 자신의 산출물과 평가 하네스를 분리하고, 반복 검토 루프의 입력/출력/종료 조건을 더 명확히 안내하며, 관련 참조 문서와 `agents/openai.yaml`까지 일관되게 갱신되는 것이다.
- 그대로 복제하면 안 되는 제약은 `autoresearch`의 무한 루프, 무조건적인 `git reset`, 단일 수치 지표 집착이다. 이번 개정은 개념 차용에 한정하고, 문서/구현 업무에 맞는 안전한 종료 조건을 유지한다.

## 2. 확인한 공식 문서와 기술 판단
- Source: `karpathy/autoresearch`의 `README.md`, `program.md`
- Relevance: 이 저장소는 고정 평가 하네스, 제한된 변경 지점, keep/discard 반복, Git 기반 상태 전진이라는 아이디어를 가장 직접적으로 보여준다.
- Decision: 두 스킬에 `고정 하네스`, `허용된 변경 지점`, `반복 평가 루프`, `채택/폐기 기준`, `명시적 종료 조건`을 도입하되, 무한 실행 규칙은 제거한다.

- Source: `/Users/ckim/.codex/skills/.system/skill-creator/SKILL.md`
- Relevance: 기존 스킬을 개정할 때 `SKILL.md`, `references/`, `agents/openai.yaml`를 함께 다루고 검증까지 수행해야 하는 절차를 제공한다.
- Decision: 본문은 간결하게 유지하면서, 반복 루프의 핵심 규칙은 `SKILL.md`와 참조 체크리스트에 분산 배치하고 변경 후 검증을 수행한다.

- Source: `/Users/ckim/.codex/skills/.system/skill-creator/references/openai_yaml.md`
- Relevance: `agents/openai.yaml`의 `display_name`, `short_description`, `default_prompt` 제약을 정의한다.
- Decision: 두 스킬의 UI 메타데이터도 개정된 루프 철학이 드러나도록 갱신하되, 짧고 직접적인 문구를 유지한다.

## 3. 현재 프로젝트 작업 범위
- In scope: `planning-skill/SKILL.md`, `planning-skill/references/plan-template.md`, `planning-skill/references/review-checklists.md`, `planning-skill/agents/openai.yaml`, `working-skill/SKILL.md`, `working-skill/references/review-checklist.md`, `working-skill/agents/openai.yaml`, 새 계획 문서
- Existing behavior: `planning-skill`은 계획 확정 전 구현 금지와 체크리스트 기반 반복 검토를 유지해야 한다. `working-skill`은 plan-first 실행, 반복 review-fix, zero-finding 종료 원칙을 유지해야 한다.
- Dependencies: 로컬 스킬 구조, `skill-creator`의 검증 규칙, 저장소의 Git 상태
- Unknowns: 반복 루프를 별도 참조 문서로 분리할지 여부. 다음 확인 액션은 기존 `SKILL.md`와 체크리스트 수정만으로 충분한지 구현 중 다시 판단한다.
- Blockers: 없음

## 4. 구현 단계
1. `planning-skill`에 `autoresearch`식 운영 모델을 도입한다.
   결과물: 계획의 고정 하네스, 변경 가능한 산출물, keep/discard 기준, 종료 조건이 `SKILL.md`와 참조 체크리스트에 반영된다.
   검증: 문서만 읽어도 계획 초안 생성과 반복 검토 루프가 구분되는지 수동 리뷰한다.
2. `working-skill`에 구현 실험 루프를 도입한다.
   결과물: plan 기반 구현, 좁은 검증, 채택/폐기 판단, review-fix 재진입 규칙이 `SKILL.md`와 체크리스트에 반영된다.
   검증: 문서만 읽어도 first working state 이후 review-fix 루프와 keep/discard 기준이 분명한지 수동 리뷰한다.
3. 두 스킬의 참조 문서와 UI 메타데이터를 정합성 있게 조정한다.
   결과물: 체크리스트, 템플릿, `agents/openai.yaml`이 개정된 흐름과 충돌하지 않는다.
   검증: `quick_validate.py`로 기본 구조를 확인하고, 메타데이터가 `SKILL.md`와 의미상 일치하는지 확인한다.

## 5. 검증 계획
- `skill-creator`의 `quick_validate.py`로 두 스킬 폴더를 검증한다.
- 수정 후 `SKILL.md`, 참조 문서, `openai.yaml`를 교차 리뷰해 용어와 단계 순서가 일치하는지 확인한다.
- 수동 검토 시 다음 회귀를 중점 확인한다: 계획 단계에서 구현으로 너무 빨리 넘어가는 회귀, 구현 단계에서 무한 루프처럼 멈추지 않는 회귀, 과도한 절차 증가로 기존 사용성이 떨어지는 회귀.

## 6. 리스크와 대응
- 리스크: `autoresearch` 개념을 과하게 옮기면 스킬이 지나치게 경직되거나 무한 실행 성향을 띨 수 있다.
- 대응: 루프를 도입하되 종료 조건과 중단 기준을 명시하고, 문서/구현 작업에 맞는 판단 기준만 남긴다.
- 리스크: 계획/구현 스킬에 동일한 표현을 과도하게 재사용하면 역할 구분이 흐려질 수 있다.
- 대응: `planning-skill`은 계획 산출물 수렴, `working-skill`은 코드 변경 수렴에 초점을 분리한다.

## 7. Out of Scope
- 새 자동화 시스템, 별도 실행 스크립트, 실험 로그 저장 포맷 추가
- `autoresearch`처럼 무한 반복을 강제하는 정책 도입
- 스킬 외 다른 저장소나 전역 설정 수정
