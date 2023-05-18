## Core Concepts:

- Data Base Connection: The data is on a remote service, specifically on a data base, therefore we need to use a web service.
- Web Service (HTTP Protocol): Use to stablish connections through the internet.  Therefore to interact with the server from our app through HTTP protocol and web services.

- API REST (Representational State Transfer) is a web service that allows for the communication between different applications through HTTP. It provides a standard set of rules for creating APIs that can be used to retrieve, create, update, and delete data in a system. REST APIs use HTTP methods such as GET, POST, PUT, and DELETE to interact with resources, and use JSON or XML to format the data. API REST is widely used in modern web development for building scalable and efficient web applications.

- Asynchronous request : The time the server use to stablish communication with the data base and stablish a response which bring us back the data to our app.

- JSON (JavaScript Object Notation) is a lightweight data interchange format that is easy for humans to read and write, and easy for machines to parse and generate. It is a text format that is language independent, and can be used to exchange data between different programming languages. JSON is commonly used in web applications to transmit data between the client and the server, and is often used as an alternative to XML.
    - Each object is encased in a set of curly braces, and it is pass as a string.
    - Since we don’t want to unpacked the JSON into a Java Script Object, it is necessary to decode the JSON into a Swift Object.

 

## Steps to follow

1. Create an instance (Class or Structure)
    
    ```swift
    struct ModelName{
    }
    ```
    
    - Since you will want to transform the JSON into a Swift Object, Swift gave us the possibility to do so, using the next three protocols:
        - Decodable
        - Encodable
        - Codable
    
    ```swift
    struct ModelName: Codable{
    }
    ```
    
2. Write the properties of the struct matching the names on the server’s response. 
    1. It is a good practice to set the properties as optionals. 
        
        ```swift
        struct ModelName: Codable{
        let nameOfThePropertieAsWrittenInTheJSON: typeOfThePropertie? 
        }
        ```
        

### Code Examples

- Weather App
    
    ```swift
    struct WeatherData: Codable {
        
        let name: String
        let main: Main
        let weather: [Weather]
        
    }
    
    struct Main: Codable{
        
        let temp: Double
        
    }
    
    struct Weather: Codable{
        
        let description: String
        let id: Int
        let main: String
    }
    ```
    

-Networking App 

```swift
struct UserResponse: Decodable {
    
    let code: Int?
    let meta: Meta?
    let data: User?
}

struct User: Decodable {
    
    let id: Int?
    let name: String?
    let email: String?
    let gender: String?
    let status: String?
    let createdAt: Date?
    let updatedAt: Date?
    
    enum CodingKeys: String, CodingKey {
        
        case id
        case name
        case email
        case gender
        case status
        case createdAt = "created_at"
        case updatedAt = "updated_at"
        
    }

}

struct Meta: Decodable {
    
}
```

1. Make the networking. 
    1. Often a final class (so no other class can inherit from it) with the name “NetworkingProvider” is created. Then a singleton object is declare (Is a design pattern that ensures a class can have only one object. ) 
    
    ```swift
    final class NetworkingProvider{
        
        static let shared = NetworkingProvider()
    }
    ```
    
    - Even though is not necessary adding a private initializer is important, because it stops other parts of our code from trying to create the class instance. However, the class creates its own instance of itself as a static variable, which means the only instance of the class is the one it created.
        
        ```swift
        final class NetworkingProvider{
            
          static let shared = NetworkingProvider()
        
        	private init() {} //This is optional
        }
        ```
        
    
    b.  URL 
    
    - Check the way the API request is written and what parameters are needed.
    - If the API requires a token make sure you own one.
        
        
        ```swift
        final class NetworkingProvider{
            
          static let shared = NetworkingProvider()
        
        	private let baseURL = "https...." //The base of the API Request without parameters	
        	private let token = "  "  //The token or API Key you own. 
        
        }
        ```
        
    
    *The next step depends on the way you are going to perform the request to the server. It could be using third party libraries like [Alamofire](https://github.com/Alamofire/Alamofire) or without it. 
    
    c. Request 
    
    ### Code with Alamofire and without it
    
    - Pure
        
        ```swift
        final class NetworkingProvider{
            
          static let shared = NetworkingProvider()
        
        	private let baseURL = "https...." //The base of the API Request without parameters	
        	private let token = "  "  //The token or API Key you own. 
        
        	func performRequest(with urlString: String){
        	        
        		let url = "\(baseURL)parameters\(token)"
        	
        	
        	        //Networking
        	
        	        //1.Create a URL
        	        if let safeURL = URL(string: url){
        	
        	            //2.Create a URLSession
        	            let session = URLSession(configuration: .default)
        	
        	            //3.Give the session a task
        	            let task = session.dataTask(with: safeURL) { (data, response, error) in
        	                if error != nil{
        	                    self.delegate?.didFailWithError(error: error!) //Optional
        	                    return
        	                }
        	                if let safeData = data {
        	                    if let nameOfData = self.parseJSON(safeData)
        	                    }
        	                }
        	            }
        	            //4.Start the task
        	            task.resume()
        	        }
        	        
        	    }
        	    
        	    func parseJSON(_ nameOfData: Data) -> ModelName?{
        	        
        	        let decoder = JSONDecoder()
        	        do {
        	            let decodedData = try decoder.decode(ModelName.self, from: nameOfData)
        	            
        	            let data = ModelName()
        	            return data
        	            
        	        } catch{
        	            delegate?.didFailWithError(error: error) //Optional
        	            return nil
        	        }
        	
        	    }
        
        }
        ```
        
    - Alamofire
        
        ```swift
        
        import Alamofire
        
        final class NetworkingProvider{
            
          static let shared = NetworkingProvider()
        
        	private let baseURL = "https...." //The base of the API Request without parameters	
        	private let token = "  "  //The token or API Key you own. 
        
        	func performRequest(){
        	
        	AF.request(<#T##convertible: URLConvertible##URLConvertible#>, 
        							method: <#T##HTTPMethod#>, parameters: <#T##Parameters?#>, 
        							encoding: <#T##ParameterEncoding#>, headers: <#T##HTTPHeaders?#>, 
        							interceptor: <#T##RequestInterceptor?#>, 
        							requestModifier: <#T##Session.RequestModifier?##Session.RequestModifier?##(inout URLRequest) throws -> Void#>)
        	}
        
        }
        ```
        
    
    ### Examples
    
    - Pure
        
        ```swift
        import UIKit
        import CoreLocation
        
        protocol WeatherManagerDelegate {
            func didUpdateWeather(_ weatherManager: WeatherManager,_ weather: WeatherModel)
            func didFailWithError(error: Error)
        }
        
        struct WeatherManager {
            
            let weatherURL = "https://api.openweathermap.org/data/2.5/weather?units=metric"
            let appid = "07bc7662e3d049e2142f786a4d8d2c18"
            
            var delegate: WeatherManagerDelegate?
            
            
            func fetchWeather(cityName: String){
                
                let urlString = "\(weatherURL)&q=\(cityName)&appid=\(appid)"
                
                performRequest(with: urlString)
            }
            
            
            func fetchWeather(latitude lat: CLLocationDegrees, longitude lon: CLLocationDegrees){
                
                let urlString = "\(weatherURL)&appid=\(appid)&lat=\(lat)&lon=\(lon)"
                
                performRequest(with: urlString)
                
            }
            
            
            func performRequest(with urlString: String){
                
                //Networking
                //1.Create a URL
                if let url = URL(string: urlString){
                    //2.Create a URLSession
                    let session = URLSession(configuration: .default)
                    //3.Give the session a taskR
                    let task = session.dataTask(with: url) { (data, response, error) in
                        if error != nil{
                            self.delegate?.didFailWithError(error: error!)
                            return
                        }
                        if let safeData = data {
                            if let weather = self.parseJSON(safeData){
                                self.delegate?.didUpdateWeather(self, weather)
                            }
                        }
                    }
                    //4.Start the task
                    task.resume()
                }
                
            }
            
            func parseJSON(_ weatherData: Data) -> WeatherModel?{
                
                let decoder = JSONDecoder()
                do {
                    let decodedData = try decoder.decode(WeatherData.self, from: weatherData)
                    let temp = decodedData.main.temp
                    let name = decodedData.name
                    let id = decodedData.weather[0].id
        
                    let weather = WeatherModel(conditionId: id, cityName: name, temperature: temp)
                    return weather
                    
                } catch{
                    delegate?.didFailWithError(error: error)
                    return nil
                }
        
            }
            
           
            
        }
        ```
        
    - Alamofire
        
        ```swift
        import Foundation
        import Alamofire
        
        final class NetworkingProvider{
            
            static let shared = NetworkingProvider()
            
            private let kBaseUrl = "https://gorest.co.in/public-api/"
            private let kStatusOk = 200...299
            private let kToken = "887bc115159181d105199373cf327ffd15ca290015bd5b4c23eb361875310692"
            
            
            
            func getUser (id: Int, success: @escaping (_ user: User) -> (), failure: @escaping (_ error: Error?)->()){
                
                let url = "\(kBaseUrl)users/\(id)"
                
                AF.request(url,method:.get).validate(statusCode: kStatusOk).responseDecodable (of: UserResponse.self, decoder: DataDecoder()) {response in
                    
                    if let user = response.value?.data {
                        
                        success(user)
                        print(user)
                        
                    } else {
                        failure(response.error)            }
                }
                
                
            }
            
            func addUser(user: NewUser, success: @escaping (_ user: User) -> (), failure: @escaping (_ error: Error?)->()){
                
                let url = "\(kBaseUrl)users"
                    
                let headers: HTTPHeaders = [.authorization(bearerToken: kToken)]
        
                AF.request(url,method:.post,parameters: user,encoder: JSONParameterEncoder.default,headers: headers).validate(statusCode: kStatusOk).responseDecodable (of: UserResponse.self, decoder: DataDecoder()) {response in
                    
                    if let user = response.value?.data, user.id != nil {
                        
                        success(user)
                        print(user)
                        
                    } else {
                        failure(response.error)
                        
                    }
                }
                
            }
            
            func updateUser(id: Int, user: NewUser, success: @escaping (_ user: User) -> (), failure: @escaping (_ error: Error?)->()){
                
                let url = "\(kBaseUrl)users/\(id)"
                    
                let headers: HTTPHeaders = [.authorization(bearerToken: kToken)]
        
                AF.request(url,method:.put,parameters: user,encoder: JSONParameterEncoder.default,headers: headers).validate(statusCode: kStatusOk).responseDecodable (of: UserResponse.self, decoder: DataDecoder()) {response in
                    
                    if let user = response.value?.data, user.id != nil {
                        
                        success(user)
                        print(user)
                        
                    } else {
                        failure(response.error)
                        
                    }
                }
                
            }
            
            
            func deleteUser(id: Int, success: @escaping () -> (), failure: @escaping (_ error: Error?)->()){
                
                let url = "\(kBaseUrl)users/\(id)"
                    
                let headers: HTTPHeaders = [.authorization(bearerToken: kToken)]
                
                AF.request(url,method:.delete,headers: headers).validate(statusCode: kStatusOk).response { response in
                    
                    if let error = response.error{
                        failure(error)
                    }else{
                        success()
                    }
                }
                
            }
            
            
            
            
            
            
            
        }
        ```
 

  
  
