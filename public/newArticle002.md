---
title: 混合コンテンツ(mixed content)エラー？
tags:
  - error
  - Laravel
  - project
private: false
updated_at: '2024-01-01T02:25:49+09:00'
id: 78b85cef57fb5920829c
organization_url_name: null
slide: false
ignorePublish: false
---
## 混合コンテンツ(mixed content)エラー？

![error](https://github.com/yuminn-k/yuminn-k/assets/55650732/ce5304ab-c1f3-4f45-863d-b457181caeb7)

前の記事でも紹介した配布サービス、`cloudtype`で配布を進めながらcssが正常に適用されないことを確認し、ネットワークタブを確認したら、ファイルを正しくロードできないことを確認しました。Statusに`(blocked:mixed-content)`というエラーが発生していたので、これについて調べてみました。

### 君はどんなエラー？

"`(blocked:mixed-content)`" というメッセージは、WebページがHTTPSプロトコルを使用してロードされたが、その中でHTTPプロトコルを使用するリソース（CSSやJSファイルなど）をロードしようとしたときに発生するセキュリティエラーを示します。このような現象を「`混合コンテンツ(mixed content)`」と呼びます。

HTTPSプロトコルはウェブ通信を暗号化してセキュリティを強化しますが、HTTPプロトコルは暗号化されないため、セキュリティに脆弱です。 したがって、ウェブブラウザはHTTPSページでHTTPリソースをロードしようとすると、これをブロックしてユーザーのセキュリティを保護します。

この問題を解決するには、すべてのリソースをHTTPSで提供する必要があります。

### なぜ発生したのか？

![error01](https://github.com/yuminn-k/yuminn-k/assets/55650732/b6df13da-a1a1-41e3-9238-eef173d44ac1)

エラー内容を詳しく見てみると、httpsではなくhttpで呼び出していることが確認できました。

### どのように解決したか？

配布版の場合、すべてのURLにhttpsを適用させてくれるコードを追加しました。この他に配布環境にAPP_ENVを与えました。

```php
// web.php
if (env('APP_ENV') == 'production') {
    \Illuminate\Support\Facades\URL::forceScheme('https');
}
```

変更事項を適用し、デプロイしたらこのようにcssが適用され、使えるようになりました！

<img width="1225" alt="error02" src="https://github.com/yuminn-k/yuminn-k/assets/55650732/ebc600d5-ff0a-4d88-9694-e18f1798718b">

<br>

📌 `参考`
  [https://laravel.io/forum/12-26-2014-routing-with-https](https://laravel.io/forum/12-26-2014-routing-with-https)
