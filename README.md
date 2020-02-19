# Cookie-Pool
一个强大的Cookie池项目，超乎你的想象

## 1 Summary
With the enhancement of the anti-scraping mechanism of large websites, using cookies to log in has become the most efficient way to create a super cookie pool project

Based on tornado web framework, integrates selenium, requests, session, scrapy, cookie string, browser cookie

Unify cookies from six sources into one format, and provide cookie detection settings and visualization through the console developed by tornado

This project is divided into cookie acquisition section, storage section, visual and central control section, and reserved automatic login acquisition section

## 2 file directory structure description
![Image 目录结构](./static/img/1-目录.png)
### 2.1 db
peeweetools: encapsulates the basic methods and functions for operating sqlite

redistools: encapsulates the basic methods and functions of redis
### 2.2 handle
getcookie: encapsulates the method for obtaining cookies from the local browser, requests, session, webdriver, and cookie strings
interface: encapsulates the method for coordinating the invocation of sqlite and redis. It is based on peeweetools and redistools. It encapsulates the function that mainly implements storing cookies and obtaining cookies.
### 2.4 static
Static file directory, storing sqlite
### 2.5 template
Visual html template file for tornado rendering
### 2.6 setting.py
Configuration file
### 2.7 web.py
The main program file, which implements the relevant interfaces for scheduling, webpage operations, and background operations
### 2.8 requirements.txt
The Python packages involved in the project are automatically installed using the pip install -r requirements.pip command
If browsercookie installation fails, please use the anaconda Python interpreter which comes with this package

## 3 Architecture description
![image 架构图](./static/img/2-架构.png)
Interact with the user in two ways: 1. using the web interface 2. calling internal methods
Selenium, requests, Session, scrapy cookies can only be stored by calling internal methods
Get Chrome browser cookies, parsing cookie strings can be achieved through online operations and internal methods

## 4 Front view
![image 前端视图](./static/img/3-前端视图.png)

4.1 Configuration detection method: selenium, requests
    Detected URLs, iconic fields after opening a URL
![image 配置检测](./static/img/3.1-检测.png)

4.2 Click Save after configuration detection, and then you can detect
![image 保存配置](./static/img/3.2-测试.png)

If the detection method and its fields are not configured, it cannot be detected.
![imge 检测状态](./static/img/3.3-检测中.png)

4.3 ** Detection principle: **
By opening the URL address we configured, we can detect the fields that can only appear in the URL address after login, such as: username, account name

Therefore, the configured URL is best to be visible after login.

4.4 ** Multi-Account Configuration **

If there are multiple cookies, they correspond to multiple accounts, but there are multiple user names. In this way, we can change all user names.

Connected in English (such as: Zhang San; Li Si; Lao Wang), the background will be divided by; sign field, as a keyword detection on each cookie page

If one of them is included, it is a valid cookie, otherwise it is an invalid cookie.

## 5 Redis、SQLite
### 5.1 Redis
Is the local Redis database used, download redis under window-sever.exe server can be opened

You can also modify the REDIS_URL address to the remote Redis address in the setting.py file
### 5.2 SQL
This project uses a SQLite database, which can also be replaced with mysql

Copy the db / peeweetools.py file

blog = PooledSqliteDatabase ('static / cookies.db') changed to PooledMySQLDatabase

See the specific configuration documentation
![image redis](./static/img/5-redis.png)
 

## 6 How to use
### 6.1 Start
With the environment configured, run the web.py file to start
### 6.2 Cookies
#### 6.2.1 Selenium cookies

```python
from selenium import webdriver
from handle.Interface import put_cookie

url = "http://www.baidu.com'
driver = webdriver.Chrome()
driver.get(url)
data = driver.get_cookies()
put_cookie(url, data)
```
At this time, the selenium cookie is placed in Redis, and the key in redis is cookies: domain format
### 6.2.2 Store cookies in local Chrome
After running web.py openhttp://localhost:port/
Fill in the URL in the Get cookie box
![image 获取cookie](./static/img/6.2.2-获取Chrome的cookie.png)
Or by calling an internal method
```python
from handle.getcookie import get_chrome_cookie
from handle.Interface import put_cookie

 url = " http://www.taobao.com "
 cookie_list = get_chrome_cookie (url) # returns Taobao's cookie list
 put_cookie (url, cookie_list)
```
### 6.2.3 cookies for requests / session
Passing a response object to the get_reque_session_cookie function will parse the cookie
```python
import requests
from requests import Session
from handle.Interface import put_cookie
from handle.getcookie import get_reque_session_cookie

url = "http://www.taobao.com"
response = requests.get(url)
session = Session.get(url)
cookies = get_reque_session_cookie(response)
cookies = get_reque_session_cookie(session) 
put_cookie(url, cookies)
```
### 6.2.4 storing scrapy cookies
The usage method is roughly the same as the method for obtaining requests / Sessioncookie

Pass scrapy response object into get_scrapy_cookie function

### 6.2.5 Store cookie strings
```python
from handle.Interface import put_cookie
 from handle.getcookie import get_text_cookie

 url = " http://www.baidu.com "

 str = "" " REALTIME_TRANS_SWITCH = 1; FANYI_WORD_SWITCH = 1; HISTORY_SWITCH = 1;
 SOUND_SPD_SWITCH = 1; SOUND_PREFER_SWITCH = 1; to_lang_often =% 5B% 7B% 22value% 22
 % 3A % 22e n % 22% 2C% 22text % 22% 3A % 22% u82F1 % u 8BED % 22% 7D% 2C% 7B% 22value % 22% 3A% 2
 2zh % 22% 2C% 22text % 22% 3A % 22% u4E2D % u 6587 % 22% 7D% 5D; from_lang_often =% 5B% 7B% 2
 2value % 22% 3A% 22zh % 22% 2C% 22text % 22% 3A % 22% u4E2D % u 6587 % 22% 7D% 2C% 7B% 22value% 2
 2% 3A % 22e n % 22% 2C% 22text % 22% 3A % 22% u82F1 % u 8BED % 22% 7D% 5D; Hm_lvt_64ecd82404c5
 1e03dc91cb9e8c025574 = 1546396967,1548035203; BAIDUID = E6BB8BCF4B51CC6A516F391
 20FB9580F: FG = 1; PSTM = 1548043541; BIDUPSID = A4BC9C784FA26726086F330B96C55BA1;
 ZD_ENTRY = baidu; delPer = 0; locale = zh; BDORZ = B490B5EBF6F3CD402E515D22BCDA1598;
 Hm_lpvt_64ecd82404c51e03dc91cb9e8c025574 = 1548057039; PSINO = 3; BDRCVFR [qQNS-
 g6pS -_] = mbxnW11j9Dfmh7GuZR8mvqV; H_PS_PSSID = 26523_1456_21118_28329_28132_26
 350_28266_27245 "" "
 cookies = get_text_cookie (url, str )
 put_cookie (url, cookies)
```
Or in the html page:
![image 解析cookie串](./static/img/6.2.3-解析cookie字符串.png)

## 6.3 Obtaining cookies
### 6.3.1 Get cookies via interface
```python
import requests
 url1 = ' http: // localhost: port number / random? url = URL ' # Get a random cookie for this domain name
 url2 = ' http: // localhost: port number / all? url = URL ' # return all cookies for this domain

 cookies = requests.get (url1)

```
### 6.3.2 Get cookies by internal methods
```python
from handle.Interface import get_cookie

url = " http://www.taobao.com "
cookies = get_cookie (url) # returns all cookies
```
## 6.4 Cookie detection
### 6.4.1 Configuring detection information
Relevant information needs to be configured before detection
![image 检测配置](./static/img/6.4.1-检测配置.png)
TEST_TYPE: detect the type, whether to open the configured URL through requests or selenium

TEST_URL: The address of the page containing the verification information such as: login page, profile page

TEST_SIGN: identify valid cookie fields such as user name, etc. Different fields of multiple cookies are connected in English;

The three fields must be configured. You cannot save them without configuration; click Save after configuration, and then click Detect.
