# ハンバーガーメニュー実装ガイド

HTMLとCSSのみで実装されたハンバーガーメニューのサンプルコードです。

## 📁 ファイル構成

- `index.html` - HTML構造
- `style.css` - スタイルシート
- `README.md` - このファイル

## 🎯 実装の仕組み

### 1. チェックボックスによる状態管理

ハンバーガーメニューの開閉は、`<input type="checkbox">`で管理しています。

```html
<input type="checkbox" id="menu-toggle">
<label for="menu-toggle" class="menu-icon">
  <span></span>
  <span></span>
  <span></span>
</label>
```

- チェックボックスは`display: none`で非表示
- `label`要素をクリックすると、チェックボックスの状態が切り替わる
- CSSの`:checked`疑似クラスで、チェック状態に応じてスタイルを変更

### 2. メニューの表示/非表示

```css
.menu {
  position: fixed;
  right: -250px; /* 画面外に隠す */
  transition: right 0.3s;
}

#menu-toggle:checked ~ .menu {
  right: 0; /* 画面内に表示 */
}
```

- 初期状態：`right: -250px`で画面外に配置
- チェック時：`right: 0`で画面内に表示
- `transition`でスムーズなアニメーション

### 3. ハンバーガーアイコンの×印アニメーション

```css
.menu-icon span {
  position: absolute;
}

#menu-toggle:checked ~ .menu-icon span:nth-child(1) {
  top: 50%;
  transform: translateY(-50%) rotate(45deg);
}
```

- 3本の線を`position: absolute`で重ねて配置
- チェック時に中央に集めて回転させ、×印を作成

---

## 📚 positionプロパティの詳しい解説

`position`プロパティは、要素の配置方法を指定する重要なCSSプロパティです。

### positionの値

#### 1. `static`（デフォルト値）
- 通常の文書フローに従って配置
- `top`、`right`、`bottom`、`left`、`z-index`は無効

#### 2. `relative`（相対配置）
- 元の位置を基準に相対的に配置
- 元の位置のスペースは残る
- `top`、`right`、`bottom`、`left`で位置を調整可能

**このコードでの使用例：**
```css
header {
  position: relative; /* 子要素の基準点になる */
  z-index: 1000;
}

.menu-icon {
  position: relative; /* 子要素（span）の基準点になる */
}
```

#### 3. `absolute`（絶対配置）
- **最も近い`position`が`relative`、`absolute`、`fixed`の親要素**を基準に配置
- 親要素がない場合は、`<body>`を基準にする
- 元の位置のスペースは残らない（他の要素が詰まる）

**このコードでの使用例：**
```css
.menu-icon span {
  position: absolute; /* .menu-icon（position: relative）を基準に配置 */
  top: 0; /* 上から0px */
}
```

**動作の仕組み：**
1. `.menu-icon`が`position: relative`なので、基準点になる
2. `.menu-icon span`が`position: absolute`なので、`.menu-icon`を基準に配置される
3. `top: 0`、`top: 50%`、`bottom: 0`で3本の線を配置

#### 4. `fixed`（固定配置）
- **ビューポート（画面）**を基準に配置
- スクロールしても位置が固定される
- 元の位置のスペースは残らない

**このコードでの使用例：**
```css
.menu {
  position: fixed; /* 画面を基準に配置 */
  top: 0;
  right: -250px;
  height: 100vh; /* 画面の高さいっぱい */
}
```

**なぜ`fixed`を使うのか：**
- メニューを画面全体に表示したい
- スクロールしてもメニューが表示されたままにするため
- `right: -250px`で画面外に隠し、`right: 0`で表示

### positionの階層関係

```
header (position: relative)
  └─ .menu-icon (position: relative)
      └─ span (position: absolute) ← .menu-iconを基準に配置
  └─ .menu (position: fixed) ← 画面を基準に配置
```

---

## 🎨 z-indexプロパティの詳しい解説

`z-index`は、要素の**重なり順（レイヤー順）**を制御するプロパティです。

### z-indexの基本

- **数値が大きいほど前面に表示される**
- `position`が`static`以外の要素にのみ有効
- 負の値も使用可能

### このコードでのz-indexの役割

```css
header {
  position: relative;
  z-index: 1000; /* ナビゲーションバー全体のレイヤー */
}

.menu-icon {
  position: relative;
  z-index: 1001; /* ハンバーガーアイコンを最前面に */
}

.menu {
  position: fixed;
  z-index: 1000; /* メニューはアイコンより後ろ */
}
```

### なぜz-indexが必要なのか

1. **メニューが開いた時の問題**
   - `.menu`が`position: fixed`で画面全体に表示される
   - `.menu-icon`が`.menu`の後ろに隠れてしまう可能性がある

2. **解決方法**
   - `.menu-icon`に`z-index: 1001`を設定
   - `.menu`に`z-index: 1000`を設定
   - これで、ハンバーガーアイコンが常にメニューより前面に表示される

### z-indexの階層構造

```
z-index: 1001  ← .menu-icon（ハンバーガーアイコン）
                ↑ 最前面：クリック可能
                │
z-index: 1000  ← .menu（メニューリスト）
                │
z-index: 1000  ← header（ヘッダー）
                │
z-index: なし  ← その他の要素（mainなど）
                ↓ 最背面
```

### z-indexの注意点

1. **親要素のz-indexが子要素に影響**
   - 親要素のz-indexが低いと、子要素のz-indexが高くても前面に出ない
   - このコードでは、`header`と`.menu`が同じ`z-index: 1000`だが、`.menu-icon`が`z-index: 1001`なので前面に出る

2. **新しいスタッキングコンテキスト**
   - `position`が`relative`、`absolute`、`fixed`で、`z-index`が設定されると、新しいスタッキングコンテキストが作成される
   - その中で子要素のz-indexが比較される

3. **数値の選び方**
   - 10、20、30...のように段階的に設定するのが一般的
   - このコードでは1000、1001と大きな値を使っているが、10、11でも問題ない
   - 大きな値を使う理由：他の要素と重ならないようにするため

---

## 🔧 主要なCSSテクニック

### 1. 兄弟セレクタ（`~`）

```css
#menu-toggle:checked ~ .menu {
  right: 0;
}
```

- `~`は「後続の兄弟要素」を選択
- `#menu-toggle`の後に来る`.menu`を選択
- チェックボックスがチェックされたら、後続のメニューを表示

### 2. 疑似クラス（`:checked`）

```css
#menu-toggle:checked ~ .menu-icon span:nth-child(1) {
  transform: translateY(-50%) rotate(45deg);
}
```

- `:checked`は、チェックボックスやラジオボタンが選択された状態を表す
- チェック状態に応じてスタイルを変更できる

### 3. メディアクエリ（`@media`）

```css
@media screen and (max-width: 768px) {
  .menu-icon {
    display: block;
  }
}
```

- 画面幅が768px以下の時のみ適用
- モバイル表示でハンバーガーメニューを表示

---

## 🎓 学習のポイント

### 理解すべき重要な概念

1. **チェックボックスとlabelの連携**
   - `label`の`for`属性でチェックボックスと紐付け
   - `label`をクリックすると、チェックボックスの状態が変わる

2. **CSSセレクタの組み合わせ**
   - `#menu-toggle:checked ~ .menu`：チェック状態のチェックボックスの後続要素
   - 兄弟セレクタ（`~`）の使い方

3. **positionとz-indexの関係**
   - `position`が`static`以外でないと`z-index`が効かない
   - レイヤー構造を理解する

4. **transformによるアニメーション**
   - `transform`は位置を変えずに要素を変形・移動できる
   - `transition`と組み合わせてスムーズなアニメーション

---

## 🚀 使い方

1. `index.html`をブラウザで開く
2. ブラウザの開発者ツールで画面幅を768px以下に調整
3. ハンバーガーアイコン（3本線）をクリック
4. メニューがスライドして表示され、アイコンが×印に変わる
5. もう一度クリックすると、メニューが閉じる

---

## 📝 まとめ

このハンバーガーメニューの実装では、以下のCSSの概念が重要です：

- **position**: 要素の配置方法（`relative`、`absolute`、`fixed`）
- **z-index**: 要素の重なり順（数値が大きいほど前面）
- **`:checked`疑似クラス**: チェック状態の判定
- **兄弟セレクタ（`~`）**: 後続要素の選択
- **transform**: 要素の変形・移動
- **transition**: スムーズなアニメーション

特に`position`と`z-index`は、レイアウトとレイヤー構造を制御する上で非常に重要なプロパティです。このコードを通じて、実践的な使い方を学ぶことができます。

