# チャレンジ11

## 目的

- ポモドーロタイマーアプリの仕組みを理解する。
- テスト駆動開発でファンクションを実装する。

## チャレンジの取り組み方

1. スターターファイルのリポジトリを、フォークして自分のGitHubアカウントにリポジトリを作りましょう。
2. マイルストーンごとに要件に合うようにファイルを編集していきます。
3. 分からない部分があれば、テキストを復習して、再度チャレンジしてみましょう。
4. 再チャレンジしてしばらく考えても分からない場合はチャットでメンターに質問しましょう。
5. 完了したら、GitHubリポジトリをメンターにシェアしてください。(質問時に既にシェアしている場合は、レビューを直接依頼しましょう。)
6. メンターから課題レビューが届きます。
7. ビデオチャットの際は、分からない点を更に突っ込んで聞いたり、より良い書き方を聞いてみましょう。

## 概要

レッスン内で作成したポモドーロタイマーアプリにタイマーのポーズ機能と、ロングブレークを実装しましょう。

## 完成イメージ

![challenge-final](https://firebasestorage.googleapis.com/v0/b/codegrit-188601.appspot.com/o/material-images%2Fjs-unit02%2Flesson11%2Fchallenge-final.png?alt=media&token=ce501588-57d5-4a38-aa9d-5bbe04c54ee8)

## スターターファイル

- [codegrit-jp-students/js-unit02-ch11-starter](https://github.com/codegrit-jp-students/js-unit02-ch11-starter)

上記のURL先のリポジトリをフォークして、コードを書き進めて行きましょう。

## マイルストーン１

### 要件

タイマーアプリにロングブレークを追加します。

1. 作業完了回数をtempCyclesのような名前で保存します。
2. longBreakLengthという変数をconstructorに作り15をセットしましょう。
3. 作業完了回数が4回になったら、longBreakLengthが適用されるようにしてください。また、tempCyclesをリセットしましょう。
4. 上記を実装する前に、index.test.js内にテストを作成してください。または既存のテストをアップデートしてください。

## マイルストーン2

### 要件

タイマーアプリに一時停止機能を追加します。

1. 一時停止は、pausedAtのような変数を用意し、一時停止開始時と再開時の差分をendAtに追加することで実現できます。
2. 一時停止中にupateTimerが呼ばれないようにしましょう。
3. ロングブレークと同様に一時停止機能のテストを書きましょう。

## 評価

課題の後、以下の２つについてメンターにフィードバックをお願いします。

1. 要件のカバー度: 1.全く出来なかった 2.ほとんど出来なかった 3. 半分ほどは出来た 4.8割ほどは出来た 5. 全部出来た
2. 難易度: 1. とても難しかった 2. 難しかった 3. ちょうど良かった 4. 簡単だった 5. とても簡単だった
