# Line APIメモ

## ウェブアプリにLINEログインを組み込む

### 参考URL
https://developers.line.biz/ja/docs/line-login/integrate-line-login/#making-an-authorization-request

### 大まかなフロー
<ol>
	<li>必要なクエリパラメータを使用して、アプリからLINEログインの認可URLにユーザーを誘導</li>
	<li>LINEログイン画面がユーザのブラウザで開く。LINEプラットフォームでユーザの認証情報が検証された後、ユーザはアプリから要求される権限を付与することに同意する必要もある。</li>
	<li>LINEプラットフォームからアプリに、redirect_uriを介してユーザーをリダイレクトします。このとき、認可コードとstateを含むクエリ文字列もアプリに送信されます。</li>
	<li>アプリは、認可コードを使ってエンドポイントURL https://api.line.me/oauth2/v2.1/token にアクセストークンを要求</li>
	<li>LINEプラットフォームがアプリからのリクエストを検証し、アクセストークンをアプリに返します。</li>
</ol>


### 認可の要求
ユーザーを認証してアプリに権限を付与するよう要求するには、以下の認可URLに必須のクエリパラメータを付けてユーザーをリダイレクトします。ユーザーをリダイレクトするには、LINEログインボタンか直接リンクを使います。
```
https://access.line.me/oauth2/v2.1/authorize
```

### スコープ
scopeパラメータに少なくとも1つのスコープを指定します。  
どの情報までアクセスできるかを決めることができる。

### 認可リクエスト例
```
https://access.line.me/oauth2/v2.1/authorize?response_type=code&client_id=1234567890&redirect_uri=https%3A%2F%2Fexample.com%2Fauth&state=12345abcde&scope=profile%20openid&nonce=09876xyz
```

### 認証の種類
認可URLにリダイレクトされると、ユーザーは以下のいずれかの認証方法でログインできます。
* 自動ログイン
* メールアドレスログイン
* QRコードログイン
* SSO(シングルサイオンオン)

※SSOの方が自動ログインよりもおすすめ。

### アクセストークンの取得
認可コードを指定してHTTP POSTリクエストを実行。
```
POST https://api.line.me/oauth2/v2.1/token
```

リクエスト例が以下
```
curl -X POST https://api.line.me/oauth2/v2.1/token \
-H 'Content-Type: application/x-www-form-urlencoded' \
-d 'grant_type=authorization_code' \
-d 'code=xxx' \
-d 'redirect_uri=xxx' \
-d 'client_id=xxx' \
-d 'client_secret=xxx'
```

レスポンス例
```
{
    "access_token": "bNl4YEFPI/hjFWhTqexp4MuEw5YPs...",
    "expires_in": 2592000,
    "id_token": "eyJhbGciOiJIUzI1NiJ9...",
    "refresh_token": "Aa1FdeggRhTnPNNpxr8p",
    "scope": "profile",
    "token_type": "Bearer"
}
```

<br/>


## LINEのログインIDとかChannelIDとかの場所
```
tabi-docker/variable.env
```
の中に全部入っている。


## Messageを送信する際の注意
[このkibelaの記事参考](https://tabitabi.kibe.la/notes/107#message%E3%82%92%E9%80%81%E4%BF%A1%E3%81%99%E3%82%8B%E9%9A%9B%E3%81%AE%E6%B3%A8%E6%84%8F%E7%82%B9)
