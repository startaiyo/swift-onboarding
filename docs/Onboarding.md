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
`var window: UIWindow?`
5. Main.storyboard, ViewControllerを削除する。
6. 図のようにAppの設定 > TARGETSのアプリ名 > Info > Main storyboard file base name を削除する(写真は削除した後。)
<img src ="images/image_1_3.png" width = "500">

7. AppDelegateの`func application(_ application: UIApplication, didFinishLaunchingWithOptions launchOptions: [UIApplication.LaunchOptionsKey: Any]?) -> Bool` 内に
```
window = UIWindow(frame: UIScreen.main.bounds)
let storyBoard = UIStoryboard(name: "Login", bundle: nil)
let loginVC = storyBoard.instantiateViewController(withIdentifier: "LoginViewController")
window?.rootViewController = loginVC
window?.makeKeyAndVisible()
```

を追加します。次に、ログイン画面を作っていきます。

**ログイン画面の作成**


### 各技術の説明
**AppDelegate.swift**
- アプリ全体の変化に関わるイベントを処理するクラス。
- アプリ全体の起動/停止に伴って実行される`didFinishLaunchingWithOptions`, `applicationWillTerminate` だけでなく、画面に関わるライフサイクルイベントもSceneDelegate同様、処理できる。

**SceneDelegate.swift**
- 2画面表示などにより複数のViewインスタンスが必要になったため、それぞれのViewの状態を管理するためのクラス。