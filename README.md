# 텅장지킴이

귀차니즘·소비요정·대출 보유자를 위한 1인용 모바일 가계부.
한 줄 입력, 오늘 써도 되는 돈, 충동구매 보류함, 은행별 통장 잔액, 카드값(할부·리볼빙), 대출 D-day와 이자 시뮬레이션.

- 앱 주소: https://jihyun981101-oss.github.io/tungjang/
- 이 저장소에는 **앱 코드만** 있습니다. 가계부 기록은 여기 올라오지 않습니다.
- 기록은 기본적으로 **폰 브라우저 안**에만 저장됩니다. 여러 기기에서 보려면 아래 클라우드 동기화를 켭니다.

## 홈 화면에 앱으로 설치

- iPhone: Safari로 주소를 연 뒤 공유 버튼 → **홈 화면에 추가**
- Android: Chrome으로 연 뒤 메뉴(⋮) → **앱 설치** 또는 **홈 화면에 추가**

홈 화면 아이콘으로 열면 주소창 없이 앱처럼 뜨고, 오프라인에서도 열립니다.

## 클라우드 동기화 설정 (Firebase)

본인 Google 계정의 Firebase(무료)에 기록을 저장하는 방식입니다. 아래 규칙을 넣으면 **로그인한 본인 계정만** 자기 기록을 읽고 쓸 수 있습니다. 10분 정도 걸립니다.

1. https://console.firebase.google.com 에 Google 계정으로 로그인 → **프로젝트 추가** (이름은 아무거나, Google 애널리틱스는 끄기).
2. 왼쪽 메뉴 **빌드 → Authentication → 시작하기 → Google** 선택 → 사용 설정 → 지원 이메일 선택 → 저장.
3. 같은 화면의 **Settings(설정) → 승인된 도메인**에 `jihyun981101-oss.github.io` 추가.
4. 왼쪽 메뉴 **빌드 → Firestore Database → 데이터베이스 만들기** → 위치는 `asia-northeast3 (Seoul)` → **프로덕션 모드**로 시작.
5. Firestore의 **규칙** 탭을 열고 아래 내용으로 바꾼 뒤 **게시**:

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

6. 왼쪽 위 톱니 **프로젝트 설정 → 일반 → 내 앱 → 웹 앱 추가(</> 아이콘)** → 닉네임 입력 → 앱 등록 → 화면에 나오는 `const firebaseConfig = { ... };` 전체를 복사.
7. 텅장지킴이 앱 → **설정 탭 → 클라우드 동기화** 칸에 붙여넣고 **설정 저장** → **Google로 로그인**.

다른 폰이나 PC에서도 같은 주소를 열고 같은 설정값을 붙여넣은 뒤 같은 Google 계정으로 로그인하면 기록이 똑같이 보입니다.

`firebaseConfig` 값은 비밀번호가 아닙니다. 공개돼도 위 규칙 때문에 다른 사람은 내 기록을 읽을 수 없습니다.

## 백업

설정 탭 → 데이터 → **백업 파일 저장**으로 JSON 파일을 받아 두고, 필요할 때 **백업 복원**으로 불러옵니다.

## 직접 고치기

`index.html` 하나에 모든 코드가 들어 있습니다. 고친 뒤 커밋하면 GitHub Pages가 1~2분 안에 반영합니다. 오프라인 캐시를 갱신하려면 `sw.js`의 `VERSION` 값을 바꿔 주세요.
