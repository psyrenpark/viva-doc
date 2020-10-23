#
# @psyrenpark/doc


이 파일은 markdown인 .md로 만들어져 있습니다. markdown뷰어나 vscode의 markdown viwer로 보시길 바랍니다. 

#
## 문의
- psyrenpark@svcorps.com
- psyrenpark@gmail.com
- www.psyrenpark.com

#
## 목표
프로젝트를 제작함에 있어 개발자는 이 라이브러리를 사용하여 빠른 개발이 가능함을 목표로함


#
## 설명
빠른 개발이 가능하도록 공수가 많은 부분을 분리하여 모듈화하여 재사용
1. 인증,권한 및 유저관리 (cogntio)
2. 파일 관리 시스탬     (s3)
3. api 서버리스       (lambda, api gateway)
4. backend lib      (express, node.js, sms, email, nosql, aws 관련 서비스, 배치) 
5. 공통 UI           (react, react-native, 로그인 관련, cms, 게시판)  [진행중]  

#  
## 인증 관련 추가 설명
인증, 토큰, email 발송등 auth에 관하여 backend와 분리하여 cognito등 인프라단에서 처리
- 클라이언트 개발자는 인증 UI 집중 가능
- 서버개발자는 api 및 DB에 집중 가능
- ddos, 다중 트래픽, DB 자원등 에서 비용, 보안, 관리적으로 장점을 가짐 
- 아마존 및 백엔드와 연결에 관한 권한 및 유저구분 등을 백엔드 서버 앞단에서 처리
- 파일서버, api등 aws 서비스에 접근, 생성, 변경, 삭제등 권한 제어
- **예를 들어 권한 및 토큰 없이 접근시 백엔드 영역 전에 http 403으로 차단** (중요)

### COGNITO 사용법
cognito는 로그인, 토큰, 소셜등 웹 및 모바일 앱에 대한 인증, 권한 부여 및 사용자 관리를 대행 서비스
- 비슷한 서비스로는 google firebase auth
- **코그니토로 회원가입후 트리거를 통해 초기 화원정보가 DB에 생성(삽입)됩니다.** (중요)
- 유저 정보는 DB에서 api를 통해서만 변경하여 사용 (cognito는 user 정보를 email, uuid외에는 들고있지 않음)
- 기본 id 베이스는 email 기반 (현재 통합을 위해 id나 전화번호를 고유키 잡는것은 사용 불가)
- 가능한 회원 관리 (회원가입, 로그인, 소셜로그인, 로그인 유지 체크, password 변경, 인증 확인)
- 회원가입시 인증이 필요없다면 자동 이메일 인증 가능 (옵션)
- 회원가입시 인증이 필요하다면 이메일 발송 (기본) (이메일 발송 양식은 하위에 추가, 문자는 SNS 비용문제로 비활성됨, 필요시 별도 directsend를 사용한 sms backend 처리)
- 소셜로그인 (현재 구글, 애플 가능 그외 추가 요청 바람)
- 참고링크 https://docs.aws.amazon.com/ko_kr/cognito/latest/developerguide/what-is-amazon-cognito.html


#  
## 파일 관리 설명
s3라는 서버리스 기반의 서비스를 사용하여 백엔드와 분리된 파일서버를 구축
- 백엔드 서버의 가장 큰 부담인 파일서버를 분리하여 트래픽, 캐싱등에서 강력한 이점
- 이로인해 백엔드는 상대적으로 적은 cpu, ram을 사용하여 서버리스로 사용가능
- DB에는 파일의 s3 url만을 저장함으로써 db 용량 및 트래픽 절감 가능   

### S3 사용법
- 클라이언트에서 파일 다운로드 (이미지, 동영상)등을 s3에서 직접 가져옴
- 클라이언트에서 파일 업로드 (이미지, 동영상)등을 s3에서 직접 업로드함
- 클라이언트(웹, 앱, cms)에서 이 라이브러리를 사용하여 직접 권한이 있는 특정 경로에 s3에 업로드하고 성공하면 key(path)를 받아오면 이를 백엔드 api를 통해 DB에 기록(저장)합니다.
- 클라이언트에서는 백엔드 api에 결과 값에 있는 file url를 통해 직접 s3에서 다운로드 받아서 사용합니다.
- 예시) url https://wsp-prod-file.s3-ap-northeast-1.amazonaws.com/public/profile/img_1.png
- https://wsp-prod-file.s3-ap-northeast-1.amazonaws.com/은 캐싱, cdn등의 이유로 url이 변경 될수 있으니 디비에는 **그 이후 public/profile/img_1.png 만 저장**하고 backend나 client에서 앞의 도메인을 붙여서 사용하는 작업이 필요(중요)
- 참고링크 https://docs.aws.amazon.com/ko_kr/AmazonS3/latest/dev/Welcome.html

#
## API 서버리스
lambda와 api gateway를 활용한 서버리스 기반의 서비스로 인프라가 구축됨
- 백엔드에서 cloud9를 사용한 개발계와 lambda로 구성된 운영계가 나누어서 실시간 배포 및 무중단 운영이 가능함 (devops)
- 서버리스이기에 운영에서 관리적 이슈, 버그가 적게 발생 하여 유지보수의 이점
- load balancing L7 및 도메인, https, 방화벽등의 역활을 대신하는 api gateway를 사용


### 서버리스
서버리스 컴퓨팅은 IT 인프라를 데이터 센터 혹은 클라우드에 별도 준비 없이, 필요한 기능을 함수(function) 형태로 구현하고, 자동 스케일링 방식으로 시시각각 변하는 자원 수요를 지원하며 전통적인 백엔드 대신 사용
 
 ### LAMBDA 
lambda는 서비스를 사용하여 상시 서버가 아닌 필요시에만 실행되는 백엔드로 구축
- lambda에 express 기반으로 하나의 lamda에서 여러 api 라우팅 처리가 가능
- lambda는 하나의 reqeust에 대해 최대 15분동안 유지가능 (무거운처리 x)
- **30분 ~ 2시간 동안 사용없다가 사용시 초기 콜드 스타트 문제 있을수 있으나 실운영에서는 30분마다 호출하는 배치가 있어 문제 없음**(중요)
- 백엔드에서 일반적으로 사용하는 세션을 사용하지 않음
- 세션이 필요하다면  백엔드에서 통신 middleware에서 세션같이 토큰안의 uuid를 기반으로 유저 정보를 DB 또는 dynamo에서 가져옴
- 외부 api등 lmabda에서 고정아이피 필요시 요청
- 람다에서 제공되는 라이브러리 외 필요시 요청 (backend lib)
- 참고링크 https://docs.aws.amazon.com/ko_kr/lambda/latest/dg/welcome.html

### API GATEWAY
규모와 관계 없이 REST 및 WebSocket API를 생성, 게시, 유지, 모니터링 및 보호하기 위한 서비스
- 인증 없이 사용가능한 cdn url과 인증이 필요한 api url 두가지로 운영 
- api gateway의 request timeout은 29s
- socket 또는 mqtt 필요시 요청
- 속도개선 보안 accesslog 분석 필요시 요청
- 참고링크 https://docs.aws.amazon.com/ko_kr/apigateway/latest/developerguide/welcome.html

#
## BACKEND LIB 설명
일반적으로 자주 사용하는 , sms, email, nosql, aws 관련 서비스를 재사용을 위해 만든 백엔드 express middleware library 
- api lambda의 express에 붙어서 작동됨
- 필요한 라이브러리만 경량화 해서 사용


#
## 추가 설명
자세한 설명은 각 라이브러리 md에 별도 설명됨 참고바람.


#
-psyrenpark-



