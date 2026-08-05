# 横瀬町地域エネルギー資源マップ

埼玉県横瀬町を対象に、OpenStreetMap上の太陽光パネル、蓄電池、送電線、変電所を重ねて表示し、地域エネルギー資源と電力インフラの位置関係を確認するWeb GISです。

- [公開Webアプリ](https://furuhashilab.github.io/2026gsc_SatoAki/)
- [GitHubリポジトリ](https://github.com/furuhashilab/2026gsc_SatoAki)

## 研究概要

横瀬町で防災型マイクログリッドや地域エネルギー拠点を検討するには、まず地域内にどのような発電設備と電力インフラが存在するかを把握する必要があります。

これまで作成した太陽光パネル解析Webアプリでは、OpenStreetMapから太陽光設備を抽出し、面積、推計容量、年間想定発電量を計算できるようにしました。しかし、太陽光パネルが存在することと、その電気を地域が必要なときに使えることは同じではありません。蓄電池の有無、停電時の自立運転、売電契約、所有者の協力意思、避難所や公共施設との位置関係などを確認しなければ、地域資源として評価することはできません。

本研究では、既存の太陽光パネル解析Webアプリの簡潔なUIと面積集計機能を維持しながら、OpenStreetMapから取得できる範囲で、太陽光パネル、蓄電池、送電線、変電所を独立したレイヤーとして表示します。現時点では地域利用可能性の判定は行わず、OSM上に既に存在する情報と、太陽光ポリゴンから計算できる値を整理することを目的とします。

## 体験設計

本Webアプリでは、情報の役割を次のように分けています。

1. **地図**: 横瀬町周辺の太陽光設備と電力インフラの位置関係を把握する
2. **太陽光パネル集計**: OSMポリゴンから面積、推計容量、年間想定発電量を確認する
3. **レイヤー切り替え**: 太陽光、蓄電池、送電線、変電所を個別に表示・非表示する
4. **ランキング**: 面積、発電量、容量、ピーク出力の大きい太陽光設備を確認する
5. **データ出力**: 取得した太陽光設備をCSV / GeoJSONとして保存する

## 現在の実装

- MapLibre GL JSによる地図表示
- Overpass APIによるOSMデータ取得
- 太陽光パネルのOSMポリゴン取得
- 太陽光パネルの面積、推計容量、年間想定発電量、ピーク出力の簡易計算
- 太陽光パネルの単一選択と多角形範囲選択
- 面積、発電量、容量、ピーク出力ランキング
- CSV / GeoJSON出力
- OAM空撮画像の重ね表示
- Esri World Imagery、国土地理院陰影起伏図、CARTOの背景地図切り替え
- OSM上の蓄電池、送電線、変電所の取得
- 太陽光、蓄電池、送電線、変電所の独立表示切り替え
- オブジェクト種別・容量に応じた色分け表示

## 前回からの発展

前回のアプリは、主に太陽光パネルの位置、面積、発電量を確認するための台帳でした。今回の構想発表版では、太陽光パネルだけでなく、OSMで取得できる送電線、変電所、蓄電池を重ねて表示し、発電設備と電力インフラの位置関係を確認できるようにしました。

主な変更点は次のとおりです。

- 太陽光パネル単体の可視化から、電力インフラを含む基礎地図へ拡張
- `power=line`、`power=minor_line`、`power=cable` の表示を追加
- `power=substation` の表示を追加
- `power=storage`、`generator:method=battery`、`battery` の表示を追加
- 太陽光、蓄電池、送電線、変電所の個別表示切り替えを追加
- 太陽光パネルを推計容量に応じて段階色で表示
- 送電線、変電所、蓄電池を種別ごとに色分け
- 裏付けデータが不足している地域利用可能性のA/B/C/D判定を削除

## 技術構成

- HTML
- CSS
- JavaScript
- MapLibre GL JS
- Turf.js
- OpenStreetMap
- Overpass API
- OpenAerialMap
- GeoJSON
- CSV
- GitHub Pages

## 使用するOSMタグ

太陽光設備は、主に以下のタグで検索します。

```overpass
way["power"="generator"]["generator:source"="solar"];
way["power"="plant"]["plant:source"="solar"];
way["building"="solar_panels"];
way["landuse"="solar_panel"];
relation["power"="plant"]["plant:source"="solar"];
relation["power"="generator"]["generator:source"="solar"];
```

電力インフラは、OSMに既に登録されている以下の地物を検索します。候補地を推定して追加するのではなく、既存のOSM情報のみを表示します。

```overpass
node["power"="storage"];
way["power"="storage"];
relation["power"="storage"];
node["generator:method"="battery"];
way["generator:method"="battery"];
relation["generator:method"="battery"];
node["battery"];
way["battery"];
way["power"="line"];
way["power"="minor_line"];
way["power"="cable"];
node["power"="substation"];
way["power"="substation"];
relation["power"="substation"];
```

## 評価計画

構想発表時点では、地域利用可能性の判定は行いません。OpenStreetMapから取得できる情報だけでは、蓄電池容量、売電契約、自立運転、所有者の協力意思を確認できないためです。

今後は、以下の観点で追加調査を行います。

- 太陽光発電設備に蓄電池が接続されているか
- 停電時に自立運転できるか
- 売電契約、PPA、リース、屋根貸しなどの制約があるか
- 所有者が災害時の地域利用に協力できるか
- 避難所、公共施設、福祉施設、通信拠点との距離
- 太陽熱、EV/V2H、小水力、木質資源など、電力以外の地域エネルギー資源

追加調査後、以下の5軸で評価することを検討します。

| 評価軸 | 内容 |
| --- | --- |
| 供給力 | 発電量、蓄電容量、熱供給量など |
| 自立性 | 停電時に単独で利用できるか |
| 公共性 | 避難所、公共施設、福祉施設、通信拠点などに近いか |
| 利用可能性 | 所有、契約、協定、地域協力意思の観点から使えるか |
| 安全性 | 土砂災害、浸水、孤立、道路寸断などのリスクを公開資料で確認する |

## 今後の予定

| 時期 | 内容 |
| --- | --- |
| 夏合宿前 | 構想発表用README作成、プロトタイプ整理 |
| 2026年8月 | Overpass API検索機能、面積・発電量集計、OSM電力インフラレイヤーの改善 |
| 2026年9月 | 避難所・公共施設データの収集、評価項目の整理 |
| 2026年10月 | 住民・施設向け調査項目の設計、ヒアリング準備 |
| 2026年11月 | 中間発表、候補エリアの仮選定 |
| 2026年12月 | Advent Calendarとして進捗公開 |
| 2027年1月 | 最終成果物・本文整理 |
| 2027年2月 | 最終発表 |

## ローカルでの確認

`file://` では地図ライブラリ、外部タイル、Overpass API、GeoJSON読み込みが正常に動作しない場合があります。ローカルサーバーを使用してください。

Pythonを使う場合:

```bash
python -m http.server 8765 --bind 127.0.0.1
```

ブラウザで以下を開きます。

```text
http://127.0.0.1:8765/
```

## ファイル構成

```text
index.html                                現行版のWebアプリ本体
README.md                                 構想発表用README
LICENSE                                   CC BY 4.0ライセンス
.nojekyll                                 GitHub Pages用設定
data/yokoze_boundary.geojson              横瀬町行政界データ
data/yokoze_solar_panels.geojson          太陽光設備ポリゴン
data/yokoze_solar_panels.csv              太陽光設備の表形式データ
data/yokoze_solar_tag_summary.csv         太陽光設備タグの集計
data/yokoze_energy_assets_sample.geojson  今後の調査用サンプルデータ
```

## 参考文献・参考資料

参考文献・引用リストは、古橋研究室のルールに従い、独立したGitHub Issueとして整理します。

現時点で参照予定の資料は以下です。

- 古橋研究室 卒論/ゼミ論ルール Issue #5
- 横瀬町 第2次ちちぶ環境基本計画
- 資源エネルギー庁 地域マイクログリッド関連資料
- OpenStreetMap Wiki
- Overpass API Documentation
- 国土数値情報
- 埼玉県 防災関連オープンデータ
- NREL Energy Resilience / REopt関連資料

## ライセンス・データ出典

- ライセンス: [CC BY 4.0](LICENSE)
- 地図ライブラリ: MapLibre GL JS
- 空間解析: Turf.js
- 地図データ: OpenStreetMap contributors
- 空撮画像: OpenAerialMap / Open Imagery Network
- 背景地図: Esri World Imagery、国土地理院、CARTO

© Furuhashi Laboratory / Sato Aki, CC BY 4.0
