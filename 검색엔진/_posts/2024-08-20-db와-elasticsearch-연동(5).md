db -> es 데이터 이전 방법을 기존과 다르게(기존 logstash 이용) db에 저장한 데이터들을 python의 elasticsearch 패키지를 설치해서 이용했다

### 1. DB에서 데이터 읽기

mariadb에서 데이터를 읽기위해서 python의 pymsql 라이브러리를 이용한다

``` python
import pymysql
import json

# DB 설정 데이터 가져오기
with open('config.json', 'r') as config_file:
    config = json.load(config_file)
    hosturl = config['host']
    username = config['username']
    userpassword = config['password']
    dbname = config['db']

# MariaDB 연결
db_connection = pymysql.connect(host=hosturl, user=username, password=userpassword, db=dbname, charset='utf8')
cursor = db_connection.cursor()

# 데이터 조회 쿼리
query = "SELECT id, url, title, content, category, site, date FROM notice_board"
cursor.execute(query)

# 결과 가져오기
data = cursor.fetchall()
db_connection.close()

# 데이터 확인
for row in data:
    print(row)

```

&nbsp;

### 2. Elasticsearch 설정

데이터를 저장할 elasticsearch와 연결을 위해서 설정을 해야한다 먼저 `pip install elasticsearch`로 설치한다

``` py
from elasticsearch import Elasticsearch

# Elasticsearch 클라이언트 설정
es = Elasticsearch(
    ['http://localhost:9200'],
    http_auth=('elastic', 'es_pw'),  # 기본 사용자와 비밀번호 초기설정시 알려줌 없으면 재발급
    scheme="https",
    port=9200
)
```

&nbsp;

### 3. Elasticsearch로 데이터 전송

db에서 가져온 데이터를 elasticsearch에 저장한다 여러 데이터들을 효율적으로 보내기 위해 bulk api를 사용했다
``` python
from elasticsearch.helpers import bulk

# Elasticsearch 인덱스 이름
index_name = 'notice_index'

# DB에서 가져온 데이터를 Elasticsearch 도큐먼트로 변환
def generate_docs(data):
    for row in data:
        yield {
            "_index": index_name,
            "_id": row[0],  # id를 문서 ID로 사용
            "_source": {
                "url": row[1],
                "title": row[2],
                "content": row[3],
                "category": row[4],
                "site": row[5],
                "date": row[6].strftime('%Y-%m-%d %H:%M:%S')  # 날짜 형식 변환
            }
        }

# 데이터 전송
success, failed = bulk(es, generate_docs(data))

print(f"Successfully indexed {success} documents.")
print(f"Failed to index {failed} documents.")

```

이렇게 3단계를 거쳐서 데이터를 이동시켰다 하지만 이 코드를은 스케줄링에 따라 매일 실행될탠테 그때마다 위 코드를 실행한다면 중복된 데이터들이 들어갈 수 있다 이를 방지하기 위해서 db에 저장 될때 사용되는 Id값을 이용해서 중복 저장을 방지했다

&nbsp;

### 4. 중복 데이터 저장 방지 

애초에 elasticsearch에 저장된 id값중 마지막 값을 구한다 db에서 데이터를 가져올때 그 id값 이후 데이터를 가져와서 데이터가 중복되서 저장되는것을 막았다

``` python
import pymysql
import json
from elasticsearch import Elasticsearch
from elasticsearch.helpers import bulk

# DB 설정 데이터 가져오기
with open('config.json', 'r') as config_file:
    config = json.load(config_file)
    db_config = {
        'host': config['host'],
        'user': config['username'],
        'password': config['password'],
        'db': config['db']
    }

# Elasticsearch 설정 데이터 가져오기
es_config = {
    'hosts': ['http://localhost:9200'],
    'http_auth': ('elastic', config['es_pw']),  
    'scheme': 'https',
    'port': 9200
}

# Elasticsearch 클라이언트 설정
es = Elasticsearch(
    es_config['hosts'],
    http_auth=es_config['http_auth'],
    scheme=es_config['scheme'],
    port=es_config['port']
)

# Elasticsearch에서 가장 최근의 `id` 가져오기
def get_last_indexed_id():
    query = {
        "size": 1,
        "sort": [
            {
                "id": {
                    "order": "desc"
                }
            }
        ],
        "_source": ["id"]
    }
    response = es.search(index="notice_index", body=query)
    hits = response['hits']['hits']
    if hits:
        return hits[0]['_id']
    return 0

# DB에서 데이터 읽기 (최신 `id` 이후 데이터만)
def fetch_data_from_db(last_id):
    db_connection = pymysql.connect(
        host=db_config['host'],
        user=db_config['user'],
        password=db_config['password'],
        db=db_config['db'],
        charset='utf8'
    )
    cursor = db_connection.cursor()

    # 데이터 조회 쿼리 (last_id 이후 데이터만 가져오기)
    query = "SELECT id, url, title, content, category, site, date FROM notice_board WHERE id > %s"
    cursor.execute(query, (last_id,))

    # 결과 가져오기
    data = cursor.fetchall()
    db_connection.close()
    return data

# DB에서 가져온 데이터를 Elasticsearch 도큐먼트로 변환
def generate_docs(data):
    for row in data:
        yield {
            "_index": 'notice_index',  # Elasticsearch 인덱스 이름
            "_id": row[0],  # id를 문서 ID로 사용
            "_source": {
                "url": row[1],
                "title": row[2],
                "content": row[3],
                "category": row[4],
                "site": row[5],
                "date": row[6].strftime('%Y-%m-%d %H:%M:%S')  # 날짜 형식 변환
            }
        }

# Elasticsearch에 데이터 전송
def index_data():
    last_id = get_last_indexed_id()  # 가장 최근의 id 가져오기
    data = fetch_data_from_db(last_id)
    success, failed = bulk(es, generate_docs(data))
    print(f"Successfully indexed {success} documents.")
    print(f"Failed to index {failed} documents.")

# 메인 실행
if __name__ == "__main__":
    index_data()

```

