# iOS Good, Better, or Best Practices

This is a compilation of practices from multiple sources that I find very good and so use personally in my projects. These approaches has been working very well for me so far, but as we all know, there's no silver bullet. Therefore, it may be good, better, or best for your own projects :)

**Networking**

Use `Resource` struct to encapsulate API endpoints, for better composition and testability.

* https://talk.objc.io/episodes/S01E1-tiny-networking-library
* https://talk.objc.io/episodes/S01E25-adding-caching

Sample usage:

```swift
struct Resource<T> {
    enum Method: String {
        case get = "GET"
        case post = "POST"
    }
    
    let path: String
    let method: Method
    let parseJSON: (Any) -> T?
    let params: [String: Any]
}
```

```swift
struct Person {
    typealias IDType = String
    
    enum Role: String {
        case admin = "admin"
        case member = "member"
    }
    
    let id: IDType
    let name: String
    let role: Role
    let picURL: URL?
}
```

```swift
extension Person {
    static func listAll(with role: Role) -> Resource<[Person]> {
        return Resource(
            path: "person",
            method: .get,
            parseJSON: { jsonObject in
                guard
                    let jsonDict = jsonObject as? [String: Any],
                    let results = jsonDict["results"] as? [[String: Any]]
                    else { return nil }
                return results.compactMap { Person.init($0) }
            },
            params: [
                "role": role.rawValue
            ])
    }
}
```

**Navigation**

Use Flow Controller as a coordinating controller that manages transitions and data flows between multiple view controllers performing a specific purpose.

* https://davedelong.com/blog/2017/11/06/a-better-mvc-part-2-fixing-encapsulation/
* https://github.com/onmyway133/blog/issues/106
