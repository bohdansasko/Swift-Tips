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
        return ["user": user]
    }

}

// somewehere in a view controller
final class HomeViewController {
  // ...
  override func viewDidLoad() {
      super.viewDidLoad()
                
      NotificationCenter.default.addObserver(
          self,
          selector: #selector(handleUserAuthorized(_:)),
          name: .userAuthorized,
          object: nil
      )

      let user = STUser()
      let userAuthorized = STNUserAuthorizated(user: user)
      NotificationCenter.default.post(name: .userAuthorized, object: userAuthorized.makeDict())
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

2. Load a view from xib to storyboard
```swift
@IBDesignable
class XibView: UIView {
    /// view form xib
    private(set) var contentView: UIView!
    
    // MARK: - Lifecycle
    
    override func awakeFromNib() {
        super.awakeFromNib()
        xibSetup()
    }

    override func prepareForInterfaceBuilder() {
        super.prepareForInterfaceBuilder()
        xibSetup()
        contentView.prepareForInterfaceBuilder()
    }
    
}

// MARK: - Setup & load view from xib

private extension XibView {
    
    func xibSetup() {
        let view = loadViewFromNib()
        view.frame = bounds
        view.autoresizingMask = [.flexibleWidth, .flexibleHeight]
        addSubview(view)
        contentView = view
    }
    
    func loadViewFromNib() -> UIView {
        let typeofSelf = type(of: self)
        let xibName = String(describing: typeofSelf)
        let bundle = Bundle(for: typeofSelf)
        let nib = UINib(nibName: xibName, bundle: bundle)
        return nib.instantiate(withOwner: self, options: nil).first as! UIView
    }
    
}
```

and then we can create custom view from xib. 
```swift
// CustomView.xib(must be as well)
// CustomView.swift
final class CustomView: XibView {    
    @IBOutlet fileprivate weak var label: UILabel!
    
    override func awakeFromNib() {
        super.awakeFromNib()
        
        label.text = "SUCCESS"
    }

}
```
*REMEMBER*: you must to setup File's owner as in new one xib as `CustomView`. Now you can add view to storyboard and setup custom class to `CustomView`. 
