# 네이버 검색 API 사용하기
## 네이버 API 사용법
* https://developers.naver.com/docs/serviceapi/search/blog/blog.md#python 에서 사용법을 확인합니다.
* 임시 인증키
    * ID : VaF0hDU4GiXIBHcQBsGh
    * PW : xWYrXTVelb 
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

* 실제 검색결과는 items라는 항목에 저장되어 있습니다.
```python
result['items']
```
* title이 제목입니다.
```python
titles = [result['items'][n]['title'] for n in range(len(result['items']))]
titles
```

* author는 작가입니다.
```python
authors = [result['items'][n]['author'] for n in range(len(result['items']))]
authors
```

* pubdate는 출판일입니다.
```python
pubdates = [result['items'][n]['pubdate'] for n in range(len(result['items']))]
pubdates
```

* DataFrame을 만들어서 데이터를 저장합니다.
```python
pd.DataFrame({'책제목' : titles, '작가' : authors, '출판일' : pubdates})
```

* 함수를 만들어서 여러 결과를 저장할 수 있도록 합니다.
```python
def get_dataframe(url):
    client_id = "YOUR_CLIENT_ID"
    client_secret = "YOUR_CLIENT_SECRET"
    
    request = urllib.request.Request(url)
    request.add_header("X-Naver-Client-Id",client_id)
    request.add_header("X-Naver-Client-Secret",client_secret)
    response = urllib.request.urlopen(request)

    result = json.loads(response.read().decode('utf-8'))
    
    end_num = result['display'] #검색 결과 갯수입니다.
    
    titles = [result['items'][n]['title'] for n in range(end_num)]
    authors = [result['items'][n]['author'] for n in range(end_num)]
    pubdates = [result['items'][n]['pubdate'] for n in range(end_num)]
    
    return pd.DataFrame({'책제목' : titles, '작가' : authors, '출판일' : pubdates})
```
```python
url = create_url("book", "파이썬", 1, 100)
result_search = get_dataframe(url)
result_search
```

* range 함수를 사용해서 결과를 가져오겠습니다.
* range(시작, 끝, 간격)
* range(1,300,100)은 1, 101, 201이 됩니다.
* concat으로 DataFrame 데이터를 합칩니다.
```pytnon
result_search = []
for n in range(1,300,100):
    url = create_url("book", "파이썬", n, 100)
    result_search.append(get_dataframe(url))
result_search = pd.concat(result_search)
result_search
```

## 엑셀로 저장하기
* xlsxwriter 모듈을 설치합니다.
* ```pip install xlsxwriter```
* pd.ExcelWriter(경로, engine='xlsxwriter')로 객체를 만듭니다.
* to_excel(writer객체, sheet_name='시트이름')으로 저장합니다.
* writer.save()로 저장합니다.
```python
writer = pd.ExcelWriter("./data/python_book_info.xlsx", engine='xlsxwriter')
result_search.to_excel(writer, sheet_name="Sheet1")
writer.save()
```
* 현재 버전에서는 writer.save()대신에 writer.close()를 사용해야 합니다.
