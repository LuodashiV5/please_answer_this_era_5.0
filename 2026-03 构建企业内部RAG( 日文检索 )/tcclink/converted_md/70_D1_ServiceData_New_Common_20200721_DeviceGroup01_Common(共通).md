# Source: 70_D1_ServiceData_New_Common_20200721.xls

## Sheet: 変更履歴

| Unnamed: 1   | Unnamed: 2          | Unnamed: 3   | Unnamed: 4   |
|:-------------|:--------------------|:-------------|:-------------|
| ＜変更履歴＞       | nan                 | nan          | nan          |
| No.          | 変更日                 | 変更者          | 変更内容         |
| 1            | 2020-07-21 00:00:00 | (S設)櫻井       | 新規作成         |
| 2            | nan                 | nan          | nan          |
| 3            | nan                 | nan          | nan          |

## Sheet: 70_D1_Request

| Unnamed: 1   | Unnamed: 2               | Unnamed: 3   | Unnamed: 4   | Unnamed: 5   | Unnamed: 6   | Unnamed: 7   | Unnamed: 8   | Unnamed: 9   | Unnamed: 10   | Unnamed: 11   | Unnamed: 12   | Unnamed: 13   |
|:-------------|:-------------------------|:-------------|:-------------|:-------------|:-------------|:-------------|:-------------|:-------------|:--------------|:--------------|:--------------|:--------------|
| コマンド         | 70                       | nan          | nan          | nan          | nan          | nan          | nan          | nan          | nan           | nan           | nan           | nan           |
| サブコマンド       | D1                       | nan          | nan          | nan          | nan          | nan          | nan          | nan          | nan           | nan           | nan           | nan           |
| 機能           | サービスデータ　システムコントローラーの基本情報 | nan          | nan          | nan          | nan          | nan          | nan          | nan          | nan           | nan           | nan           | nan           |
| nan          | 1分周期で収集することを前提           | nan          | nan          | nan          | nan          | nan          | nan          | nan          | nan           | nan           | nan           | nan           |
| nan          | nan                      | b7           | b6           | b5           | b4           | b3           | b2           | b1           | b0            | 旧データ 70 DN    | 新データ 70 DN    | 備考            |
| nan          | CMD                      | コマンド         | nan          | nan          | nan          | nan          | nan          | nan          | nan           | -             | -             | nan           |
| nan          | nan                      | 70           | nan          | nan          | nan          | nan          | nan          | nan          | nan           | -             | -             | nan           |
| nan          | D0                       | サブコマンド       | nan          | nan          | nan          | nan          | nan          | nan          | nan           | -             | -             | nan           |
| nan          | nan                      | D1           | nan          | nan          | nan          | nan          | nan          | nan          | nan           | -             | -             | nan           |

## Sheet: 70_D1_Response

| Unnamed: 1   | Unnamed: 2                       | Unnamed: 3   | Unnamed: 4   | Unnamed: 5              | Unnamed: 6   | Unnamed: 7                  | Unnamed: 8   | Unnamed: 9   | Unnamed: 10   | Unnamed: 11   | Unnamed: 12   | Unnamed: 13   |
|:-------------|:---------------------------------|:-------------|:-------------|:------------------------|:-------------|:----------------------------|:-------------|:-------------|:--------------|:--------------|:--------------|:--------------|
| コマンド         | 70                               | nan          | nan          | nan                     | nan          | nan                         | nan          | nan          | nan           | nan           | nan           | nan           |
| サブコマンド       | D1                               | nan          | nan          | nan                     | nan          | nan                         | nan          | nan          | nan           | nan           | nan           | nan           |
| 機能           | サービスデータ　システムコントローラーの基本情報         | nan          | nan          | nan                     | nan          | nan                         | nan          | nan          | nan           | nan           | nan           | nan           |
| nan          | 1分周期、または自身の現在ステータスが変化した際に、自発的に送信 | nan          | nan          | nan                     | nan          | nan                         | nan          | nan          | nan           | nan           | nan           | nan           |
| nan          | nan                              | b7           | b6           | b5                      | b4           | b3                          | b2           | b1           | b0            | 旧データ 70 DN    | 新データ 70 DN    | 備考            |
| nan          | CMD                              | コマンド         | nan          | nan                     | nan          | nan                         | nan          | nan          | nan           | -             | -             | nan           |
| nan          | nan                              | 70           | nan          | nan                     | nan          | nan                         | nan          | nan          | nan           | -             | -             | nan           |
| nan          | D0                               | サブコマンド       | nan          | nan                     | nan          | nan                         | nan          | nan          | nan           | -             | -             | nan           |
| nan          | nan                              | D1           | nan          | nan                     | nan          | nan                         | nan          | nan          | nan           | nan           | nan           | nan           |
| nan          | D1                               | nan          | nan          | プライマリー/セカンダリー           | nan          | 現在ステータス                     | nan          | nan          | nan           | nan           | nan           | nan           |
| nan          | nan                              | nan          | nan          | 0=未確定 1=プライマリー 2=セカンダリー | nan          | 1=定常状態 2=ユニット認識中 3=認識完了待機中  | nan          | nan          | nan           | nan           | nan           | nan           |
|              |                                  |              |              |                         |              | 3=システム停止中 4= テストモード         |              |              |               |               |               |               |
| nan          | D2                               | 予備           | nan          | nan                     | nan          | nan                         | nan          | nan          | nan           | nan           | nan           | nan           |
| nan          | D3                               | 予備           | nan          | nan                     | nan          | nan                         | nan          | nan          | nan           | nan           | nan           | nan           |
| nan          | D4                               | 予備           | nan          | nan                     | nan          | nan                         | nan          | nan          | nan           | nan           | nan           | nan           |
| nan          | D5                               | 予備           | nan          | nan                     | nan          | nan                         | nan          | nan          | nan           | nan           | nan           | nan           |