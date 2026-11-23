# carrotdev — Turnlight 차종표

사이트: https://www.joongyu.co.kr/carrotdev/

CarrotPilot 깜빡이(Turnlight) 기능이 현대·기아·제네시스 차종별로 어떤 CAN 경로로 되고 안 되는지 정리한 표입니다.
칸이나 행 이름을 누르면 근거와 시험 방법(전제·켤 설정·순서·확인할 것·기록할 것·미확인)이 열립니다.

## 파일

| 파일 | 내용 |
|---|---|
| `index.html` | 전체 표 화면. 데이터를 담지 않고 아래 JSON을 읽어 그린다 |
| `cars.html` | 차종별 카드 화면(「내 차 깜빡이, 되나요?」). `matrix.json`의 `guide`·`paths[].short`와 `tests.json`의 `params`(설정 제목)를 읽는다 |
| `data/matrix.json` | 차종 목록, 경로×차종 표, 설정×차종 표, 차종 요약, 근거 |
| `data/tests.json` | 설정별 시험 방법, 설정 키의 이름·범위·기본값(`params`), 기록 방법 |

## 결과 갱신 (JSON 수정 → push)

1. `data/matrix.json`에서 행을 찾는다. 경로는 `paths[]`, 설정은 `settings[]`(`id`가 표의 번호).
2. 그 행의 `cells.<차 id>`를 고친다. 형식은 `[상태, "한국어 근거", "English evidence"]`.
   - 상태: `ok` ✅ · `should` △ · `no` ❌ · `unk` ? · `na` –
   - 근거에는 날짜와 결과만 쓴다. 예: `"P단 16번 시험 좌·우 점등(11-20)"`
   - 네 번째 값을 넣으면 기호 대신 그 글자를 보여 준다(예: `"1만"`).
3. 맨 위 `updated` 날짜를 바꾼다. 차종 요약이 달라지면 `summary`도 고친다.
   - 차주용 카드(`cars.html`)는 `guide.<차 id>`다. `verdict`(`ok` 주행 중 Kick·리모컨으로 켜짐 · `partial` P단 시험만 또는 큰 단서 · `no` 시험했지만 안 켜짐 · `unk` 결과 없음)와 언어별 `line`·`how`·`hazard`·`caution`·`next`.
   - `how`는 한 줄에 `키 = 값` 하나. 키가 `tests.json`의 `params`에 있으면 카드에 설정 제목이 같이 나온다.
   - 차를 추가하면 `cars[]`, 모든 `paths[]`·`settings[]` 행의 `cells`, `summary`, `guide`에 같은 id를 넣는다.
4. 시험 방법을 고칠 때는 `data/tests.json`의 `tests.<id>`를 고친다.
5. 커밋하고 push한다. GitHub Pages가 1~2분 안에 반영한다.

```bash
git add data && git commit -m "data: <차종> <설정> 결과" && git push
```

JSON 문법이 틀리면 페이지 범례 자리에 `data load failed`가 뜬다. push 전에 브라우저로 한 번 연다.

## 설정 값의 출처

`data/tests.json`의 `params`는 `joongyu01/carrot-wip-dev`의 `openpilot/selfdrive/carrot_settings.json`에서 옮겼다(`source` 칸에 커밋).
설정 이름·범위·기본값이 바뀌면 그 파일에서 다시 옮기고 `source`를 고친다.

## 공개 전 확인

공개 사이트다. 차주 이름·닉네임, DongleId, route 이름, 전화번호, 위치는 넣지 않는다. 차종과 날짜만 남긴다.
시험 방법은 기존 설정 메뉴로 하는 시험과 로그 확인만 적는다.

## 로컬 미리보기

`fetch`로 JSON을 읽으므로 `file://`로는 열리지 않는다. 아무 정적 서버로 이 폴더를 열면 된다.
