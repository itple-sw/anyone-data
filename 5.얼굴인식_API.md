# 얼굴인식 등의 이미지 관련 API 사용하기
## 닮은 연예인 찾기
* 모듈을 설치합니다.
* ```pip install pillow```
* ```pip install requests```

```python
import matplotlib.pyplot as plt
import matplotlib.image as mpimg
%matplotlib inline
```

* 사진을 다운로드 받습니다.
```python
img = mpimg.imread('./data/face_detection_test_01.jpg')

plt.figure(figsize=(10,8))
plt.imshow(img)
plt.show()
```

* Clova Face Recognition을 설정해서 사용합니다.
* https://developers.naver.com/docs/clova/api/CFR/API_Guide.md에서 사용법을 확인합니다.
* 애플리케이션을 등록하고 아이디와 비밀번호를 입력합니다.
* post 방식으로 요청합니다.
* 사진은 2MB보다 작아야 합니다.
```python
import requests

client_id = "" # 자신의 ID를 입력합니다.
client_secret = "" # 자신의 비밀키를 입력합니다.

url = "https://openapi.naver.com/v1/vision/celebrity"
files = {'image': open('./data/face_detection_test_01.jpg', 'rb')}
headers = {'X-Naver-Client-Id' : client_id, 'X-Naver-Client-Secret' : client_secret}
response = requests.post(url, files=files, headers=headers)
```

* json 모듈로 데이터를 읽습니다.
* ensure_ascii=False를 사용하면 utf-8로 인코딩된 한글을 잘 표시해줍니다.
```python
import json
parsed = json.loads(response.text)
print(json.dumps(parsed, indent=4, sort_keys=False, ensure_ascii=False))
```

## 얼굴 인식하기
* 요청 API 주소를 "https://openapi.naver.com/v1/vision/face"로 바꿔서 얼굴 정보를 확인합니다.
* 얼굴 정보를 사진에 표시해줍니다.
* roi는 얼굴의 위치입니다.
* gender는 성별입니다.
* emotion은 감정입니다.
* confidence는 확률입니다.
```python
url = "https://openapi.naver.com/v1/vision/face"
files = {'image': open('./data/face_detection_test_01.jpg', 'rb')}
headers = {'X-Naver-Client-Id' : client_id, 'X-Naver-Client-Secret' : client_secret}
response = requests.post(url, files=files, headers=headers)

detect_result = json.loads(response.text)
detect_result
```

* 딕셔너리.values()로 값을 반환합니다.
```python
x, y, w, h = detect_result['faces'][0]['roi'].values()
gender, gender_confidence = detect_result['faces'][0]['gender'].values()
emotion, emotion_confidence = detect_result['faces'][0]['emotion'].values()
age, age_confidence = detect_result['faces'][0]['age'].values()
```

* 사진에 표시할 내용을 정리합니다.
* 괄호 안에 넣습니다.
```python
annotation = (gender + " : " + str(gender_confidence) + '\n' +  emotion + " : " + str(emotion_confidence) + '\n' +  age + " : " + str(age_confidence))
```

* matplotlib.patches는 사각형과 같은 도형을 그릴 수 있게 도와주는 모듈입니다.
* Rectangle로 사각형을 그립니다.
  * facecolor는 사각형 안 색깔(채움색)입니다. 
* plt.text로 글자를 씁니다.
```python
import matplotlib.patches as patches

img = mpimg.imread('./data/face_detection_test_01.jpg')

fig, ax = plt.subplots(figsize=(10,10))
ax.imshow(img)

rect_face = patches.Rectangle((x,y),w,h, linewidth=5, edgecolor='r', facecolor='none')
ax.add_patch(rect_face)

plt.text(10, 400, annotation, wrap=True, fontsize=17, color='white')
plt.show()
```

* 여러 얼굴이 있는 사진에 대한 정보를 표시해보겠습니다.
* for문을 사용해서 얼굴 갯수만큼 정보를 확인합니다.
* 얼굴 아래에 성별, 감정, 나이 정보를 글자로 표시하는 코드를 완성합니다.
```python
url = "https://openapi.naver.com/v1/vision/face"
files = {'image': open('./data/face_detection_test_02.jpg', 'rb')}
headers = {'X-Naver-Client-Id' : client_id, 'X-Naver-Client-Secret' : client_secret}
response = requests.post(url, files=files, headers=headers)

detect_result = json.loads(response.text)

img = mpimg.imread('./data/face_detection_test_02.jpg')

fig, ax = plt.subplots(figsize=(14,10))
ax.imshow(img)

for each in detect_result['faces']:
    x, y, w, h = each['roi'].values()
    gender, gender_confidence = each['gender'].values()
    emotion, emotion_confidence = each['emotion'].values()
    age, age_confidence = each['age'].values()
    
    rect_face = patches.Rectangle((x,y),w,h, linewidth=5, edgecolor='r', facecolor='none')
    ax.add_patch(rect_face)
    # 얼굴 아래에 성별, 감정, 나이 정보를 글자로 표시하는 코드를 넣습니다.  
```
