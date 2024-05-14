# 臺灣 YouTube 頻道策略分析與優化（Python）

[Colab Notebook 完整內容在這裡](https://github.com/ThomasTsao47/Youtube_Project/blob/main/Files/Youtube_data_project.ipynb)

## 0.目錄
1. 專案概述
2. 資料來源
3. 工具使用
4. 套件載入
5. 資料收集<br>
  5-1 載入 YOUTUBE API KEY<br>
  5-2 取得 channelId<br>
  5-3 Channel<br>
  5-4 Playlist<br>
  5-5 Video<br>
  5-6 VideoCategory<br>
  5-7 Comment<br>
6. 資料合併
7. 資料探索及清洗
8. 資料分析<br>
  8-1 頻道總訂閱數，總影片數，總觀看數<br>
  8-2 每月平均觀看數<br>
  8-3 觀眾互動程度<br>
  8-4 每月發布影片數及影片平均長度<br>
  8-5 影片發布時間<br>
  8-6 影片最常設定的標籤（Tags）<br>
  8-7 觀看數最多的影片標題<br>
  8-8 影片標題及影片說明的字數 & 標籤使用數量<br>
9. 結論
10. 建議
11. 參考資料

## 1.專案概述
為了解臺灣知名 Youtube 頻道（包含Joeman，木曜4超玩，志祺七七X圖文不符，Dcard Video，Men's Game 玩物誌）經營 YT 的相關策略及表現，因此透過 YouTube Data API 取得所需資料，並使用 Google Colab Notebook 分析頻道訂閱數，影片數，觀看數，觀眾互動程度，影片發布策略，影片文字設定策略等，以了解各頻道間的差異，並提供相關決策建議，期望提升影片點擊率及吸引目標用戶。

## 2.資料來源
* 資料來源：[YouTube Data API](https://developers.google.com/youtube/v3/getting-started?hl=zh-tw)
* 資料期間：由各頻道成立起至2024年4月27日（後續分析會排除上傳時間在2024年3月31日之後的影片，而在2024年4月1日之前上傳的影片皆會統計到2024年4月27日）
* 資料集：[Datasets](https://github.com/ThomasTsao47/Youtube_Project/tree/main/Data)

## 3.工具使用
* Python 3.10.12（requests，pandas，matplotlib，seaborn，os，asyncio，aiohttp，tqdm，nest_asyncio，ast，re）
* [YouTube Data API (v3)](https://developers.google.com/youtube/v3/getting-started?hl=zh-tw)

## 4.套件載入
```
# 安裝套件（用於處理異步 HTTP 請求的庫）
!pip install aiohttp
```

```
# 載入套件
import requests
import pandas as pd
import matplotlib.pyplot as plt
import seaborn as sns
import os
import asyncio
import aiohttp
from tqdm import tqdm
import nest_asyncio
import ast
import re
from matplotlib.font_manager import FontProperties as font
```

## 5.資料收集
以下先透過 API 取得 channelId，再透過 channelId 依序取得頻道，播放清單，影片，影片類型及觀眾留言資料，並將資料存進個人雲端硬碟。

說明：
* 使用 Youtube Data API 中的大多數方法會消耗1點 quota cost，而無效的請求也會產生至少1點的 quota cost；另外如果使用會傳回多頁結果的方法（如search.list），則每個檢索附加結果頁的請求都會產生額外的 quota cost。
* Google Cloud API Quota Cost 歸零時間是台灣時間每日下午3點
* Queries per day limitation（Youtube Data API）：10,000
* [YouTube Data API (v3) - Quota Calculator](https://developers.google.com/youtube/v3/determine_quota_cost)
  
### 5-1 載入 YOUTUBE API KEY
### 5-2 取得 channelId
### 5-3 Channel
### 5-4 Playlist
### 5-5 Video
### 5-6 VideoCategory
### 5-7 Comment

## 6.資料合併
因取得上述資料的過程有可能發生連線中斷的問題，所以在資料取得後皆先存進雲端硬碟，下面再從雲端路徑將各個檔案讀取進來並做合併。

## 7.資料探索及清洗
* 檢查是否有重複值
* 處理缺失值
* 調整資料型態
* 排除異常資料(影片未播，影片測試等)
* published_at 欄位為 ISO 8601 時間長度，因此將其轉換為分鐘(取到小數點後1位)，並創建一個新的欄位存儲轉換後的分鐘數
* 篩選上傳時間最晚為 113/3/31 當天結束為止的影片

**清洗後資料**：<br>
![Cleaned Data](Images/Cleaned_Data.png)<br><br>
**筆數及型態**：<br>
![Cleaned Data](Images/Cleaned_Data_info.png)<br><br>

## 8.資料分析
以下分析的資料包含 Youtube 所有的影片類型：一般影片，shorts，直播，Podcast

### 8-1 頻道總訂閱數，總影片數，總觀看數
頻道成立時間先後排序為：Men's Game 玩物誌（2007年），Joeman（2010年），Dcard Video（2016年），木曜4超玩（2017年），志祺七七X圖文不符（2018年）。

首先初探幾個宏觀指標，包含總訂閱數，總影片數，總觀看數，以了解各頻道目前的情況，並展開後續的分析。

