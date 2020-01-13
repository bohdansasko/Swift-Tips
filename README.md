# Swift-Tips

1. Post object via `NotificationCenter`
```swift
struct STNUserAuthorizated {
    var user: STUser
}

// MARK: - Notification

extension STNUserAuthorizated {

    init?(with n: Notification) {
        let userObj = n.userInfo?["user"] as? STUser
        guard let user = userObj else {
          return nil
        }
        self.user = user
    } 
    
    func makeDict() -> [String: Any] {
        return ["name": user]
    }

}

// somewehere in a view controller
final class HomeViewController {
  // ...
  override func viewDidLoad() {
      super.viewDidLoad()
      
      NotificationCenter.default.addObserver(
          .userAuthorized,
          #selector(handleUserAuthorized(_:))
      )

      let user = STUser()
      let userAuthorized = STNUserAuthorizated(user: user)
      NotificationCenter.default.post(.userAuthorized, object: userAuthorized.makeDict())
  }

  @objc private func handleUserAuthorized(_ n: Notification) {
      guard let authorizedNotification = STNUserAuthorizated(with: n) else {
          return
      }
      print(authorizedNotification.user)
  }
  // ...
}
```
