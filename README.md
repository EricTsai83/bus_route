# 獲取交通部提供的交通數據
## step1. 取得串接交通部 [ptx 公共運輸整合資訊流通服務平台的API](https://ptx.transportdata.tw/PTX/) 的 app_id 和 app_key

[操作指南參考](https://motc-ptx-api-documentation.gitbook.io/motc-ptx-api-documentation/api-zi-liao-shi-yong-zhu-yi-shi-xiang)

## step2. WEB API 應用程式介接
資料說明: [參考](https://ptx.transportdata.tw/MOTC?t=Bus&v=2#!/CityBus/CityBusApi_StopOfRoute_1)

先去上方網頁介面，透過介面選取你要的資料，再點選 `Try it out!`，下方會產出資料取得的方法和資料範例，找到Request URL並複製(用來讓 python 爬資料的頁面)


## step3. 透過 request_bus_data.ipynb 取得交通資料
---
### 取得版本詳細資訊
>資料說明:
```
DisplayHealth {
    ServiceID (String): API服務代碼 ,
    ServiceName (String): API服務中文名稱 ,
    Inbound (Inbound, optional): 本平台資料來源轉入狀態 ,
    Outbound (Outbound, optional): 本平台API狀態
    }
    Inbound {
    CheckTime (DateTime): 檢核時間 ,
    Status (Int32): 檢核結果狀態 : [0:'失敗',1:'成功'] ,
    Reason (String, optional): 檢核失敗原因
    }
    Outbound {
    CheckTime (DateTime): 檢核時間 ,
    Status (Int32): 檢核結果狀態 : [0:'失敗',1:'成功'] ,
    Reason (String, optional): 檢核失敗原因
}
```
### 市區公車之路線資料
>資料說明:
```
BusRoute {
    RouteUID (String): 路線唯一識別代碼，規則為 {業管機關簡碼} + {RouteID}，其中 {業管機關簡碼} 可於Authority API中的AuthorityCode欄位查詢 ,
    RouteID (String): 地區既用中之路線代碼(為原資料內碼) ,
    HasSubRoutes (Boolean): 實際上是否有多條附屬路線。(此欄位值與SubRoutes結構並無強烈的絕對關聯。詳細說明請參閱swagger上方的【資料服務使用注意事項】) ,
    Operators (Array[RouteOperator]): 營運業者 ,
    AuthorityID (String): 業管機關代碼 ,
    ProviderID (String): 資料提供平台代碼 ,
    SubRoutes (Array[BusSubRoute], optional): 附屬路線資料(如果原始資料並無提供附屬路線ID，而本平台基於跨來源資料之一致性，會以SubRouteID=RouteID產製一份相對應的附屬路線資料(若有去返程，則會有兩筆)) ,
    BusRouteType (Int32): 公車路線類別 : [11:'市區公車',12:'公路客運',13:'國道客運',14:'接駁車'] ,
    RouteName (NameType): 路線名稱 ,
    DepartureStopNameZh (String, optional): 起站中文名稱 ,
    DepartureStopNameEn (String, optional): 起站英文名稱 ,
    DestinationStopNameZh (String, optional): 終點站中文名稱 ,
    DestinationStopNameEn (String, optional): 終點站英文名稱 ,
    TicketPriceDescriptionZh (String, optional): 票價中文敘述 ,
    TicketPriceDescriptionEn (String, optional): 票價英文敘述 ,
    FareBufferZoneDescriptionZh (String, optional): 收費緩衝區中文敘述 ,
    FareBufferZoneDescriptionEn (String, optional): 收費緩衝區英文敘述 ,
    RouteMapImageUrl (String, optional): 路線簡圖網址 ,
    City (String, optional): 路線權管所屬縣市(相當於市區公車API的City參數)[若為公路/國道客運路線則為空值] ,
    CityCode (String, optional): 路線權管所屬縣市之代碼(國際ISO 3166-2 三碼城市代碼)[若為公路/國道客運路線則為空值] ,
    UpdateTime (DateTime): 資料更新日期時間(ISO8601格式:yyyy-MM-ddTHH:mm:sszzz) ,
    VersionID (Int32): 資料版本編號
}
RouteOperator {
    OperatorID (String): 營運業者代碼 ,
    OperatorName (NameType): 營運業者名稱 ,
    OperatorCode (String): 營運業者簡碼 ,
    OperatorNo (String): 營運業者編號[交通部票證資料系統定義]
}
BusSubRoute {
    SubRouteUID (String): 附屬路線唯一識別代碼，規則為 {業管機關簡碼} + {SubRouteID}，其中 {業管機關簡碼} 可於Authority API中的AuthorityCode欄位查詢 ,
    SubRouteID (String): 地區既用中之附屬路線代碼(為原資料內碼) ,
    OperatorIDs (Array[string]): 營運業者代碼 ,
    SubRouteName (NameType): 附屬路線名稱 ,
    Headsign (String, optional): 車頭描述 ,
    HeadsignEn (String, optional): 車頭英文描述 ,
    Direction (Int32): 去返程 : [0:'去程',1:'返程',2:'迴圈',255:'未知'] ,
    FirstBusTime (String, optional): 平日第一班發車時間 ,
    LastBusTime (String, optional): 平日返程第一班發車時間 ,
    HolidayFirstBusTime (String, optional): 假日去程第一班發車時間 ,
    HolidayLastBusTime (String, optional): 假日返程第一班發車時間
}
NameType {
    Zh_tw (String, optional): 中文繁體名稱 ,
    En (String, optional): 英文名稱
}
```

### 市區公車之路線站序資料
>資料說明:
```
BusStopOfRoute {
    RouteUID (String): 路線唯一識別代碼，規則為 {業管機關簡碼} + {RouteID}，其中 {業管機關簡碼} 可於Authority API中的AuthorityCode欄位查詢 ,
    RouteID (String): 地區既用中之路線代碼(為原資料內碼) ,
    RouteName (NameType): 路線名稱 ,
    Operators (Array[RouteOperator], optional): 營運業者 ,
    SubRouteUID (String): 附屬路線唯一識別代碼，規則為 {業管機關簡碼} + {SubRouteID}，其中 {業管機關簡碼} 可於Authority API中的AuthorityCode欄位查詢 ,
    SubRouteID (String): 地區既用中之附屬路線代碼(為原資料內碼) ,
    SubRouteName (NameType): 附屬路線名稱 ,
    Direction (Int32, optional): 去返程 : [0:'去程',1:'返程',2:'迴圈',255:'未知'] ,
    City (String, optional): 站牌權管所屬縣市(相當於市區公車API的City參數)[若為公路/國道客運路線則為空值] ,
    CityCode (String, optional): 站牌權管所屬縣市之代碼(國際ISO 3166-2 三碼城市代碼)[若為公路/國道客運路線則為空值] ,
    Stops (Array[Stop]): 所有經過站牌 ,
    UpdateTime (DateTime): 資料更新日期時間(ISO8601格式:yyyy-MM-ddTHH:mm:sszzz) ,
    VersionID (Int32): 資料版本編號
}

NameType {
    Zh_tw (String, optional): 中文繁體名稱 ,
    En (String, optional): 英文名稱
}

RouteOperator {
    OperatorID (String): 營運業者代碼 ,
    OperatorName (NameType): 營運業者名稱 ,
    OperatorCode (String): 營運業者簡碼 ,
    OperatorNo (String): 營運業者編號[交通部票證資料系統定義]
}

Stop {
    StopUID (String): 站牌唯一識別代碼，規則為 {業管機關簡碼} + {StopID}，其中 {業管機關簡碼} 可於Authority API中的AuthorityCode欄位查詢 ,
    StopID (String): 地區既用中之站牌代碼(為原資料內碼) ,
    StopName (NameType): 站牌名稱 ,
    StopBoarding (Int32, optional): 上下車站別 : [-1:'可下車',0:'可上下車',1:'可上車'] ,
    StopSequence (Int32): 路線經過站牌之順序 ,
    StopPosition (PointType): 站牌位置 ,
    StationID (String, optional): 站牌所屬的站位ID ,
    StationGroupID (String): 站牌所屬的組站位ID ,
    LocationCityCode (String, optional): 站牌位置縣市之代碼(國際ISO 3166-2 三碼城市代碼)[若為公路/國道客運路線則為空值]
}

PointType {
    PositionLon (number, optional): 位置經度(WGS84) ,
    PositionLat (number, optional): 位置緯度(WGS84) ,
    GeoHash (String, optional): 地理空間編碼
}
```


>### 市區公車之站牌資料
```
BusStop {
    StopUID (String): 站牌唯一識別代碼，規則為 {業管機關簡碼} + {StopID}，其中 {業管機關簡碼} 可於Authority API中的AuthorityCode欄位查詢 ,
    StopID (String): 地區既用中之站牌代碼(為原資料內碼) ,
    AuthorityID (String): 業管機關代碼 ,
    StopName (NameType): 站牌名稱 ,
    StopPosition (PointType): 站牌位置 ,
    StopAddress (String, optional): 站牌地址 ,
    Bearing (String, optional): 方位角，E:東行;W:西行;S:南行;N:北行;SE:東南行;NE:東北行;SW:西南行;NW:西北行 ,
    StationID (String, optional): 站牌所屬的站位ID ,
    StationGroupID (String): 站牌所屬的組站位ID ,
    StopDescription (String, optional): 站牌詳細說明描述 ,
    City (String, optional): 站牌權管所屬縣市(相當於市區公車API的City參數)[若為公路/國道客運路線則為空值] ,
    CityCode (String, optional): 站牌權管所屬縣市之代碼(國際ISO 3166-2 三碼城市代碼)[若為公路/國道客運路線則為空值] ,
    LocationCityCode (String, optional): 站牌位置縣市之代碼(國際ISO 3166-2 三碼城市代碼)[若為公路/國道客運路線則為空值] ,
    UpdateTime (DateTime): 資料更新日期時間(ISO8601格式:yyyy-MM-ddTHH:mm:sszzz) ,
    VersionID (Int32): 資料版本編號
}
NameType {
    Zh_tw (String, optional): 中文繁體名稱 ,
    En (String, optional): 英文名稱
}
PointType {
    PositionLon (number, optional): 位置經度(WGS84) ,
    PositionLat (number, optional): 位置緯度(WGS84) ,
    GeoHash (String, optional): 地理空間編碼
}
```