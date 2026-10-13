# ara-marketplace

아라 전용 Claude Code 플러그인 마켓플레이스. 플러그인은 직업 페르소나이고, 그 안의 스킬·에이전트·훅은 그 직업이 가진 능력이다.

## 설치

```bash
claude plugin marketplace add joara-frontend/ara-marketplace
claude plugin install ara-reviewer@ara-marketplace
claude plugin install ara-git@ara-marketplace
claude plugin install ara-notify@ara-marketplace
```

로컬에서 테스트할 때는 저장소 경로를 그대로 등록한다.

```bash
claude plugin marketplace add C:\codeit\ara-marketplace
```

## 플러그인

| 플러그인       | 들어 있는 것                                   | 하는 일                                                                                                            |
| -------------- | ---------------------------------------------- | ------------------------------------------------------------------------------------------------------------------ |
| `ara-reviewer` | 에이전트 `pr-code-reviewer`                    | PR 번호를 주면 diff를 분석해 한국어로 코드 리뷰를 작성한다. "PR #42 리뷰해줘"처럼 요청한다.                        |
| `ara-git`      | 스킬 `commit`, `commit-push`, `commit-push-pr` | 프로젝트 컨벤션을 읽어 검증 → 코드 검토 → 커밋 → push → PR 생성까지 처리한다. `/ara-git:commit`처럼 직접 호출한다. |
| `ara-notify`   | 훅 `Notification`, `Stop`                      | Claude가 선택을 기다리거나 응답을 마치면 Windows 알림창을 띄운다. **Windows(PowerShell) 전용**이다.                |

## ara-git 사용법

세 스킬은 모두의 식물(modusplant_frontend) 프로젝트의 스킬을 기준으로, 프로젝트마다 다른 부분을 자동으로 판단하도록 바꾼 것이다.

| 스킬                      | 검증·검토 | 커밋 | push | PR  |
| ------------------------- | :-------: | :--: | :--: | :-: |
| `/ara-git:commit`         |    ✅     |  ✅  |      |     |
| `/ara-git:commit-push`    |    ✅     |  ✅  |  ✅  |     |
| `/ara-git:commit-push-pr` |    ✅     |  ✅  |  ✅  | ✅  |

### 프로젝트에 맞춰 자동으로 정하는 것

- **검증 명령**: CLAUDE.md에 적힌 명령이 우선한다. 없으면 lockfile로 pnpm / yarn / npm을 고르고, `package.json`에 있는 `build`·`lint` 스크립트만 실행한다.
- **커밋 메시지 형식**: CLAUDE.md Git Conventions → commitlint 설정 → 최근 커밋 로그 순서로 찾는다. 아무것도 없으면 `type: 한글 설명`을 쓴다.
- **PR 본문**: `.github/pull_request_template.md`가 있으면 그것을 쓰고, 없으면 기본 템플릿을 쓴다.

### 팀 / 1인 모드

push 규칙은 협업 모드에 따라 달라진다.

|                                       | team      | solo                        |
| ------------------------------------- | --------- | --------------------------- |
| `commit-push`를 main/develop에서 실행 | 중단      | main에 바로 push            |
| `commit-push-pr`를 main에서 실행      | 중단      | 중단하고 `commit-push` 안내 |
| PR merge                              | 하지 않음 | 하지 않음                   |

모드는 아래 순서로 판별한다.

1. CLAUDE.md의 `협업 모드: solo` / `협업 모드: team` 선언
2. GitHub collaborator 수 (`gh api`)
3. 커밋 작성자 수 (`git shortlog`)

자동 판별이 틀릴 수 있으니 프로젝트 CLAUDE.md에 한 줄 적어 두는 것을 권장한다.

```markdown
## Git Conventions

- 협업 모드: solo
```

> 각 프로젝트에 이미 있는 `/commit` 스킬과는 이름이 겹치지 않는다. 플러그인 스킬은 `/ara-git:` 접두사가 붙는다.

## 기존 기능 목록 (마켓 미포함, 기록용)

마켓에 올리지 않고 각 위치에 그대로 두는 기능들이다.

### 전역 (`~/.claude`)

| 종류 | 이름                  | 비고                                                             |
| ---- | --------------------- | ---------------------------------------------------------------- |
| 스킬 | `coding-standards`    | everything-claude-code(ECC) 출처                                 |
| 스킬 | `react-patterns`      | ECC 출처                                                         |
| 스킬 | `react-performance`   | ECC 출처, Vercel `react-best-practices`(MIT) 기반                |
| 스킬 | `frontend-patterns`   | ECC 출처, `settings.local.json`에서 꺼 둠                        |
| MCP  | `context7`            | 라이브러리 문서 조회. 전역 CLAUDE.md에 "항상 Context7 사용" 지침 |
| 설정 | `effortLevel: medium` |                                                                  |
