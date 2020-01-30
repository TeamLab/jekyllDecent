---
layout: post
title: "Selenium으로 네이버 연극 데이터 크롤링하기 with Python"
date: 2020-01-17 14:30:00 +0300
tags: crawling, selenium, crawling with Python
category: Crawling with Python
author: leejiheon
math: false
published: true
comments: true
---


이 포스팅은 Selenium으로 웹 브라우저를 제어해서 네이버(Naver) 월/일/주간/주말 별 연극 데이터를 파이썬을 통한 웹 크롤링을 하고 데이터는 csv 파일의 형태로 저장까지 하는 방법을 차례대로 말씀드리도록 하겠습니다.


## Selenium으로 네이버 연극 데이터 크롤링하기 with Python

![Naver_theater](https://user-images.githubusercontent.com/48443734/72893751-32196780-3d5d-11ea-9ec3-6b5fa24a3409.PNG)

다음과 같이 보이는 웹페이지에서 연극 데이터를 크롤링 하는 것이 목표입니다. 하지만 이 웹페이지는 다음 버튼을 클릭해야만 하기 때문에 BeautifulSoup만 이용하여 크롤링을 해야한다면 어려움이 생깁니다. 그래서 Selenium을 활용해야 합니다.

### Selenium
Selenium은 웹을 테스트하기 위한 프레임워크입니다. 또한 브라우저를 제어할 수 있기 때문에 로그인이 필요한 웹 사이트나 자바스크립트(JavaScript)로 동적으로 생성되는 웹 사이트의 데이터를 크롤링할 때 매우 유용하게 사용되는 스크래핑 도구입니다.

### 사전준비
- **Selenium Install**<br>
터미널 창에서 다음과 같이 명령어를 입력하면 Selenium을 설치 할 수 있습니다.<br>
**(파이썬이 없다면 파이썬을 먼저 설치 해주세요.)**
  <pre>$ pip install selenium</pre>

  >아나콘다를 이용하여 파이썬을 설치하셨을 경우
  <pre>$ conda install selenium</pre>

- **Chromedriver Install**<br>
원하는 웹 드라이버를 설치합니다. 다운 받은 압축(Zip) 파일은 압축을 해제하고 chromedriver.exe 파일은 크롤링 파일과 함께 위치 할 수 있는 폴더로 이동 시킵니다.
<br>

  >이 포스팅에서는 Chrome 브라우저를 사용합니다. 링크를 누르면 다운로드 받을 수 있습니다.

  https://sites.google.com/a/chromium.org/chromedriver/ (Chrome 브라우저용)
  <br>

  ![스크린샷, 2020-01-16 15-54-43](https://user-images.githubusercontent.com/48443734/72500788-a002f200-3878-11ea-832f-69470c3d4509.png)
<br>

- **Urllib Install**<br>
터미널 창에서 다음과 같이 명령어를 입력하면 Urllib을 설치 할 수 있습니다.<br>
  <pre>$ pip install urllib</pre>

  >이 포스팅에서는 input을 받기 때문에 한글 텍스트를 퍼센트 인코딩으로 변환하기 위한 quote_plus를 호출하기 위해 urllib을 설치합니다.

<br>

- **Pandas Install**<br>
터미널 창에서 다음과 같이 명령어를 입력하면 Pandas를 설치 할 수 있습니다.<br>
  <pre>$ pip install pandas</pre>

  >DataFrame을 만들고 csv 형태의 파일로 저장하는데 유용합니다.

<br>

### Selenium 사용법
- **selenium을 호출한 후 드라이버 생성하기**<br>

```python
from selenium import webdriver    # 라이브러리에서 사용하는 모듈만 호출


chromedriver = '{chromedriver.exe 파일이 존재하는 경로}'
driver = webdriver.Chrome(chromedriver)
```

<br>

- **크롤링 사이트 호출 및 크롬 브라우저 닫기**<br>

```python
driver.get('{url}')    # 크롤링할 사이트 호출

drver.quit()    # 크롬 브라우저 닫기
```

<br>

- **데이터를 가져오기 위한 주요 함수**<br><br>
  - **find_element_by_class_name()** : class name이 입력한 값과 일치하는 것 중에서 가장 먼저 발견된 한 개만 가져옴
  - **find_elements_by_class_name()** : class name이 입력한 값과 동일한 모든 것을 리스트로 가져옴


```python
# tag, id, css_selector, xpath로도 사용 가능

mydata = driver.find_element_by_class_name('list_title')  # 클래스 명이 list_title인 모든 것을 리스트로 가져와서 mydata에 할당
```

<br>

### Headless Chrome

- **Headless Chrome** : PhantomJS와 유사한 기술로 크롬브라우저 기능으로 개발됨
  > PhantomJS : 크롬 브라우저 화면을 띄우지 않고 제어가 가능, 현재는 지원을 안하며 Headless Chrome 사용을 권장

<br>

- **Headless Chrome 사용법**<br>

```python
from selenium import webdriver


options = webdriver.ChromeOptions()
options.add_argument('headless')    # 웹 브라우저를 띄우지 않는 headless chrome 옵션 적용
options.add_argument('disable-gpu')    # GPU 사용 안함
options.add_argument('lang=ko_KR')    # 언어 설정
driver = webdriver.Chrome(chromedriver, options=options) #  옵션 적용
```

<br>

### Crawling with Python Code
작성한 코드를 살펴봅니다.<br>먼저 필요한 라이브러리와 모듈을 호출합니다.<br>By, WebDriverWait, expected_conditions, TimeoutException, time들은 아래에서 코드를 보며 어떻게 쓰이는지 설명 드리도록 하겠습니다.

```python
from urllib.parse import quote_plus    # 한글 텍스트를 퍼센트 인코딩으로 변환
from selenium import webdriver    # 라이브러리에서 사용하는 모듈만 호출
from selenium.webdriver.common.by import By
from selenium.webdriver.support.ui import WebDriverWait   # 해당 태그를 기다림
from selenium.webdriver.support import expected_conditions as EC
from selenium.common.exceptions import TimeoutException    # 태그가 없는 예외 처리
import time
import pandas as pd
```

<br>

네이버에서 url을 분석합니다. 자세하게 보면 빨간색으로 표시한 곳만 변경되는 것을 확인할 수 있습니다.

![1월16일연극검색URL](https://user-images.githubusercontent.com/48443734/72531199-53d5a300-38b4-11ea-81e0-8ea619b59000.PNG)
![이번주연극검색URL](https://user-images.githubusercontent.com/48443734/72531200-53d5a300-38b4-11ea-86f1-5861d0b12932.PNG)

<br>

이것으로 입력한 결과에 따라 원하는 데이터를 얻을 수 있기 때문에 아래와 같이 input을 받아서 format하여 url을 만듭니다. 하지만 한글로 입력 시 인식을 못하는 이슈가 발생하기 때문에 quote_plus로 한글 텍스트를 퍼센트 인코딩으로 변환해 줍니다.

<br>

```python
user_input = quote_plus(input('''-월--일, -월, 이번주, 이번주말 중 선택하여 입력해주세요.
                                 (-은 숫자 입력, 이번년도만 가능) : '''))

url = f'https://search.naver.com/search.naver?where=nexearch&sm=tab_etc&query={user_input}%20%EC%97%B0%EA%B7%B9%20%EA%B3%B5%EC%97%B0'
```

<br>

Chromedriver에 headless 옵션을 다음과 같이 적용합니다.

<br>

```python
options = webdriver.ChromeOptions()
options.add_argument('headless')    # 웹 브라우저를 띄우지 않는 headless chrome 옵션 적용
options.add_argument('disable-gpu')    # GPU 사용 안함
options.add_argument('lang=ko_KR')    # 언어 설정
driver = webdriver.Chrome(chromedriver, options=options)

driver.get(url)
```

<br>

- **태그 존재 여부 확인 기능** : 해당 태그가 존재하는지 확인합니다.

```python
from selenium.webdriver.common.by import By
# 사용 예시: (By.CLASS_NAME, 'list_title')
# CSS_SELECTOR, ID, NAME, TAG_NAME 로도 가능
```

<br>

- **일정 시간동안 태그를 기다리는 기능** : 해당 태그를 찾을 때 까지 정해둔 시간동안 기다립니다.

```python
try:    # 정상 처리
    element = WebDriverWait(driver, 3).until(
        EC.presence_of_element_located((By.CLASS_NAME, 'list_title'))
    )    # 해당 태그 존재 여부를 확인하기까지 3초 기다림
```

<br>

1. 비어있는 리스트(theater_list)를 생성합니다.
2. pageNum은 다음으로 넘어가는 페이지의 최대 수를 class name으로 가져와서 해당 숫자만큼 for문으로 반복합니다.
3. list_title에 해당하는 모든 데이터를 리스트로 가져와서 비어있던 리스트에 append 시킵니다. (.text를 적용하면 텍스트 형태로 추출이 가능합니다.)
4. 첫 페이지를 크롤링 했다면 다음 페이지로 넘어가기 위해 해당 태그를 찾아서 .click()을 적용하여 마우스를 자동화 시킨 후 새로운 내용이 업로드 될 때 까지 time.sleep(2)를 적용하여 2초 동안 기다리게 합니다.

```python
for i in range(1, pageNum):
        theater_data = driver.find_elements_by_class_name('list_title')

        for k in theater_data:
            theater_list.append(k.text.split('\n'))

        driver.find_element_by_xpath("//a[@class='btn_page_next _btnNext on']").click()
        time.sleep(2)
```

<br>

- **예외 처리 기능** : 해당 태그를 찾을 때 없다면 except TimeoutException만 실행 시킴

```python
from selenium.common.exceptions import TimeoutException

except TimeoutException:    # 예외 처리
    print('해당 페이지에 연극 정보가 존재하지 않습니다.')
```

<br>

- **반드시 실행 시키는 기능** : 정상, 예외 처리 둘 중 하나여도 반드시 실행시킴

```python
finally:
    driver.quit() # 종료
```

<br>

- **이미지 크롤링 png 파일 저장**

```python
img_data = driver.find_elements_by_class_name('list_thumb')

for j in img_data:
            count += 1
            j.screenshot(f'img/{count}.png')
```

![Theater IMG](https://user-images.githubusercontent.com/48443734/73162391-074e5b00-4131-11ea-9efa-cd26b27ab940.PNG)

<br>

- **Pandas**

```python
import pandas as pd

theater_df = pd.DataFrame(theater_list,
                          columns=['연극명', '기간', '장소', '개막일', '폐막일', '오픈런'])
theater_df.index = theater_df.index + 1    # 인덱스 초기값 1로 변경
theater_df['개막일'] = pd.to_datetime(theater_df['개막일'], format='%y.%m.%d.')
theater_df['폐막일'] = pd.to_datetime(theater_df['폐막일'], format='%y.%m.%d.')
```

theater_list를 Pandas.DataFrame으로 다음과 같이 표현할 수 있다.

![Theater DataFrame](https://user-images.githubusercontent.com/48443734/72877924-48afc680-3d3d-11ea-8e88-828df67bf9a2.png)

theater_df를 csv 파일의 형태로 저장한다.

```python
theater_df.to_csv(f'theater_{_input}_df.csv', mode='w', encoding='utf-8-sig',
                   header=True, index=True)
```

![Theater CSV](https://user-images.githubusercontent.com/48443734/72878495-6598c980-3d3e-11ea-8628-0617b17d7467.PNG)

<br>

최종코드는 다음과 같습니다.


```python
from urllib.parse import quote_plus    # 한글 텍스트를 퍼센트 인코딩으로 변환
from selenium import webdriver
from selenium.webdriver.common.by import By
from selenium.webdriver.support.ui import WebDriverWait   # 해당 태그를 기다림
from selenium.webdriver.support import expected_conditions as EC
from selenium.common.exceptions import TimeoutException    # 태그가 없는 예외 처리
import time
import pandas as pd

_input = input('''-월--일, -월, 이번주, 이번주말 중 선택하여 입력해주세요.
                                 (-은 숫자 입력, 이번년도만 가능) : ''')
user_input = quote_plus(_input)

url = f'https://search.naver.com/search.naver?where=nexearch&sm=tab_etc&query={user_input}%20%EC%97%B0%EA%B7%B9%20%EA%B3%B5%EC%97%B0'
chromedriver = '/home/leejiheon/workspace/crawling/chromedriver'

options = webdriver.ChromeOptions()
options.add_argument('headless')    # 웹 브라우저를 띄우지 않는 headlss chrome 옵션 적용
options.add_argument('disable-gpu')    # GPU 사용 안함
options.add_argument('lang=ko_KR')    # 언어 설정
driver = webdriver.Chrome(chromedriver, options=options)

driver.get(url)

try:    # 정상 처리
    element = WebDriverWait(driver, 3).until(
        EC.presence_of_element_located((By.CLASS_NAME, 'list_title'))
    )    # 해당 태그 존재 여부를 확인하기까지 3초 정지
    theater_list = []
    pageNum = int(driver.find_element_by_class_name('_totalCount').text)
    count = 0
    
    for i in range(1, pageNum):
        theater_data = driver.find_elements_by_class_name('list_title')
        img_data = driver.find_elements_by_class_name('list_thumb')

        for k in theater_data:
            theater_list.append(k.text.split('\n'))
        
        for j in img_data:  # 이미지 크롤링
            count += 1
            j.screenshot(f'img/{count}.png')
        
        driver.find_element_by_xpath("//a[@class='btn_page_next _btnNext on']").click()
        time.sleep(2) # 웹페이지를 불러오기 위해 2초 정지

except TimeoutException:    # 예외 처리
    print('해당 페이지에 연극 정보가 존재하지 않습니다.')

finally:    # 정상, 예외 둘 중 하나여도 반드시 실행
    driver.quit()

for i in range(len(theater_list)):
    theater_list[i].append(theater_list[i][1].split('~')[0])
    theater_list[i].append(theater_list[i][1].split('~')[1])

for i in range(len(theater_list)):
    if theater_list[i][4] == '오픈런':
        theater_list[i][4] = '50.01.01.'
        theater_list[i].append('True')
    else:
        theater_list[i].append('False')

theater_df = pd.DataFrame(theater_list,
                          columns=['연극명', '기간', '장소', '개막일', '폐막일', '오픈런'])
theater_df.index = theater_df.index + 1    # 인덱스 초기값 1로 변경
theater_df['개막일'] = pd.to_datetime(theater_df['개막일'], format='%y.%m.%d.')
theater_df['폐막일'] = pd.to_datetime(theater_df['폐막일'], format='%y.%m.%d.')
theater_df.to_csv(f'theater_{_input}_df.csv', mode='w', encoding='utf-8-sig',
                   header=True, index=True)

print('웹 크롤링이 완료되었습니다.')

```
 
<br>
크롤링을 하기 위해 여러가지 기법을 다루었습니다. 직접 다른 웹 사이트를 크롤링하기 위한 코드를 작성하며 이러한 기법들을 적용해보며 학습하시면 큰 도움이 될 것 같습니다.
