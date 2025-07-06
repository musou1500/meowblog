# CSSデザインガイドライン

このプロジェクトのための実用的なCSSルール。機能することに重点を置き、過度な設計を避ける。

## 核となる原則

1. **既存のものを使用** - 絶対に必要でない限り、新しいCSS変数を追加しない
2. **BEMに従う** - `block__element--modifier`の命名を一貫して使用
3. **デフォルトから始める** - まず`--spacing-md`、`--color-text`、`--font-size-base`を使用
4. **動作するパターンをコピー** - 実証済みの組み合わせについてはdesign-system.htmlを参照

## クイック判断ガイド

### どのCSS変数を使用すべきか？

**スペーシングの場合:**
- ページセクション間？ → `--spacing-xxl` (48px)
- コンポーネント間？ → `--spacing-xl` (32px)  
- コンポーネント内？ → `--spacing-md` (16px) ← **ここから始める**
- ボタンパディング？ → `--spacing-sm` (8px)
- アイコンからテキスト？ → `--spacing-xs` (4px)

**カラーの場合:**
- メインテキスト？ → `--color-text` ← **ここから始める**
- セカンダリテキスト？ → `--color-text-light`
- インタラクティブ要素？ → `--color-primary`
- ハイライト？ → `--color-accent`

**タイポグラフィの場合:**
- ページタイトル？ → `--font-size-xxl` (32px)
- セクション見出し？ → `--font-size-xl` (24px)
- カードタイトル？ → `--font-size-large` (18px)
- 本文テキスト？ → `--font-size-base` (16px) ← **ここから始める**
- キャプション？ → `--font-size-small` (14px)

**シャドウの場合:**
- カード？ → `--shadow-md` ← **ここから始める**
- ドロップダウン？ → `--shadow-lg`  
- モーダル？ → `--shadow-xl`
- ホバー効果？ → `--shadow-sm`

## BEMルール（シンプル版）

### クラス命名: `block__element--modifier`

**良い例:**
- `.article-card` (ブロック)
- `.article-card__title` (エレメント)  
- `.article-card--featured` (モディファイア)

**悪い例:**
- `.article-card__header__title` (深すぎる - 最大1レベル)
- `.articleCard` (camelCaseではなくkebab-caseを使用)

### コンポーネントを分割するタイミング

**一緒にしておく場合:**
- すべてが1つの明確な目的を果たしている
- 1つの文で説明できる
- 合計10未満の要素

**分割する場合:**
- コンポーネントが複数のことを行っている
- 要素が他の場所で再利用される可能性がある
- 中身を覚えるのが困難

### ファイル構成

```
css/
├── base.css (variables only)
├── blocks/
│   ├── article-card.css
│   ├── nav-tabs.css
│   └── button.css
└── pages/
    ├── home.css (page-specific overrides)
    └── article.css
```

## よくあるパターン（これらをコピー）

### 標準ボタン
```css
.button {
  padding: var(--spacing-sm) var(--spacing-md);
  background: var(--color-primary);
  color: white;
  border-radius: var(--border-radius);
  border: none;
  cursor: pointer;
}

.button:hover {
  background: var(--color-primary-hover);
}
```

### カードコンポーネント  
```css
.card {
  padding: var(--spacing-md);
  background: var(--color-background);
  border-radius: var(--border-radius);
  box-shadow: var(--shadow-md);
}

.card__title {
  font-size: var(--font-size-large);
  color: var(--color-text);
  margin-bottom: var(--spacing-sm);
}
```

### テキスト階層
```css
h1 { font-size: var(--font-size-xxl); }  /* Page titles */
h2 { font-size: var(--font-size-xl); }   /* Section titles */
h3 { font-size: var(--font-size-large); } /* Card titles */
p { font-size: var(--font-size-base); }   /* Body text */
small { font-size: var(--font-size-small); } /* Captions */
```

## 影響範囲ガイドライン

### 問題: 不明確な影響
CSSのスコープが不明確だと、開発者は変更によって何が影響を受けるか予測できません。これはレイアウトの破綻やスタイルの修正への恐怖を引き起こします。

### 核ルール: スコープを明確にする
すべてのCSSルールは、それが**正確に**どの要素に影響を与えるかを明確にすべきです。

### タグセレクタ: 通常は避ける
```css
/* 悪い - すべての場所のh1要素に影響 */
h1 {
  font-size: 32px;
  color: red;
}

/* 良い - 記事タイトルのみに影響 */
.article__title {
  font-size: var(--font-size-xxl);
  color: var(--color-text);
}
```

**例外:** タグセレクタはプリミティブ/リセットスタイルでは問題ありません:
```css
/* OK - 基本リセット/プリミティブスタイル */
* { box-sizing: border-box; }
body { font-family: var(--font-family-primary); }
img { max-width: 100%; }
```

### クラスセレクタ: 推奨
```css
/* 良い - 明確なスコープ */
.hero-illustration__person::before {
  content: '👩‍💻';
  font-size: 80px;
}

/* 良い - モディファイアが特定の状態に影響 */
.nav-tabs__item--active {
  background: var(--color-primary);
  color: white;
}
```

### 子孫セレクタ: 注意して使用
```css
/* リスキー - ネストされたコンポーネントに影響する可能性 */
.article p {
  margin-bottom: var(--spacing-md);
}

/* より良い - ターゲットについて明示的 */
.article__content p {
  margin-bottom: var(--spacing-md);
}

/* 最適 - BEMクラスを使用 */
.article__paragraph {
  margin-bottom: var(--spacing-md);
}
```

### ページオーバーライド: 最小限で明示的
```css
/* 悪い - スコープが幅広すぎる */
.p-about h1 { font-size: 40px; }

/* 良い - 具体的で包含された */
.p-about__hero-title {
  font-size: var(--font-size-xxl);
}

/* OK - 明確にスコープされたオーバーライド */
.p-design-system .article-card {
  margin-bottom: var(--spacing-xl);
}
```

### 判断フレームワーク
CSSを書く前に問いかける:
1. **これは正確に何に影響するか？** 正確に答えられない場合は、より具体的なセレクタを使用。
2. **HTML構造が変わったらこれは壊れるか？** はいの場合、代わりにBEMクラスを使用。
3. **これは他のコンポーネントを誤ってスタイルする可能性があるか？** はいの場合、特定性を上げる。
4. **他の開発者がスコープを理解できるか？** いいえの場合、コメントを追加するか、より明確なセレクタを使用。

## やってはいけないこと

### 新しい変数を作成しない
```css
/* 悪い - 新しい変数を作成 */
:root {
  --my-special-spacing: 13px;
  --custom-blue: #1234ff;
}

/* 良い - 既存のものを使用 */
.my-component {
  padding: var(--spacing-md);
  color: var(--color-primary);
}
```

### BEMで深くなりすぎない
```html
<!-- 悪い - ネストが深すぎる -->
<div class="card__header__title__icon--small">

<!-- 良い - 抽出または平均化 -->
<div class="card__title-icon card__title-icon--small">
<!-- or -->
<div class="title-icon title-icon--small">
```

### ページですべてをオーバーライドしない
```css
/* 悪い - ページスタイルがやりすぎ */
.p-home .card { /* completely different card */ }
.p-home .button { /* completely different button */ }

/* 良い - レイアウトのみのページスタイル */
.p-home { /* page layout */ }
.p-home__hero { /* page-specific sections */ }
```

### 不明確なスコープでCSSを書かない
```css
/* 悪い - 何に影響するか不明 */
h1 { font-size: 40px; color: blue; }
p { margin: 20px; }
.content div { padding: 10px; }

/* 良い - 明示的なスコープ */
.page-title { font-size: var(--font-size-xxl); }
.article__paragraph { margin-bottom: var(--spacing-md); }
.sidebar__widget { padding: var(--spacing-sm); }
```

### 過度に複雑なセレクタを使用しない
```css
/* 悪い - 脆弱で不明確 */
.main .content .sidebar ul li a:not(.active) {
  color: var(--color-text-light);
}

/* 良い - シンプルで明確 */
.sidebar__link {
  color: var(--color-text-light);
}

.sidebar__link--active {
  color: var(--color-primary);
}
```

## 開発コマンド

```bash
# 開発サーバー
npm run dev

# 本番用ビルド  
npm run build
```

## 使用可能CSS変数（合計30個）

### カラー (14個)
- `--color-primary` #4a9eff
- `--color-primary-hover` #3b87d9
- `--color-accent` #ffb347
- `--color-text` #333
- `--color-text-light` #666
- `--color-text-lighter` #999
- `--color-background` #ffffff
- `--color-background-light` #f5f5f5
- `--color-background-lighter` #fafafa
- `--color-border` #e0e0e0
- `--color-border-light` #f0f0f0

### タイポグラフィ (6個)
- `--font-family-primary` (system fonts)
- `--font-size-base` 16px
- `--font-size-small` 14px
- `--font-size-large` 18px
- `--font-size-xl` 24px
- `--font-size-xxl` 32px

### スペーシング (6個)
- `--spacing-xs` 4px
- `--spacing-sm` 8px  
- `--spacing-md` 16px ← **Default**
- `--spacing-lg` 24px
- `--spacing-xl` 32px
- `--spacing-xxl` 48px

### シャドウ (4個)
- `--shadow-sm` (hover effects)
- `--shadow-md` (cards) ← **Default**
- `--shadow-lg` (dropdowns)
- `--shadow-xl` (modals)

### その他
- `--border-radius` 8px
- `--border-radius-sm` 4px
- `--border-radius-lg` 12px
- `--transition-fast` 150ms ease-in-out

以上です！シンプルに保ちましょう。