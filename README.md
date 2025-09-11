# 医療用 画像切り取りカメラ (PWA) – ver1.0

**CT/MRI などの医療画像を“丸枠で切り抜いて”即共有できる、ブラウザ動作のカメラアプリ**です。  
iPhone でも動作し、**PWA（ホーム画面追加）**と **Web Share API** に対応。撮影 → 保存 → 「共有」から **メッセージ（SMS/MMS）やメール等に画像を直接添付**できます。

> 現行ベースファイル: `circle_crop_camera_v17_fitinside_visiblefix.html`

---

## 主な機能

- **丸型マスクでの切り抜き撮影**（1080×1080 / JPEG 品質 0.9）
- **iPhone 表示最適化**：`object-fit: cover` による横幅フィット
- **PWA + Web Share API**：保存直後に「共有」ボタン → iOS 共有シートから送信
- **設定の永続化**（localStorage）  
  丸枠サイズ（S/M/L）、位置、グリッド、ロック、はみ出し防止、フルスクリーン、カメラ面（背面固定）
- **操作性の改善**
  - ダブルタップで**中央に戻す**
  - **保存トースト**（ファイル名表示）
  - **処理中スピナー**（切り抜き/保存中）
  - **ロック可視化**（黄色リング + 🔒）
  - **可視領域に限定したはみ出し防止**（`object-fit: cover` 時の画面外を除外）

> **ファイル名**: `CTMRI_YYYYMMDD_HHMMSS.jpg`（例: `CTMRI_20250911_120301.jpg`）

---

## 画面・操作

- **撮影**：中央のシャッターボタン
- **丸枠サイズ**：S / M / L（トグル）
- **位置調整**：ドラッグ、**ダブルタップで中央戻し**
- **グリッド**：ON/OFF
- **ロック**：ON 中は枠色が**黄**、🔒表示
- **はみ出し防止**：ON で**可視領域内に自動クランプ**
- **全画面**：トグル
- **共有**：保存直後に「共有」ボタンが表示 → iOS の共有シートへ

> 対応端末では**ズーム**も利用可能（ブラウザ/デバイスのカメラ機能次第）。

---

## ファイル構成

```
/ (公開ルート)
├─ circle_crop_camera_v17_fitinside_visiblefix.html  # アプリ本体（単一HTML）
├─ manifest.webmanifest                               # PWA マニフェスト
├─ sw.js                                              # Service Worker（キャッシュ）
└─ icon-512.png                                       # PWA アイコン
```

> 既存の `start_url` や `scope` は、これらのファイルが**同じディレクトリ**にある前提です。

---

## セットアップ & デプロイ

### 1) ローカル試用（開発）
- **HTTPS が必須**（iOS Safari は http/localhost 例外が弱い）  
  すぐ試す場合は、Netlify/Vercel/GitHub Pages 等の**HTTPS ホスティング**を推奨。
- デスクトップでの動作確認のみなら、Chrome/Edge は `https://` または `http://localhost` でカメラが動作。

### 2) GitHub Pages（推奨）
1. この 4 ファイルを GitHub リポジトリに置く  
   - ルート直下、または `docs/` 配下でも可（Pages 設定に合わせる）
2. GitHub の **Settings → Pages** で公開ブランチ/フォルダを指定
3. 公開 URL にアクセス  
   - ファイル名を **`index.html` にリネーム**する場合は、`manifest.webmanifest` の `start_url` も `/` か `./` に更新するのが簡便です

### 3) Netlify / Vercel
- リポジトリを接続してデプロイ（ビルド不要）。
- **ルート直下**に 4 ファイルが配置されるようにするだけで OK。

---

## PWA（ホーム画面追加）
- iPhone Safari でページを開き → 共有 → **「ホーム画面に追加」**  
- `manifest.webmanifest` と `sw.js` により、オフライン時も起動用 HTML をキャッシュ（完全オフライン撮影は端末制約で不可）。

---

## 権限 & 必要条件

- **HTTPS 必須**、**カメラ権限**が必要です
- **iOS 16.4 以上**：Web Share API（**ファイル共有**）対応  
  対応外端末では「共有」ボタンは非表示（機能自動無効化）

---

## 既知の制限 / 注意

- **SMS/MMS の容量制限**により、大きな画像は送信に失敗する場合があります
  - 必要に応じて、将来的に**出力サイズ/品質のプリセット**を追加してください（例：1080/720/512）
- ブラウザごとにカメラの**ズーム/手ブレ補正**対応は異なります
- 端末の省電力/スリープ設定により連続使用時に画面が消灯する場合があります（Wake Lock は将来拡張で対応予定）

---

## セキュリティ / プライバシー

- 本アプリは**スタンドアロン（サーバー送信なし）**です。撮影・切り抜き・保存・共有は**端末内で完結**します。
- ただし、**医療情報（PHI）**の取り扱いにはご注意ください。
  - 現状、画像の**透かし/匿名化**はオプション実装前です。業務要件に応じて**白/黒縁・透かし**等を追加してください。
  - 施設のセキュリティポリシーに従って運用してください。


## 免責事項 / DISCLAIMER
本アプリは医療用の情報共有目的で作成したカメラですが、医療用以外での用途で使用しても構いません。現在は試験的に運用中です。  
本アプリの使用に起因して生じたいかなる損害についても、作者は責任を負いません。  
取り扱う画像・個人情報は、所属組織のセキュリティポリシーおよび関連法令に従って管理してください。


---

## トラブルシューティング

- **カメラが起動しない**：  
  - iPhone Safari では **HTTPS 接続**が必要です  
  - ブラウザ設定で **カメラ許可** を ON にしてください
- **共有ボタンが出ない**：  
  - 端末/OS が **Web Share API (files)** に未対応の可能性があります（iOS 16.4+ を推奨）
- **切り抜きがずれる**：  
  - v17 では `object-fit: cover` と同じ倍率計算（`Math.max`）で**座標補正済み**  
  - 「はみ出し防止」ON で**可視領域外に行かない**ようクランプ済み

---

## ライセンス

```
MIT License 

Copyright (c) 2025_09_11 Tomohito Kadota

Permission is hereby granted, free of charge, to any person obtaining a copy
of this software and associated documentation files (the “Software”), to deal
in the Software without restriction, including without limitation the rights
to use, copy, modify, merge, publish, distribute, sublicense, and/or sell
copies of the Software, and to permit persons to whom the Software is
furnished to do so, subject to the following conditions:

The above copyright notice and this permission notice shall be included
in all copies or substantial portions of the Software.

THE SOFTWARE IS PROVIDED “AS IS”, WITHOUT WARRANTY OF ANY KIND, EXPRESS
OR IMPLIED, INCLUDING BUT NOT LIMITED TO THE WARRANTIES OF MERCHANTABILITY,
FITNESS FOR A PARTICULAR PURPOSE AND NONINFRINGEMENT. IN NO EVENT SHALL THE
AUTHORS OR COPYRIGHT HOLDERS BE LIABLE FOR ANY CLAIM, DAMAGES OR OTHER
LIABILITY, WHETHER IN AN ACTION OF CONTRACT, TORT OR OTHERWISE, ARISING FROM,
OUT OF OR IN CONNECTION WITH THE SOFTWARE OR THE USE OR OTHER DEALINGS IN
THE SOFTWARE.

```

---

## 変更履歴（要約）
- 2025_09_11  ver1.0: iPhone 横幅フィット（`object-fit: cover`）
-                     設定永続化 / 保存トースト / ダブルタップ中央 / スピナー / タップ領域拡大
-                     ロック可視化（黄リング＋🔒）/ エラー文言強化 / コントラスト調整
-                     PWA + Web Share 対応（共有ボタン）


---

## 著者
- Maintainer: Dr. Tomohito Kadota (Kochi) / Contributors welcome
