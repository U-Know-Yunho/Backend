# Backend

# CGV Project : Sign Up & Sign In Process

## 1. Local Sign Up (회원가입)

- 요청 URL : <domain_name>/api/members/signup/
- 요청 Method : POST 요청
- 전송 데이터 형식 : JSON
- 다음과 같은 필드에 데이터를 전송해야 한다.
  - username
  - password
  - last_name
  - first_name
  - email
  - phone_number
- Sign Up 페이지의 경우는 Password 필드가 2개 존재해야한다. Password와 Password 확인란이다. 전송할 때에는 둘 중 하나만 password 필드에 담아 보낸다. 
- 요청이 성공한 경우, 생성된 data(User 객체)와 HTTP 상태코드(status code) 201 가 요청 URL로 return된다.



## 1-1. Check Username Exists (Username 중복 검사)

- 요청 URL : <domain_name>/api/members/checkID/
- 요청 Method : POST 요청
- 전송 데이터 형식 : JSON
- 다음 필드에 데이터를 전송한다.
  - username
- Sign Up 페이지에서 Username 입력 칸 옆에 중복 검사 버튼을 생성한다.
- 해당 버튼에 위 URL에 POST 요청을 보내도록 하며, 전송하는 데이터는 위 username 데이터이다.
- 이미 존재하는 username의 경우 {"message" : ' 이미 존재하는 아이디입니다.' }  가 return된다.
- DB에 없는 username의 경우 { "username" :  입력했던 data, "message": '사용 가능한 아이디입니다' } 가 return된다.



## 2. Local Sign In (로그인)

- 요청 URL : <domain_name>/api/members/login/
- 요청 Method : POST 요청
- 전송 데이터 형식 : JSON
- 다음과 같은 필드에 데이터를 전송해야 한다.
  - username
  - password
- 요청이 성공한 경우, { 'token' : token.key } 와 HTTP 상태코드(status code) 200 이 요청 URL로 return된다.
- return된 "token" 키를 로그인 이후 이루어질 모든 동작에서 request의  Header부분에 포함시켜 전달할 수 있도록 만들어야한다.
- Header에 해당 토큰이 없을 경우 로그인 된 유저라고 인식되지 않는다.



## 3. Social Sign up& Sign in (Facebook 회원가입/로그인)

- 요청 URL : <domain_name>/api/members/facebook-login/
- 요청 Method : POST 요청
- 전송 데이터 형식 : JSON
- Facebook SDK를 통해 얻은 사용자 정보를 다음과 같은 필드에 담아 전송해야 한다.
  - user_id
  - last_name
  - first_name
  - email
  - phone_number
- email 과 phone_number는 빈 데이터를 전송해도 상관 없다.
- DB에 해당 user_id를 username으로 갖는 User객체가 없을 경우, 자동으로 전달받은 데이터를 기반으로 User를 생성한다.
- 요청이 성공한 경우, { 'token' : token.key } 와 HTTP 상태코드(status code) 200 이 요청 URL로 return된다.
- return된 "token" 키를 로그인 이후 이루어질 모든 동작에서 request의  Header부분에 포함시켜 전달할 수 있도록 만들어야한다.
- Header에 해당 토큰이 없을 경우 로그인 된 유저라고 인식되지 않는다.



## 4. User Profile (마이페이지 : 유저 프로필)

- 요청 URL : <domain_name>/api/members/profile/
- GET, PATCH 두 가지 Method를 받을 수 있으며, 요청 Method에 따라서 기능이 달라진다.
- Request Method  GET(현재 유저 프로필 조회):
  - 현재 로그인 한(Token을 부여받은) User 객체의 Profile데이터가 return된다.
    - username
    - password
    - last_name
    - first_name
    - email
    - phone_number
  - return 된 데이터를 받아 Template에 미리 위 데이터를 채워넣어 둬야 한다.
  - username은 수정할 수 없도록 막아둬야 한다.
- Request Method PATCH(프로필 수정 데이터 전송):
  - GET요청에서 받은 데이터를 적절히 수정한 후, 수정한 데이터를 User 정보로 저장하기 위한 요청이다.
  - 위와 동일한 필드로, 새롭게 입력한 값을 PATCH 요청으로 위 URL로 다시 보낸다.



## 5. Logout (로그아웃)

- 요청 URL : <domain_name>/api/members/logout/
- 요청 Method : GET 요청
- Logout 버튼 클릭 시 해당 URL로 GET요청을 보내면 된다.
- 전송해야 할 데이터는 없다.
- 로그인 된(Token을 부여받은) 해당 유저의 Auth Token을 DB에서 삭제한다.
- HTTP 상태코드(status code) 200이 return된다.

