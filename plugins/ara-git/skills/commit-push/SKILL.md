---
name: commit-push
description: 프로젝트의 Git Conventions에 따라 변경 파일을 커밋한 뒤 현재 브랜치를 push한다. 팀 프로젝트면 main/develop 직접 push를 막고, 1인 프로젝트면 main에도 바로 push한다. 빌드/린트 검증과 code-review 스킬 검토를 거친다.
disable-model-invocation: true
user-invocable: true
---

프로젝트의 Git Conventions에 따라 커밋 후 현재 브랜치를 push해줘.
.gitignore 파일은 항상 커밋에서 제외해줘.

1. **협업 모드 판별**: 아래 순서로 판별하고, 결과(`team` 또는 `solo`)와 근거를 한 줄로 먼저 알려줘.
   1. CLAUDE.md에 `협업 모드: solo` 또는 `협업 모드: team`이 적혀 있으면 그대로 따른다.
   2. 없으면 `gh api repos/{owner}/{repo}/collaborators --jq length`로 협업자 수를 확인한다. 2명 이상이면 team, 1명 이하이면 solo.
   3. `gh` 호출이 실패하면 `git shortlog -sne --all`의 작성자 수로 판단한다(같은 사람의 다른 이메일은 한 명으로 본다). 2명 이상이면 team, 1명 이하이면 solo.
2. **브랜치 확인**
   - team: 현재 브랜치가 `main`이나 `develop`이면 즉시 중단하고 알려줘 (작업 브랜치로 이미 전환돼 있어야 함 — 새 브랜치를 대신 만들지는 마).
   - solo: 브랜치 분기 없이 `main`을 포함한 현재 브랜치에 바로 커밋·push해도 돼. CLAUDE.md의 "main, develop 브랜치 직접 push 금지" 같은 규칙은 1인 프로젝트에는 적용하지 않아.
3. **검증 명령 결정**: CLAUDE.md에 커밋 전 검증 명령이 적혀 있으면 그대로 따라줘. 없으면 lockfile로 패키지 매니저를 정해줘(`pnpm-lock.yaml` → pnpm, `yarn.lock` → yarn, `package-lock.json` → npm). `package.json`에 `build`·`lint` 스크립트가 있는 것만 실행하고, 없는 스크립트는 건너뛴다고 알려줘.
4. **검증 실행**: 3에서 정한 명령을 실행해줘. 실패 시 즉시 중단하고 원인을 알려줘. 임의로 수정하지 마.
5. **코드 검토**: 검증 통과 후, 커밋하기 전에 `code-review` 스킬(effort: medium)로 스테이징된 변경사항(`git diff --cached`)을 correctness 관점에서 검토해줘. 이슈가 발견되면 내용을 보여주고 계속 커밋할지 먼저 고칠지 물어봐줘 — 자동으로 커밋을 막거나 자동으로 코드를 고치지 마. 이슈가 없으면 그대로 다음 단계로 진행해줘.
6. **커밋 메시지 형식 결정**: 아래 순서로 찾은 첫 번째 규칙을 따라줘.
   1. CLAUDE.md의 Git Conventions (예: `<type>(이슈키): 설명`처럼 Jira 이슈 키를 요구하는 경우)
   2. commitlint 설정 (`.commitlintrc*`, `commitlint.config.*`)의 type 목록과 scope 규칙
   3. `git log --oneline -20`에서 보이는 기존 커밋 스타일
   4. 위 셋이 모두 없으면 `type: 설명` — type만 영문, 설명은 한글. 예) `feat: SEO를 위한 OG metadata 추가`
7. **커밋**: git hook(`pre-commit`, `commit-msg`)이 실패하면 원인을 알려주고 직접 고쳐서 새로 커밋해줘 — `--no-verify`로 훅을 건너뛰지 마.
8. **push**: 커밋이 성공한 경우에만 현재 브랜치를 push해줘. upstream이 없으면 `git push -u origin <현재 브랜치>`로 push해줘. push 중 에러 발생 시 즉시 중단하고 원인을 알려줘. force push는 하지 마.

$ARGUMENTS
