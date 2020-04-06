# ngrok


## ngrokとは
localhostで動いているサーバーを、LANの外からアクセスできるようにできるツール。  
webhookなどの受信が必要なシステムでも、ローカルで動かすことが可能。


## ngrok起動
```
ngrok http 3000
```
これでブラウザから`http://xxxxxxxx.ngrok.io`へアクセスする。
