Working Guide
=

---
## AES256 En/Decryption

_Algorithm_

: - Algorithm : AES  
- Keysize : 256 bit  
- Mode : CBC

_Key, Initial vector_

: - Key: 32 byte  (PallyCon CP Admin 페이지에서 확인 가능)
- Initial vector: 16 byte  (고정값: **1234567890abcdef**)

_Padding_

: - PKCS7





---
## Packaging [Pre-Package]

### Content ID Issue Page

#### ▪︎ REQUEST DATA

```c
http(s)://ContentIDIssuePageURL&data=Base64Encoding(AES256Encyption(XML DATA))String
```


_Format_

: - UTF-8 String Encoding 사용
- POST Method 
- URL: http(s)://Content ID Issue Page URL  
- Body
 **Parameter :** _data_ **Value II:** _XML DATA_


**_XML_**

```XML
<?xml version='1.0' encoding='UTF-8'?>
<RES>
    <FILEPATH>C:\DATA\package_content_ori.mp4</FILEPATH>
	<FILENAME>package_content_ori.mp4</FILENAME>
    <NONCE>fdfe321xb74azv</NONCE>
</RES>
```


_XML Standards_

: - **_FILEPATH_**: Packager에서 전달한 파일의 전체 경로입니다. 
- **_FILENAME_**: Packager에서 올라온 FilePath의 파일명입니다. 
- **_NONCE_**: PallyCon Cloud 서비스에서 CID 발급 페이지와 통신하는 데이터를 인증하기 위한 값입니다. 



#### ▪︎ RESPONSE DATA

```
Base64Encoding(AES256Encyption(XML DATA)) String 
```

_Format_ 

: - Base64Encoding(AES256Encyption(XML DATA)) String  
- UTF-8 String Encoding 사용


**_XML_**

*[CID 발급 성공]*
```XML
<?xml version='1.0' encoding='UTF-8'?>
<RES>
    <ERROR>0000</ERROR>
	<CID>idd_32_geedesef123fde34512dfe</CID>
    <NONCE>fdfe321xb74azv</NONCE>
</RES>
```

*[CID 발급 실패]*
```XML
<?xml version='1.0' encoding='UTF-8'?>
<RES>
    <ERROR>0001</ERROR>
	<ERRMSG>No Registerd File</ERRMSG>
    <NONCE>fdfe321xb74azv</NONCE>
</RES>
```


_XML Standards_

: - **_ERROR_**: 정의된 Error code로, 필수로 사용해야 하는 parameter입니다. 고정된 4byte의 길이를 가져야 하며, 빈 값은 허용하지 않습니다. 성공일 때는 `0000`으로 표시하고, 그외에는 실패입니다. 
- **_ERRORMSG_**: `ERROR`값이 실패이었을 때, 그와 관련된 메시지입니다. 이 parameter는 필수로 사용할 필요가 없으며, 빈값이 허용됩니다. 최대 255byte까지 허용됩니다. 
- **_CID_**: CID 발급 페이지에서 CID 정책에 의해 생성된 CID로, 성공시 필수로 사용해야 하는 parameter입니다. 빈값은 허용하지 않으며, 최대 **28byte**까지 허용합니다. CID는 영문, 숫자, 일부 특수 문자 (' _ ', ' . ')만 허용합니다. 
- **_NONCE_**: PallyCon Cloud 서비스에서 CID 발급 페이지와 통신하는 데이터를 인증하기 위한 값으로, 필수로 사용해야 하는 parameter입니다. 빈값은 허용하지 않으며, **반드시 Request Data에서의 'NONCE' 값과 동일한 값을 보내주셔야 합니다.**




### CLI(Commnad Line Interface) Packager

#### ▪︎ Input

 _Format_
 : **pallyconpack pack Site_ID Packager_Key “SourceFilePath” “DestinationFilePath” (Content_ID)**


**_CLI_**

```
// cid 입력하지 않을 때
>pallyconpack pack F001 FVJDp2LT2Xr0f4Di18z6lzv3DKvNOP20  “d:\content\OT.mp4” “d:\NCGcontent\OT.mp4.ncg” 

// cid 입력할 때
pallyconpack pack F001 FVJDp2LT2Xr0f4Di18z6lzv3DKvNOP20  “d:\content\OT.mp4” “d:\NCGcontent\OT.mp4.ncg” DELSKD34kwdKW2KHqKOERjJILSMQ
```


_CLI Standards_

: -  **_pack_**: 콘텐츠를 Packaging을 한다는 값으로, 고정 4byte 값입니다. 소문자로 사용하세요.
- **_Site_ID, Packager_Key_**: PallyCon Cloud 서비스 사이트에서 부여 받은 값입니다. 
- **_SourceFilePath, DestinationFilePath_**: 원본 파일의 전체 경로와 DRM 파일이 생성되는 전체 경로(절대 경로)입니다. 공백이 있을 경우에는 반드시 ""로 묶어서 사용해야합니다. 
- **_Content_ID_**: Content ID입니다. 직접입력할 경우에 맨 마지막에 넣어주셔야 합니다. 28byte이하의 영문과 숫자, 일부 특수문자 ( ' . ' , ' _ ' )만 가능합니다. 




### PallyCon App Packager, CLI Packager Output

> **Important** 
>  
>  - 출력은 패키징에 실패하였을 때 나타납니다.
>  - App Packager의 경우, 메인화면 하단 실행창에 표시됩니다. 


**_XML_**

```XML
<?xmlversion="1.0"encoding="UTF‐8"?>
<NCGPACKAGER>
<RESULT>1103</RESULT>
<ERRORMSG>실행 명령어 다음의 인자 값의 개수가 맞지 않습니다.</ERRORMSG>
<INFO>원본 파일이 설정되지 않았습니다.</INFO>
<WHERE>[FileName=d:\ncg_sdk_svn\application\pallyconpackagerlib\cpallyconpackconfig.cpp,Line=74]</WHERE>
</NCGPACKAGER>
<STACKTRACE>fromCPallyConPackConfig::LoadConfig</STACKTRACE>
```




_XML Standards_

: - **_RESULT_**: Packaging 결과 code 값,  성공 – 0 / 실패 – Error Code
- **_ERRORMSG_**: RESULT에 해당하는 메시지
- **_INFO_**: 오류에 따라 추가적인 정보가 들어갑니다.
- **_WHERE_**: 오류가 발생한 위치을 알려줍니다.
- **_STACKTRACE_**: 오류가 발생한 함수의 스택을 알려줍니다.





#### ▪︎ ERROR CODE

|Error Code |Explanation|
|-----------|-----------|
|**0**      |성공
|**1101**   |실행 명령어 뒤에 인자 값들이 하나도 전달되지 않았습니다.
|**1102**	|잘못된 인자 값이 입력되었습니다. (INFO 참조 바랍니다.)
|**1103**   |실행 명령어 다음의 인자 값의 개수가 맞지 않습니다.
|**1201**   |파일이 지정된 경로에 위치하지 않습니다.
|**1202**   |파일에 접근 할 수 없습니다. (권한 / 파일이름 문제)
|**1203**   |파일/폴더 생성에 실패하였습니다. (깊이가 너무 깊습니다.)
|**1204**   |파일/폴더 생성에 실패하였습니다. (이름이 너무 깁니다.)
|**1205**   |파일/폴더 생성에 실패하였습니다. (용량이 부족합니다.)
|**1206**   |파일의 해당 위치로 이동을 실패하였습니다.
|**1207**   |파일의 크기를 얻어오는데 실패하였습니다.
|**1208**   |파일/폴더를 찾을 수 없습니다.
|**1301**   |CID가 빈 값임
|**1302**	|CEK가 빈 값임
|**1303**	|NCG 헤더 길이가 잘못되었습니다.
|**1304**	|잘못된 암호화 수준입니다.
|**2001**   |서버로 요청을 보내는 중 오류가 발생하였습니다.
|**2002**   |서버로부터 오류가 반환되었습니다. (INFO 참조 바랍니다.)
|**2003**   |잘못된 블록 사이즈입니다.
|**2004**   |개인 키가 없습니다.
|**3001**   |Config 객체가 설정되지 않았습니다.
|**3002**   |Server Comm 객체가 설정되지 않았습니다.
|**4001**   |런타임 에러입니다.
|**4002**   |잘못된 xml 스키마 입니다.
|**4003**   |잘못된 시그니처 입니다.
|**-1**   	|예상 외 오류가 발생하였습니다.
|---------------|----------|-------------






---
## License

### Content Usage Right Info Page

#### ▪︎ REQUEST DATA

```c
http(s)://ContentUsageRightsInfoURL&data=Base64Encoding(AES256Encyption(XML DATA))String
```

_Format_

: - UTF-8 String Encoding 사용
- POST Method 
- URL: http(s)://Content Usage Rights Info URL 
- Body
 **Parameter :** _data_ **Value II:** _XML DATA_


**_XML_**

```XML
<?xml version='1.0' encoding='UTF-8'?>
<RES>
    <CID>C000004731001HDKLSE23KFL34KJQNC2</CID>
    <USERID>1113654772</USERID>
    <DEVICEID>YqiqMLuuKbIVtPp+Vl36RdLbE+cMmtADw5M=</DEVICEID>
    <DEVICEMODEL>iPhone</DEVICEMODEL>
    <OID>MOVIE-J-R15-2011</OID>
    <NONCE>fdfe321xb74azv</NONCE>
</RES>
```


_XML Standards_

: - **_CID_**:  사용하려는 콘텐츠에서 추출한 Site ID가 포함된 32 byte의 Content ID로, Packaging시 사용된 값입니다. 
- **_USERID_**: 콘텐츠를 사용하려는 사용자 정보입니다. 
- **_DEVICEID_**: 콘텐츠를 사용하려는 기기의 ID입니다. 
- **_DEVICEMODEL_**: 기기의 모델명입니다. 
- **_OID_**: 콘텐츠의 주문정보 (Order ID)입니다. 
- **_NONCE_**: PallyCon Cloud 서비스 사이트에서 Content Usage Info 페이지와 통신하는 데이터를 인증하기 위한 값입니다.





#### ▪︎ RESPONSE DATA

```c
Base64Encoding(AES256Encyption(XML DATA)) String 
```

_Format_ 

: - Base64Encoding(AES256Encyption(XML DATA)) String  
- UTF-8 String Encoding 사용


**_XML_**
```XML
<?xml version="1.0" encoding="UTF-8" standalone="yes"?>
<RES>
    <ERROR>0000</ERROR>
    <ERRMSG>OK</ERRMSG> 
    <LIMIT>Y</LIMIT>
    <PD_COUNT></PD_COUNT>
    <PD_START>2013-12-17T13:27:01</PD_START>
    <PD_END>2013-12-18T10:44:56</PD_END>
    <NONCE>fdfe321xb74azv</NONCE>
</RES>
```


_XML Standards_

: - **_ERROR_**: 정의된 Error code로, 필수로 사용해야 하는 parameter입니다. 고정된 4byte의 길이를 가져야 하며, 빈 값은 허용하지 않습니다. 성공일 때는 `0000`으로 표시하고, 실패일때는 업체에서 정의한 4byte의 에러코드로 정의하십시오. 
- **_ERRORMSG_**: `ERROR`값이 실패이었을 때, 그와 관련된 메시지입니다. 이 parameter는 필수로 사용해야하며, 빈값은 Error일 경우가 아닐 때만 허용됩니다. 최대 255byte까지 허용됩니다. 
- **_LIMIT_**: 콘텐츠가 무제한으로 재생될 지 결정하는 parameter입니다. 'Y', 'N'만 허용됩니다. 그외의 문자는 에러로 처리됩니다. 
- **_PD_COUNT_**: 횟수제 타입에서 사용해야 하는 횟수입니다. 횟수제 타입을 선택하시면 반드시 0보다 큰 값을 선택하셔야 합니다. 
- **_PD_START_**: 기간제 타입에서 사용해야 하는 기간 중 시작 날짜와 시간입니다. 기간제 타입을 선택하시면 반드시 이 parameter에 해당하는 값을 넣어 주셔야 합니다. 날짜와 시간은 GMT 시간으로 설현
- **_PD_END_**: CID 발급 페이지에서 CID 정책에 의해 생성된 CID로, 성공시 필수로 사용해야 하는 parameter입니다. 빈값은 허용하지 않으며, 최대 32byte까지 허용합니다. CID는 영문, 숫자, 일부 특수 문자 (' _ ', ' . ')만 허용합니다.
- **_NONCE_**: PallyCon Cloud 서비스에서 CID 발급 페이지와 통신하는 데이터를 인증하기 위한 값으로, 필수로 사용해야 하는 parameter입니다. 빈값은 허용하지 않으며, **반드시 Request Data에서의 'NONCE' 값과 동일한 값을 보내주셔야 합니다.**






### License Rule Info 

>- **횟수제:** 횟수에 따라 재생을 관리합니다. 
>- **기간제:** 기간에 따라 재생을 관리합니다. 
>- **무제한:** 무제한으로 재생할 수 있도록 합니다. 
>- **횟수제 + 기간제:** 일정 기간동안에 지정된 횟수만큼 재생되도록 관리합니다. 기간이 만료되었을 때 횟수가 남아있어도 재생은 되지 않으며, 횟수가 0이 되었을 때 기간이 남아있어도 재생되지 않습니다. 

아래의 표는 **콘텐츠 사용 권한 정보 설정에 따라 License Rule이 어떻게 설정되는지에 대한 표**입니다. 

|License Rules|LIMIT|PD_START|PD_END|COUNT|
|-------------|-----|--------|------|-----|
|**횟수제**		|Y	    |Empty 	  			|Empty	 				|0 보다 큰 값		| 
|**기간제**		|Y		|PD_END보다 이전 날짜	|PD_START보다 이후 날짜 	|Empty			|
|**기간제+횟수제**	|Y		|PD_END보다 이전 날짜	|PD_START보다 이후 날짜		|0 보다 큰 값		|
|**무제한**		|N		|Empty 	  			|Empty	 				|Empty			|
|---------------|






---
## Mobile Player

### Service Page

#### ▪︎ URL Scheme

> Single-Download URL Scheme
>: pallyconplayerplayerdownload://info=Base64Encoding(AES256Encyption(JSON DATA))&site_id=SITE ID 
> 
>Multi-Download URL Scheme
>: pallyconplayerplayerdownloads://info=Base64Encoding(AES256Encyption(JSON DATA))&site_id=SITE ID
>
>Streaming URL Scheme
>: pallyconplayerplayerplay://info=Base64Encoding(AES256Encyption(JSON DATA))&site_id=SITE ID


_Format_

: -  **UTF-8 String Encoding** 사용
- **_SITE ID_**: 서비스 사이트 식별 ID (PallyCon CP Admin 페이지에서 확인 가능)


**_JSON_**
*[Single-Download JSON String, Streaming JSON String]* 

```json
{  
"user_id":"ID",  
”current_url":"http://demo.netsync.co.kr/download_new2.asp",  
"enterprise_icon_url":"http://demo.netsync.co.kr/KR2/image/a_logo.png",  
"play_type":"0",
"service_manager":"http://demo.netsync.co.kr/LIC/service_manager.asp",
"log_type":"0",
"category_name":"Category",
"category_person":"Person",
"category_image":"http://demo.netsync.co.krKR2/image/korean_icon.png",
"category_assortment":"Assortment",
"contents":[
{  
"content_file":"http://112.136.244.30/1_low_language_basic.mp4.ncg",
 	”content_name":"Content”,
 	"content_qna":"QnA",
 	"info_orderID":"OrderID",
 	"info_one":"One",
 	"info_two":"Two",
 	"info_three":"Three",
 	"info_four":"Four"
 }
]}
```

*[Multi-Download JSON String (2 Contents)]*    
```json
{  
"user_id":"ID",  
”current_url":"http://demo.netsync.co.kr/download_new2.asp",  
"enterprise_icon_url":"http://demo.netsync.co.kr/KR2/image/a_logo.png",  
"play_type":"0",
"service_manager":"http://demo.netsync.co.kr/LIC/service_manager.asp",
"log_type":"0",
"category_name":"Category",
"category_person":"Person",
"category_image":"http://demo.netsync.co.krKR2/image/korean_icon.png",
"category_assortment":"Assortment",
"contents": [
{  
"content_file":"http://112.136.244.30/1_low_language_basic.mp4.ncg",
 	”content_name":"Content”,
 	"content_qna":"QnA",
 	"info_orderID":"OrderID",
 	"info_one":"One",
 	"info_two":"Two",
 	"info_three":"Three",
 	"info_four":"Four"
 },
{  	"content_file":"http://112.136.244.30/1_low_language_basic.mp4.ncg",
 	”content_name":"Content”,
 	"content_qna":"QnA",
 	"info_orderID":"OrderID",
 	"info_one":"One",
 	"info_two":"Two",
 	"info_three":"Three",
 	"info_four":"Four"
 }
]}
```


_JSON Standards_

: - **_user_id_**: 서비스 사이트 사용자 ID로, 반드시 사용해야 하는 parameter입니다. PallyCon Player에서는 웹 페이지 만료시 null로 전달받는 것에 대한 처리가 되어있습니다. 
- **_current_url_**: Download를 취소하거나 Streaming을 완료하였을 때, 웹페이지로 이동하기 위한 URL으로, parameter는 반드시 사용할 필요는 없습니다. 다만, 사용하지만 값이 없을 경우 빈값("")으로 넣어 주셔야 합니다.
- **_enterprise_icon_url_**: PallyCon Player의 업체 리스트에서 보여질 아이콘의 파일 주소로, parameter는 반드시 사용할 필요는 없습니다. 사용할 경우, parameter의 값이 없을 경우 리스트에는 빈 공백/기본 이미지로 보이게 됩니다. 
- **_play_type_**: PallyCon Player에서 다운로드 받은 콘텐츠를 어떤 조건에서 재생하는지 설정하는 값으로, parameter는 반드시 사용할 필요는 없습니다. 하지만, 사용할 경우 반드시 값을 넣어 주셔야 합니다. 사용하지 않을 경우, 기본값은 '2'로 설정됩니다. 
> **play_type 값 : 0 | 1 | 2**     
>• **0:** 온라인 상태에서는 콘텐츠 재생에 대한 유효성 체크(Validity check)를 하고 그 응답값에 따라 재생 여부를 결정하고, 오프라인 상태에서는 무조건 재생
>• **1:** 온라인 상태에서만 콘텐츠 재생에 대한 유효성 체크(Validity check)를 하여 그 응답값에 따라 재생 여부를 결정
>• **2:** 온라인, 오프라인에서 무조건 재생

: - **_service_manager_**: PallyCon Player에서 웹 서버와 통신할 때 사용하는 Service Manager URL으로, parameter는 반드시 사용해야 합니다. 또한 빈 값은 허용하지 않습니다.  
- **_log_type_**: PallyCon Player에서 로그 설정을 할 수 있는 parameter로, 반드시 사용할 필요는 없습니다. 하지만 사용할 경우 반드시 값을 넣어 주셔야합니다. 사용하지 않을 경우, 기본값은 '2'로 설정됩니다.   
연동 테스트나 고객 서포트 시 통신, 재생, 라이선스 정보를 얻기 위해 로그를 활용할 수 있습니다. 
> **log_type 값: 0 | 1 | 2**
>• **0:** Local DB에 로그를 3000 byte만큼 가지고 있다가 통신이나 라이선스 등 업체에서 수정해야 할 내용이 있는 경우, 웹 서버로 전송하도록 설정 (아직 제공하지 않음)  
>• **1:** 서비스 전 PallyCon Player와  연동 테스트를 하기 위해 설정되는 값, Local DB에 길이 제한 없이 로그를 저장. '환경 설정' 페이지에서 로그 버튼이 나타나며 로그를 바로 확인 가능
>• **2:** 로그를 사용하지 않음

: - **_category_name_**: PallyCon Player의 카테고리 리스트에 보여지는 카테고리 이름으로, parameter는 반드시 사용해야 합니다. 모든 문자를 허용하고, 빈값은 허용하지 않습니다. 
- **_category_person_**: 카테고리 리스트에 카테고리 이름과 함께 보여지는 카테고리 기타 정보를 넣을 수 있는 값으로, parameter는 반드시 사용해야 합니다. 모든 문자를 허용하고, 빈값은 허용하지 않습니다. 
- **_category_image_**: 카테고리 리스트에 보여질 카테고리 아이콘의 파일 주소로, parameter는 반드시 사용할 필요는 없습니다. parameter의 값이 없을 경우, 리스트에는 빈 공백/기본 이미지로 보이게 됩니다.
- **_category_assortment_**: 카테고리를 구분하기 위한 parameter로, 추후에 리스트 정렬 방식에 사용될 계획입니다. 이 값은 반드시 사용할 필요는 없습니다. 빈값을 허용합니다.
- **_contents_**: 다운로드되거나 재생되는 콘텐츠에 대한 정보가 들어있는 parameter입니다. 멀티 다운로드를 진행할 경우, contents에 해당하는 콘텐츠 정보를 배열 형태로 넣어 주셔야 합니다.
> **Single-download (Streaming)**
> • "contents":[{"content_file":"",...}]  
> **Multi-download**
> • "contents":[{"content_file":"",...},{"content_file":"",...},...]

: - **_content_file_**: 다운로드 / 재생 될 파일이 있는 서버 주소(URL)로, parameter는 반드시 사용해야 합니다. 빈값은 허용하지 않습니다.
- **_content_name_**: PallyCon Player의 콘텐츠 리스트에 보여질 콘텐츠 이름으로, parameter는 반드시 사용해야 합니다. 모든 문자를 허용하고, 빈값은 허용하지 않습니다. 
- **_content_qna_**: 재생 중 질문을 남기기 위해 브라우저의 웹페이지로 이동하기 위한  URL 주소로, parameter는 반드시 사용할 필요는 없습니다. 빈값은 허용합니다. 
- **_order_id_**: 콘텐츠의 주문 정보입니다. 이 정보는 라이선스를 발급받을 때 구매 확인을 위한 정보로 활용되며, parameter는 필수로 입력해야 합니다. 또한 빈값은 허용하지 않습니다. 
- **_info_one, info_two, info_three, info_four_**: 다운로드 완료, 유효성 체크 정보 전달시 콘텐츠의 구분을 위한 추가정보로, parameter는 필수로 입력할 필요는 없습니다. 또한 빈값을 허용합니다. 




### Service Manager Page

_[REQUEST DATA FORMAT]_

```c
http(s)://Service_Manager_URL?data=Base64Encoding(AES256Encryption(JSON DATA))String
```

_Format_

: - **UTF-8 String Encoding** 사용
- POST Method 
- URL: http(s)://Service manager URL  
- Body 
**Parameter**: _data_  
**Value**: _Base64Encoding(AES256Encyption(JSON DATA))String_    


_[RESPONSE DATA FORMAT]_

```c
Base64Encoding(AES256Encyption(JSON DATA))String
```

_Format_ 

: - Base64Encoding(AES256Encyption(JSON DATA)) String  
- **UTF-8 String Encoding** 사용




#### ▪︎ Device Registration

##### REQUEST DATA

**_JSON_**

```c
{
“mode":"Device Register”,
"site_id":"Site ID",
”user_id":"test2”,
"device_id":"DID01_WZnTQFOHmZiv1vsevtorDtFHRNc=",
"device_model":"iPod5,1",
” app_version":"1.0”
}
```


_JSON Standards_

: - **_mode_**: 모든 기능에서 웹 서버와 통신은 하나의 Service manager URL을 사용합니다. Service manager URL에서 기능별 분기를 태울 수 있도록 하는 값으로 기기 등록은 'Device Register'입니다. 
- **_site_id_**: CP Admin 페이지에서 확인이 가능한 업체에게 발급되는 값으로 고정값입니다. 
- **_user_id_**: 기기 DB에 저장되어 있는 사용자 ID 값입니다. 
- **_device_id_**: 기기에서 추출한 기기 ID입니다. 
> **IMPORTANT: 기기 ID 생성**   
>• *iOS*: UUID를 이용하여 기기 ID를 생성합니다. PallyCon Player와 웹 서버의 기기 등록 해제 없이 공장 초기화를 하고 다시 PallyCon Player를 설치할 경우, 새로운 기기 ID를 만들기 때문에 기기 등록이 실패됩니다. 이 경우에는 업체에 연락하여 기기 등록 해제를 요청해야 합니다.   
>• *Android*: 기기내의 여러 정보를 조합하여 생성합니다. 

: - **_device_model_**: 기기 모델 명입니다. 
> **기기 모델**   
> • *iOS*: 기기 플랫폼을 추출합니다.   *ex)* iPhone6,1(iPhone5S)  
> • *Android*: 기기의 환경설정에 있는 기기 모델명을 추출합니다. 

: - **_app_version_**: 마켓에 등록되어 표시되고 있는 PallyCon Player 버전입니다. 





##### RESPONSE DATA

**_JSON_**
```c
{
"response":"Y",
"message":"SUCCESS",
"message_two":""
}
```


_JSON Standards_

: - **_response_**: 기기 등록 성공 여부로, parameter는 필수로 입력하셔야 합니다. 또한 빈값은 허용하지 않습니다. 값은 'Y | N'으로 내려주셔야 합니다.
- **_message_**: 기기 등록 성공 여부에 맞는 메시지로 PallyCon Player 에서 알럿으로 표시됩니다. 이 parameter는 필수로 입력하셔야 합니다. parameter의 값은 빈값을 허용하며, 빈값일 경우 성공 여부에 따라 '기기 등록에 성공하였습니다.' 혹은 '기기 등록에 실패하였습니다.' 라는 메시지를 기본값으로  설정됩니다. 
- **_message_two_**: 성공 여부와 메시지 외에 사용할 수 있는 인터페이스 입니다. 이 parameter는 Android에서 'device_id'가 동일하게 추출되는 경우에 'NOT_SUPPORT_DEVICE_MODEL'을 값으로 넣어주면, device_id를 다시 추출하여 등록을 다시 시도합니다. 





#### ▪︎ Download Complete

##### REQUEST DATA

**_JSON_**

```c
{
“mode":"Download Complete”,
"site_id":"Site ID",
"user_id":"test2",
"device_id":"DID01_WZnTQFOHmZiv1vsevtorDtFHRNc=",
"date":"201405191120",
” download_status":"Y",
” info_orderID":"0”,
” info_one":"",
"info_two":"”,
"info_three":"A",
"info_four":"1”
}
```


_JSON Standards_

: - **_mode_**: 모든 기능에서 웹 서버와 통신은 하나의 Service manager URL을 사용합니다. Service manager URL에서 기능별 분기를 태울 수 있도록 하는 값으로 다운로드 완료는 'Download Complete'입니다. 
- **_site_id_**: Cloud Admin 페이지에서 확인이 가능한 업체에게 발급되는 값으로 고정값입니다. 
- **_user_id_**: 기기에 저장되어 있는 사용자 ID 값입니다. 
- **_device_id_**: 기기에서 추출한 기기 ID입니다. 
- **_date_**: 기기에서 추출한 현재 날짜입니다. **GMT가 아니니 주의하세요.** 
> _날짜 규격_
> **yyyyMMddHHmm**: 년도(yyyy) | 월(MM) | 일(dd) | 시(HH) | 분(mm)  
> *ex)* 201412012359      ->      2014년 12월 1일 23시 59분

: - **_download_status_**: 다운로드 완료 기능에서 다운로드가 완료되었음을 알리는 parameter입니다. 값은 Y | N으로 표시됩니다. 
- **_info_orderID, info_one, info_two, info_three, info_four_**: Protocol I(URL Scheme)으로 전달받은 값을 기기에 저장하였다가 불러온 parameter와 값들입니다. 웹 서버에서 콘텐츠 구분을 위한 추가 정보로 활용됩니다. 





##### RESPONSE DATA

**NO Response**





#### ▪︎ Validation Check

##### REQUEST DATA

**_JSON_**
```c
{
“mode":"Validity Check”,
"site_id":"Site ID",
"user_id":"test2",
"device_id":"DID01_WZnTQFOHmZiv1vsevtorDtFHRNc=",
”category_name":"[A사_무료기획]2014국어특강”,
"content_name":"1강_저_국어의 기초",
”info_orderID":"0”,
”info_one":"”,
"info_two":"",
"info_three":"A",
"info_four":"1”
}
```


_JSON Standards_

: - **_mode_**: 모든 기능에서 웹 서버와 통신은 하나의 Service manager URL을 사용합니다. Service manager URL에서 기능별 분기를 태울 수 있도록 하는 값으로 유효성 체크은 'Validity Check'입니다. 
- **_site_id_**: Cloud Admin 페이지에서 확인이 가능한 업체에게 발급되는 값으로 고정값입니다. 
- **_user_id_**: 기기에 저장되어 있는 사용자 ID 값입니다
- **_device_id_**: 기기에서 추출한 기기 ID입니다. 
- **_category_name_**: 기기 DB에 저장되어 있는 카테고리 이름입니다. 
- **_content_name_**: 기기 DB에 저장되어 있는 콘텐츠 이름입니다. 
- **info_orderID, info_one, info_two, info_three, info_four**: Protocol I(URL Scheme)으로 전달받은 값을 기기에 저장하였다가 불러온 parameter와 값들입니다. 웹 서버에서 콘텐츠 구분을 위한 추가 정보로 활용됩니다.





##### RESPONSE DATA

**_JSON_**
```c
{
"response":"0",
"message":"SUCCESS"
}
```


_JSON Standards_

: - **_response_**: 재생하려는 콘텐츠의 유효성을 서버에서 체크하여 결과를 PallyCon Player에 전달해 주는 parameter입니다. 빈값은 허용하지 않습니다. 값은 '0 | 1 | 2 | 3' 중 하나를 내려주셔야 합니다. 
> **response 값: 0 | 1 | 2 | 3 ** 
> • *0*: 재생 허가  
> • *1*: 휴강 (해당 콘텐츠의 라이선스를 삭제합니다.)  
> • *2*: 환불 (해당 콘텐츠가 포함되어 있는 카테고리까지 삭제합니다.)  
> • *3*: 재생 제한 

: - **_message_**: PallyCon Player에서 알럿으로 표시하는데 사용할 서버의 메시지를 넣는 parameter입니다. 필수로 사용해야만 합니다. 재생을 허가하는 경우 (response: 0)에는 빈값을 허용합니다. 그외에는 빈값을 허용하지 않습니다. 






#### ▪︎ LMS

##### REQUEST DATA

**_JSON_**
```c
{
“mode":"LMS”,
"site_id":"Site ID",
"user_id":"test3",
"device_id":"DID01_WZnTQFOHmZiv1vsevtorDtFHRNc=",
”date”:”201407170506",
"category_name":"Test”,
"content_name":"1강_저_국어의 기초",
”info_orderID":"0”,
”info_one":"”,
"info_two":"",
"info_three":"A",
"info_four":"1”,
”last_play_time”:”0”,
”lms_percent”:”0”,
”lms_sections”:”0:10,11:30”
}
```


_JSON Standards_

: - **_mode_**: 모든 기능에서 웹 서버와 통신은 하나의 Service manager URL을 사용합니다. Service manager URL에서 기능별 분기를 태울 수 있도록 하는 값으로 유효성 체크은 'Validity Check'입니다. 
- **_site_id_**: Cloud Admin 페이지에서 확인이 가능한 업체에게 발급되는 값으로 고정값입니다. 
- **_user_id_**: 기기에 저장되어 있는 사용자 ID 값입니다
- **_device_id_**: 기기에서 추출한 기기 ID입니다. 
- **_date_**: 진도 정보를 전달할 때의 날짜 입니다. (YYYYMMDDHHmm)
- **_category_name_**: 기기 DB에 저장되어 있는 카테고리 이름입니다. 
- **_content_name_**: 기기 DB에 저장되어 있는 콘텐츠 이름입니다. 
- **_info_orderID, info_one, info_two, info_three, info_four_**: Protocol I(URL Scheme)으로 전달받은 값을 기기에 저장하였다가 불러온 parameter와 값들입니다. 웹 서버에서 콘텐츠 구분을 위한 추가 정보로 활용됩니다.
- **_last_play_time_**: 사용자가 재생을 완료할 때의 시간입니다. 단위는 '초'입니다. 단위는 생략되어 있습니다. 
- **_lms_percent_**:  진도율로 사용자가 재생했던 부분의 퍼센트입니다. 단위는 '%'입니다. 단위는 생략되어 있습니다. 
- **_lms_sections_**: 재생 구간으로 사용자가 재생했던 부분의 구간들입니다. 단위는 '초'입니다. 단위는 생략되어 있습니다. 
> **Warnning:** PallyCon Player에서 생성하는 재생 구간은 가공된 것입니다. 이전 구간과 새로운 구간을 합쳐서 만들어집니다. 합치는 규칙은 다음과 같습니다. 
> - 재생한 구간이 겹쳐질 경우 하나의 구간으로 결정 (ex: 이전구간(10:20) + 새로운 구간(15:50) = 결과(10:50))
> - 재생한 구간이 이어질 경우 하나의 구간으로 결정 (ex: 이전구간(10:20) + 새로운 구간(21:50) = 결과(10:50))




##### RESPONSE DATA

**_JSON_**
```c
{
"response":"Y",
"message":"Success"
}
```


_JSON Standards_

:  - **_response_**: 진도 정보 처리 성공 여부로, parameter는 필수로 입력하셔야 합니다. 또한 빈값은 허용하지 않습니다. 값은 'Y | N'으로 내려주셔야 합니다.
- **_message_**: 진도 정보 처리 성공 여부에 맞는 메시지입니다. PallyCon Player 에서는 별도로 처리되지 않습니다. 






#### ▪︎ Device Un-registration

##### REQUEST DATA

**_JSON_**

```c
{
“mode":"Device Release”,
"site_id":"Site ID",
"user_id":"test3",
"device_id":"DID01_WZnTQFOHmZiv1vsevtorDtFHRNc="
}
```


_JSON Standards_

: - **_mode_**: 모든 기능에서 웹 서버와 통신은 하나의 Service manager URL을 사용합니다. Service manager URL에서 기능별 분기를 태울 수 있도록 하는 값으로 기기 등록 해제는 'Device Release'입니다. 
- **_site_id_**: Cloud Admin 페이지에서 확인이 가능한 업체에게 발급되는 값으로 고정값입니다. 
- **_user_id_**: 기기에 저장되어 있는 사용자 ID 값입니다
- **_device_id_**: 기기에서 추출한 기기 ID입니다. 






##### RESPONSE DATA

**_JSON_**

```c
{
"response":"Y",
"message":"Success"
}
```


_JSON Standards_

: - **_response_**: 기기 등록 해제 성공 여부로, parameter는 필수로 입력하셔야 합니다. 또한 빈값은 허용하지 않습니다. 값은 'Y | N'으로 내려주셔야 합니다.
- **_message_**: 기기 등록 해제 성공 여부에 맞는 메시지로 PallyCon Player 에서 알럿으로 표시됩니다. 이 parameter는 필수로 입력하셔야 합니다. parameter의 값은 빈값을 허용하며, 빈값일 경우 성공 여부에 따라 '기기 등록 해제에 성공하였습니다.' 혹은 '기기 등록 해제에 실패하였습니다.' 라는 메시지를 기본값으로  설정됩니다. 

---
