# Legal Chihuahua 判決文獻搜尋 API

> [!IMPORTANT]
>
> 此服務僅限 [2024 法律 x 法遵科技黑客松](https://hackathon.lawsnote.com/) 比賽期間免費開放無上限額度版本。

Legal Chihuahua 是一套用來搜尋台灣判決文獻的服務，其核心由卓騰的 [CopyToaster 自然語言智慧文件搜尋助理](https://api.droidtown.co/copyToaster/) 所構成。在使用者輸入一段特定詞句後，經由獨家演算法結合 [Articut 詞性標記](https://api.droidtown.co/ArticutAPI/document/#ArticutAPI) 與 `語意計算(Semantic Computing)`，挑選出與輸入文義 **最相近** 的幾則判決書原文，協助使用者快速的從茫茫資料海中找到想要的文件。

想吉人或是被吉了嗎？別擔心，就讓 Legal 吉娃娃來幫助你快速找到想要的判決紀錄吧！

<img src="https://github.com/Droidtown/Legal_Chihuahua/blob/main/Legal_Chihuahua.png?raw=true" width="400">


### Legal Chihuahua API 參數設定 

在 [呼叫 Legal Chihuahua API](#呼叫-Legal-Chihuahua-API) 時，您可以自訂以下的欄位內容：

#### 判決類別 (`copytoaster_key`)
為了令使用者能更精確的搜尋到想要的文件，Legal Chihuahua API 將判決文獻歸為以下四大類別，每個類別皆伴隨著一個使用金鑰：
- 刑事_Judgement: `hZYNiEDyd5VCpP(FJS1w@UeRsgLr$QA`
- 民事_Judgement: `gO(oHjM6!qyc7$iRWV@nD0GvIaTw13t`
- 高等刑事_Judgement: `NGaX&K2P5G39cpKHFNSrMyK=wzPJJSb`
- 高等民事_Judgement: `P7zdKSLg2_vC9Q$)bUFGqtfX6kTcpNJ`

> [!IMPORTANT]
> 
> 在使用 Legal Chihuahua API 時，請在 `copytoaster_key` 欄位內指定您欲搜尋的判決書類別金鑰，以獲得最優良的搜尋結果。

#### 搜尋法院 (`category`)
依現行法條規範，不同的判決類別會由各自的管轄法院進行裁決，因此在使用 Legal Chihuahua API 時，您需要指定要搜尋的法院名稱，以提昇搜尋的精準度。

> [!IMPORTANT]
> 
> 若是想同時搜尋所有法院，請分項依序搜尋各個法院，再將結果統整。

> [!NOTE]
>
> 您可以在這個欄位填入法院全名，亦或是一些常用的別名。以使用 `臺灣臺北地方法院` 為例，您可以填入以下的名稱：
> - 臺灣臺北地方法院
> - 臺北地方法院
> - 臺北地院
> 
> `臺` 與 `台` 兩字可通用

詳細可使用的法院列表請參考 [附錄_法院列表](#附錄_法院列表)

#### 關鍵字段 (`input_str`)
在此欄位輸入任何您想要查詢的文字即可。

#### [可選]輸出判決數目 (`count`)
指定本次搜尋輸出的判決文數目。
預設為 `15` 篇，上限可達 `200` 篇（此數量已充分您實作一個RAG功能了！）

### 呼叫 Legal Chihuahua API

以下以 `Python` 程式碼為範例，詳述如何呼叫 Legal Chihuahua API：
```python
from requests import post

# copytoaster_key
# hZYNiEDyd5VCpP(FJS1w@UeRsgLr$QA => 刑事_Judgement
# gO(oHjM6!qyc7$iRWV@nD0GvIaTw13t => 民事_Judgement
# NGaX&K2P5G39cpKHFNSrMyK=wzPJJSb => 高等刑事_Judgement
# P7zdKSLg2_vC9Q$)bUFGqtfX6kTcpNJ => 高等民事_Judgement

url = "https://api.droidtown.co/CopyToaster/API/"
payload = {
    "username": "legaltech@droidtown.co", # 請勿更改此欄位
    "copytoaster_key": "hZYNiEDyd5VCpP(FJS1w@UeRsgLr$QA", # 這裡替換成您想要搜尋的法院金鑰！
    "category": "臺灣台北地方法院", # 這裡輸入您想搜尋的法院名稱
    "input_str": "用LINE恐嚇取財", # 這裡輸入您想要搜尋的文句
    "count": 1 # optional, default = 15
}

print(post(url, json=payload).json())
```

成功呼叫 Legal Chihuahua API 後，您應該會得到如以下的結果：

```json
{
  "status": true,
  "msg": "Success!",
  "results": [
    {
      "category": "臺灣臺北地方法院",
      "title": "刑事判決_112,易,534_2023-11-16",
      "document": "臺灣臺北地方法院>>\n臺灣臺北...(中略)...在其與丙○○(所涉恐嚇取財等罪嫌，另為不起訴處分)、乙○○3人之LINE群組內，以暱稱「Yoting su小祐」，對乙○○傳送「你是皮在癢你他媽下週給我滾出來」、「不然我他馬的一定打爆你」等加害於乙○○身體之惡害告知訊息，致乙○○心生畏懼，致生危害於其安全。...(下略)",
      "highlight": ["上級法院", "中華民國刑法", "...(下略)"]
    }
  ]
}
```

### Legal Chihuahua API 回傳值

#### 成功
成功呼叫 Legal Chihuahua API 時，您應該會得到下列的回傳欄位：
- status: `Boolean`, 此次呼叫 Legal Chihuahua 是否成功執行。
- msg: `String`, 此次呼叫 Legal Chihuahua 的提示訊息。
- results: `List of Object`, 搜尋結果列表，列表內的判決文數量取決於呼叫時的參數 `count`。<br>其中每一篇判決文的欄位如下：
  - category: `String`, 此案件所屬管轄法院名稱。
  - title: `String`, 此案件之裁判編號。
  - document: `String`, 此案件之判決全文。
  - highlight: `List of String`, 此案件之重點詞彙。

#### 失敗
以下簡述幾項呼叫 Legal Chihuahua API 失敗時的回傳結果：

##### [帳號/判決類別金鑰]錯誤
當您輸入錯誤的 帳號(`username`) 或是 判決類別金鑰(`copytoaster_key`) 時，會出現以下的錯誤：
```json
{
    "status": false,
    "msg": "Invalid project."
}
```

##### 法院名稱錯誤
當您輸入錯誤的 法院名稱(`category`) 時，會出現以下的錯誤：
```json
{
    "status": false,
    "msg": "Invalid category."
}
```

##### 關鍵字段搜尋失敗
當您輸入的 關鍵字段(`input_str`) 與資料庫內所有判決書的關聯過小，此時將不會有判決文獻輸出，並出現以下的錯誤：
```json
{   
    "status": false,
    "msg": "Document not found."
}
```

### 附錄_法院列表
以下條列在 Legal Chihuahua API 中，欄位法院 (`category`) 內可搜尋的法院名稱：

#### 刑事_Judgement
copytoaster_key: `hZYNiEDyd5VCpP(FJS1w@UeRsgLr$QA`
- 臺灣澎湖地方法院
- 臺灣南投地方法院
- 臺灣嘉義地方法院
- 臺灣臺北地方法院
- 臺灣橋頭地方法院
- 臺灣新竹地方法院
- 臺灣臺南地方法院
- 臺灣基隆地方法院
- 臺灣苗栗地方法院
- 臺灣臺中地方法院
- 臺灣彰化地方法院
- 臺灣新北地方法院
- 臺灣士林地方法院
- 臺灣桃園地方法院
- 臺灣高雄地方法院
- 臺灣花蓮地方法院
- 臺灣宜蘭地方法院
- 臺灣臺東地方法院
- 臺灣屏東地方法院
- 臺灣雲林地方法院
- 福建連江地方法院
- 福建金門地方法院
- 智慧財產及商業法院
- 臺灣高雄少年及家事法院
- 司法院刑事補償法庭

#### 民事_Judgement
copytoaster_key: `gO(oHjM6!qyc7$iRWV@nD0GvIaTw13t`
- 臺灣花蓮地方法院
- 臺灣澎湖地方法院
- 臺灣宜蘭地方法院
- 臺灣臺東地方法院
- 臺灣南投地方法院
- 臺灣苗栗地方法院
- 臺灣基隆地方法院
- 臺灣橋頭地方法院
- 臺灣雲林地方法院
- 臺灣屏東地方法院
- 臺灣嘉義地方法院
- 臺灣臺南地方法院
- 臺灣彰化地方法院
- 臺灣新竹地方法院
- 臺灣高雄地方法院
- 臺灣桃園地方法院
- 臺灣士林地方法院
- 臺灣臺中地方法院
- 臺灣臺北地方法院
- 臺灣新北地方法院
- 福建連江地方法院
- 福建金門地方法院
- 智慧財產及商業法院
- 臺灣高雄少年及家事法院

#### 高等刑事_Judgement
copytoaster_key: `NGaX&K2P5G39cpKHFNSrMyK=wzPJJSb`
- 臺灣高等法院 臺南分院
- 臺灣高等法院 高雄分院
- 臺灣高等法院 花蓮分院
- 臺灣高等法院 臺中分院
- 福建高等法院金門分院
- 臺灣高等法院
- 最高法院

#### 高等民事_Judgement
copytoaster_key: `P7zdKSLg2_vC9Q$)bUFGqtfX6kTcpNJ`
- 臺灣高等法院 臺中分院
- 臺灣高等法院 高雄分院
- 臺灣高等法院 花蓮分院
- 臺灣高等法院 臺南分院
- 福建高等法院金門分院
- 臺灣高等法院
- 最高法院
