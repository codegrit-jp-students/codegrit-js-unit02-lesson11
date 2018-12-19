## STEP8: 今日の開始時より7日以上前のデータを削除する。

最後に古いデータをアプリ立ち上げ時に削除するremoveOldHistoryファンクションを実装していきましょう。まずはテストを書きます。

```javascript
describe('removeOldHistory', () => {
  test('古いデータを削除する。', () => {
    const startOfToday = moment().startOf('day');
    const val1 = moment(startOfToday).subtract(8, 'days').add(30, 'minutes').valueOf(); // 8日前のデータを作成
    const val2 = moment(startOfToday).subtract(5, 'days').add(60, 'minutes').valueOf(); // 5日前のデータを作成
    const collection = [val1, val2];
    document.body.innerHTML = template;
    const app = new App();
    localStorage.setItem('intervalData', JSON.stringify(collection));
    app.removeOldHistory();
    expect(App.getHistory()).not.toContain(val1); // 8日前のデータは削除される。
    expect(App.getHistory()).toContain(val2); // 5日前のデータは削除されず残る。
    localStorage.clear();
  });
});
```

次にテストが通るようにファンクションを実装します。

```javascript
class App {
  constructor() {
    ...省略
    this.removeOldHistory();
  }
  ...
  removeOldHistory() {
    const now = moment();
    const startOfToday = now.startOf('day'); // 今日の開始時
    const sevenDaysAgo = startOfToday.subtract(7, 'days'); // 今日の開始時から7日前
    const collection = this.getHistory(); 
    // フィルター関数で今日の開始時から今日の開始時から7日前までの間のデータのみを取得する
    const newCollection = collection.filter((item) => {
      const timestampOfItem = parseInt(item, 10);
      return timestampOfItem >= sevenDaysAgo;
    });
    // 取得したデータを再度保存する。
    localStorage.setItem('intervalData', JSON.stringify(newCollection));
  }
  ...
}
```

これでほぼ全て完成しましたが、もう一つやり残していることがあります。現状だと、アプリ立ち上げ時には本日の回数が更新されるのですが、作業完了時には更新されません。そのためupdateTimer関数をアップデートする必要があります。

```javascript
updateTimer(time = moment()) {
  const rest = this.endAt.diff(time);
  if (rest <= 0) {
    if (this.onWork) {
      this.saveIntervalData(time);
      this.displayCyclesToday(); // 追加
      this.displayHistory(); // 追加
    }
    this.onWork = !this.onWork;
    this.startAt = time;
    this.endAt = this.onWork ? moment(time).add(this.workLength, 'minutes')
      : moment(time).add(this.breakLength, 'minutes');
  }
  this.displayTime(time);
}
```

長くなりましたが、これで、全ての仕様が満たされたアプリが完成しました。

## まとめ

いかがだったでしょうか、テストをしながらアプリを完成させるまでの流れを一通り掴んで頂けたのではないかと思います。また、JavaScriptのみでHTMlを更新するアプリを作るのが大変という気もしたのではないでしょうか? 実際の業務では今回のように、全てのコードを一から実装してアプリを作るということはほとんどなく、React.jsやVue.js、Angular.jsのようなライブラリを利用するのが一般的です。こうしたライブラリを利用することでより複雑なアプリもうまくコードを管理しながら作れるようになります。一からアプリを作った経験は、フレームワークやライブラリを利用する際にも生きてくるはずです。

## 完成版のコード

- [codegrit-jp-students/js-unit02-lesson11-final](https://github.com/codegrit-jp-students/js-unit02-lesson11-final)

## 更に学ぼう

### 記事で学ぶ

- [moment.js公式ドキュメント](https://momentjs.com/docs/)
