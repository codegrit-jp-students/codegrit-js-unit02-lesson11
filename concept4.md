## STEP3: startTimerファンクションを実装する

さて、止まった状態の表示が出来たのでいよいよタイマーを動かしていきます。カウントダウンの表示の流れは以下のようになります。

1. ユーザーが「スタート」ボタンを押すとstartTimerファンクションが呼び出される。
2. 開始時の時間がstartAt、25分後の時間がendAtとしてインスタンスにセットされる。
3. setIntervalでupdateTimerファンクションを定期的に呼び出し、終了時間(endAt)とチェック時の時刻の差を残り時間として、表示する。

### 3-1. 変数とファンクションの定義

この3つを満たすように、まずはstartAtとendAtの2つのインスタンス変数、そしてstartTimerとupdateTImerの2つのファンクションを定義します。また、それと同時に後ほど使うスタートボタンとストップボタンの2つの要素も取得しておきます。

```javascript
class App {
  constructor() {
    ...省略
    this.startAt = null; // カウントダウン開始時の時間
    this.endAt = null; // カウントダウン終了時の時間

    this.timeDisplay = document.getElementById('time-display');
    this.startButton = document.getElementById('start-button'); // スタートボタン
    this.stopButton = document.getElementById('stop-button'); // ストップボタン

    this.displayTime();
  }

  startTimer() {}
  updateTimer() {}
  ...省略
}
```

### 3-2. スタートボタンとstartTimerファンクションを連動させる

さて、次にスタートボタンを押したときにstartTimerが呼び出されるようにaddEventListnerを設定します。

ここで、constructor内で設定してもいいのですがスタートボタン以外にも、ストップボタンにも設定する必要が後々出てきますので、toggleEventsというファンクションを作成して、ここでまとめて設定するようにしましょう。

```javascript
class App {
  constructor() {
    ...省略

    this.startTimer = this.startTimer.bind(this); 

    this.timeDisplay = document.getElementById('time-display');
    this.startButton = document.getElementById('start-button'); // スタートボタン
    this.stopButton = document.getElementById('stop-button'); // ストップボタン

    this.toggleEvents();
    this.displayTime();
  }
  
  toggleEvents() {
    this.startButton.addEventListener('click', this.startTimer);
  }

  startTimer(e = null) {
    if (e) e.preventDefault();
  }
  updateTimer() {}
```

併せて、現在constructor内に書いている、要素の取得も別のgetElementsというファンクションにまとめてしまいましょう。

```javascript
class App {
  constructor() {
    ...省略

    // 以下の3つを別ファンクションに移す。
    // this.timeDisplay = document.getElementById('time-display');
    // this.startButton = document.getElementById('start-button'); // スタートボタン
    // this.stopButton = document.getElementById('stop-button'); // ストップボタン

    this.getElements();
    this.toggleEvents();
    this.displayTime();
  }

  getElements() {
    this.timeDisplay = document.getElementById('time-display');
    this.startButton = document.getElementById('start-button');
    this.stopButton = document.getElementById('stop-button');
  }

  ...省略
  
}
```

これでconstructor内がすっきりとしました。

### 3-3. startTimerファンクションでのボタンの属性変更をテストする。

さて、では次にstartTimerファンクションのテストを書いていきましょう。startTimerを押したときには、startAtとendAtが設定される他に、スタートボタンにdisable属性が追加されてクリックできないようになります。逆にストップボタンからはdisable属性が外されてクリック可能となります。また、isTimerStoppedも、trueからfalseへと変更されなければなりません。この3つをテストに盛り込みます。

```javascript
describe('startTimer', () => {
  test('スタートボタンにdisable属性を追加', () => {
    document.body.innerHTML = template;
    const app = new App();
    app.startTimer();
    const startButton = document.getElementById('start-button');
    const stopButton = document.getElementById('stop-button');
    expect(startButton.disabled).toEqual(true);
    expect(stopButton.disabled).toEqual(false);
    expect(app.isTimerStopped).toEqual(false);
  });
});
```

さて、テスト結果を再度見るとテスト失敗しているはずです。テストが通るようにstartTimerファンクションに以下を追加します。

```javascript
startTimer(e = null) {
  if (e) e.preventDefault();
  this.startButton.disabled = true;
  this.stopButton.disabled = false;
  this.isTimerStopped = false;
}
```

再度テスト結果を確認してみましょう。無事テストが通っているはずです。

### 3-4. startTimerファンクションでのstartAtとendAtの割当をテストする

さて、次にテストすべきはstartAtとendAtに適切な時間が入っているかです。JavaScritp組み込みのクラスを使って時間を扱ってもいいのですが、今回はJavaScriptプログラマ間でよく使われている時間操作のためのライブラリmoment.jsを使っていきます。

以下は、今回のレッスンで使用するmoment.jsの関数のリストです。

```javascript
let momentItem = moment(); // 現在の時刻でmomentインスタンスを作成する。 
momentItem.format('MM月DD日') // 様々なフォーマットで時間を表示する。
momentItem.add(25, 'minutes'); // momentインスタンスの時間に25分を加える。
momentItem.subract(25, 'minutes'); // momentインスタンスの時間を25分戻す。
let cloneItem = moment(momentItem); // momentインスタンスを複製する。
momentItem.valueOf(); 
/* 時間をタイムスタンプ形式で取得する。2つの時間を比較するときに便利。 
例: 
momentItem.format(); => "2018-07-02T15:44:40+08:00"
momentItem.valueOf(); => 1530517480830
*/
momentItem.diff(cloneItem) // 2つのmomentインスタンスのタイムスタンプの差を返す。ここでは0が返る。
momentItem.startOf('day'); // 1日の始まりの時間を取得する。
```

さてstartTimerファンクションでは現在時刻をstartAtとして保存するのですが、刻々と変わっていく現在時刻をテストするのは大変です。そこでstartTimerファンクションの引数にtimeを設定します。またtimeに何も与えられない場合は現在時刻を初期値として入れます。

```javascript
startTimer(e = null, time = moment()) {
  if (e) e.preventDefault();
  this.startButton.disabled = true;
  this.stopButton.disabled = false;
  this.isTimerStopped = false;
}
```

ではテストを書いていきましょう。

```javascript
describe('startTimer', () => {
  ...省略
  test('startAtとendAtを適切に設定する。', () => {
    document.body.innerHTML = template;
    const app = new App();
    const now = moment(); // 現在時刻のmomentインスタンスを作成
    app.startTimer(null, now); 
    expect(app.startAt.valueOf()).toEqual(now.valueOf()); // startAtに現在時刻が与えられているかをテスト
    expect(app.endAt.valueOf()).toEqual(now.add(25, 'minutes').valueOf());
    // endAtがstartAtから25分後になっているかテスト
  });
});
```

これが通るようにファンクションを実装します。

```javascript
startTimer(e = null, time = moment()) {
  if (e) e.preventDefault();
  this.startButton.disabled = true;
  this.stopButton.disabled = false;
  this.isTimerStopped = false;
  this.startAt = time;
  const startAtClone = moment(this.startAt);
  this.endAt = startAtClone.add(this.workLength, 'minutes');
  this.timerUpdater = window.setInterval(this.updateTimer, 500);
  // タイムラグがあるので、0.5秒ごとにアップデートする。
}
```

上記のコードでは、startAtとendAtを設定した後に、timerUpdaterという変数に`window.setInterval(this.updateTimer, 500);`を格納して、500ミリ秒ごとにupdateTimerファンクションが呼び出されるようにしています。