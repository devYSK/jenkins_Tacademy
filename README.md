# jenkins_Tacademy

T아카데미 Jenkins를 활용한 CI/CD 강의 정리

# 강의 목표

- CI/CD 파이프라인의 기본 개념에 대해서 이해한다.
- 기본적인 운영환경(DEV, QA, PROD) 이 어떻게 구성되고 운영되는지 이해한다.
- Jenkins 의 기본 개념에 대해 이해한다.
- Jenkins 를 통해 기본적인 배포 파이프라인을 직접 구축할 수 있다.
- 실제 운영기에서 특히 AWS 기반의 클라우드 환경에서 Jenkins 가 어떻게 활용 되는지 알 수 있다.

# 목차

1. CI/CD 란 무엇인가?
2. Jenkins 의 기본 개념과 동작 방식
3. 개발 환경 및 CI/CD 의 기본 동작 이해
4. Jenkins 및 플러그인 설치 실습
5. CI/CD 파이프라인 구축및 QnA
6. 실제 운영 환경에서 Jenkins 사용 사례 알아보기(DW ETL Pipeline)

# 1. CI/CD 란 무엇인가?

## CI/CD 란? 
* ### Continuous Integration => 뭘 통합한다는 거야?
  * 여러 개발자들의 코드베이스를 계속해서 통합하는 것.
    * 서로 차이가 있는걸 머지하고 배포하는게 아닌, 합해서 지속적으로 가능한 빠르게 배포하는것 
  * continuous integration (CI) is the practice of merging all developers' working copies to a shared mainline several times a day


* ### Continuous Delivery => 무엇을 배달한다는 거야?
  * 사용자에게 제품을 서비스를 지속적으로 배달한다!
  * 코드베이스가 항상 배포가능한 상태를 유지하는 것.

* ### Continuous Deployment
  * 코드베이스를 사용자가 사용가능한 환경에 배포하는 것을 자동화함


> 즉, CI/CD 란 각각의 개발자들이 개발을 하는 개발 환경을 사용자가 사용 가능한 서비스로 전달하는 모든 과정을 지속 가능한 형태로 또 가능하다면 자동으로 해서 개발자와 사용자 사이의 격차를
없애는 것이다!  
이러한 과정에는 코드를 빌드하고, 테스트하고 배포하는 활동이 있다.

* ![](img/a748f5b0.png)


## CI가 왜 필요한가?

- 10명의 개발자가 열심히 개발
  - Merge할때 지옥이다
  - 마지막 커밋 누구냐? 내꺼 안되잖아
  - 10명걸 한번에 합칠라 하면 오류가 난다.

* 코드를 작성하자마자 합치자!

## CD는 왜 필요한가?

- 백엔드 코드 개발
  - 프론트와 협업해야 하니 배포를 해볼까
  - 누가 배포했어 내꺼 안되는데 등 


* 가능한 최대한 많이 빨리빨리 내 코드를 코드베이스에 안착시키자!  
  테스트 코드 없는 무서운 코드 버그 더미 코드를 애초에 코드베이스에서쫒아내자

##   CD와 함께라면?

- 10명의 개발자가 열심히 개발
- 끝. (머지됬으니까 내 역할은 여기까지 peace…)

- QA 엔지니어와 같은 내부사용자 혹은 실제 production 환경의 사용자에게
  지속적이고 안정적으로 서비스를 제공한다.

# 젠킨스(Jenkins)??? - 비서

* Java Runtime 위에서 동작하는 자동화 서버!  
  빌드, 테스트, 배포 등 모든 것을 자동화 해주는 자동화 서버!

* 비서! 난 개발만 할테니까 귀찮은건 니가 다해라!

## 젠킨스의 기본 개념과 동작 방식

* ### 젠킨스는 서버다!

* 기본 개념
  - Java Runtime Environment 에서 동작
  - 다양한 플러그인들을 활용해서 각종 자동화
  작업을 처리할 수 있음
  - 일련의 자동화 작업의 순서들의 집합인 Pipeline 을 통해 CI/CD 파이프라인을 구축함

* Plugin
  - 정~~~말 많은 플러그인들이 존재
  - 대표적인 플러그인들
    - Credentials Plugin
    - Git Plugin
      - 젠킨스가 git에있는 소스코드를 긁어와서 빌드하고 테스트 배 
    - Pipeline (핵심 기능인 파이프라인 마저도 플러그인!)
  - 처음에는 그냥 Recommend 해주는 것을 깔면 어지간한건 다 깔려있다!

# Plugin 살펴보기
* Credentials Plugin
  * Jenkins 는 그냥 단지 서버일 뿐이기 때문에 배포에 필요한 각종 리소스( 가령 클
라우드 리소스 혹은 베어메탈에 ssh 접근 등) 에 접근하기 위해서는 여러가지 중
요 정보들을 저장하고 있어야 한다.
  * 이런 중요한 정보 (AWS token, Git access token, etc…) 들을 저장해 주는 플러그인

* Pipeline Plugin
  * 젠킨스의 핵심 기능인 Pipeline 을 관리할 수 있게 해주는 플러그인

* Docker plugin and Docker Pipeline
  * Docker agent 를 사용하고 jenkins 에서 도커를 사용하기 위함

* ## Pipeline (젠킨스의 핵심!)
* 젠킨스가 돌아가는걸 설명 : 파이프라인들이 파이프라인을 타고 뭔 서비스가 흘러가는데 그 파이프라인을 구성하는게 여러 플러그인들이다. 
* 파이프라인이란 CI/CD 파이프라인을 젠킨스에 구현하기 위한 일련의 플러그인들
의 집합이자 구성.
* 즉 여러 플러그인들을 이 파이프라인에서 용도에 맞게 사용하고 정의함으로써 파
이프라인을 통해 서비스가 배포됨
* Pipeline DSL(Domain Specific Language) 로 작성

* ![](img/fb5a8568.png)

* Pipeline 을 구성하는 요소
  * 파이프라인이란 CI/CD 파이프라인을 젠킨스에 구현하기 위한 일련의 플
  러그인들의 집합이자 구성.
  * 즉 여러 플러그인들을 이 파이프라인에서 용도에 맞게 사용하고 정의함
  으로써 파이프라인을 통해 서비스가 배포됨
  * 두가지 형태의 Pipeline syntax가 존재(1. Declarative, 2. Scripted Pipeline)
  * 우리는 최신이자 더 가독성 좋은 문법을 가진 Declarative Pipeline syntax 를 사용

## Pipeline Syntax(문법)

* ### Sections
  1. Agent section
  2. Post section
  3. Stages section
  4. Steps section
    으로 구성

1. Agent Section
  * > 젠킨스는 많은 일들을 해야 하기 때문에 혼자 하기 버겁다.
  여러 slave node 를 두고 일을 시킬 수 있는데, 이처럼 어떤 젠킨스가 일을
  하게 할 것인지를 지정한다.
  젠킨스 노드 관리에서 새로 노드를 띄우거나 혹은 docker 이미지등을 통해
  서 처리할 수 있음

2. Post section
   * 스테이지가 끝난 이후의 결과에 따라서 후속 조치를 취할 수 있다.
   * Ex) success, failure, always, cleanup
     * 성공시, 실패시, 항상, 초기화시 등  
   * Ex) 성공시에 성공 이메일, 실패하면 중단 혹은 건너뛰기 등등…

3. Stages Section
   * 어떤 일들을 처리할 건지 일련의 stage 를 정의함

4. Steps Section
   * 한 스테이지 안에서의 단계로 일련의 스텝을 보여줌

* ### Declaratives
* 뭐 웹팩도 빌드하고, 스태틱파일도 넣어주고 옮겨주고 해야할 때 
  * Environment, stage, options, parameters, triggers, when
  등의 Declarative 가 있음
  * Environment -> 어떤 pipeline 이나 stage scope 의 환경 변수 설정
  * Parameter -> 파이프라인 실행시 파라미터 받음
  * Triggers -> 어떤 형태로 트리거 되는가 (어떤 주기로 실행이 되는가 )
  * When -> 언제 실행되는가


* Steps
  * 플러그인별 스텝 종류들 : https://www.jenkins.io/doc/pipeline/steps/
  - Steps 내부는 여러가지 스텝들로 구성
  - 여러 작업들을 실행가능
  - 플러그인을 깔면 사용할 수 있는 스텝들이 생겨남

##  젠킨스 설치하기 

```shell
yum update -y
# Jenkins 패키지 추가
sudo wget -O /etc/yum.repos.d/jenkins.repo http://pkg.jenkins.io/redhat/jenkins.repo
&&
sudo rpm --import https://pkg.jenkins.io/redhat/jenkins.io.key
# Install java, docker, git
sudo yum install -y java-1.8.0-openjdk jenkins git docker
# 자바 버전 8 로 설정
alternatives --config java
service jenkins start
```

# 개발 환경 및 CI/CD 동작 이해 

## 개발 환경의 종류에는 무엇이 있을까?
- 개발자가 개발을 하는 Local 환경(자신의 workspace 안에서 일을함)
- 개발자들끼리 개발 내용에 대한 통합 테스트를 하는 Development 환경
- 개발이 끝나고 QA 엔지니어 및 내부 사용자들이 사용해 보기 위한 QA 환경
- 실제 유저가 사용하는 Production 환경
- 쉽게 DEV, QA, PROD 환경으로 부르자!

## 개발 프로세스
1. 개발자가 자신의 PC 에서 개발을 진행한다.
2. 다른 개발자가 작성한 코드와 차이가 발생하지 않는지 내부 테스트를 진행한다.
3. 진행한 내용을 다른 개발자들과 공유하기 위해 git 과 같은 SCM에 올린다.
   => 흔히 dev 브랜치
4. Dev브랜치의 내용을 개발 환경에 배포하기 전에 테스트와 Lint 등 코드 포맷팅을
   한다.
5. 배포하기 위한 빌드과정을 거친다.
6. 코드를 배포한다.
7. 테스트를 진행한다.
8. 위 모든 과정을 DEV, QA, PROD 환경에서 모두 하고 각각에 맞는 환경에 배포한다

## 여러 배포 환경의 관리
여러 배포환경의 관리에서 핵심은

* `### 인프라를 모듈화 하여 어떤것이 변수인지 잘 설정하고 이를 잘 설계하는것!`
가령 APP_ENV 처럼 현재 배포하고자 하는 것이 무슨 환경인지 설정하고
앱 내에서 사용하는 다양한 변수들을 APP_ENV 에 맞게 잘 가져다 쓰는것이 핵심.

서비스 내부의 변수 뿐만 아니라 클라우드 리소스를 많이 활용해서 개발하는 요
즘에는 클라우드 리소스 내에서 인프라별 키관리가 매우 중요해서 aws system
manager 의 parameter store 와 같은 키 관리 서비스를 쓰는것을 강추!


## 예시 배포 환경
1. 웹사이트 코드를 작성한다.
2. 웹사이트 코드를 린트, 웹팩 빌드 해서 AWS S3 bucket 에 html 파일을 업로드 한다
3. Node.js 백엔드 코드를 typescript 작성한다.
4. 위 코드를 javascript compile 하고, 테스트 코드를 돌려서 도커 이미지를 만들어 ECR 에 올린다.
5. 업로드한 ECR 이미지로 ECS 서비스를 재시작한다(rolling deploy) =>
   continouse deploy

## AWS 리소스 간단 리뷰 – S3
- Simple Storage Service 의 약자로 그냥 클라우드 스토리지
- 정적 웹사이트 코드배포에 용이
- 정적 웹사이트 호스팅에 필요한 다양한 기능 제공
- AWS Cloudfront와 함께 사용해서 최적화 가능하고 DNS 관리도 가능

## ECR
- Elastic Container Registry
- 도커 이미지를 저장하는 프라이빗 레포지토리
- 실제 프로덕션 환경에서는 container 기반의 배포(ECS 등을 활용) 할 것이기 때문에 반드시 repository 가 있어야 함


## AWS 리소스 간단 리뷰 – ECS
- Elastic Container Service
- 도커 컨테이너 기반으로 서비스 운용을 가능하게 해주는 간단한 서비스
- `무중단 배포(rolling update)를 제공하며 scale up 이 가능한 특징`
- `백엔드 서비스를 스케일업 가능한 형태로 배포하는데 최적화`
- 수많은 도커 컨테이너 서버를 띄우고 LB 가 이들 사이에 밸런싱을 해줌.
- fargate, ec2 모드가 있어서 docker container 리소스만 띄우거나 혹은 물리적인 EC2 instance 클러스터로 구성 가능)

* => ECS 혹은 k8s 등을 통해 rolling deploy가 처리되기 때문에 jenkins 의 역할은 배포 명령만 내려주면 된다!
  * Ex) aws ecs update-service ‘서비스 이름

### 개발환경 예시 1
* 예시는 예시일뿐, 회사(기업)마다 환경이 다르다. 

* ![](img/b26e0b85.png)
* ![](img/cecb19a5.png)











