---
title: "Creating an XPC Service in Swift"
---

Say you want to add an [XPC Service](https://developer.apple.com/library/archive/documentation/MacOSX/Conceptual/BPSystemStartup/Chapters/CreatingXPCServices.html#//apple_ref/doc/uid/10000172i-SW6-SW1) to your Swift app. Fine idea, but notice that Xcode spits out Objective-C when you add an XPC Service target.[^1] Nobody has time for that. Fortunately converting Xcode's starter code to Swift is straightforward.[^2] Let's twiddle some knobs and twist a few dials and get you rolling.

After creating an XPC Service target, here named "MyService", we have four files: *main.m*, *MyService.h*, *MyService.m*, and *MyServiceProtocol.h*. Rename these to *main.swift*, *MyService.swift*, *MyServiceDelegate.swift*, and *MyServiceProtocol.swift*. Next, add them to the target's "Compile Sources" build phase.

![Xcode Build Phases](/images/xpc-build-phases.png)

Now replace the Objective-C code in each file with its Swift translation.

```swift
// main.swift
import Foundation

let delegate = MyServiceDelegate()
let listener = NSXPCListener.service()
listener.delegate = delegate
listener.resume()
```

```swift
// MyService.swift
import Foundation

class MyService: NSObject, MyServiceProtocol {
    func upperCaseString(_ string: String, withReply reply: @escaping (String) -> Void) {
        let response = string.uppercased()
        reply(response)
    }
}
```

```swift
// MyServiceDelegate.swift
import Foundation

class MyServiceDelegate: NSObject, NSXPCListenerDelegate {
    func listener(_ listener: NSXPCListener, shouldAcceptNewConnection newConnection: NSXPCConnection) -> Bool {
        let exportedObject = MyService()
        newConnection.exportedInterface = NSXPCInterface(with: MyServiceProtocol.self)
        newConnection.exportedObject = exportedObject
        newConnection.resume()
        return true
    }
}
```

```swift
// MyServiceProtocol.swift
import Foundation

@objc public protocol MyServiceProtocol {
    func upperCaseString(_ string: String, withReply reply: @escaping (String) -> Void)
}
```

Before compiling this code we need to tweak build settings:

- **Install Objective-C Compatibility Header**: NO
- **Objective-C Generated Interface Header Name**: ""
- **Runtime Search Paths**: @loader_path/../../../../Frameworks
- **Swift Language Version**: whatever version of Swift you use

![Xcode Build Settings](/images/xpc-build-settings.png)

With any luck we can now build. In our main application we delegate a task to our service by creating an [`NSXPCConnection`](https://developer.apple.com/documentation/foundation/nsxpcconnection) with the name of the target's bundle identifier then calling the functions defined in `MyService`.

```swift
import MyService

...

let connection = NSXPCConnection(serviceName: "com.matthewminer.MyService")
connection.remoteObjectInterface = NSXPCInterface(with: MyServiceProtocol.self)
connection.resume()

let service = connection.remoteObjectProxyWithErrorHandler { error in
    print("Received error:", error)
} as? MyServiceProtocol

service?.upperCaseString("hello XPC") { response in
    print("Response from XPC service:", response)
}
```

That's it. Boy howdy.

---

[^1]: As of Xcode 9, at least. I hope Apple changes this in future versions and this bone dry article can be tossed into the World Wide Web's trash bin.

[^2]: Code snippets [on GitHub](https://gist.github.com/mminer/be55bcdf7c4ff004ecafba6a664addc5). Thanks to [adur1990](https://github.com/adur1990) for Swift 4 compatibility fixes.
