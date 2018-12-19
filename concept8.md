## STEP7: 7日分のデータを表示する

さて、上とほぼ同じ流れで過去７日分のデータも取得出来ます。この場合は、for文を利用して今日の開始時の7日前から今日の開始時までの回数を1日ずつ取得します。データの取得と表示にはdisplayHistoryというファンクションを利用します。

まずはテストを書きましょう。

```javascript
describe('displayHistory', () => {
  test('it should show the numbrer of finished work sessions up to 7 days ago', () => {
    document.body.innerHTML = template;
    const startOfToday = moment().startOf('day');
    const SevenDaysAgo = moment(startOfToday).subtract(7, 'days');
    const val1 = moment(SevenDaysAgo).add(50, 'minutes').valueOf();
    const val2 = moment(SevenDaysAgo).add(80, 'minutes').valueOf();
    const val3 = moment(SevenDaysAgo).add(2, 'days').add('3', 'hours').valueOf();
    const val4 = moment(SevenDaysAgo)
      .add(3, 'days')
      .add('2', 'hours')
      .valueOf();
    const collection = [val1, val2, val3, val4];
    localStorage.setItem('intervalData', JSON.stringify(collection));
    const app = new App();
    const sevenDaysAgoTh = document.getElementsByTagName('th')[0];
    const fiveDaysAgoTh = document.getElementsByTagName('th')[2];
    const sevenDaysAgoTd = document.getElementsByTagName('td')[0];
    const fiveDaysAgoTd = document.getElementsByTagName('td')[2];
    expect(sevenDaysAgoTh.innerHTML).toEqual(SevenDaysAgo.format('MM月DD日'));
    expect(fiveDaysAgoTh.innerHTML).toEqual(SevenDaysAgo.add(2, 'days').format('MM月DD日'));
    expect(sevenDaysAgoTd.innerHTML).toEqual('2回<br>達成率50%');
    expect(fiveDaysAgoTd.innerHTML).toEqual('1回<br>達成率25%');
    expect(app.getHistory().length).toEqual(4);
  });
});
```

データの数が多いですが、先ほどの`displayCyclesToday`とほぼ同じ内容のテストです。このテストが通るようにファンクションを定義します。

```javascript
class App {
  constructor() {
    ...
    this.displayHistory = this.displayHistory.bind(this);
    ...
    this.displayCyclesToday();
    this.displayHistory();
  }
  ...
  getElements() {
    this.timeDisplay = document.getElementById('time-display');
    this.countOfTodayDisplay = document.getElementById('count-today');
    this.percentOfTodayDisplay = document.getElementById('percent-today');
    this.historyDisplay = document.getElementById('history');
    this.startButton = document.getElementById('start-button');
    this.stopButton = document.getElementById('stop-button');
  }
  ...
  displayHistory(time = moment()) {
    const collection = this.getHistory();
    const startOfToday = time.startOf('day');
    const startOfTodayClone = moment(startOfToday);
    const sevenDaysAgo = startOfTodayClone.subtract(7, 'days');
    const valOfSevenDaysAgo = sevenDaysAgo.valueOf();
    const tableEl = document.createElement('table');
    tableEl.classList.add('table', 'table-bordered');
    const trElDate = document.createElement('tr');
    const trElCount = document.createElement('tr');
    for (let i = 0; i <= 6; i += 1) {
      const filterItems = collection.filter((item) => {
        const timestampOfItem = parseInt(item, 10);
        return timestampOfItem >= valOfSevenDaysAgo + i * DAY
          && timestampOfItem < valOfSevenDaysAgo + (i + 1) * DAY;
      });
      const count = filterItems.length;
      const thElDate = document.createElement('th');
      const tdElCount = document.createElement('td');
      const sevenDaysAgoCloen = moment(sevenDaysAgo);
      thElDate.innerHTML = sevenDaysAgoCloen.add(i, 'day').format('MM月DD日');
      tdElCount.innerHTML = `${count}回<br>達成率${count / 4 * 100}%`;
      trElDate.appendChild(thElDate);
      trElCount.appendChild(tdElCount);
    }
    tableEl.appendChild(trElDate);
    tableEl.appendChild(trElCount);
    this.historyDisplay.appendChild(tableEl);
  }
}
```

上記のファンクションでは次のことをしています。

1. table要素を作成する。
2. 日付を表示するためのtr要素と、データを表示tr要素を作成
3. for分を使って、7日分の日付とデータを作成したtrの子要素に追加する。
4. 最後にtable要素に2つのtr要素を加えて、これをhistoryDisplay要素に追加する。

実際の画面を確認してみましょう。完成イメージと同じものが出来ているはずです。