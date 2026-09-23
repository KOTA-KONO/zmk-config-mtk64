# mtk64ebt Rev3 ドングルファームウェア (zmk-config-mtk64)

mtk64ebt Rev3 用の ZMK ファームウェア（1000Hz Nordic ESB ドングル接続構成）リポジトリです。
PC に接続した専用 USB ドングルを Central（親機）とし、左右キーボードおよびフットスイッチから **Nordic ESB (Enhanced ShockBurst)** により超低遅延通信を行います。

Rev4 で培われた最新の ZMK 統合機能（Auto Mouse Layer、スクロール反転、動的CPI切替、ZMK Studio対応、ドングルOLED表示）を、Rev3 のハードウェア仕様（PixArt PMW3610 トラックボール）に合わせて最適化・移植しています。

### Rev3 ドングル接続の主な特徴

* **Zephyr 公式純正 PMW3610 ドライバの採用**:
  外部モジュール依存を完全に撤廃し、Zephyr upstream 純正の `input_pmw3610` ドライバを統合。
* **1000Hz 超低遅延 ESB 通信 & 軽量バッテリー駆動**:
  ペリフェラル（左右手・フットスイッチ）は子機として動作し、超軽量な ESB 通信により消費電力を最小化しながら、キーストロークやトラックボール操作を極限まで低遅延でドングル親機へ送信します。
* **動的 CPI 変更（200 〜 3200 CPI） & ドングル OLED 表示**:
  キーボード上のキー操作（`&cpi_inc`, `&cpi_dec`）でリアルタイムに感度を 15 段階で調整可能。設定値はフラッシュメモリ（NVS）に自動永続化され、ドングル OLED 画面上に現在の CPI がリアルタイム表示されます。
* **Auto Mouse Layer (AML) & スクロール方向反転**:
  ボールを転がすと自動でマウス操作レイヤーへ遷移する AML 機能や、スクロール方向の切り替え（通常 ⇔ 反転 / ナチュラルスクロール）をワンキーで切り替え可能です。
* **ZMK Studio 対応**:
  ドングルを USB 接続した状態で、ブラウザやアプリからリアルタイムにキーマップを変更できます。

> [!NOTE]
> トラックボールセンサーとして **PixArt PMW3610** を搭載した **mtk64ebt Rev3** 向けのファームウェアです。
> ※ ドングルを使用しない Bluetooth 直接接続（BLE Split）については、[`right_left_rev3`](https://github.com/mentako-ya/zmk-config-mtk64/tree/right_left_rev3) ブランチをご参照ください。

---

## 提供ファームウェアパッケージ (ZIP)

本ブランチの GitHub Actions 自動ビルドにより、以下の 5 種類のドングル構成に対応したファームウェアパッケージが生成・配布されます。

| パッケージ名 (ZIP) | 構成内容 | 含まれるファームウェア (.uf2) |
| :--- | :--- | :--- |
| **`mtk64ebt_Right_Left_Dongle_disp_foot.zip`** | 左右＋ドングルOLED＋フット | `mtk64_DONGLE_display.uf2`<br>`mtk64_R_dongle.uf2`<br>`mtk64_L_dongle.uf2`<br>`mtk64_FOOT.uf2`<br>`settings_reset.uf2` |
| **`mtk64ebt_Right_Left_Dongle_foot.zip`** | 左右＋ドングル（画面なし）＋フット | `mtk64_DONGLE.uf2`<br>`mtk64_R_dongle.uf2`<br>`mtk64_L_dongle.uf2`<br>`mtk64_FOOT.uf2`<br>`settings_reset.uf2` |
| **`mtk64ebt_Right_Left_Dongle_display.zip`** | 左右＋ドングルOLED | `mtk64_DONGLE_display.uf2`<br>`mtk64_R_dongle.uf2`<br>`mtk64_L_dongle.uf2`<br>`settings_reset.uf2` |
| **`mtk64ebt_Right_Left_Dongle.zip`** | 左右＋ドングル（画面なし） | `mtk64_DONGLE.uf2`<br>`mtk64_R_dongle.uf2`<br>`mtk64_L_dongle.uf2`<br>`settings_reset.uf2` |
| **`mtk64ebt_Right_Left_Dongle_disp_leftball.zip`** | 左ボール右エンコーダー＋ドングルOLED | `mtk64_DONGLE_display.uf2`<br>`mtk64_L_leftball.uf2`<br>`mtk64_R_leftball.uf2`<br>`settings_reset.uf2` |

* **配布先**: [Releases (latest-dongle-rev3)](https://github.com/mentako-ya/zmk-config-mtk64/releases/tag/latest-dongle-rev3) および [mtk64ebt リポジトリ (`firmware/rev3/`)](https://github.com/mentako-ya/mtk64ebt/tree/main/firmware/rev3)

---

## ドングル接続の手順

1. **ドングルの接続**:
   * ドングル（Seeed XIAO BLE）を PC の USB ポートに接続します。
2. **ペリフェラル（左右手・フットスイッチ）の起動**:
   * 各キーボードのバッテリー駆動スイッチを ON にします。
   * ドングルおよび各子機基板上のリセットボタンを 1 回押して通信を開始します。
3. **接続状態の確認**:
   * ドングル OLED 画面（ディスプレイ付き構成時）に、左右手およびフットスイッチのバッテリー残量（R/L/F）、現在のレイヤー名、BongoCat アニメーション、CPI 感度が表示されます。

---

## 機能と操作方法 (Rev3)

### 1. ZMK Studio でのリアルタイムキーマップ変更
ドングル親機が ZMK Studio に対応しています。ソースコードを書き換えて再ビルドすることなく、ブラウザやアプリから直感的にキーマップを変更できます。

* **ブラウザ版**: ドングルを USB 接続し、[https://zmk.studio/](https://zmk.studio/) にアクセス。
* **アプリ版**: [https://zmk.studio/download](https://zmk.studio/download) からダウンロード。
* ※ ロックされている場合は、レイヤー1 右手右上の **`&studio_unlock`** キーを押して UNLOCK してください。

### 2. トラックボール機能のリアルタイム制御（最右列キー）
トラックボールの各種設定（CPI感度、Auto Mouse Layer、スクロール方向反転）は、**レイヤー 1（精密モード）の右手最右列に縦一列で集約**されています。各設定値はフラッシュメモリ（NVS）に自動保存され、電源オフや再起動後も状態を維持します。

> [!NOTE]
> 通常マウスモードである **レイヤー 2（MSE）** では、マウス操作中の意図しないキー誤入力を防止するため調整キーを配置せず、透過（`&trans`）としています。トラックボールの設定変更は **レイヤー 1（SNP）** から行います。

#### 右手最右列のキー配置比較

| 段 | レイヤー 1 (SNP: 精密モード) | レイヤー 2 (MSE: 通常マウスモード) | 説明 |
| :--- | :--- | :--- | :--- |
| **ROW 1（最上段）** | `&studio_unlock` | `&kp F11` | ZMK Studio アンロック / ファンクションキー |
| **ROW 2** | **`&scrl_inv_tog`** | `&trans`（透過） | **スクロール反転切替**（通常 ⇔ 反転 / ナチュラルスクロール） |
| **ROW 3** | **`&aml_tog`** | `&trans`（透過） | **Auto Mouse Layer 切替**（自動レイヤー遷移の ON ⇔ OFF） |
| **ROW 4** | **`&cpi_inc`** | `&trans`（透過） | **CPI 感度UP**（解像度を 1 段階上げる） |
| **ROW 5（最下段）** | **`&cpi_dec`** | `&trans`（透過） | **CPI 感度DOWN**（解像度を 1 段階下げる） |

#### 各制御キーの機能詳細
* **`&scrl_inv_tog`（スクロール反転切替）**:
  * レイヤー 3（スクロールモード）での**上下スクロール方向（通常 ⇔ 反転 / ナチュラルスクロール）**をリアルタイムにトグルします。
* **`&aml_tog`（Auto Mouse Layer ON/OFF切替）**:
  * トラックボール操作時にレイヤー 6（マウス操作レイヤー）へ自動遷移する機能の**有効 / 無効**をトグルします。OFF にするとボールを動かしてもレイヤーが切り替わらず、純粋なカーソル移動のみが行われます。
* **`&cpi_inc` / `&cpi_dec`（感度調整）**:
  * トラックボールの解像度（CPI）を 1 段階ずつ調整します（**全 15 段階: 200 〜 3200 CPI**）。
  * 設定テーブル: `200`, `400`, `600`, `800`, `1000` (デフォルト), `1200`, `1400`, `1600`, `1800`, `2000`, `2200`, `2400`, `2600`, `2800`, `3200` CPI
  * ※ ドングル OLED 画面上に現在の CPI がリアルタイム表示されます。

### 3. レイヤー別トラックボール動作
* **レイヤー 0 (Base / DEF)**: 通常のキー入力レイヤー。
* **レイヤー 1 (Snipe / SNP)**: トラックボール精密操作モード。右手側がマウスボタンになり、移動速度が減速します。**右手最右列で各種トラックボール設定（反転・AML・CPI）を変更可能**です。
* **レイヤー 2 (Mouse / MSE)**: 通常マウスモード。右手側がマウスボタンになります。**最右列は透過（`&trans`）**のため、通常マウス操作中の誤爆を防止します。
* **レイヤー 3 (Scroll / SCR)**: スクロールモード。トラックボールを転がすと上下左右スクロールになります（`&scrl_inv_tog` で上下反転可能）。
* **レイヤー 6 (Auto Mouse / AML)**: トラックボール操作時に自動遷移するレイヤー（5秒間操作がないと自動復帰。`&aml_tog` で機能全体の ON/OFF が可能）。

---

## ファームウェア書き込み手順

各モジュール（ドングル、右手、左手、フットスイッチ）へ対応するファームウェアを書き込みます：

1. **バッテリー駆動スイッチを OFF** にします（バッテリー給電下では書き込み後の自動リセットが正常に行われないため）。
2. 書き込むデバイスを USB ケーブルで PC に接続します。
3. Xiao BLE 基板上のリセットスイッチを **素早く 2 回（ダブルクリック）** 押します。PC にリムーバブルディスク **`XIAO-SENSE`** が認識されます。
4. **設定初期化（推奨）**:
   * `settings_reset.uf2` を `XIAO-SENSE` にドラッグ＆ドロップします（書き込み後、自動再起動）。
5. **ファームウェア書き込み**:
   * 再度リセットスイッチを素早く 2 回押し、各構成に対応する `.uf2` ファイルをドラッグ＆ドロップします。

---

## フォークとカスタマイズ手順

キーマップやオーバーレイをご自身のアカウントでカスタマイズ・自動ビルドする場合の手順です。

1. **リポジトリをフォーク**:
   * GitHub 画面右上の「Fork」ボタンをクリックしてフォークします。
   * フォーク後、**「Actions」タブ** $\to$ **「I understand my workflows, go ahead and enable them」** をクリックして GitHub Actions を有効化します。
2. **ローカルへクローン**:
   ```sh
   git clone https://github.com/<your_username>/zmk-config-mtk64.git
   cd zmk-config-mtk64
   ```
3. **作業ブランチへ切り替え**:
   ```sh
   git checkout right_left_dongle_rev3
   ```
4. **編集とプッシュ**:
   * `config/mtk64.keymap` などを編集後、コミットしてプッシュします：
     ```sh
     git add .
     git commit -m "feat: customize keymap"
     git push origin right_left_dongle_rev3
     ```
5. **ファームウェアのダウンロード**:
   * プッシュすると GitHub Actions が自動実行され、ビルド完了後にフォーク先リポジトリの **「Releases」** または Actions 画面の **「Artifacts」** から ZIP ファイルをダウンロードできます。
