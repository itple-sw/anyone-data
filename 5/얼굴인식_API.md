# 얼굴인식 등의 이미지 관련 API 사용하기
* 모듈을 설치합니다.
* ```pip install pillow```
* ```pip install requests```

```python
import matplotlib.pyplot as plt
import matplotlib.image as mpimg
%matplotlib inline
```

```python
img = mpimg.imread('./data/face_detection_test_01.jpg')

plt.figure(figsize=(10,8))
plt.imshow(img)
plt.show()
```

* Clova Face Recognition을 설정해서 사용합니다.
```python
import requests

client_id = "" # 자신의 ID를 입력합니다.
client_secret = "" # 자신의 비밀키를 입력합니다.

url = "https://openapi.naver.com/v1/vision/celebrity"
files = {'image': open('./data/face_detection_test_01.jpg', 'rb')}
headers = {'X-Naver-Client-Id' : client_id, 'X-Naver-Client-Secret' : client_secret}
response = requests.post(url, files=files, headers=headers
```

```python
import json
parsed = json.loads(response.text)
print(json.dumps(parsed, indent=4, sort_keys=False, ensure_a
```
