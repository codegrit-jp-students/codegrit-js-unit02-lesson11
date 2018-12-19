## STEP6: 本日の作業回数を表示する

さて、タイマーが出来たら後はそれほど難しくありません。まずは、本日の作業回数を表示していきましょう。これは以下のステップで行うことが出来ます。

1. getHistoryファンクションでlocalStorageから現在のデータ一覧を取得する
2. getHistoryで取得したデータ一覧に、新しい作業データを追加してlocalStorageに保存する。
3. getHistoryで取得したデータ一覧から本日の開始から現在までの作業終了回数を数えて表示する。

### 6-1. getHistoryファンクションで作業データを取得する。

localStorageにデータを保存していく前に、まずデータを取得出来るようにgetHistoryというファンクションを実装していきましょう。

まずはテストを書きます。

```javascript
describe('App.getHistory', () => {
  test('終了した作業インターバルの終了時間一覧を取得する。', () => {
    const startOfToday = moment().startOf('day');
    const val1 = moment(startOfToday).subtract(5, 'days').add(30, 'minutes').valueOf();
    const val2 = moment(startOfToday).subtract(5, 'days').add(60, 'minutes').valueOf();
    const collection = [val1, val2];
    // intervalDataというkey名でデータを保存します。
    localStorage.setItem('intervalData', JSON.stringify(collection)); 
    expect(App.getHistory()).toContain(val1);
    localStorage.clear();
  });
});
```

ここで一点注意したいのは、localStorageにはArrayを直接保存出来ないので、ArrayをJSON形式の文字列に変換する必要があることです。そのためJSON.stringify(collection)としてデータを変換しています。

このテストが通るようにgetHistoryファンクションを実装していきましょう。

```javascript
class App {
  constructor() {
    ...
    this.getHistory = App.getHistory.bind(this);
    ... 
  }
  static getHistory() {
    const items = localStorage.getItem('intervalData');
    let collection = [];
    // localStorageにはArrayを直接保存出来ないので、JSON形式で保存しています。
    // 取り出す時は、JSON.parseでarrayに戻します。
    if (items) collection = JSON.parse(items);
    return collection;
  }
}
```

ご覧の通り、localStorageからJSON形式のデータを取得して、それをJSON.parseでArrayに変換しています。

### 6-2. saveIntervalDataファンクションを実装する。

localStorageへのデータの保存にはsaveIntervalDataというファンクションを使います。まずはこのsaveIntervalDataのテストを書いていきましょう。

```javascript
describe('saveIntervalData', () => {
  test('it should save array of items', () => {
    document.body.innerHTML = template;
    const app = new App();
    const startOfToday = moment().startOf('day');
    // 作業終了時の時間のテストデータを作成
    const item = moment(startOfToday).subtract(5, 'days').add(60, 'minutes');
    app.saveIntervalData(item);
    expect(JSON.parse(localStorage.getItem('intervalData'))).toContain(item.valueOf());
    localStorage.clear();
  });
});
```

このテストが通るようにファンクションを実装していきます。

```javascript
class App {
  constructor() {
    ...
    this.saveIntervalData = this.saveIntervalData.bind(this);
  }
  ...省略
  saveIntervalData(momentItem) {
    const collection = this.getHistory(); // 既に保存されているデータの取得。
    collection.push(momentItem.valueOf()); // 新しいデータを追加する。
    // JSON形式で再度保存する。
    localStorage.setItem('intervalData', JSON.stringify(collection));
  }
}
```

### 6-3: 作業時間から休憩時間への切替りじにデータを保存する

さてsaveIntervalDataが出来たので、updateTimer内で作業から休憩へ切り替わるときにデータが保存されるようにしましょう。

まずはupdateTimerのテストをアップデートします。

```javascript
describe('updateTimer', () => {
  test('作業時間が終わったら休憩時間に切り替える。', () => {
    ...省略
    expect(app.getHistory()).toEqual([endAt.add(100, 'millisecond').valueOf()]); // データの保存を確認
  });
  ...
});
```

次にupdateTimerファンクション上でsaveIntervalを呼び出すようにします。

```javascript
updateTimer(time = moment()) {
  const rest = this.endAt.diff(time);
  if (rest <= 0) {
    if (this.onWork) {
      this.saveIntervalData(time); // 作業時からの切り替り時のみsaveIntervalを呼び出す。
    }
    this.onWork = !this.onWork;
    this.startAt = time;
    this.endAt = this.onWork ? moment(time).add(this.workLength, 'minutes')
      : moment(time).add(this.breakLength, 'minutes');
  }
  this.displayTime(time);
}
```

これで無事に作業時からの切り替えの際にデータが保存されるようになりました。準備が整ったので本日の作業回数を表示していきましょう。

### 6-4: 本日の作業回数を表示する

本日の作業回数は次の流れで取得出来ます。

1. 一度getHistoryでデータを取得
2. Arrayのfilterファンクションを利用して、本日のデータのみを得る。
3. 得たデータの長さを得る。

作業回数表示にはdisplayCyclesTodayというファンクションを利用します。まずはテストを書いていきます。

```javascript
describe('displayCyclesToday', () => {
  test('当日の完了した作業サイクル数を表示する。', () => {
    document.body.innerHTML = template;
    const app = new App();
    const startOfToday = moment().startOf('day');
    const time = moment(startOfToday).add(5, 'hours'); // 現在時刻を、午前5時に設定
    const val1 = moment(startOfToday).add(30, 'minutes').valueOf(); // 午前0:30に作業完了
    const val2 = moment(startOfToday).add(60, 'minutes').valueOf(); // 午前1:00に作業完了
    const collection = [val1, val2];
    localStorage.setItem('intervalData', JSON.stringify(collection));
    app.displayCyclesToday(time);
    const countToday = document.getElementById('count-today');
    const percentToday = document.getElementById('percent-today');
    expect(countToday.innerHTML).toEqual('2回 / 4回');
    expect(percentToday.innerHTML).toEqual('目標を50％達成中です。');
    localStorage.clear();
  });
});
```

次に、このテストが通るようにファンクションを実装しましょう。

```javascript
class App {
  constructor() {
    ...
    this.displayCyclesToday = this.displayCyclesToday.bind(this);
    ...
    this.resetValues();
    this.getElements();
    this.toggleEvents();
    this.displayTime();
    this.displayCyclesToday();
  }
  ...
  getElements() {
    this.timeDisplay = document.getElementById('time-display');
    this.countOfTodayDisplay = document.getElementById('count-today'); // 回数の表示
    this.percentOfTodayDisplay = document.getElementById('percent-today'); // ％の表示
    this.startButton = document.getElementById('start-button');
    this.stopButton = document.getElementById('stop-button');
  }
  ...
  displayCyclesToday(time = moment()) {
    const collection = this.getHistory();
    const startOfToday = time.startOf('day');
    // 今日の始まりより後の時間のデータのみを取得してfilterItemsに格納する。
    const filterItems = collection.filter(item => (
      parseInt(item, 10) >= startOfToday.valueOf()
    ));
    const count = filterItems.length;
    const percent = count / 4 * 100;
    this.countOfTodayDisplay.innerHTML = `${count.toString()}回 / 4回`;
    this.percentOfTodayDisplay.innerHTML = `目標を${percent}％達成中です。`;
  }
}
```

上記で述べた流れの通り、全データ取得後、フィルターをかけて本日のデータのみを取得後、長さを取得しています。これでテストが問題なく通るはずです。