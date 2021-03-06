# SwiftUtilCodes
Swiftでよく使う便利コード集

## IDFAのステータス取得 => アラート表示
``` Swift
import AdSupport
import AppTrackingTransparency

// ステータス取得
if #available(iOS 14, *) {
    switch ATTrackingManager.trackingAuthorizationStatus {
    case .authorized:
        print("認証済み")
    case .denied:
        print("拒否")
    case .restricted:
        print("制限")
    case .notDetermined:
        print("未設定")
        showRequestTrackingAuthorizationAlert()
    @unknown default:
                fatalError()
     }
} else {
     if ASIdentifierManager.shared().isAdvertisingTrackingEnabled {
                print("トラッキングを許可")
     } else {
          print("制限")
    }
}

private func showRequestTrackingAuthorizationAlert() {
    if #available(iOS 14, *) {
        ATTrackingManager.requestTrackingAuthorization(completionHandler: { status in
            switch status {
            case .authorized:
                print("認証")
                print("IDFA: \(ASIdentifierManager.shared().advertisingIdentifier)")
            case .denied, .restricted, .notDetermined:
                print("制限、拒否")
            @unknown default:
                fatalError()
            }
        })
        
    }
}
```

## 設定アプリに遷移
```Swift
private func toSettingApp() {
    UIApplication.shared.open(URL(string: UIApplication.openSettingsURLString)!)
}
```

## 通知許可
```Swift
// 通知設定を取得
private func checkNotificationSettings() {
    //通知の設定を取得
    UNUserNotificationCenter.current().getNotificationSettings { setting in
        if setting.authorizationStatus == .authorized {
            print("許可")
        } else {
            print("未許可")
        }
    }
}

//通知設定用のアラートを表示(１度しか表示されない)
private func showNotificationSettingAlert() {
    let authOptions: UNAuthorizationOptions = [.alert, .badge, .sound]
    UNUserNotificationCenter.current().requestAuthorization(options: authOptions) { authorized, error in
        if let error = error {
            print(error)
            return
        }
        
        print("許可")
    }
}
```