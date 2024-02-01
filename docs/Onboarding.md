author: shotaro.doi
summary: swiftのオンボーディングプロジェクト
id: startaiyo-swift-onboarding
categories: codelab, markdown
environments: Web
status: ok
feedback link: https://github.com/startaiyo/swift-onboarding
analytics account: Google Analytics ID

# Swift Onboarding Project
## はじめに
### 本オンボーディングの目的
- 簡単なアプリの作成を通じて、Swiftの基本を学び、配属先での業務に活かす。
- [Swiftのロードマップ](https://medium.com/@andres.carort/ios-developer-roadmap-2023-330fd5cb7479)に基づき、Swiftの網羅的な知識を実装レベルにまで持っていってもらう。
### 学習内容
1. ログイン画面を表示する。<!-- AppDelegate、storyboardの説明と最初の画面表示 -->
2. メイン画面へ移動可能にする。<!-- Coordinator、NavigationControllerの説明と遷移 -->
3. メイン画面にデータを表示する。 <!-- MVVMでデータ表示のためのファイル分割 -->
4. APIを叩いてデータを取得する。 <!-- Services/Modelの作成でデータ取得 -->
5. データを保存する。<!-- Realmやfirebaseを使ってlocal/remoteにデータ保存 -->
6. 取得したデータをリアルタイムで更新する。<!-- Async awaitで取得データのリアルタイム反映 -->
7. ログイン機能を作る。<!-- firebase authで作ってみる -->
8. チャット画面を作ってみる <!-- SwiftUIを使う -->
9. エラーを表示する <!-- エラーハンドリング -->
10. <!-- 未定。他の技術を試しに使ってみる。 -->
### 必要な準備
- [Xcode](https://developer.apple.com/xcode/) *必須
- [fork](https://git-fork.com/)

## 第一章 ログイン画面を表示する
### 概要
ダミーのログイン画面の作成を通じて、アプリ全体に関わる処理に関する知識を学びます。
### 本章で学ぶこと
- AppDelegate
- storyboard
### 完成イメージ

|iPhone|iPad|
|-|-|
|![Alt text](images/image_1_14.png)|![Alt text](images/image_1_15.png)|

### 手順
**Xcodeのプロジェクトを作る**
1. Xcodeを開き、Create a new Xcode projectを選ぶ。
2. iOS > Appを選ぶ  
<img src ="images/image_1_1.png" width = "350">

3. 任意の名前をつけ、プロジェクトを保存する。

**GitHubのリポジトリを作る**
1. Source control navigator > Repositories > Remotes(画像参照)を右クリック  
<img src ="images/image_1_2.png" width = "400">

2. New "App名" Remote...をクリックし、自分のGitHubアカウント情報を入力する。
3. Personal access tokenについては、[こちら](https://docs.github.com/ja/authentication/keeping-your-account-and-data-secure/managing-your-personal-access-tokens) を参考にし、前手順で指定されたscopeを持ったPersonal access tokenを発行し、登録する。

以上でGitHubとの連携は完了です。

**画面表示のための下準備**
1. info.plistというファイルを開き、`Information Property List`内にある`Application Scene Manifest`をマイナスボタンで削除する。
2. SceneDelegate.swiftを物理削除する。
3. AppDelegateから以下の二つの関数を削除する。
```
func application(_ application: UIApplication, configurationForConnecting connectingSceneSession: UISceneSession, options: UIScene.ConnectionOptions) -> UISceneConfiguration {
    // Called when a new scene session is being created.
    // Use this method to select a configuration to create the new scene with.
    return UISceneConfiguration(name: "Default Configuration", sessionRole: connectingSceneSession.role)
}

func application(_ application: UIApplication, didDiscardSceneSessions sceneSessions: Set<UISceneSession>) {
    // Called when the user discards a scene session.
    // If any sessions were discarded while the application was not running, this will be called shortly after application:didFinishLaunchingWithOptions.
    // Use this method to release any resources that were specific to the discarded scenes, as they will not return.
}
```
4. AppDelegateクラス定義内に以下変数を追加する。

```
var window: UIWindow?
```

5. Main.storyboard, ViewControllerを削除する。
6. 図のようにAppの設定 > TARGETSのApp名 > Info > Main storyboard file base name を削除する(写真は削除した後。)

<img src ="images/image_1_3.png" width = "500">

7. AppDelegateの`func application(_ application: UIApplication, didFinishLaunchingWithOptions launchOptions: [UIApplication.LaunchOptionsKey: Any]?) -> Bool` 内に

```
window = UIWindow(frame: UIScreen.main.bounds)
let storyBoard = UIStoryboard(name: "Login", bundle: nil)
let loginVC = storyBoard.instantiateViewController(withIdentifier: "LoginViewController")
window?.rootViewController = loginVC
window?.makeKeyAndVisible()
```

を追加します。※このままでは動きません。
次に、ログイン画面を作っていきます。

**ログイン画面の表示**
1. AppDelegateが含まれているApp名のディレクトリで右クリックし、New Groupを選択すると、新たなディレクトリが作れる。名前は"Presentation"(画面表示に関わるファイルを格納するディレクトリ)にする。
2. Presentationディレクトリの下に、ログイン画面に関わるファイルを格納する"Login"ディレクトリを作成する。
3. Loginディレクトリで右クリックし、New Fileを選択 > iOS > User Interfaceにある"Storyboard"を選択。名前は"Login"にし、保存。  
- すると、下記のような白地のiPhoneが出てくると思います。ここにパーツを置き、画面を完成させます。

<img src ="images/image_1_4.png" width = "500">

4. LoginディレクトリでさらにNew File > iOS > SourceのSwift Fileを選択し、名前を`LoginViewController`にする。
5. そして、デフォルトで存在する`import foundation`を削除したのちに以下のコードを記述し、LoginViewControllerクラスを作成する。

```
import UIKit

final class LoginViewController: UIViewController {}
```

- これがコードとstoryboardを橋渡ししてくれます。

6. 再びLogin.storyboardに戻り、左側のDocument Outlineの赤枠で囲ったView Controllerをクリック > 右側のInspectorsの赤枠で囲ったCustom Class > ClassからLoginViewControllerを選択。そして同じく赤枠のIdentity > Storyboard IDにも同様に`LoginViewController`を入力。

<img src ="images/image_1_5.png" width = "500">

- これでコード上から参照できるようになりました。

7. 右上の赤枠で囲った"➕"ボタン(以下、library)をクリックすると画面に配置できるパーツ一覧が出てくる。まず"Label"をクリックしたまま離さず、白地のiPhoneの上でクリックを離すと、Labelと書かれたパーツが画面上に配置される。

<img src ="images/image_1_6.png" width = "400">

8. ここで試しにXcode左上の再生ボタンを押してビルドしてみると、Labelと書かれた画面が出現する。

<img src ="images/image_1_7.png" width = "200">

- これで画面表示まではされるようになりました。次に色々なパーツを組み合わせ、ログイン画面を作成していきます。

**ログイン画面の作成**
1. libraryをクリックし、TextFieldを二つ、Buttonを一つ、先ほどのLabelの下に順に配置する。

<img src ="images/image_1_8.png" width = "200">

- 基本的に、storyboardはパーツが平面上のどこにあるのかを指定するだけで、ここでは見た目的な部分に修正は加えません。見た目はViewControllerから整えていきます。

2. Add Editor on Right(libraryの+ボタンの下、赤枠)によりxcodeの編集画面を2画面にし、片方のEditorをクリックした後LoginViewController.swiftファイルを開く。
3. もう片方で開いているLogin.storyboard(開いてなければ開く)のLabelを、controlキーを押したままクリック離さずにLoginViewController.swiftファイル上ドラッグすると、図のように青い線と共に`Insert Outlet or Outlet Collection`と出てくる。

<img src ="images/image_1_9.png" width = "500">

このタイミングでクリックを離し、StorageをStrongにし、nameを`titleLabel`にすると、自動的に

```
@IBOutlet var titleLabel: UILabel!
```

というコードが出現する。しかしこのインスタンスはクラス内からしか参照しないため、`@IBOutlet`と`var`の間に`private`を記述することでそれを明示する。
4. 同様に、上から順にprivateの@IBOutletプロパティである`emailField`, `passwordField`, `loginButton`を作成すると、`LoginViewController.swift`は以下のようなコードになる。

```
import UIKit

final class LoginViewController: UIViewController {
    @IBOutlet private var titleLabel: UILabel!
    @IBOutlet private var emailField: UITextField!
    @IBOutlet private var passwordField: UITextField!
    @IBOutlet private var loginButton: UIButton!
}
```

5. 各パーツのデザインを作る。まず、titleLabelのテキストを"Login"にしたいので、`titleLabel: UILabel!`のすぐ横から以下のコードを記述する。

```
@IBOutlet private var titleLabel: UILabel! {
    didSet {
        titleLabel.text = "Login"
    }
}
```

- これで、LoginViewController上でtitleLabelがインスタンス化された(Setされた)際、titleLabelのtextが"Login"になることを示している。

6. 同様に、各パーツの`didSet`内に下記を記述する。

```
// emailField内
emailField.placeholder = "email"

// passwordField内
passwordField.placeholder = "password"

// loginButton内
loginButton.setTitle("Login",
                                 for: .normal)
```

**各パーツの位置調整**
- 以上の配置は、様々な画面の大きさがあるiOSに対して柔軟性に欠ける。例えば、iPad上に表示しようとした際には以下のようになってしまう。そのため、以下のAutoLayoutの設定を行うことにより、相対的な配置に変える必要がある。

<img src ="images/image_1_10.png" width = "500" style="border: 1px black solid;">

1. まず、以下の手順ですべてのパーツを水平方向の中心に持っていきたい。Title Labelに対して、先程のようにcontrolボタンを押しながらドラッグして出てくる青いポインターを、上部のViewにまでドラッグすると、下図のような黒いポップアップが出てくる。これの"Center Horizontally"をクリックすると、CenterX(水平方向の中心に位置する)の制約(Constraint)が付与される。

- これはすなわち、View(画面全体)に対して、titleLabelは水平方向の中心に配置してください、という制限を我々がかけたことになる。

<img src ="images/image_1_11.png" width = "500">

2. 他のパーツについても同様にCenterXのConstraintをViewに対して付与する。
3. 続いて、以下の手順で全てのパーツをY軸方向に50ずつ離して配置する。まず、PasswordFieldにViewに対してCenter VerticallyのConstraintを付与する。
4. 3.で付与されたConstraintをクリックすると、図のようにConstraintの詳細を設定できる部分が開くので、こちらのConstantに25を入力する。

<img src ="images/image_1_12.png" width = "500">

5. emailFieldから今度はpasswordFieldに対して、"vertical spacing"のconstraintを付与する。4.同様に付与されたConstraintを開き、Constantを50に設定する。
6. 同様にtitleLabelからemailField, loginbuttonからpasswordFieldに対してconstantが50のvertical spacingを付与する。
7. テキストフィールドの幅が少し狭いので、emailField, passwordFieldそれぞれに対し、widthのconstantが220のconstraintを設定する。

- 以上により、画面の大きさに関わらず、違和感のない配置にする事ができる。

<img src ="images/image_1_13.png" width = "500">

### 各技術の説明
**AppDelegate.swift**
- アプリ全体の変化に関わるイベントを処理するクラス。
- アプリ全体の起動/停止に伴って実行される`didFinishLaunchingWithOptions`, `applicationWillTerminate` だけでなく、画面に関わるライフサイクルイベントもSceneDelegate同様、処理できる。

**SceneDelegate.swift**
- 2画面表示などにより複数のViewインスタンスが必要になったため、それぞれのViewの状態を管理するためのクラス。