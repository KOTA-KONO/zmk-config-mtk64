# mtk64ebt Rev3 ファームウェア (zmk-config-mtk64)

mtk64ebt Rev3 用の ZMK ファームウェア（Direct PC 接続 / Bluetooth BLE Split）リポジトリです。
Rev4 で培われた最新の ZMK 統合機能（Auto Mouse Layer、スクロール反転、動的CPI切替、ZMK Studio対応）を、Rev3 のハードウェア仕様（PixArt PMW3610 トラックボール）に合わせて最適化・移植しています。

### Rev3 の主な特徴とアップデート内容

* **Zephyr 公式純正 PMW3610 ドライバの採用**:
  外部モジュール依存を完全に撤廃し、Zephyr upstream 純正の `input_pmw3610` ドライバを統合。高い安定性と保守性を実現しています。
* **物理サンプリングに同期した低遅延レート制御**:
  PMW3610 のハードウェア物理サンプリング周期（4ms）に同期させ、**USB 接続時は 250Hz フラット送出**、**BLE 接続時は 133.3Hz（7.5msスロット同期）のオンデマンド Pull 方式**を採用。遅延ジッターやパケットドロップを最小限に抑えます。
* **動的 CPI 変更（200 〜 3200 CPI）**:
  キーボード上のキー操作（`&cpi_inc`, `&cpi_dec`）でリアルタイムに感度を 15 段階で調整可能。設定値はフラッシュメモリ（NVS）に自動永続化されます。
* **Auto Mouse Layer (AML) & スクロール方向反転**:
  ボールを転がすと自動でマウス操作レイヤーへ遷移する AML 機能や、スクロール方向の切り替え（通常 ⇔ 反転 / ナチュラルスクロール）をワンキーで切り替え可能です。
* **ZMK Studio 対応**:
  ファームウェアを再ビルドすることなく、ブラウザやアプリからリアルタイムにキーマップを変更できます。

> [!NOTE]
> トラックボールセンサーとして **PixArt PMW3610** を搭載した **mtk64ebt Rev3** 向けのファームウェアです。
> ※ PAW3222 センサーを搭載した Rev4 については、[`right_left_rev4`](https://github.com/mentako-ya/zmk-config-mtk64/tree/right_left_rev4) ブランチをご参照ください。

---

## 提供ファームウェアパッケージ (ZIP)

本ブランチの GitHub Actions 自動ビルドにより、以下の 4 種類の構成に対応したファームウェアパッケージが生成・配布されます。

| パッケージ名 (ZIP) | 接続形態 / 構成 | 含まれるファームウェア (.uf2) |
| :--- | :--- | :--- |
| **`mtk64ebt_Right_Left.zip`** | 左右構成（右手親機） | `mtk64_R.uf2`<br>`mtk64_L.uf2`<br>`settings_reset.uf2` |
| **`mtk64ebt_Right_Left_Foot.zip`** | 左右＋フットスイッチ | `mtk64_R.uf2` (Central枠=2)<br>`mtk64_L.uf2`<br>`mtk64_FOOT.uf2`<br>`settings_reset.uf2` |
| **`mtk64ebt_Right_Left_leftball.zip`** | 左ボール右エンコーダー | `mtk64_L_leftball.uf2` (Central)<br>`mtk64_R_leftball.uf2`<br>`settings_reset.uf2` |
| **`mtk64ebt_Right_Left_Foot_leftball.zip`** | 左ボール＋フットスイッチ | `mtk64_L_leftball.uf2` (Central枠=2)<br>`mtk64_R_leftball.uf2`<br>`mtk64_FOOT.uf2`<br>`settings_reset.uf2` |

* **配布先**: [Releases (latest-rev3)](https://github.com/mentako-ya/zmk-config-mtk64/releases/tag/latest-rev3) および [mtk64ebt リポジトリ (`firmware/rev3/`)](https://github.com/mentako-ya/mtk64ebt/tree/main/firmware/rev3)

---

## キーボードの Bluetooth 接続手順

1. キーボードのバッテリー駆動スイッチを ON にします。
2. セントラル親機（標準: 右手 / 左ボール: 左手）および子機基板上のリセットボタンを 1 回押して左右間をペアリングします。
3. PC の Bluetooth 設定画面を開き、検出された **`mtk64`** を選択して接続します（通常は数字コード入力不要でワンクリック接続）。
4. **接続状態の確認（LED）**:
   * セントラル親機側 LED: 接続中🔵、ペアリング待機中🟡、切断中🔴
   * ペリフェラル子機側 LED: セントラル接続中🔵、切断中🔴

---

## 機能と操作方法 (Rev3)

### 1. ZMK Studio でのリアルタイムキーマップ変更
Rev3 でも最新の ZMK Studio に対応しており、ソースコードを書き換えて再ビルドすることなく、ブラウザやアプリから直感的にキーマップを変更できます。

* **ブラウザ版**: 親機キーボードを USB 接続し、[https://zmk.studio/](https://zmk.studio/) にアクセス。
* **アプリ版**: [https://zmk.studio/download](https://zmk.studio/download) からダウンロード。無線接続のままでもキーマップ変更が可能です。
* ※ ロックされている場合は、レイヤー1 右手右上の **`&studio_unlock`** キーを押して UNLOCK してください。

### 2. トラックボール機能のリアルタイム制御（最右列キー）
トラックボールの各種設定（CPI感度、Auto Mouse Layer、スクロール方向反転）は、**レイヤー 1（精密モード）の右手最右列に縦一列で集約**されています。各設定値は Xiao BLE 内部のフラッシュメモリ（NVS）に自動保存され、電源オフや再起動後も状態を維持します。

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

### 3. レイヤー別トラックボール動作
* **レイヤー 0 (Base / DEF)**: 通常のキー入力レイヤー。
* **レイヤー 1 (Snipe / SNP)**: トラックボール精密操作モード。右手側がマウスボタンになり、移動速度が減速します。**右手最右列で各種トラックボール設定（反転・AML・CPI）を変更可能**です。
* **レイヤー 2 (Mouse / MSE)**: 通常マウスモード。右手側がマウスボタンになります。**最右列は透過（`&trans`）**のため、通常マウス操作中の誤爆を防止します。
* **レイヤー 3 (Scroll / SCR)**: スクロールモード。トラックボールを転がすと上下左右スクロールになります（`&scrl_inv_tog` で上下反転可能）。
* **レイヤー 6 (Auto Mouse / AML)**: トラックボール操作時に自動遷移するレイヤー（5秒間操作がないと自動復帰。`&aml_tog` で機能全体の ON/OFF が可能）。

---

## ファームウェア書き込み手順

各モジュール（右手、左手、フットスイッチ）へ対応するファームウェアを書き込みます：

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
   git checkout right_left_rev3
   ```
4. **編集とプッシュ**:
   * `config/mtk64.keymap` などを編集後、コミットしてプッシュします：
     ```sh
     git add .
     git commit -m "feat: customize keymap"
     git push origin right_left_rev3
     ```
5. **ファームウェアのダウンロード**:
   * プッシュすると GitHub Actions が自動実行され、ビルド完了後にフォーク先リポジトリの **「Releases」** または Actions 画面の **「Artifacts」** から ZIP ファイルをダウンロードできます。
