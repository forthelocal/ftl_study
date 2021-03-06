# 遅延設計
- ある機能の実装が別クラスに切り出すことが自然に見えても、現在の状況でその決断が難しいのであれば、決断は先延ばしにするべき
    - その時点で要件が不明瞭で、確実にそのクラスが必要であり、再利用の必要性があると判断できない
    - 他のメンバーが切り出したクラスに依存関係を作ってしまうなどの懸念があるときなど
- クラススコープ内での無名クラスや構造体への切り出しにとどめておき、コンテキストを絞って使用する
    - どうなるかわからないケース


## 設計しすぎてはいけない理由（設計のコスパ）
### 「未来の変更に備える」とは先回りをして実装をすることではない

- 実際に未来が来るまで（変更の要件が確定するまで）
 「変更しやすい」状態を保ちつづけること
- 未来に必要になる機能を予測して今のうちに先回りして実装することではない
  - 間違った未来を想定してしまうと、実際の未来が来たときに大きな修正コストを支払うことになる

---

### どの程度まで設計を行うか？

仕事としてコードを書いているので、都度そのコスパ（費用対効果）を意識する必要がある。

### 設計のコスパは以下の２つの要素に左右される

1. プログラマのスキル（正しさと速さ）
2. 結果が出るまでの時間

「設計に半月かかり利益が出るようになるまで1年かかる」のなら、
おそらくその設計には投下したコストに見合うほどの価値はない。

**短期間で行われた正しい設計が、期間を空けずに成果を出すことが求められる。**

---
## 「未来の変更に備える」とは何を行えばよいのか？

### 「コードを変更しやすく保つ」ことと「高い確度の予測に対応」すること

- 予想した「内容」と「確率」を基に、設計を作り込むリスクとコストのバランスを取る
	- 変更の確率が高く、その変更への対応（修正）に時間がかかるなら、その変更予測に対応しないことはリスクになる
	- 変更の確率が低く、その変更への対応（修正）には時間がかからないなら、その変更予測に対応してもコストを回収できない

---

## 設計を行う上で前提にすべきこと

- 未来は誰にもわからない
- 決定している未来もある（要件追加や変更の計画など）
	- 予定どおり実行されるかは未来になるまでわからない
- 未来の変更に備える必要がある
	- 常にコードを変更しやすく保つ必要がある
- 未来は現在の情報から予想することができる
 	- 専門家の意見、統計データ、経験などから自分で予想を立てることができる

---
## 未来を予想しすぎた設計の罠
### 失敗パターン (1/2)

- 変更後の（まだ誰も正解を知らない）具体的な仕様を想定した設計をしてしまう
	- 使われないクラスや機能の実装に費やした時間が無駄に
	- 使われないクラスや機能によってアプリケーションの不必要な複雑化を招き、開発効率の低下やバグの発生を誘発する
	- 実際の要件が想定と違った場合、要件変更への対応に加え
	「作り込んだ箇所の削除と修正」というコストが更に必要に
- 不必要に抽象化した設計をしてしまう
	- 現在求められる要件を満たしているが不必要に複雑で
	 可読性が悪い（理解に時間やスキルが必要）
	- 抽象化された設計は、それ自体の実装に時間がかかる
	- 要件の確定まで設計の決断を「先送り」にすべき

---

## 未来を予想しすぎた設計の罠
### 失敗パターン (2/2)

- 変更の確率が低く、影響範囲も小さい変更予測に対応してしまう
	 - 掛けたコストの回収見込みが立たないので無視すべき

---

## 未来を予想しすぎた設計の罠
### 失敗パターンの心理

- よかれと思って設計しすぎてしまう
	- 設計を作り込むメリットにばかり目が行ってしまう
	- 設計をしないリスク（技術的負債の可能性）を過剰に見積もっている
- 情熱のあまり、不適切な箇所に原則を適用し、存在しないところにパターンを見出してしまう
	- 設計の「原則」と「パターン」の適用は、そこにメリットがあるときだけ行う
    - 逆に行わないことで、コードが単純でわかりやすくなることもある。アプリケーションの規模次第で基準も変わる。

---
### まとめ

- 設計を作り込むべきタイミングを見極める
	- 現在のアプリケーションに関する知識から、未来の「変更内容」とその変更が行われる「確率」を予測する
	- 可能な限り判断は遅らせる
		- 未来予測は常に間違えている可能性がある
		- 情報が出てくるほどより正しい決断ができる
		- 何もしないで放置するリスクと変更するコストに大差ないとき、変更の決断は遅らせたほうがよい
- 設計を速く、正しく行う
	- 原則を理解し、パターンを憶える
