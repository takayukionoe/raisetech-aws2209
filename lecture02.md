# Raise Tech 第2回講座

## バージョン管理システム
- すでに動いているプログラムを修正すると動かなくなるリスクがあるため、バージョン管理によってリスクを防ぐ
- SVNは、中央集権型の単一のリポジトリを使用するバージョン管理システムで、今はメインでは使われない

## Git
- 主流となっているバージョン管理システム（分散型）
- Gitは、特定の変更点から分岐（ブランチ）したり、分岐した開発を統合（マージ）できる
- ブラントは別の生きたコードを生み出すことができる（漫画のパーマンのコピーロボットがイメージ近い）
※漫画読みます！

## Markdown
- 「#（見出し）」や「-（リスト）」を使用して、HTMLを使用しなくても記号で装飾できる

## 開発で役立つGithub TIPS
- Issu（イシュー）：改善や実装が必要な修正点を記録しておく場所
- Pull Request（プルリクエスト）：間違いを減らして、修正を行うために使う、承認制機能
- 「コメント」「承認」「File Changed」などGitHubの画面でよくみる項目は理解必須


## その他気づき
- 開発においては、コードの先祖返りにどう対処するのかと疑問に思っていたが、GitHubによるバージョン管理で防いでいることを学んだ
- 今回の課題の流れとしては下記
1. GitHubアカウントの作成
2. リポジトリの作成
3. ローカルレポジトリで、ブランチ：lecture02を作成
4. ローカルレポジトリで、講座の学びをlecture02.mdにマークダウン
5. ローカルレポジトリをpush