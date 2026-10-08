# ara-marketplace

    claude plugin marketplace add joara-frontend/ara-marketplace
    claude plugin install ara-secretary@ara-marketplace

## 🎉 2.0 업데이트

`/mcp`에서 Notion만 연결하면 나머지는 알아서 해요. Notion에 페이지를 만들고, 그 아래에 회의록 DB와 액션 아이템 DB, 캘린더 보기까지 정리해 줘요.

    /mcp

처음 회의록을 정리할 때 설정이 자동으로 시작돼요. 설정을 다시 하고 싶으면 아래 명령을 쓰세요.

    /ara-secretary:setup

녹음 파일은 클로바노트나 회의 도구의 녹취 기능으로 먼저 텍스트로 변환하세요.

## 사용 예시

회의록을 정리하면 액션 아이템도 이어서 바로 정리돼요. 따로 실행할 필요가 없어요.

    /ara-secretary:minutes 회의녹취록.txt

액션 아이템만 따로 필요하면 단독으로도 쓸 수 있어요.

    /ara-secretary:action-items 회의녹취록.vtt
