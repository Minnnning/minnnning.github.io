# spring boot로 backend 작업

이번에는 요청을 처리할 api를 spring boot로 작성하는 과정입니다

현재 만들어야할 api는 crud로 이렇게 4가지를 만들어야한다

* Create -> post 요청으로 입력 받은 데이터를 받아  db에 저장한다
* Read -> get 요청으로 해당 id를 가진 글을 불러온다
* Edit -> put 요청으로 새로 들어온 데이터로 수정한다
* Delete -> delete 요청 해당된 id를 가진 글을 삭제한다

&nbsp;

&nbsp;

## intellij를 설치한다 or vscode

https://www.jetbrains.com/ko-kr/idea/download/?section=mac

vscode 이용하기

https://five-tracker-718.notion.site/c78a6da32ac04c39b3edb9fbea7ae252?pvs=4

## java 설치하기

intellij에서 sdk 추가로도 가능하다
<center>
<img src="https://github.com/Minnnning/minnnning.github.io/assets/80758613/5fb1501d-9dea-45e1-a943-5d271a191063" style="zoom:40%;">
</center>
<center>
<img src="https://github.com/Minnnning/minnnning.github.io/assets/80758613/39351816-adb7-4d23-a242-1153005222de" style="zoom:40%;">
</center>

`brew install openjdk@17`  java 17 버전 기준

설치후 path를 지정해야한다

1. `vi ~/.zshrc`

2. `export JAVA_HOME=/opt/homebrew/Cellar/openjdk@17/17.0.8.1/libexec/openjdk.jdk/Contents/Home`

3. `export PATH=${PATH}:$JAVA_HOME/bin`

4. `source ~/.zshrc`

5. `java -version` 설치된 자바 버전을 확인한다

<center>
<img src="https://github.com/Minnnning/minnnning.github.io/assets/80758613/ed65c19c-38e8-49c7-aba3-19cc48a2de99" style="zoom:50%;">
</center>

초기 설정 끝

&nbsp;

&nbsp;

## 프로젝트 생성하기

인텔리 제이  스프링 부트 프로젝트 생성하기 클릭

spring Initializr 클릭 후 설정 종속성에 spring boot devtools와 spring web을 추가후 프로젝트 생성을 완료한다

<center>
<img src="https://github.com/Minnnning/minnnning.github.io/assets/80758613/91f0be23-6721-4f3e-bd1f-0a2efd7e1810" style="zoom:50%;">
</center>

<center>
<img src="https://github.com/Minnnning/minnnning.github.io/assets/80758613/57771b6c-35fb-47a5-8877-d6778ad118f5" style="zoom:50%;">
</center>



## Hello 띄우기 backend 실행시키기

<center>
<img src="https://github.com/Minnnning/minnnning.github.io/assets/80758613/0446a8e3-2a15-4b8f-8197-3b1dcce0cc6f" style="zoom:50%;">
</center>

해당 위치에 Hello.java 파일을 생성후 아래 작성

``` java
package com.example.backend;

import org.springframework.stereotype.Controller;
import org.springframework.web.bind.annotation.GetMapping;
import org.springframework.web.bind.annotation.ResponseBody;

@Controller
public class Hello {
    @GetMapping("/hello")
    @ResponseBody
    public String hello() {
        return "Hello World";
    }
}
```

&nbsp;

### 로컬에서 실행 시키기
그냥 인텔리제이 상단 삼각형 눌러도 된다
&nbsp;
해당 프로젝트 경로에서 빌드하기

```
./gradlew build
```

프로젝트 실행하기

```
./gradlew bootRun
```

프로젝트 jar로 빌드하기

```
./gradlew bootJar
```

&nbsp;

### 프로젝트 실행 확인

<center>
<img src="https://github.com/Minnnning/minnnning.github.io/assets/80758613/92d59df4-8b02-4806-9745-2cedbb740010" style="zoom:50%;">
</center>

위 경로로 들어갔을때 hello world가 떴다면 정상적으로 실행된것이다

&nbsp;

## 프로젝트 db H2데이터베이스

build.gradle 파일에 `runtimeOnly 'com.h2database:h2` 추가

``` java
dependencies {
    implementation 'org.springframework.boot:spring-boot-starter-web'
    developmentOnly 'org.springframework.boot:spring-boot-devtools'
    testImplementation 'org.springframework.boot:spring-boot-starter-test'
    compileOnly 'org.projectlombok:lombok'
    annotationProcessor 'org.projectlombok:lombok'
    runtimeOnly 'com.h2database:h2'
}
```

이후 command + shift + i로 gradle 변경내용 로드

설치한 h2 db를 이용하기 위해서 application.properties를 수정해야한다 `/src/main/resources/application.properties`

``` java
# DATABASE
spring.h2.console.enabled=true
spring.h2.console.path=/h2-console
spring.datasource.url=jdbc:h2:~/local
spring.datasource.driverClassName=org.h2.Driver
spring.datasource.username=sa
spring.datasource.password=
```

- spring.h2.console.enabled - H2 콘솔의 접속을 허용할지의 여부이다. true로 설정한다.
- spring.h2.console.path - 콘솔 접속을 위한 URL 경로이다.
- spring.datasource.url - 데이터베이스 접속을 위한 경로이다.
- spring.datasource.driverClassName - 데이터베이스 접속시 사용하는 드라이버이다.
- spring.datasource.username - 데이터베이스의 사용자명이다. (사용자명은 기본 값인 sa로 설정한다.)
- spring.datasource.password - 데이터베이스의 패스워드이다. 로컬 개발 용도로만 사용하기 때문에 패스워드를 설정하지 않았다.

&nbsp;

### 데이터베이스 파일 생성

spring.datasource.url에 데이터베이스 파일 위치를 설정 했기 때문에 해당위치에 파일을 만들어줘야 한다
<hr>
<storng>맥</storng> : 터미널에서 `cd ~`로 홈으로 이동
`touch local.mv.db`로 데이터 베이스 파일을 생성한다
<hr>
<storng>윈도우</storng> :cmd실행
`type NUL > local.mv.db` CMD에서 빈 파일 생성 명령어
dir 명령어로 생성 확인 가능
<hr>
파일을 생성하지 않는다면 오류발생
<center>
<img src="https://github.com/Minnnning/minnnning.github.io/assets/80758613/0663275d-438c-40e4-a051-b69714d74418" style="zoom:80%;">
</center>

&nbsp;

### 데이터 베이스 접속하기

`localhost:8080/h2-console`로 접속한다

<center>
<img src="https://github.com/Minnnning/minnnning.github.io/assets/80758613/a63b4bda-3430-47f6-8c26-b44bbb386203" style="zoom:50%;">
</center>

위치를 test에서 위 설정에서 바꿔준 local로 변경한다

<center>
<img src="https://github.com/Minnnning/minnnning.github.io/assets/80758613/92fbf727-aac4-4247-b00e-e54859113884" style="zoom:30%;">
</center>

위 사진과 같이 정상으로 접속된다

&nbsp;

### JPA환경 설정

자바 프로그램에서 h2 db를 사용할 수 있도록 해야한다 자바 프로그램에서 h2 db를 조작할려면 JPA를 사용해야한다

build.gradle 수정

``` java
dependencies {
    implementation 'org.springframework.boot:spring-boot-starter-web'
    developmentOnly 'org.springframework.boot:spring-boot-devtools'
    testImplementation 'org.springframework.boot:spring-boot-starter-test'
    compileOnly 'org.projectlombok:lombok'
    annotationProcessor 'org.projectlombok:lombok'
    runtimeOnly 'com.h2database:h2'
    implementation 'org.springframework.boot:spring-boot-starter-data-jpa'
}
```

그리고 JPA를 설정하기위해 application.properties를 수정

``` java
# database
spring.h2.console.enabled=true
spring.h2.console.path=/h2-console
spring.datasource.url=jdbc:h2:~/local
spring.datasource.driverClassName=org.h2.Driver
spring.datasource.username=sa
spring.datasource.password=

# JPA
spring.jpa.properties.hibernate.dialect=org.hibernate.dialect.H2Dialect
spring.jpa.hibernate.ddl-auto=update
```

* spring.jpa.properties.hibernate.dialect - 데이터베이스 엔진 종류를 설정한다.

* spring.jpa.hibernate.ddl-auto - 엔티티를 기준으로 테이블을 생성하는 규칙을 정의한다.

  위 설정에서 spring.jpa.hibernate.ddl-auto를 update로 설정했다. update와 같은 설정값에 대해서 간단히 알아보자.

  - none - 엔티티가 변경되더라도 데이터베이스를 변경하지 않는다.
  - update - 엔티티의 변경된 부분만 적용한다.
  - validate - 변경사항이 있는지 검사만 한다.
  - create - 스프링부트 서버가 시작될때 모두 drop하고 다시 생성한다.
  - create-drop - create와 동일하다. 하지만 종료시에도 모두 drop 한다.

  개발 환경에서는 보통 update 모드를 사용하고 운영환경에서는 none 또는 validate 모드를 사용한다.

&nbsp;

## 엔티티

엔티티는 모델 또는 도메인 모델이라고 부르며 데이터베이스 테이블과 매핑되는 자바 클래스를 말한다 해당 프로젝트에서는 방명록 남기기에 대한 엔티티가 있어야 한다

&nbsp;

### 엔티티 속성 

만들 방명록에는 id, 작성자, 제목, 내용, 생성날짜등의 속성이 필요하다 

``` java
package com.example.backend;

import java.time.LocalDateTime;

import jakarta.persistence.Column;
import jakarta.persistence.Entity;
import jakarta.persistence.GeneratedValue;
import jakarta.persistence.GenerationType;
import jakarta.persistence.Id;
import lombok.Getter;
import lombok.Setter;

@Getter
@Setter
@Entity
public class Comment {
    @Id
    @GeneratedValue(strategy = GenerationType.IDENTITY)
    private Integer id;

    @Column(length = 200)
    private String subject;

    @Column(columnDefinition = "TEXT")
    private String content;

    @Column(columnDefinition = "TEXT")
    private String writer;

    private LocalDateTime createDate;
}
```

엔티티로 만들기 위해 Question 클래스에 @Entity 애너테이션을 적용했다. @Entity 애너테이션을 적용해야 JPA가 엔티티로 인식한다

&nbsp;

### 생성된 테이블 확인

<center>
<img src="https://github.com/Minnnning/minnnning.github.io/assets/80758613/3c79e0d8-7326-4526-8c01-215ca2728b9b" style="zoom:40%;">
</center>

COMMENT 테이블과 그 속성이 제대로 생성된것을 알 수 있다

&nbsp;

&nbsp;

## 레포지토리

엔티티는 데이터 속성을 정의한것일 뿐 데이터베이에 조회하거나 저장할 수 없다 데이터 처리를 위해서 레포지토리가 필요하다

> 리포지터리는 엔티티에 의해 생성된 데이터베이스 테이블에 접근하는 메서드들(예: findAll, save 등)을 사용하기 위한 인터페이스이다. 데이터 처리를 위해서는 테이블에 어떤 값을 넣거나 값을 조회하는 등의 CRUD(Create, Read, Update, Delete)가 필요하다. 이 때 이러한 CRUD를 어떻게 처리할지 정의하는 계층이 바로 리포지터리이다.

``` java
package com.example.backend;

import org.springframework.data.jpa.repository.JpaRepository;

public interface CommentRepository extends JpaRepository<Comment, Integer> {
}
```

CommentRepository.java 생성하고 작성한다

&nbsp;

&nbsp;

## 컨트롤러

주소에 따라 실행할 것을 정하는 자바 파일이다 여기에서는 crud를 구현했다

CommentController.java 파일 생성후 아래 작성

``` java
package com.example.backend;

import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.http.HttpStatus;
import org.springframework.http.ResponseEntity;
import org.springframework.web.bind.annotation.*;

import java.time.LocalDateTime;
import java.util.List;
import java.util.Optional;

@RestController
@RequestMapping("/comments")
public class CommentController {

    private final CommentRepository commentRepository;

    @Autowired
    public CommentController(CommentRepository commentRepository) {
        this.commentRepository = commentRepository;
    }

    @GetMapping
    public List<Comment> list() {
        return this.commentRepository.findAll();
    }
  
  	@GetMapping("/{id}")
    public ResponseEntity<Comment> getComment(@PathVariable Integer id) {
        Optional<Comment> optionalComment = commentRepository.findById(id);

        if (optionalComment.isPresent()) {
            Comment comment = optionalComment.get();
            return new ResponseEntity<>(comment, HttpStatus.OK);
        } else {
            return new ResponseEntity<>(HttpStatus.NOT_FOUND);
        }
    }

    @PostMapping
    public ResponseEntity<String> createComment(@RequestBody Comment comment) {
        // 클라이언트에서 전달받은 Comment 객체를 저장하기 전에 createDate를 설정
        comment.setCreateDate(LocalDateTime.now());

        // Comment 객체를 데이터베이스에 저장
        Comment savedComment = commentRepository.save(comment);

        if (savedComment != null) {
            return new ResponseEntity<>("Comment created successfully", HttpStatus.CREATED);
        } else {
            return new ResponseEntity<>("Failed to create Comment", HttpStatus.INTERNAL_SERVER_ERROR);
        }
    }

    @PutMapping("/{id}")
    public ResponseEntity<String> updateComment(@PathVariable Integer id, @RequestBody Comment updatedComment) {
        Optional<Comment> optionalComment = commentRepository.findById(id);

        if (optionalComment.isPresent()) {
            Comment existingComment = optionalComment.get();
            existingComment.setSubject(updatedComment.getSubject());
            existingComment.setContent(updatedComment.getContent());
            existingComment.setWriter(updatedComment.getWriter());

            // 변경된 Comment 객체를 데이터베이스에 저장
            commentRepository.save(existingComment);

            return new ResponseEntity<>("Comment updated successfully", HttpStatus.OK);
        } else {
            return new ResponseEntity<>("Comment not found", HttpStatus.NOT_FOUND);
        }
    }

    @DeleteMapping("/{id}")
    public ResponseEntity<String> deleteComment(@PathVariable Integer id) {
        Optional<Comment> optionalComment = commentRepository.findById(id);

        if (optionalComment.isPresent()) {
            commentRepository.deleteById(id);
            return new ResponseEntity<>("Comment deleted successfully", HttpStatus.OK);
        } else {
            return new ResponseEntity<>("Comment not found", HttpStatus.NOT_FOUND);
        }
    }
}

```

&nbsp;
## API

이제 api를 다 만들었다

위 코드에 따라

* /comments	get방식 -> 모든 데이터를 배열로 반환한다
* /comments    post방식 -> 입력받은 데이터를 저장한다
* /comments/{id}    get방식 -> 해당 id comment를 가져온다
* /comments/{id}    put방식 -> 해당 id comment를 수정한다
* /comments/{id}    delete방식 -> 해당 id comment를 삭제한다
&nbsp;
### 테스트
테스트를 위해 postman다운로드 -> 웹 개발시 많이 사용
&nbsp;
**post 요청**
<center>
<img src="https://github.com/Minnnning/minnnning.github.io/assets/80758613/6a0e07ca-03e6-4831-b46a-6ef7ada82a6c" style="zoom:80%;">
</center>
&nbsp;
**get 요청 리스트 조회**
<center>
<img src="https://github.com/Minnnning/minnnning.github.io/assets/80758613/d761efa5-555f-4eea-8c2e-7f7dce0e05e6" style="zoom:80%;">
</center>
