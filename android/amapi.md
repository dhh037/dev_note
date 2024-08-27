
## Android Management API

Android Management API는 조직에서 Android 기기를 관리할 수 있도록 지원하는 API입니다. 기업 환경에서 모바일 관리 기기(Mobile Device Management, MDM)와 모바일 애플리케이션 관리 (Mobile Application Management, MAM)를 통합적으로 수행하는데 최적화되어 있습니다. 이를 통해 직원들에게 배포한 Android 기기에 설정, 보안, 정책, 앱 설치 등을 원격으로 관리할 수 있습니다.

(공식문서에 [빠른 시작](https://developers.google.com/android/management/quickstart?hl=ko)을 참고하면 전반적인 흐름을 쉽게 파악할 수 있습니다.)

전체적인 흐름은 아래와 같습니다.   
Google Cloud 계정 및 프로젝트 생성 -> Android Management API 활성화 -> 서비스 계정 생성 및 Google OAuth 2.0 인증 -> 엔터프라이즈 가입 URL 생성 (signupUrls) -> 엔터프라이즈 생성 (enterprises.create) -> 엔터프라이즈 정책 생성 (policy) -> 기기 프로비저닝

(Android Management AP를 호출하려면 Google에서 제공하는 클라이언트 라이브러리를 사용하는걸 권장하지만 편의상 Postman으로 진행합니다.)

우선 Google API 콘솔에 로그인해서 프로젝트를 만든 후 "API 및 서비스" 항목에서 "Android Management API"를 사용설정 해줍니다. 
그 다음 왼쪽 사이드바에 "사용자 인증 정보" 탭으로 가서 "OAuth 클라이언트 ID"를 생성합니다. 이때 postman으로 진행할거기 때문에 어플리케이션은 데스크톱 앱으로 설정합니다. 그 후 클라이언트 아이디 정보가 담긴 JSON 파일을 다운로드 한 다음 postman에서 새로운 요청정보를 만들고 Authorization 탭에 Auth Type을 "OAuth 2.0"으로 선택 후 JSON 파일을 참고해서 인증정보를 기입해줍니다. 이때 scope는 "https://www.googleapis.com/auth/androidmanagement"로 기입해줘야 합니다. 이 scope은 요청 공식문서에 기재되어 있습니다. 또한 Callback URL 역시 JSON 정보와 일치하게 작성해줘야 합니다. (http://localhost) 

postman에서 OAuth 인증절차를 끝냈다면 [공식 문서](https://developers.google.com/android/management/reference/rest?hl=ko)를 참고해서 아래 순서대로 API를 요청해줍니다.
1. signupUrls
2. enterprises.create
3. plicies.patch
4. enrollmentTokens.create

최종적으로 enrollmentTokens를 생성했다면 이 토큰으로 제어할 기기에 프로비저닝 해주면 됩니다.
enrollmentToken json 구조를 보면 qr 데이터가 따로 있습니다. 그대로 복사 후 QR로 만들어주고 Android Device Policy 앱을 설치한 뒤 카메라로 QR을 인식 시켜주면 직장 프로필 생성이 완료됩니다. 카메라를 사용할 수 없는 경우 "value" 키값에 들어있는 토큰(코드)를 직접 입력해주면 됩니다. 이 프로비저닝을 공장초기화 없이 진행했다면 "프로필 소유자" 권한을 얻게되고 "기기 소유자" 권한 보다 제한적인 기능을 갖게 됩니다. (kiosk 모드, 시스템 자동 업데이트 등)

기업용 완전 제어형 기기를 만들기 위해서는(기기 소유자 권한) 공장초기화가 필요합니다. 초기화면에서 탭을 여러번 눌러서 QR코드를 인식할 수 있는 화면으로 진입 후 거기서 QR를 인식시킨 다음 프로비저닝을 진행하면 됩니다.

그렇게 프로비저닝이 완료된 기기의 경우 정책(policy)를 업데이트 하면서 기기를 관리해주면 됩니다. 자세한 정책 정보는 [링크](https://developers.google.com/android/management/compliance?hl=ko)를 통해 확인할 수 있습니다.