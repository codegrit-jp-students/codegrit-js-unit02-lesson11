## STEP5: stopTimerを実装する。

### 5-1: stopTimerのテストとファンクションを書く

さて、次にstopTimerの実装ですが、これは単にスタート前の状態にタイマーをリセットするだけですので簡単です。まずはテストを書きましょう。

```javascript
describe('stopTimer', () => {
  test('it should reset the timer', () => {
    document.body.innerHTML = template;
    const app = new App();
    const now = moment();
    const startOfToday = now.startOf('day');
    app.startButton.disabled = true;
    app.stopButton.disabled = false;
    app.isTimerStopped = false;
    app.startAt = startOfToday;
    app.endAt = moment(now).add(20, 'minutes');
    app.stopTimer();
    const timeDisplay = document.getElementById('time-display');
    expect(timeDisplay.innerHTML).toEqual('25:00');
    expect(app.onWork).toBeTruthy();
    expect(app.isTimerStopped).toBeTruthy();
    expect(app.startButton.disabled).not.toBeTruthy();
  });
});
```

次に、stopTimerファンクションを実装していきます。

```javascript
class App {
  constructor() {
    ...省略
    this.stopTimer = this.stopTimer.bind(this);
    ...省略
  }
  toggleEvents() {
    this.startButton.addEventListener('click', this.startTimer);
    this.stopButton.addEventListener('click', this.stopTimer); // ストップボタンに対するクリックイベントでstopTimerファンクションを呼び出す。
  }
  stopTimer(e = null) {
    if (e) e.preventDefault();
    this.startAt = null;
    this.endAt = null;
    this.onWork = true;
    this.isTimerStopped = true;
    this.startButton.disabled = false;
    this.stopButton.disabled = true;
    window.clearInterval(this.timerUpdater);
    this.timerUpdater = null;
    this.displayTime();
  }
}
```

無事にテストが通ったでしょうか? 実際の画面でテストすろとタイマーの開始と終了がどちらも出来るようになったことが分かるはずです。

### 5-2: resetValuesファンクションを作成する。

さて、ここで分かる通りstopTimerはclearIntervalを除けば、初期状態に値をリセットしているだけなので、これをresetValuesというファンクションにまとめるとより、値をリセットしていることがクリアになるはずです。これを実装していきましょう。

```javascript
...
class App {
  constructor() {
    this.startTimer = this.startTimer.bind(this);
    this.updateTimer = this.updateTimer.bind(this);
    this.stopTimer = this.stopTimer.bind(this);
    this.resetValues = this.resetValues.bind(this);
    this.displayTime = this.displayTime.bind(this);

    this.resetValues();
    this.getElements();
    this.toggleEvents();
    this.displayTime();
  }
  resetValues() {
    this.workLength = 25;
    this.breakLength = 5;
    this.startAt = null;
    this.endAt = null;
    this.isTimerStopped = true;
    this.onWork = true;
  }
  ...
  stopTimer(e = null) {
    if (e) e.preventDefault();
    this.resetValues();
    this.startButton.disabled = false;
    this.stopButton.disabled = true;
    window.clearInterval(this.timerUpdater);
    this.timerUpdater = null;
    this.displayTime();
  }
  ...
```

これで全体が少しスッキリしました。