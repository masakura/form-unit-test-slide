1. API 呼び出し部分を interface 抽出
2. 画面はスタブも使えるように
3. 単体テストでは API を使わない
   - `0000000` - 短い住所
   - `0000001` - 一般的な住所
   - `0000002` - 長い住所
   - `9999991` - 例外パターン 1
   - ...
   - `9999999` - 例外パターン 9
