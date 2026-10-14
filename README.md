# うな和 名古屋 Web アプリ版（試作・非公式）

このフォルダは、うな和 名古屋のアプリ（Expo / React Native）を Web 向けに書き出したものです。
静的なファイルだけでできていて、サーバーのプログラムは要りません。スマートフォンでは画面いっぱいのアプリとして、
パソコンでは中央のスマートフォンの形の枠の中でアプリが動き、左右に店舗の案内を置いた画面で開きます。

## はじめに（大切なこと）

- **試作・非公式です。うな和の公式アプリではありません。** 画面にはいつも「試作・非公式」と出ます。
- 料理と店舗の写真・店名・お品書きなどの権利は **うな和** にあります。うな和の許可なく公開・転載しないでください。
- 会員登録・クーポンはデモです。入力した内容はこのブラウザの中にだけ保存され、どこにも送られません。
  **実際のメールアドレスやパスワードは入れないでください。**
- クーポンのスタッフ確認画面には「見本（店舗ではご利用いただけません）」と出ます。お店では使えません。
- 検索エンジンには載せない指定（noindex）にしています。

## 手元で開く

`index.html` をダブルクリックして開く（file://）と動きません。静的なファイルを配るサーバーで開きます。

1. ターミナルでこのフォルダに移動します。
2. 次のどちらかを実行します。
   - おすすめ（Node.js が必要。初回だけ `serve` をダウンロードします）
     ```sh
     npx serve -s .
     ```
     `-s` は「見つからないパスには index.html を返す」設定です。これがあると `/store/tsurumai` などの
     深いページで再読み込みしても、その画面が開きます。
   - Node.js がないとき（Python）
     ```sh
     python3 -m http.server 8000
     ```
     この方法では深いページの再読み込みが 404 になります。`http://localhost:8000/` から開いてください。
3. 表示された URL（例 `http://localhost:3000`）をブラウザで開きます。

スマートフォンで試すときは、パソコンと同じ Wi-Fi につなぎ、パソコンの IP アドレスの URL
（例 `http://192.168.1.23:3000`）を開きます（`npx serve` が「Network:」として表示します）。
オフラインで開く機能（Service Worker）はありません。

### ホーム画面に追加する（PWA）

- iPhone：Safari の共有ボタン →「ホーム画面に追加」。ホーム画面の「うな和」から全画面で開きます。
- Android：Chrome のメニュー →「アプリをインストール」または「ホーム画面に追加」。
- パソコン（Chrome・Edge）：アドレスバーのインストールのボタン（出ないときはメニューの「キャスト、保存、共有」→
  「ページをアプリとしてインストール」や「ショートカットを作成」）。
- アプリとしてのインストールは、HTTPS で公開した URL か `localhost` で使えます（LAN の `http://192.168…` では
  ショートカットになります）。

### パソコンの広い画面

幅 960 以上・高さ 680 以上の画面をマウスやトラックパッドで開くと、中央にスマートフォンの形の枠（画面 390×844 まで）が出て、
その中でアプリが動きます（シート・確認の窓・お知らせの帯も枠の中に出ます）。左にブランドの印と断り書き、右に三つの店
（営業の状況・時間・住所。押すと店舗の画面が開きます）と、スマートフォンで開くための QR コードが出ます。
QR コードは公開した URL で開いたときだけ出ます（`localhost` では出ません）。タブレットを指で使うときは枠を出さず、
最大幅 480 の列で開きます。

## 公開するとき（静的ホスティング）

- **そのまま置けるところ**：GitHub Pages（ユーザー・組織のページ、または独自ドメインの根）、Netlify、Cloudflare Pages。
  このフォルダには `404.html`（GitHub Pages 用）、`_redirects`（Netlify・Cloudflare Pages 用）、`.nojekyll`
  （GitHub Pages が `_expo/` を消さないように）が入っています。
- **そのほかのサーバー**：「見つからないパスには `/index.html` を返す」（SPA のフォールバック）設定にしてください。
  - nginx：`location / { try_files $uri $uri/ /index.html; }`
  - Apache（.htaccess）：`FallbackResource /index.html`
  - Firebase Hosting：`"rewrites": [{ "source": "**", "destination": "/index.html" }]`
  - Vercel：`vercel.json` に `{ "rewrites": [{ "source": "/(.*)", "destination": "/index.html" }] }`
- **サブパスに置くとき**（例 `https://<ユーザー>.github.io/unawa-nagoya-app/`）は、そのパス用に書き出し直します
  （このフォルダは `/` に置く用です。そのままサブパスに置くと、JS と画像を `/` から読もうとして開きません）。
  アプリのフォルダで `node scripts/build-web.mjs --base-url /unawa-nagoya-app` と実行すると、JS・画像・書体・
  `manifest.webmanifest`・アイコン・`_redirects` がそのパスに合わせて書き出されます。
- 公開の前に、うな和の許可を確認してください（写真と店名を使っているため）。

## 中身

| ファイル | 中身 |
|---|---|
| `index.html`・`404.html` | アプリの入口（同じもの） |
| `_expo/static/js/web/` | アプリの JS |
| `assets/` | 写真・書体（Cormorant Garamond・Material Symbols）・画面の部品の画像 |
| `manifest.webmanifest`・`icons/` | ホーム画面に追加したときの名前とアイコン（PWA） |
| `favicon.ico` | ブラウザのタブのアイコン |
| `_redirects`・`.nojekyll` | 静的ホスティングの設定（上の「公開するとき」） |
| `metadata.json` | Expo の書き出しの情報（Web では使いません） |

## 作り直す

アプリのフォルダ（`outputs/app`）で次を実行すると、このフォルダを消して作り直します。

```sh
node scripts/build-web.mjs                               # サイトの根（/）に置く用
node scripts/build-web.mjs --base-url /unawa-nagoya-app  # サブパスに置く用
```

PWA のアイコンを作り直すときは `scripts/generate-brand-assets.py --web-only`（使い方はスクリプトの先頭）。
