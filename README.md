# Backend

# CGV Project : DataBase Model Document

## CGV Flow for MVP

1. User 회원가입 - 로그인이 이루어집니다.
2. 인증된(로그인 된) 유저에 한정하여 예매 기능이 활성화 됩니다.
3. 예매 기능
   1. 영화를 선택합니다. (Movie 객체 리스트 중 선택)
   2. 극장을 선택합니다. ( Theater 객체 리스트 중 선택)
   3. 상영관과 상영 시간를 선택합니다. ( Screening(상영관)과 해당하는 상영 시간(Screening time)을 선택)
   4. 좌석을 선택합니다. ( 선택한 상영관에 소속된 모든 좌석(Seat) 객체를 가져오고, 이들 중 reservation이 False인 좌석 객체를 선택합니다.)
   5. 결제가 진행됩니다. ( 구현 미정)
   6. 결제가 완료되면 My Page로 Redirect됩니다.(예매 내역 표시 : 입력된 정보들을 조합해 Reservation 객체를 생성하고, 이를 바탕으로 예매 내역에 필요한 정보들이 표시됩니다.)



## DB Model Details

### 1. User

- username : 일반적인 아이디 역할을 하는 필드입니다.
- password : 패스워드 필드입니다.
  - 회원가입(Sign up) 페이지 작성 시 Password / Password 재입력으로 form을 만들도록 합니다.
- first name : 유저 이름 필드입니다.
- last name : 유저 성(姓) 필드입니다.
  - 유저의 이름을 first name과 last name으로 나눈 것은 이후 Facebook, Google등  Social Account와 연결시킬 때 해외 서비스들이 전달하는 유저 정보가 이름을 first name, last name으로 나누어 전달하기 때문에, 이를 용이하게 받기 위함입니다.
- email : email 필드입니다.
- phone number : 전화번호 필드입니다.(character field)
  - phone number를 " - " 없이 입력하도록 합니다. (ex) 41524204242 )

### 2. Movie

- title : 영화 제목 필드입니다.

- director : 영화감독 필드입니다.

- cast : 영화 출연 배우 필드입니다.

  - List를 저장할 수 있는 DB Model이 없기 때문에, Crawling 시에 문자열(Character)로 받아, 이후 split method로 list화 시켜 사용하도록 합니다.

- description : 영화 줄거리 텍스트 필드입니다.

- duration_min : 영화 총 상영 시간 필드입니다 ( 134 분)

- trailer : 영화 트레일러 URL입니다.

  - CGV 웹사이트를 확인해본 결과 트레일러의 경우 CGV측에서 저작권 등의 이유로 이를 크롤링할 수 없도록 막아두었습니다. 따라서 유튜브 등에서 직접 트레일러 URL주소를 입력하거나 혹은 프론트엔드/iOS에서 동영상을 넣는 방법을 사용해야합니다. (**상의 필요)

- opening_date : 영화 개봉일입니다.

- reservation_score : 예매율입니다(Float field). Reservation(예매) 생성 및 비활성화(예매 취소) 시에 자동으로 예매율이 변동됩니다.

- now_show : 현재 상영 중인 영화인지를 나타내는 Boolean필드입니다.

- main_img : 메인 포스터 이미지 필드입니다. 파일의 url이 전달됩니다.(절대경로)

  ### 2-1. Stillcut

  해당 영화의 스틸컷 이미지 파일 테이블입니다. 외래키로 연결되어 특정 영화를 참조합니다. 이미지 파일은 최대 5개 입니다.

  - movie : 연결되어 있는 영화 id입니다.
  - image : 스틸컷 이미지 파일 필드입니다. 파일의 url이 전달됩니다.(절대경로)

### 3.  Theater

- location : 지역 대분류입니다. ( ex) 서울 / 충청남도 )

- sub_Locaiton : 지역 소분류이자 영화관의 이름입니다. ( ex) 강남점 / 신촌점 )

  - CGV 에서 예매를 진행할 때 영화관 선택화면은

    1) 지역 대분류 선택 ( 서울/경기/충청남도...) 2) 영화관 선택(지역 소분류) (강남점/신촌점/용산점...) 과 같이 나타납니다.

- address : 상세 주소입니다. " 서울특별시 성북구 종암동 ... " 과 같은 형식으로 설정했습니다.

  - 혹은 위도/경도 정보를 넣을 수 있습니다. 이 경우에는 Google Map 연동을 통해서 추가적인 기능을 넣을 수 있는 가능성이 있습니다.  (**상의 필요)

- current Movie : 현재 해당 극장에 상영 중인 영화의 목록이 들어갑니다.

  - 위 DB 설계도의 경우는 외래키로 참조관계로 되어있으나, 실제로는 ManyToMany 관계로 구현되어있습니다.
  - Screening을 through로 갖기 때문에, Movie.theaters(related_name).all() 과 같은 방식의 ORM을 사용

### 4. Auditorium

- name : 상영관의 이름입니다. ( A관, 1관, 2관 ... )
- seats_no : 해당 상영관에 있는 총 좌석 수입니다. 기본값은 0이며, 현재 좌석 개수(Seat)에 따라서 계속 변동하도록 설정했습니다.
- theater : 해당 상영관이 어떤 극장에 소속되어있는지를 나타냅니다.

### 5. Screening

- ** movie와 theater 모델을 이어주는 immediate 모델입니다.

  이는 어떤 시간에 어떤 영화가 상영되는지, 그 "상영" 자체를 모델링화 한 것입니다. 

  ( '18:25분'에 '보헤미안 랩소디'가 '서울 강남점'에서 "상영")

- movie :  어떤 영화가 상영되는지 나타내는 필드입니다.

- theater : 어떤 극장에서 상영이 이루어지는지 나타내는 필드입니다.

- auditorium : 어떤 상영관에서 상영이 이루어지는지 나타내는 필드입니다.

  ### 5-1. ScreeningTime

  - 상영 시간 개체들입니다. 위 Screening에 연결되어 참조합니다.
  - screening_id : 연결된 상영의 id 입니다.
  - time : Datetime이 기록됩니다.

### 6. Seat

- row : 좌석의 행 정보입니다 (1행, 2행...) Int 필드입니다.
- number : 좌석의 열 정봉비니다. (1행 7번 좌석, 3행 11번 좌석...) Int필드입니다.
- auditorium : 좌석이 속한 상영관을 나타내는 필드입니다.
- reservation_check : Boolean 필드로, 좌석이 예매가 되었는지(True) 혹은 아직 비어있는지 (False)를 나타냅니다.
- save(), delete()가 이루어질 때 마다 auditorium의 좌석 수가 업데이트됩니다.

### 7. Reservation

- user : 어떤 유저가 예약했는지 나타냅니다. ( 로그인한 유저의 정보가 자동으로 들어갑니다)

- screening : 어떤 "상영" 객체를 예약했는지 나타내기 위한 필드입니다.

  - 해당 필드에서 참조하고 있는 Movie id , Theater id , Auditorium id를 확인합니다.
  - 이를 My Page에서 적절히 query하여 출력시키도록 ORM을 진행합니다. 

- screening_time : 위 상영 중 어떤 시간의 영화를 선택했는지 나타낼 수 있도록 합니다.

- is_active : 예약의 활성화 여부(취소 여부)를 보여주는 Boolean field입니다. 예약이 생성/변경(비활성화) 될 때 마다 해당 영화의 예매율을 자동으로 업데이트합니다.

  ### 7.1 SelectedSeat

  - reservation : 특정 예약 객체를 참조하고 있습니다. 

  - seat : 어떤 좌석을 선택했는지 좌석 정보를 나타낼 필드입니다.
  - My Page에서는 해당 id에 해당하는 좌석의 row / number를 나타낼 것입니다
  
  
  
  

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

