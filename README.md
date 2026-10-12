# university-list

합격 조회 프로그램(`hapgyeok-check-portable`)이 사용하는 **대학별 합격자 발표 조회 설정** 목록입니다.

프로그램은 실행할 때마다 이 저장소의 `adapters/adapters.json`을 내려받아 사용합니다.
여기를 고치면 프로그램을 새로 받지 않아도 다음 실행부터 바로 반영됩니다.

## 주의

- **학생 개인정보(이름·생년월일·수험번호 등)는 절대 올리지 마세요.** 이 저장소는 공개되어 있습니다.
- 여기에는 대학별 발표 페이지 주소, 입력칸 이름, 합격·불합격 판정 문구만 둡니다.

## 파일

| 파일 | 내용 |
|---|---|
| `adapters/adapters.json` | 대학별 조회 설정 (대학 이름·별칭, 발표 페이지 주소, 입력칸, 판정 규칙) |

## 조회 설정 하나의 구성

```json
"cau": {
  "univ_id": "cau",
  "name": "중앙대학교",
  "aliases": ["중앙대"],
  "engine": "http",
  "encoding": "euc-kr",
  "request": {
    "url": "발표 페이지 주소",
    "method": "POST",
    "fields": { "name": "이름 입력칸", "exam_no": "수험번호 입력칸", "birth8": "생년월일(8자리) 입력칸" },
    "const_params": { "mode": "check" }
  },
  "verdict_rules": [
    { "match": "contains", "text": "합격을 축하", "result": "pass" }
  ]
}
```

- `name`, `aliases`: 엑셀의 대학명과 맞춰 보는 이름. 분교는 이름에 `(미래)`, `(세종)`처럼 캠퍼스를 표시합니다.
- `fields`에 쓸 수 있는 값: `name`(이름), `exam_no`(수험번호), `birth8`(생년월일 8자리), `birth6`(생년월일 6자리, YYMMDD), `inquiry_pw`(조회 비밀번호).
  `birth6`은 합격 조회 프로그램 v7 이상에서만 읽을 수 있습니다.
- `verdict_rules`: 위에서부터 차례로 확인해 처음 맞는 규칙으로 판정합니다
  (`pass` 합격, `fail` 불합격, `waitlist` 예비, `not_open` 발표 전, `no_match` 정보 불일치).

## 자동 조회하지 않는 대학 (`manual_check`)

`adapters/adapters.json` 맨 위의 `manual_check`에 적힌 대학은 자동 조회하지 않고 "직접 확인"으로 표시합니다.
대학 이름과 이유를 적으면 되고, 프로그램 v8 이상은 다음 실행부터 반영합니다.

```json
"manual_check": {
  "이화여자대학교": "개인 인증번호 필요",
  "서강대학교": "개인 인증번호 필요",
  "국민대학교": "전화번호 필요"
}
```

## 진학어플라이 대학 (프로그램 v8 이상)

`request.url`에 진학어플라이 발표 주소를 넣으면, 프로그램이 그 페이지가 요구하는 입력칸을 스스로 읽어 조회합니다.

- 발표 하나: `https://pims.jinhakapply.com/Pass/PassLogin.aspx?Code=…`
- 발표 목록: `https://pims.jinhakapply.com/Pass/PassList.aspx?Code=…` (지금 열려 있는 발표를 차례로 조회)

발표 회차가 바뀌면 대학 공지의 새 주소로 `url`만 바꾸면 됩니다.

## 변경 이력

- 2026-10: 기존 35개 대학 설정으로 시작. 연세대학교(서울)가 미래캠퍼스 설정으로 잘못 연결되던 별칭 수정.
- 2026-10-12: 연세대학교(서울) 추가 (2026 수시 최종 발표 페이지 구조 기준, 2027 발표 주소는 발표 시 확인 필요).
- 2026-10-12: "직접 확인" 대학 목록(manual_check)을 저장소로 이동, 국민대학교 추가(전화번호 필요).
