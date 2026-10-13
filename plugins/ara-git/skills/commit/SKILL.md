---
name: commit
description: 프로젝트의 Git Conventions(CLAUDE.md, commitlint 설정, 기존 커밋 로그)에 따라 변경 파일을 커밋한다. 빌드/린트 검증과 code-review 스킬 검토를 거친 뒤 커밋 메시지 컨벤션에 맞춰 커밋한다.
disable-model-invocation: true
user-invocable: true
---

프로젝트의 Git Conventions에 따라 변경 파일을 커밋해줘. push는 하지 마.
.gitignore 파일은 항상 커밋에서 제외해줘.

1. **검증 명령 결정**: CLAUDE.md에 커밋 전 검증 명령이 적혀 있으면 그대로 따라줘. 없으면 lockfile로 패키지 매니저를 정해줘(`pnpm-lock.yaml` → pnpm, `yarn.lock` → yarn, `package-lock.json` → npm). `package.json`에 `build`·`lint` 스크립트가 있는 것만 실행하고, 없는 스크립트는 건너뛴다고 알려줘. 패키지 매니저가 없는 프로젝트면 CLAUDE.md를 따르고, 그것도 없으면 검증 없이 진행한다고 알려줘.
2. **검증 실행**: 1에서 정한 명령을 실행해줘. 실패 시 즉시 중단하고 원인을 알려줘. 임의로 수정하지 마.
3. **코드 검토**: 검증 통과 후, 커밋하기 전에 `code-review` 스킬(effort: medium)로 스테이징된 변경사항(`git diff --cached`)을 correctness 관점에서 검토해줘. 이슈가 발견되면 내용을 보여주고 계속 커밋할지 먼저 고칠지 물어봐줘 — 자동으로 커밋을 막거나 자동으로 코드를 고치지 마. 이슈가 없으면 그대로 다음 단계로 진행해줘.
4. **커밋 메시지 형식 결정**: 아래 순서로 찾은 첫 번째 규칙을 따라줘.
   1. CLAUDE.md의 Git Conventions (예: `<type>(이슈키): 설명`처럼 Jira 이슈 키를 요구하는 경우)
   2. commitlint 설정 (`.commitlintrc*`, `commitlint.config.*`)의 type 목록과 scope 규칙
   3. `git log --oneline -20`에서 보이는 기존 커밋 스타일
   4. 위 셋이 모두 없으면 `type: 설명` — type(`feat`/`fix`/`docs`/`style`/`refactor`/`chore` 등)만 영문, 설명은 한글. 예) `feat: SEO를 위한 OG metadata 추가`

   이슈 키나 스코프는 규칙이 요구할 때만 붙여줘.
5. **커밋**: husky 등 git hook(`pre-commit`, `commit-msg`)이 활성화되어 있으면 훅이 다시 검증해. 훅이 실패하면 원인을 알려주고 직접 고쳐서 새로 커밋해줘 — `--no-verify`로 훅을 건너뛰지 마. 훅이 비활성화되어 있으면 4의 형식을 직접 확인해줘.

$ARGUMENTS
