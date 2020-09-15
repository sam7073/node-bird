# Make SNS Service with Express

## NodeBird - Used Packages

1. DB: Mysql (Sequelize)
2. Login: Passport Package
3. Image Upload: Multer Package
4. Template Engine: Nunjucks

---

## Functions

1. Login
2. Kakao Login
3. Image Upload
4. Write a Posts
5. Search by Hashtag
6. Follower / Following

---

## TODO

1. Delete Following
2. Change Profile
3. Like a Posts
4. Delete Posts

---

## 디렉토리별 세부 사항

1. **models**: 시퀄라이즈의 모델들, 사용자 모델과 게시글 모델, 해시태그 모델 정의.
   - **index.js**: 생성한 모델들을 시퀄라이즈에 등록하는 스크립트. 각각의 모델들을 시퀄라이즈 객체에 연결한다.
   - **user.js**: 시퀄라이즈 모델을 상속 받아 생성자에서 부모의 생성자를 실행. 첫 번째 파라미터는 테이블 칼럼에 대한 설명이고 두 번째 파라미터는 테이블 자체에 대한 설정이다. 이메일, 닉네임, 비밀번호, 제공자, SNS 아이디를 저장한다. 제공자가 local 이라면 로컬 로그인을 한 것이고 kakao라면 카카오 로그인을 한 것이다. 테이블 옵션으로 timestamps 와 paranoid 가 true로 주어졌으므로 createdAt, updatedAt, deletedAt 칼럼도 생성되게 된다. **associate** 에서 다른 모델들과의 관계를 설정한다. User:Post의 관계는 1:N 관계이므로 hasMany 메서드를 사용했다. User:User의 관계는 팔로우 기능을 넣기 위하여 설정했으며 N:M 관계이므로 belongsToMany 메서드를 사용했다. 같은 테이블 간 N:M 관계에서는 모델 이름과 칼럼 이름을 따로 정해야 하므로 through 옵션과 as 옵션을 활용했다.
   - **post.js**: user.js 모델과 비슷하며 게시글의 내용과 이미지 경로를 저장한다. 게시글 등록자 아이디와 같은 다른 칼럼들은 관계 설정 시 시퀄라이즈가 알아서 생성하므로 만들지 않는다. **associate** 에서 User에 종속됨을 설정하고 Hashtag와 N:M 관계를 정의한다. PostHashtag 라는 중간 모델이 생성되며
   - **hashtag.js**: 위 두 모델과 비슷하며 태그의 이름을 저장한다. 태그로 검색하는 기능을 지원하기 위해서 모델을 따로 만들었다.
2. **passport**: 회원가입과 로그인을 직접 구현할 수도 있지만 세션과 쿠키 처리 등 복잡한 작업이 많으므로 검증된 모듈을 사용하는 것이 좋다. Passport 모듈은 serializeUser와 deserializeUser가 핵심이다. serializeUser는 로그인 시 실행되며 req.session 객체에 어떤 데이터를 저장할지 정하는 메서드이다. deserializeUser는 매 요청 시 실행되며 serializeUser 메서드에서 deon의 두 번째 인수로 넣은 데이터가 deserializeUser의 매개변수가 된다. 전자는 사용자 정보를 세션에 아이디로 저장하는 것이고 후자는 세션에 저장한 아이디를 통해 사용자 정보 객체를 불러오는 것이며 이는 세션에 불필요한 데이터를 담아두지 않기 위한 과정이다. 로그인 전체 과정은 다음과 같다. 라우터를 통해 로그인 요청이 들어오고 라우터에서 passport.authenticate 메서드가 호출된다. 그 후 로그인 전략이 수행되고 성공 시 사용자 정보 객체와 함께 req.login을 호출한다. req.login 메서드가 passport.serializeUser를 호출하며 req.session에 사용자 아이디만 저장되며 로그인이 완료된다. 로그인 이후에 요청이 들어오면 passport.session 미들웨어가 passport.deserializeUser 메서드를 호출하고 req.session에 저장된 아이디로 DB에서 사용자를 조회한 뒤 조회된 사용자 정보를 req.user에 저장하므로 라우터에서 req.user 객체를 사용 가능하게 된다. localStrategy, kakaoStrategy는 로그인 전략에 대한 파일이다. 로그인 시의 동작을 전략이라는 용어로 표현하며 로그인 과정을 어떻게 처리할 지 설명하는 파일이다.
3. **routes**: 라우터들.
4. **uploads**: nodebird의 이미지 업로드는 multer 패키지를 통해 게시글을 저장할 때 DB에 이미지 경로만을 저장하고 이미지는 서버 디스크에 저장하는 방식으로 진행된다. Uploads 디렉터리에는 사용자들이 업로드한 실제 이미지가 저장된다.
5. **views**: 클라이언트 코드.
