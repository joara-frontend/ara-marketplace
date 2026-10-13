---
name: commit-push-pr
description: 프로젝트의 Git Conventions에 따라 커밋, push 후 gh CLI로 PR을 생성한다. 팀/1인 모드를 판별해 브랜치 규칙을 적용하고, 빌드/린트 검증, code-review 스킬 검토, PR 템플릿 작성을 포함한다.
disable-model-invocation: true
user-invocable: true
---

프로젝트의 Git Conventions에 따라 커밋, push 후 gh CLI로 PR을 생성해줘.
.gitignore 파일은 항상 커밋에서 제외해줘.

1. **협업 모드 판별**: 아래 순서로 판별하고, 결과(`team` 또는 `solo`)와 근거를 한 줄로 먼저 알려줘.
   1. CLAUDE.md에 `협업 모드: solo` 또는 `협업 모드: team`이 적혀 있으면 그대로 따른다.
   2. 없으면 `gh api repos/{owner}/{repo}/collaborators --jq length`로 협업자 수를 확인한다. 2명 이상이면 team, 1명 이하이면 solo.
   3. `gh` 호출이 실패하면 `git shortlog -sne --all`의 작성자 수로 판단한다(같은 사람의 다른 이메일은 한 명으로 본다). 2명 이상이면 team, 1명 이하이면 solo.
2. **브랜치 확인**
   - team: 현재 브랜치가 `main`이나 `develop`이면 즉시 중단하고 알려줘 (작업 브랜치로 이미 전환돼 있어야 함 — 새 브랜치를 대신 만들지는 마).
   - solo: 현재 브랜치가 `main`이면 PR 대상이 없으니 즉시 중단하고, main에 바로 올리려면 `/ara-git:commit-push`를 쓰라고 안내해줘. 작업 브랜치면 그대로 진행해.
3. **fork 저장소 확인**: `git remote`에 `upstream`이 있으면 fork 저장소야. `gh`는 default repo가 설정돼 있지 않으면 PR을 원본(upstream)으로 잘못 보낼 수 있으니, `gh repo set-default --view`로 default repo가 PR을 보낼 저장소로 설정돼 있는지 확인해줘. 설정이 없거나 의도와 다르면 즉시 중단하고 알려줘 — 임의로 `gh repo set-default`를 실행하지 마.
4. **검증 명령 결정**: CLAUDE.md에 커밋 전 검증 명령이 적혀 있으면 그대로 따라줘. 없으면 lockfile로 패키지 매니저를 정해줘(`pnpm-lock.yaml` → pnpm, `yarn.lock` → yarn, `package-lock.json` → npm). `package.json`에 `build`·`lint` 스크립트가 있는 것만 실행하고, 없는 스크립트는 건너뛴다고 알려줘.
5. **검증 실행**: 4에서 정한 명령을 실행해줘. 실패 시 즉시 중단하고 원인을 알려줘. 임의로 수정하지 마.
6. **코드 검토**: 검증 통과 후, 커밋하기 전에 `code-review` 스킬(effort: medium)로 스테이징된 변경사항(`git diff --cached`)을 correctness 관점에서 검토해줘. 이슈가 발견되면 내용을 보여주고 계속 커밋할지 먼저 고칠지 물어봐줘 — 자동으로 커밋을 막거나 자동으로 코드를 고치지 마. 이슈가 없으면 그대로 다음 단계로 진행해줘.
7. **커밋 메시지 형식 결정**: 아래 순서로 찾은 첫 번째 규칙을 따라줘.
   1. CLAUDE.md의 Git Conventions (예: `<type>(이슈키): 설명`처럼 Jira 이슈 키를 요구하는 경우)
   2. commitlint 설정 (`.commitlintrc*`, `commitlint.config.*`)의 type 목록과 scope 규칙
   3. `git log --oneline -20`에서 보이는 기존 커밋 스타일
   4. 위 셋이 모두 없으면 `type: 설명` — type만 영문, 설명은 한글. 예) `feat: SEO를 위한 OG metadata 추가`
8. **커밋**: git hook(`pre-commit`, `commit-msg`)이 실패하면 원인을 알려주고 직접 고쳐서 새로 커밋해줘 — `--no-verify`로 훅을 건너뛰지 마.
9. **push**: 커밋이 성공한 경우에만 현재 브랜치를 push해줘. upstream이 없으면 `git push -u origin <현재 브랜치>`로 push해줘. force push는 하지 마.
10. **PR 생성**: `gh pr create`로 PR을 생성해줘. base 브랜치는 CLAUDE.md에 정해져 있으면 그것을, 없으면 `develop`이 있으면 `develop`, 없으면 저장소 기본 브랜치로 해줘. PR 제목은 커밋 메시지로 작성해줘.
11. **merge 금지**: PR 생성까지만 진행하고, 병합(merge)은 절대 직접 수행하지 마.
    - team: 브랜치 보호 규칙(approve 필요)이 있을 수 있으니 approve 여부와 관계없이 병합은 사용자가 직접 처리해.
    - solo: 별도 리뷰어가 없어도 사용자가 PR 내용을 직접 확인한 뒤 본인이 머지해.

충돌 또는 에러 발생 시 즉시 중단하고 원인과 해결 방법을 알려줘. 자동으로 수정하지 마.

PR 본문은 저장소에 `.github/pull_request_template.md`가 있으면 그 템플릿을 그대로 사용해줘 — 섹션 구성이나 체크리스트 문구를 임의로 바꾸지 말고 각 섹션 안의 내용만 채워줘. 템플릿이 없으면 아래 템플릿을 사용해줘. 선택 항목은 관련 내용이 없으면 생략해도 돼.

체크리스트는 항목별로 실제 검증한 뒤 결과에 따라 [x]/[ ]를 표시해줘. 임의로 전부 체크하지 마.
- PR 제목을 규칙에 맞게 작성했나요? → 7에서 정한 커밋/제목 규칙과 실제 PR 제목을 비교해서 확인
- 코드 컨벤션을 준수했나요? → 5의 lint 결과로 확인. 에러가 있으면 체크하지 말고 어떤 에러가 남아있는지 알려줘. lint 스크립트가 없으면 체크하지 말고 그 사실을 알려줘
- PR을 올리기 전에 로컬에서 빌드/테스트를 진행했나요? → 5의 build 결과로 확인

```
## 🎯 작업 개요
{변경 사항의 핵심 목적을 1~2줄로 요약}

## ✨ 상세 작업 내용
- [ ] {주요 변경 항목 1}
- [ ] {주요 변경 항목 2}

## 💡 고민한 부분 및 해결 과정 (선택)
{설계 결정, 트레이드오프, 특이한 구현 방식이 있다면 작성}

## 📸 스크린샷 (선택)
{UI 변경이 있을 경우 스크린샷 첨부}

## 📝 체크리스트
- [ ] PR 제목을 규칙에 맞게 작성했나요?
- [ ] 코드 컨벤션을 준수했나요?
- [ ] PR을 올리기 전에 로컬에서 빌드/테스트를 진행했나요?
```

`상세 작업 내용`의 체크박스는 실제로 완료한 항목만 `- [x]`로 표시해줘. 스크린샷은 직접 캡처할 수 없으니 해당 섹션은 생략하거나 사용자에게 첨부를 맡겨줘.

$ARGUMENTS
