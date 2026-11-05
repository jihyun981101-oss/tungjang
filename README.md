# 텅장지킴이

귀차니즘·소비요정·대출 보유자를 위한 1인용 모바일 가계부.
한 줄 입력, 오늘 써도 되는 돈, 충동구매 보류함, 은행별 통장 잔액, 카드값(할부·리볼빙), 대출 D-day와 이자 시뮬레이션, 비밀번호 잠금.

- 앱 주소: https://jihyun981101-oss.github.io/tungjang/
- 이 저장소에는 **앱 코드만** 있습니다. 가계부 기록은 여기 올라오지 않습니다.
- 기록은 기본적으로 **폰 브라우저 안**에만 저장됩니다. 여러 기기에서 보려면 아래 "내 계정"을 켭니다.

## 홈 화면에 앱으로 설치

- iPhone: Safari로 주소를 연 뒤 공유 버튼 → **홈 화면에 추가**
- Android: Chrome으로 연 뒤 메뉴(⋮) → **앱 설치** 또는 **홈 화면에 추가**
- 컴퓨터: Chrome/Edge 주소창 오른쪽 **설치** 아이콘

## 내 계정 (여러 기기 동기화) 준비: Firebase 한 번 만들기

기록은 본인 Google 계정으로 만든 Firebase(무료) 프로젝트에 저장됩니다. 아래 규칙 덕분에 **로그인한 본인만** 자기 기록을 읽고 쓸 수 있습니다. 한 번만 하면 됩니다. 5분 정도.

1. https://console.firebase.google.com 에 Google 계정으로 로그인 → **프로젝트 만들기** (이름 아무거나, Google 애널리틱스는 끄기).
2. 왼쪽 **빌드 → Authentication → 시작하기** → 로그인 방법에서 **이메일/비밀번호** 선택 → 사용 설정 → 저장.
3. 왼쪽 **빌드 → Firestore Database → 데이터베이스 만들기** → 위치 `asia-northeast3 (Seoul)` → **프로덕션 모드**로 시작.
4. Firestore의 **규칙** 탭을 열고 아래 내용으로 바꾼 뒤 **게시**:

```
rules_version = '2';
service cloud.firestore {
  match /databases/{database}/documents {
    match /users/{uid}/{document=**} {
      allow read, write: if request.auth != null && request.auth.uid == uid;
    }
  }
}
```

5. 왼쪽 위 톱니 **프로젝트 설정 → 일반 → 내 앱 → 웹 앱 추가(</> 아이콘)** → 닉네임 입력 → 앱 등록 → 화면의 `const firebaseConfig = { ... };` 전체 복사.
6. 복사한 값을 앱 코드에 심거나(`index.html`의 `FB_DEFAULT_CFG`), 각 기기에서 앱 **설정 탭 → 클라우드 동기화** 칸에 붙여넣고 **설정 저장**.

그 다음부터는 어느 기기에서든 **설정 탭 → 내 계정**에서 이메일·비밀번호로 **계정 만들기** 한 번, 이후엔 **로그인**만 하면 같은 기록이 보입니다.

`firebaseConfig` 값은 비밀번호가 아닙니다. 공개돼도 위 규칙 때문에 다른 사람은 내 기록을 읽을 수 없습니다.

## 백업

설정 탭 → 데이터 → **백업 파일 저장**으로 JSON 파일을 받아 두고, 필요할 때 **백업 복원**으로 불러옵니다.

## 직접 고치기

`index.html` 하나에 모든 코드가 들어 있습니다. 고친 뒤 커밋하면 GitHub Actions가 1~2분 안에 배포합니다. 오프라인 캐시를 갱신하려면 `sw.js`의 `VERSION` 값을 바꿔 주세요.
