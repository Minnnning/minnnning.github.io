---
layout: post
image: "https://github.com/Minnnning/minnnning.github.io/assets/80758613/54426edc-c793-4cdc-814f-83580e5711a6"
---

데이터를 크롤링해서 db에 저장했다면 검색 엔진을 테스트할 수 있도록 logstash와 elasticsearch의 연결이 필요하다 먼저 logstash의 yml파일을 수정해서 db에서 데이터를 가져올 수 있도록 한다

## Logstash와 Elasticseatch 설치

``` shell
sudo apt install elasticsearch
```

elasticsearch를 우분투에서 설치

``` shell
sudo systemctl start elasticsearch
```

elasticsearch를 실행시키기

&nbsp;

``` shell
sudo apt install logstash
```

logstash를 우분투에 설치

``` shell
sudo systemctl start logstash
```

elasticsearch를 실행시키기&nbsp;

## jdbc 설치

마리아나 db를 사용하기 때문에 해당 db를 연결해줄 수 있는 jdbc를 다운 받아야한다

https://mariadb.com/downloads/connectors/connectors-data-access/java8-connector/

이 링크에서 자신에게 맞는 jdbc를 다운 받는다 

<center>
<img src="https://github.com/Minnnning/minnnning.github.io/assets/80758613/54426edc-c793-4cdc-814f-83580e5711a6" style="zoom:50%;">
</center>

<center>
<img src="https://github.com/Minnnning/minnnning.github.io/assets/80758613/08702af3-b670-43b2-b2c2-ad5365ba235a" style="zoom:30%;">
</center>

내가 설치할 위치로 이동해서 wget으로 설치를 진행

``` sh
wget https://dlm.mariadb.com/3752081/Connectors/java/connector-java-3.3.3/mariadb-java-client-3.3.3.jar
```

&nbsp;

## logstash yml 파일 설정하기

Logstash는 conf파일을 만들어서 데이터를 읽고 어떻게 저장할지를 작성하는 문서가 필요하다

notices.conf로 파일을 생성

``` yaml
input {
   jdbc {
        jdbc_driver_library => "/etc/logstash/mariadb-java-client-3.3.3.jar" # jdbc의 위치를 작성
        jdbc_driver_class => "org.mariadb.jdbc.Driver"
        jdbc_connection_string => "jdbc:mariadb://localhost:3306/notice" # database name
        jdbc_user => "test" #user name
        jdbc_password => "1234"
        # use_column_value => true # 추적할 컬럼을 사용할 것인가 -> 나중에 불필요한 중복 방지를 위해서 사용
        # tracking_column => idx
        # last_run_metadata_path => "/etc/logstash/inspector-index.dat" #추적 데이터 저장 파일
        statement => "select * from notice_table" # 실행한 sql명령어
        # schedule =>"57 * * * *" # 주기적으로 실행할 때 사용함
    }
}

output {
  elasticsearch {
    hosts => ["localhost:9200"]
    index => "testindex" # 인덱스 이름
    document_id => "%{title}"  # 고유 ID 필드가 있다면 이를 문서 ID로 사용
    # document_id가 겹친다면 최신 데이터만 반영
  }
  stdout { codec => json_lines }
}
```

&nbsp;

`etc/logstash/conf.d`안에 파일을 위치시킨다면 logstash를 실행 시킬 때 자동으로 실행된다 만약 정상적으로 실행이 안된다면 `etc/logstash`의 pipeline.yml 내용에 추가

```  yaml
- pipeline.id: main
  path.config: "/etc/logstash/conf.d/*.conf"
```

해당 폴더 내에 모든 conf파일을 실행한다는 명령어

&nbsp;

``` sh
sudo -u logstash /usr/share/logstash/bin/logstash --path.settings /etc/logstash -t
```

conf 파일이 정상인지 테스트 오류가 없다면

Using config.test_and_exit mode. Config Validation Result: OK. Exiting Logstash를 출력

&nbsp;

``` sh
sudo tail -f /var/log/logstash/logstash-plain.log
```

logstash의 실시간 로그를 보는 명령어

&nbsp;

## 결과

<center>
<img src="https://github.com/Minnnning/minnnning.github.io/assets/80758613/806ba39a-235a-49d6-9a8b-23f269c91126" style="zoom:170%;">
</center>
