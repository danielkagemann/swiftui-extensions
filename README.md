# Swift UI extensions

Useful set of swift ui extensions.

## why not a package?
Usually a package would be great so that you can just add as dependency and you are done. But then you get all extension no matter if you need them. So here is just a list and you copy what you need.

## Int
```swift
extension Int {
   /**
    * use this if you want single or plural text depending on he value
    * e.g. 10.plural(single: "Tag", multiple: "Tage", prefixValue: true) returns 10 Tage
    */
   func plural(single: String, multiple: String, prefixValue: Bool = false) -> String {
      if self == 1 {
         return prefixValue ? "\(self) \(single)" : single
      }
      return prefixValue ? "\(self) \(multiple)" : multiple
   }
}
```
## String
```swift
extension String {
   /**
    * convert string to Color
    * e.g. "255,255,255".rgbToUIColor()
    */
   func rgbToUIColor () -> Color {
      let tokens = self.replacingOccurrences(of: " ", with: "").split(separator: ",")
      
      return Color(red: Double(tokens[0])!/255, green: Double(tokens[1])!/255, blue: Double(tokens[2])!/255)
   }
}
```

## Application
```swift
extension UIApplication {
   /**
    * return appversion
    */
   static var appVersion: String {
      let app = Bundle.main.object(forInfoDictionaryKey: "CFBundleShortVersionString") as? String ?? "?"
      let build = Bundle.main.object(forInfoDictionaryKey: kCFBundleVersionKey as String) as? String ?? "?"
      return "Version \(app) build \(build)"
   }
}
```

## View Modifier for Shake gesture
```swift
// The notification we'll send when a shake gesture happens.
extension UIDevice {
   static let deviceDidShakeNotification = Notification.Name(rawValue: "deviceDidShakeNotification")
}

//  Override the default behavior of shake gestures to send our notification instead.
extension UIWindow {
   open override func motionEnded(_ motion: UIEvent.EventSubtype, with event: UIEvent?) {
      if motion == .motionShake {
         NotificationCenter.default.post(name: UIDevice.deviceDidShakeNotification, object: nil)
      }
   }
}

// A view modifier that detects shaking and calls a function of our choosing.
struct DeviceShakeViewModifier: ViewModifier {
   let action: () -> Void
   
   func body(content: Content) -> some View {
      content
         .onAppear()
         .onReceive(NotificationCenter.default.publisher(for: UIDevice.deviceDidShakeNotification)) { _ in
            action()
         }
   }
}

// A View extension to make the modifier easier to use.
extension View {
   func onShake(perform action: @escaping () -> Void) -> some View {
      self.modifier(DeviceShakeViewModifier(action: action))
   }
}
```
