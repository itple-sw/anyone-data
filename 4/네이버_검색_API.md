# 네이버 검색 API 사용하기
* https://developers.naver.com/docs/serviceapi/search/blog/blog.md#python 에서 사용법을 확인합니다.
* 블로그에서 검색한 결과를 알려줍니다.
```python
import urllib.request
client_id = "YOUR_CLIENT_ID"
client_secret = "YOUR_CLIENT_SECRET"
encText = urllib.parse.quote("검색할 단어")
url = "https://openapi.naver.com/v1/search/blog?query=" + encText # JSON 결과
# url = "https://openapi.naver.com/v1/search/blog.xml?query=" + encText # XML 결과
request = urllib.request.Request(url)
request.add_header("X-Naver-Client-Id",client_id)
request.add_header("X-Naver-Client-Secret",client_secret)
response = urllib.request.urlopen(request)
rescode = response.getcode()
if(rescode==200):
    response_body = response.read()
    print(response_body.decode('utf-8')) #한글을 읽기 위해서 utf-8로 디코딩합니다.
    #print(response.read().decode('utf-8'))
else:
    print("Error Code:" + rescode)
```

* API 요청 주소를 만드는 함수를 정의합니다.
* 
```python
def create_url(api_node, search_text, start_num, disp_num):
    base = "https://openapi.naver.com/v1/search"
    node = "/" + api_node + ".json"
    param_query = "?query=" + urllib.parse.quote(search_text)
    param_start = "&start=" + str(start_num)
    param_disp = "&display=" + str(disp_num)
    return base + node + param_query + param_start + param_disp
```
* https://openapi.naver.com/v1/search/book.json이 책정보 API 요청 주소입니다. 
```python
create_url("book", "파이썬", 1, 10)
```

* json 모듈을 사용해서 json 파일을 읽습니다.
```python
import json

client_id = "YOUR_CLIENT_ID"
client_secret = "YOUR_CLIENT_SECRET"

url = create_url("book", "파이썬", 1, 3)

request = urllib.request.Request(url)
request.add_header("X-Naver-Client-Id",client_id)
request.add_header("X-Naver-Client-Secret",client_secret)
response = urllib.request.urlopen(request)

result = json.loads(response.read().decode('utf-8'))
result
```
