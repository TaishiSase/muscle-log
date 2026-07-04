# パパの筋トレ記録

## アプリ概要
佐瀬家パパ（大志）専用の筋トレ記録Webアプリ。カレンダーから日付を選んで種目・重量・回数・セット数を入力し保存する。グラフ・PR・体組成記録・種目管理も備える。

- 本番URL: Vercel（GitHubプッシュで自動デプロイ）
- GitHub: https://github.com/TaishiSase/muscle-log

## 技術スタック
- フロントエンド: HTML + CSS + Vanilla JS（フレームワークなし）、単一ファイル `index.html`
- DB: Supabase（PostgreSQL）
- ホスティング: Vercel
- 認証: Supabase Auth（パパ・ママのメールアドレスでログイン）

## ファイル構成
```
index.html    ← HTML・CSS・JS すべて1ファイルにまとめてある
config.json   ← Supabase接続情報（supabaseUrl, supabaseKey, papaEmail, mamaEmail）
vercel.json   ← Vercel設定
```

## Supabaseスキーマ

### exercises（種目マスタ）
| カラム | 型 | 備考 |
|---|---|---|
| id | UUID PK | |
| name | text | 種目名 |
| is_cardio | boolean | 有酸素種目フラグ（kmで記録） |
| default_kg | numeric | デフォルト重量 |
| default_reps | int | デフォルト回数 |
| default_sets | int | デフォルトセット数 |
| sort_order | int | 表示順 |

### workout_sessions（筋トレセッション）
| カラム | 型 | 備考 |
|---|---|---|
| id | UUID PK | |
| date | DATE | 筋トレした日 |
| created_at | TIMESTAMP | |

### workout_logs（種目ごとの記録）
| カラム | 型 | 備考 |
|---|---|---|
| id | UUID PK | |
| session_id | UUID FK | workout_sessions.id |
| exercise_id | UUID FK | exercises.id |
| kg | numeric | 重量（筋トレ種目） |
| reps | int | 回数 |
| sets | int | セット数 |
| km | numeric | 距離（有酸素種目） |
| levelup_flag | boolean DEFAULT false | 次回もっと重くいけるかチェック |

### body_metrics（体組成記録）
| カラム | 型 | 備考 |
|---|---|---|
| id | UUID PK | |
| date | DATE UNIQUE | |
| weight | numeric | 体重(kg) |
| body_fat | numeric | 体脂肪率(%) |
| muscle_mass | numeric | 筋肉量(kg) |
| visceral_fat | int | 内臓脂肪レベル |
| body_age | int | 体内年齢 |

## 主要機能
1. **カレンダー**: 月単位で表示、記録のある日にドットを表示
2. **記録シート（ボトムシート）**: 各種目をチェック → 重量/回数/セット入力 → 保存
3. **前回値プリフィル**: 直近セッションの値を自動表示
4. **次回レベルアップチェック**: 種目ごとに「次回もっと重くいける」チェックボックス。次回セッションで「⬆️ 前回UP可」バッジを表示
5. **デイリーサマリー**: 消費カロリー・総ボリュームを食べ物・物体と比較してリアルタイム表示
6. **グラフ**: 種目ごとの重量/距離推移（3ヶ月/6ヶ月/全期間）
7. **PR（自己ベスト）**: 種目ごとの最大重量/距離
8. **体組成グラフ**: 体重・体脂肪率などの推移
9. **種目管理**: 種目の追加・編集・削除

## 認証
- Supabase Authのメール＋パスワードで認証
- 未ログインでも閲覧可能、保存時にログインを求める
- パパ: `taish.dengel@gmail.com` / ママ: `vv8.shk.4ill@hotmail.co.jp`

## 開発規約
- JSはVanilla JS、フレームワーク不使用
- CSSもフレームワーク不使用、CSS変数でテーマ管理
- HTMLはすべて動的生成（innerHTML）
- DBアクセスはSupabase JS SDK（CDN）を使用
- `esc()` 関数でXSSを防ぐ（innerHTMLに挿入する文字列は必ずesc()を通す）
