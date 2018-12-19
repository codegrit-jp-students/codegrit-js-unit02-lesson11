## STEP1: Appクラスを作成しよう

### 1-1: ロード時にAppクラスのインスタンスを作成する。

まずは、srcフォルダ内のindex.jsファイルを開いて、Appクラスを作成します。またindex.html内で、bundle.jsが読み込まれたときにAppクラスのインスタンスが作成されるようにします。

index.js

```javascript
import './assets/scss/styles.scss';

class App {
  constructor() {}
}

// ロード時にAppクラスをインスタンス化する。
window.addEventListener('load', () => new App()); 
```

### 1-2: 初期パラメータを設定する。

このインスタンス化された際の初期のパラメーターを設定していきます。まずは、タイマーの状態を表すために以下の項目の初期値を設定しましょう。

- 作業時間と休憩時間の長さ(workLengthとbreackLength)
- タイマーが動いているかどうかの状態(isTimerStopped)
- 作業中か休憩中かの状態(onWork)

```javascript
...省略
class App {
  constructor() {
    this.workLength = 25; // 25分間
    this.breakLength = 5; // 5分間
    this.isTimerStopped = true; // 最初はタイマーは止まっている
    this.onWork = true; // 最初は作業からタイマーは始まる
  }
}
...省略
```