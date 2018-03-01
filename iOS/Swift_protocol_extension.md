這篇文章是介紹如何用 Swift 實作一個 `API Request client` *(What)*，  
會大量使用 `Protocol extension` *(How)* 特性來擴充這個模組，  
近而讓`程式變得容易維護且擴充性高` *(Why)*。



# 情境
常常我們需要跟後端 API 做溝通，會建立 URLRequest，基本上都需要做各種設定：

1. URL
2. Http Method
3. Request Header
4. Request Body
5. Response Body

這篇文章主要針對 3, 4, 5 項來進行擴充，
因為此三類裡面都會有很多不同的格式(灰色方框)，例如： 

1. Request Header
	1. FormURLEncodedRequestHeader
	2. JSONRequestHeader
2. Request Body
	1. IgnorableRequestBody
	2. JSONRequestBody
	3. JSONArrayRequestBody
3. Response Body 
	1. 	StringResponseBody
	2. IgnorableResponseBody
	3. JSONResponseBody
	4. JSONModelResponseBody

會介紹如何擴充這些灰色方框，達到 [Design principle S.O.I.L.D](http://teddy-chen-tw.blogspot.tw/2014/04/solid.html) 裡的 [OCP](http://teddy-chen-tw.blogspot.tw/2011/12/2.html) 原則。

![](https://i.imgur.com/spCmYSe.png)

一個 API Request Client 模組，如果一開始為了應付幾個簡單的 API，可能會這樣設計：

```swift
struct Request {
    var baseURL: String
    var path: String
    var method: String
    var headers : [String: String]
    var body : Data?
    
    func buildRequest() -> URLRequest {
        let urlString = "\(baseURL)/\(path)"
        let url = URL(string: urlString)
        var request = URLRequest(url: url!)
        
        for (key, value) in headers {
            request.setValue(value, forHTTPHeaderField: key)
        }
        
        request.httpMethod = method
        request.httpBody = body
        
        return request
    }
    
    func sendRequest(success: @escaping(String) -> (), failure: @escaping(String) -> ()) {
        let session = URLSession.shared
        
        session.dataTask(with: buildRequest()) { (data, response, error) in
            if error != nil {
                failure(error!.localizedDescription)
                return
            }
            
            let responseString = String(data: data!, encoding: .utf8)!
            success(responseString)
        }.resume()
    }
}
```



```swift
let fetchAllUsersRequest = Request(baseURL: "http://api.xxx.com",
                                   path: "users",
                                   method: "get",
                                   headers: ["Content-Type":"application/json"],
                                   body: nil)

fetchAllUsersRequest.sendRequest(success: { (users) in
    print(users)
}) { (failure) in
    print(failure)
}
```


會發現有個問題，每個 API 都要把 request header 再寫一次，  
但也不能直接寫在 `buildRequest()` 裡面，因為有時候要指定別的 header，
例如：

1. `Content-Type=application/json`
2. `Content-Type=application/x-www-form-urlencoded; charset=UTF-8`


這時我們就可以用 Protocol extension 來讓 header 的設定封裝在 extension，而不用每個 API request 都指定一次。
# 建立 RequesHeader Protocol

```swift
protocol RequestHeader {
    var headers: [String : String] { get }
}

protocol FormURLEncodedRequestHeader : RequestHeader {}

extension FormURLEncodedRequestHeader {
    var headers: [String : String] {
        return ["Content-Type" : "application/x-www-form-urlencoded; charset=UTF-8"]
    }
}

protocol JSONRequestHeader : RequestHeader {}

extension JSONRequestHeader {
    var headers: [String : String] {
        return ["Content-Type" : "application/json"]
    }
}
```

宣告兩個 header，讓不同 API 可以指定。


```swift
protocol Request : RequesHeader {
    var baseURL: String {get}
    var path: String {get}
    var method: String {get}
    var body : Data? {get}
}


extension Request {
    var baseURL: String {
        return "http://api.xxx.com"
    }
    var body: Data? {
        return nil
    }
}
```

先將 `struct Request` 改成 `protocol Request`，  
因為 `headers` 改由 `RequestHeader` 裡面拿，所以要 confirms to RequesHeader。  
再 `extension Request` 裡面寫預設的 `baseURL`，就不用每個 API 都寫一次。    



```swift
extension Request {
    func buildRequest() -> URLRequest {
        let urlString = "\(baseURL)/\(path)"
        let url = URL(string: urlString)
        var request = URLRequest(url: url!)
        
        for (key, value) in headers {
            request.setValue(value, forHTTPHeaderField: key)
        }
        
        request.httpMethod = method
        request.httpBody = body
        
        return request
    }
    
    func sendRequest(success: @escaping(String) -> (), failure: @escaping(String) -> ()) {
        let session = URLSession.shared
        
        session.dataTask(with: buildRequest()) { (data, response, error) in
            if error != nil {
                failure(error!.localizedDescription)
                return
            }
            
            let responseString = String(data: data!, encoding: .utf8)!
            success(responseString)
        }.resume()
    }
}

```

原本放到 `struct Request` 裡面的方法，移至到 `extension Request`。

```swift
struct FetchAllUsersRequest : Request, JSONRequestHeader {
    let path = "users"
    let method = "get"
}
```

原本是用 `struct Request` 類別產生 API Request 物件，現在直接定義 struct RequestAPI。  
這樣一來，如果這個 API 會呼叫一次以上，有些固定的設定(`path`, `method`)就不需要重打一次。



```swift
FetchAllUsersRequest().sendRequest(success: { (users) in
    print(users)
}) { (failure) in
    print(failure)
}
```
使用的時候，不再需要打 `在 app 期間都不會修改的設定`，因為已經封裝到 protocol 裡面。

# 建立 RequestBody Protocol

```swift
protocol RequestBody {
    func buildRequestBody() -> Data?
}

protocol IgnorableRequestBody : RequestBody {}

extension IgnorableRequestBody {
    func buildRequestBody() -> Data? {
        return nil
    }
}


protocol JSONRequestBody : RequestBody {
    var bodyDict : [String : Any] {get set}
}

extension JSONRequestBody {
    func buildRequestBody() -> Data? {
        do {
            let data = try JSONSerialization.data(withJSONObject: bodyDict, options: [])
            return data
        } catch let err {
            print(err)
        }
        
        return nil
    }
}

protocol JSONArrayRequestBody : RequestBody {
    var bodyDicts : [[String : Any]] {get set}
}



extension JSONArrayRequestBody {
    func buildRequestBody() -> Data? {
        do {
            let data = try JSONSerialization.data(withJSONObject: bodyDicts, options: [])
            return data
        } catch let err {
            print(err)
        }
        
        return nil
    }
}
```

這邊建立常用的三個 `RequetBody`：  
1. `IgnorableRequestBody`  
2. `JSONRequestBody`  
3. `JSONArrayRequestBody`  

以上這些 protocol 都 confirms to `RequetBody` 的 `buildRequestBody` 方法，  
必須將特定格式的資料轉成統一的 `Data` 型別。

```swift
protocol Request : RequestHeader, RequestBody {
    var baseURL: String {get}
    var path: String {get}
    var method: String {get}
}

extension Request {
   func buildRequest() -> URLRequest {
        // ...
        request.httpBody = buildRequestBody()
        
        return request
    }
}
```

因為 `body` 改由 `RequestBody` 裡面拿，所以要 confirms to RequestBody。  


```swift
struct UpdateUserNameRequest : Request, JSONRequestHeader, JSONRequestBody {
    let path = "user"
    let method = "post"
    var bodyDict: [String : Any]
}

UpdateUserNameRequest(bodyDict: ["name":"David", "id": 4]).sendRequest(success: { (result) in
    print(result)
}) { (failure) in
    print(failure)
}
```

# 建立 ResponseBody Protocol

原本的 `sendRequest` 在 success closure 裡面只支援 `String`  

```swift
func sendRequest(success: @escaping(String) -> (), failure: @escaping(String) -> ())
```

但實際上 ResponseBody 的格式會有很多種，  
例如 JSON, String,
甚至不在乎會傳什麼，只要知道成功就好了，  
所以會希望 success closure 都支援這些型態，就像這樣：

```swift
fileprivate func sendURLRequest(request: URLRequest, success: @escaping(Data) -> (), failure: @escaping(String) -> ()) {
    let session = URLSession.shared
    
    session.dataTask(with: request) { (data, response, error) in
        if error != nil {
            failure(error!.localizedDescription)
            return
        }
        
        if let data = data {
            success(data)
            return
        }
        
        failure("data is nil")
        
    }.resume()
}
```

先寫一個共用的 `sendURLRequest`，因為處理錯誤的邏輯可以共用。

```swift
protocol ResponseBody {
    var urlRequest : URLRequest {get}
}

extension ResponseBody where Self : Request {
    var urlRequest : URLRequest {
        return buildRequest()
    }
}
```

新增 `protocol ResponseBody`

```swift
protocol StringResponseBody : ResponseBody {}
extension StringResponseBody {
    func sendRequest(success: @escaping(String) -> (),
                     failure: @escaping(String) -> ()) {
        
        sendURLRequest(request: urlRequest, success: { (result) in
            let responseString = String(data: result, encoding: .utf8)!
            success(responseString)
        }, failure: failure)
        
    }
}

protocol IgnorableResponseBody : ResponseBody {}
extension IgnorableResponseBody {
    func sendRequest(success: @escaping() -> (),
                     failure: @escaping(String) -> ()) {
        sendURLRequest(request: urlRequest, success: { (result) in
            success()
        }, failure: failure)
        
    }
}

protocol JSONResponseBody : ResponseBody {}
extension JSONResponseBody {
    func sendRequest(success: @escaping([String: Any]) -> (),
                     failure: @escaping(String) -> ()) {
        sendURLRequest(request: urlRequest, success: { (data) in
            do {
                print("ffff")
                if let jsonDictionary = try JSONSerialization.jsonObject(with: data, options: .allowFragments) as? [String: Any] {
                    success(jsonDictionary)
                }
            }
            catch _ {
                failure("jsonParseError")
            }
        }, failure: failure)
        
    }
}

protocol JSONModelResponseBody : ResponseBody {
    associatedtype ResponseModel : Decodable
}

extension JSONModelResponseBody {
    func sendRequest(success: @escaping(ResponseModel) -> (),
                     failure: @escaping(String) -> ()) {
        sendURLRequest(request: urlRequest, success: { (data) in
            let decoder = JSONDecoder()
            do {
                let model = try decoder.decode(ResponseModel.self, from: data)
                success(model)
            }
            catch {
                print("decoder error")
            }
        }, failure: failure)
        
    }
}

protocol Request : RequestHeader, RequestBody, ResponseBody {
    var baseURL: String {get}
    var path: String {get}
    var method: String {get}
}
```

ResponseBody 支援以下四種格式：  
1. `StringResponseBody`  
2. `IgnorableResponseBody`  
3. `JSONResponseBody`  
4. `JSONModelResponseBody` 

其中 `JSONModelResponseBody` 比較特別，   
先在 `protocol` 裡面宣告返回的 dataModel 需要是 `Decodable`，   
這樣在 `sendRequest` 時就會自動將 data 轉成物件。

Request 也要多 confirms to `ResponseBody`

實際使用就會變成：

```swift
struct User : Decodable {
    var name: String
}

struct FindUserRequest : Request, JSONRequestHeader, IgnorableRequestBody, JSONModelResponseBody {
    typealias ResponseModel = User

    let path = "users/1"
    let method = "get"
}


FindUserRequest().sendRequest(success: { (user) in
    print(user.name)
}) { (failure) in
    print(failure)
}
```


到目前為止我們已經支援的格式擴充到這樣：


1. Request Header
	1. FormURLEncodedRequestHeader
	2. JSONRequestHeader
2. Request Body
	1. IgnorableRequestBody
	2. JSONRequestBody
	3. JSONArrayRequestBody
3. Response Body 
	1. 	StringResponseBody
	2. IgnorableResponseBody
	3. JSONResponseBody
	4. JSONModelResponseBody

當然還有很多可以擴充的，
例如 Response Body 可能會多個 `JSONArrayResponseBody`、`JSONModelArrayResponseBody` 等等，  
等未來如果擴充這些格式時，既有的程式不必做修改，只需定義新的 protocal 即可。

文中的程式還有許多地方可以修改的，例如：

1. API 錯誤、Http Method 應該要用 enum 定義比較洽當，但本文章的目的不是這些，所以不再另外寫。  
2. 還可以擴充 URL 的產生方式，例如有些參數會打在網址上，但目前擴充以上三類，覺得已經足夠說明 `Protocol Extension` 這個概念，所以就不再贅述。

[程式](https://gist.github.com/willard1218/958eb31fa49b2c15ca1fdbae75c5b40d)