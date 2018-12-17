## 예약 API(iOS전용)

### 1. 예약 가능 영화 리스트

- URL : api/tickets/m/movies/
- GET method
- Header에 토큰 필요

### 2. 예약 필터

- URL: api/tickets/m/filter/<int : pk>/
- 1단계 혹은 영화 차트에서 고른 영화의 pk 를 URL에 전달해야합니다.
- GET method
  - 2개 파라미터를 전달하여 출력되는 결과를 바꿀 수 있습니다. (선택적, 필수 아님)
  - "location" : "서울"
  - "time" : "2018-12-18" 
- Header 에 토큰 필요
- Header에 Content-Type application/json 필요

### 3. 좌석 선택

- URL: api/tickets/m/seats/<int : pk>/
- 이전 단계에서 고른 상영 시간의 pk를 URL로 받아야합니다.
- GET method
- Header에 토큰 필요

### 4. 예약 생성

- URL : api/tickets/m/reservations/
- 이전 단계에서 고른 좌석의 pk들과 2단계에서 골랐던 pk(3단계가 전달받았던) 상영시간 pk 2개를 받아야합니다.
- GET method
  - "screen" : 상영시간 pk
  - "seats" : [ "선택 좌석 pk" , "선택 좌석 pk", ...] (Array 자료형)
- Header에 토큰 필요
- Header에 Content-Type application/json 필요



### 유저 예매 내역은 이전에 만든 것과 동일합니다 

- URL : api/members/reservations/<int : pk> /
- Header 에 토큰 필요
- URL 마지막의 pk 는 예매 취소 시에만 추가하면 됩니다.(pk는 해당 예약의 pk(=예매번호))
- GET method : 유저의 예매 내역 리스트가 돌아옵니다.
- PATCH method (URL에 pk전달 필수):  전달한 pk 에 해당하는 예약의 is_active가 비활성화됩니다.












- 메인 페이지에 Response가 리턴됩니다.
  - Front(Web), iOS(App)용 URL이 다릅니다. 전달되는 요소는 크게 다르지 않습니다.
  - Web : 본래 도메인 주소 그대로 가면 됩니다.(뒤에 아무것도 넣지 않습니다)
  - App : 도메인 뒤에 m을 추가합니다(https://younghoonjean.com/m/) 
  - admin page에서 추가하는 것을 추천합니다. ( 트레일러는 특히 필요한 요소가 다르며, 사진 크기가 크게 달라지므로 분리 했습니다.)
  - 4개의 컨테이너로 분류됩니다.
    1. 메인 컨테이너 : 홈페이지 상단에 위치한 메인 이벤트(슬라이드)를 위한 공간입니다. ( 이벤트 이미지, 해당 이벤트 링크)
    2. 트레일러 : 영화 트레일러입니다. admin에서 직접 추가하며, 여러 개의 트레일러들을 등록할 경우, 랜덤으로 1개가 선택되어 반환됩니다. 매 접속마다 달라집니다. 웹과 앱이 분리되어 있으므로 주의. 영화를 선택하면 영화에 등록되어있는 트레일러가 자동으로 들어갑니다.
    3. 이벤트 컨테이너 , 4.하단 이벤트 컨테이너 : 웹 홈페이지 기준으로 만든 요소입니다. 이벤트 요소를 넣을 수 있습니다. ( 이벤트 이미지, 해당 이벤트 링크)

- Movie List에 pagination 이 추가되었습니다.

  - api/members/list/?page=1/ 처럼 GET method로 "page" : <int> 를 보내면 됩니다.

  - 한 페이지 당 8개의 영화가 출력되며, 다음 페이지 링크와 이전 페이지 링크가 함께 출력될 것입니다.

- Movie List, Detail, Reservation(예약 및 유저 예약정보 조회 시) 에 영화 메인 포스터 이미지 썸네일 파일 url 이 추가되었습니다.

  - 썸네일은 메인 포스터 이미지에만 적용되었습니다.

  - 썸네일 이미지는 400*560 size, quality는 원본의 80%로 설정되었습니다.

- 예약 필터 > 좌석 선택 > 예약 생성 > 유저 예약 내역 확인 & 예매 취소(비활성화) API가 추가되었습니다.

  - 예약 프로세스의 모든 API는 Auth Token을 필요로 합니다.

  - 예약 프로세스 API:

    - 티켓 필터 : api/tickets/filter/ ,

      GET method > movie, location, subLocation, time (이전과 동일)

    - 좌석 선택 : api/tickets/seats/<pk>/ ,

      GET method > 티켓 필터과정에서 선택한 상영 인스턴스의 pk(상영시간의 pk) 를 URL로 전달. 

      100개의 Seat에는 

      ​	row, number(행/열, int),

      ​	seat_name(좌석명(**신규 추가), ex) A1, B10),

      ​	reservationCheck(예매 여부, Boolean)가 전달됩니다.

    - 예약 생성 : api/tickets/reservation/,

      GET method > 위 두개 과정에서 선택한 상영시간 pk, 선택한 좌석들의 pk를 전달해야합니다.

      상영시간 pk 는 "screen", 선택 좌석 pk는 "seats"

      **이후 POST method로 변경될 수 있습니다.

      이전에 상의한 내용이 전달됩니다.

      전달되는 pk는 예매번호로 사용하도록 합니다. (취소 등에 용이하게 사용됩니다.)

    - 유저 예약 내역 확인 : api/members/reservations/ ,

      GET method로 요청, 전달해야할 Parameter는 없습니다.

      만약 예매 내역이 없을 경우에는 예매 내역이 없음을 알리는 메세지가 돌아갑니다.

      각각의 예매 내역이 출력되는 결과는 위 예약 생성에서 출력될 사항과 동일합니다.

    - 유저 예약 취소 : api/members/reservations/<pk>/,

      PATCH method로 전달하며,

      비활성화 하고 싶은 예약의 pk(예매번호)를 URL로 함께 전달해야 합니다.

  - ** 주의사항 : 현재 예매율 자동 계산이 해제되어 있습니다. 비동기 계산식을 구현해볼 계획이기 때문에 현재는 예약이 이루어져도 예매율이 그대로 0인 상태입니다.
