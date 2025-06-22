# buildhat-switch-control
Nintendo Switch コントローラーを使って LEGO SPIKE のモーターを制御する Python プロジェクトです。buildhat ライブラリと evdev を用いて、スティックやボタン入力に応じた直感的なモーター制御を実現します。教育・実験・拡張に最適。



# LEGO SPIKE モーターコントローラー（Nintendo Switch対応）

このプロジェクトは、Raspberry Pi 上で Nintendo Switch コントローラーを使用して LEGO SPIKE のモーターをリアルタイムに制御する Python プログラムです。`evdev` ライブラリでゲームパッド入力を取得し、`buildhat` ライブラリを使って BuildHAT 経由でモーター制御を行います。

## ✨ 特徴
- Switch コントローラーの左スティックでモーターのスピードと方向を制御
- ZL, ZR, START（+）, SELECT（-）などのボタン入力に対応
- スティックの方向を三角関数で処理し、自然なモーター挙動を実現
- Python の `asyncio` による非同期処理構成
- 教育や拡張開発にも適した構造

---

## 🛠 必要な環境
- Raspberry Pi（推奨: Raspberry Pi 4以降）
- LEGO BuildHAT および SPIKE 対応モーター（例: ポート B/C）
- Nintendo Switch Pro コントローラー（USB または Bluetooth）
- Python 3.9 以上
- Bluetooth サポート（無線接続時）

### Python ライブラリ依存
```txt
buildhat
evdev
```
インストールコマンド：
```bash
pip install buildhat evdev
```

---

## 📁 プロジェクト構成
```
spike-controller-via-gamepad/
├── motor_controller.py         # スティック操作とモーター制御のメインロジック
├── controller_input_test.py    # 入力テスト用スクリプト（ボタン・スティックのデバッグ）
├── utils/
│   └── motor_math.py           # 三角関数を用いた速度ベクトル計算
├── requirements.txt            # 必要なPythonパッケージ一覧
└── README.md                   # このドキュメント
```

---

## ⚙️ 処理概要
### 1. コントローラー入力の読み取り
- `/dev/input/eventX` を指定して `evdev.InputDevice` を初期化
- 左スティックのアナログ値を `ABS_X`, `ABS_Y` で読み取る
- ボタン入力（例: `BTN_ZL`, `BTN_START`）も検出可能

### 2. 角度・傾きから速度ベクトル計算
- スティックの方向を `atan2()` でラジアン角に変換：
```python
angle = math.atan2(dy, dx)
```
- 傾きの強さ（0〜1）を正規化：
```python
magnitude = math.hypot(dx, dy) / 128
```
- モーターの速度として変換：
```python
left_speed = int(magnitude * 100 * math.sin(angle + math.pi / 4))
right_speed = int(magnitude * 100 * math.sin(angle - math.pi / 4))
```
- 速度を -100 〜 100 にクリップして `MotorPair.start()` で出力

---

## 🚀 はじめかた
1. LEGO モーターを BuildHAT のポート B/C に接続
2. コントローラーを接続
   - USB接続：/dev/input/eventX を確認
   - Bluetooth接続：`bluetoothctl` を使用してペアリング
3. `evtest` や `ls /dev/input/` でデバイスパスを特定
4. メインスクリプトを実行：
```bash
python3 motor_controller.py
```
5. 左スティックを操作してモーターを動かす

---

## 🧪 開発予定
- [ ] 右スティック（ABS_RX / ABS_RY）の対応
- [ ] デッドゾーン（微入力の無効化）調整機能
- [ ] モーターポート設定の外部ファイル化
- [ ] スティック方向を可視化するGUI/ターミナル描画
- [ ] Web UIによる遠隔操作・状態確認

---

## 📝 ライセンス
MIT ライセンスに基づいて公開しています。

---

## 🤝 コントリビューション歓迎
バグ報告や改善提案、PR（Pull Request）はいつでも歓迎です。
自律移動やセンサー連携などへの拡張もぜひお試しください！
