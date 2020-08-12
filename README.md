## 전자정부 표준 프레임워크  커스터마이징

***
파스타 클라우드 활용
1. 스프링프로젝트 bora-egov 변경 -> bora-egov
2. 이클립스에서 bora-egov 프로젝트를 파스타에 배포.(Mysql용)
3. bora-egov 프로젝트용 클라우드 DB생성: 서비스명은 egov-mysql-db
4. 파스타 클라우드에서 Mysql서비스 생성.(원격접속이름과 암호를 확인가능)
5. 이미 생성된 phpmyadmin 어플리케이션명: bora-myadmin 실행.
6. http://bora_myadmin.paas-ta.org 접속후 전자정부 프로젝트용 더미데이터 인서트.
7. http://bora-egov.paas-ta.org 사이트에서 파스타 배포결과 확인.
***

### 20200812(수) 작업내역(아래)
- 4). Junit 테스트로 CRUD 확인.
- 3). Service 클래스에서 insertMember, updateMember, deleteMember매서드 생성
- 2). DAO 클래스에서  insertMember, updateMember, deleteMember 매서드 생성
- 1). 쿼리 생성 :
src/main/resources/egovframework/mapper/com/member/member_mysql.xml

### 20200811(화) 작업내역(아래)
- Junit 테스터로 DAO의 selectMember 실행하기

```
3. egov-com-servlet.xml 파일에서 component-scan 부분에서 제외된 (exclude)를 -> 포함시킴(include)
2. src/test/java/~/TestMember.java 추가함. @contextConfigration 경로가 2개 추가.
1. 전자정부 프로젝트는 기본 Junit이 없기 때문에, 테스트환경 만들어야 함.. pom.xml에 Junit 모듈 추가하기.
```

- DAO(@Repository), Service(@Service) 만들기.

```
3. service/impl/MemberServiceImpl.java (구현클래스) @Resource 대신 @inject 사용
2. service/memberService.java (인터페이스)
1. service/MemberDAO.java (추상클래스를 사용,extends EgovAbstractMapper 필수)
```

- 프로젝트에서 마이바티스 사용하기

```
4.스프링-마이바티스 설정파일 추가: context-mapper.xml 
- configLocation: 마이바티스 설정파일 위치 mapper-config.xml 추가
- mapperLocation: 쿼리가 존재하는 폴더위치 member_mysql.xml 추가(쿼리)
3. 관리자관리 테이블과 get,set 하는 VO 만들기 : EmployerInfVO.java 
- 테이블 생성쿼리에서 필드명 복사 VO 자바파일에서 사용, 특이사항, 대->소문자Ctrl + Shift + y 단축기
2. 관리자관리에 사용되는 테이블 확인: emplyrinfo
1. 메이븐 모듈 추가(pom.xml)
<!-- 마이바티스 사용 모듈 추가 --> 
<dependency>
	<groupId>org.mybatis</groupId>
	<artifactId>mybatis</artifactId>
	<version>3.2.8</version>
</dependency>
<dependency>
	<groupId>org.mybatis</groupId>
	<artifactId>mybatis-spring</artifactId>
	<version>1.2.2</version>
</dependency>
```

### 20200810(월) 작업내역(아래)
- context-datasource.xml : Hsql 데이터베이스 사용 주석처리

```
!-- hsql -->
<!--여기만주석처리
    <!-- <jdbc:embedded-database id="dataSource-hsql" type="HSQL">
      <jdbc:script location= "classpath:/db/shtdb.sql"/>
   </jdbc:embedded-database> 
   -->
```
- globals.properties :(주,유니코드 에디터로 수정) DB에 관련된 전역변수 지정.

```
<!--주석해제-->
# DB서버 타입(mysql,oracle,altibase,tibero) - datasource 및 sqlMap 파일 지정에 사용됨
Globals.DbType = mysql
Globals.UserName= root
Globals.Password= apmsetup
<!--주석해제-->
# mysql
Globals.DriverClassName=net.sf.log4jdbc.DriverSpy
Globals.Url=jdbc:mysql://127.0.0.1:3306/sht
<!--주석-->
#Hsql - local hssql 사용시에 적용
#Globals.DriverClassName=net.sf.log4jdbc.DriverSpy
#Globals.Url=jdbc:log4jdbc:hsqldb:hsql://127.0.0.1/sampledb
```
- web.xml : 톰캣(was)가 실행될때 불러들이는 xml설정들 확인.

```
efov-com-servlet.xml(아래)
-DispatcherServlet(서블렛배치=콤포넌트=scan:@Controller,@Service,@Respositroty에 관련된 설정 수정)
-<context:component-scan base-package="egovframework,edu">
-위에서  ,edu 추가:edu.human.com패키지추가로 해당패키지로 시작하는 모든 콤포넌트를 빈(실행가능한 클래스)으로 자동등록하게 처리
```
- pom.xml : 메이븐 설정 파일중 Hsql DB를 Mysql DB사용으로 변경

```
<!--주석처리
<!--    <dependency>
         <groupId>org.hsqldb</groupId>
         <artifactId>hsqldb</artifactId>
         <version>2.3.2</version>
      </dependency> -->
      -->
      <!-- mysql driver 주석해제-->
  <dependency>
    <groupId>mysql</groupId>
    <artifactId>mysql-connector-java</artifactId>
    <version>5.1.31</version>
</dependency>

<!-- log4jdbc driver 주석해제기능: Console창에 쿼리보이기역할-->
<dependency>
    <groupId>com.googlecode.log4jdbc</groupId>
    <artifactId>log4jdbc</artifactId>
    <version>1.2</version>
    <exclusions>
        <exclusion>
            <artifactId>slf4j-api</artifactId>
            <groupId>org.slf4j</groupId>
        </exclusion>
    </exclusions>
</dependency>
```