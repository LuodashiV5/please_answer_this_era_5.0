# Source: 70_00_ServiceData_New_Common_20200721.xls

## Sheet: 改訂履歴

| Unnamed: 1   | Unnamed: 2          | Unnamed: 3   | Unnamed: 4   |
|:-------------|:--------------------|:-------------|:-------------|
| ＜変更履歴＞       | nan                 | nan          | nan          |
| No.          | 変更日                 | 変更者          | 変更内容         |
| 1            | 2020-07-21 00:00:00 | (S設)櫻井       | 新規作成         |
| 2            | nan                 | nan          | nan          |
| 3            | nan                 | nan          | nan          |

## Sheet: 70_00_Setting

| Unnamed: 1   | Unnamed: 2                                                                                   | Unnamed: 3   | Unnamed: 4   | Unnamed: 5   | Unnamed: 6   | Unnamed: 7   | Unnamed: 8   | Unnamed: 9   | Unnamed: 10   | Unnamed: 11   | Unnamed: 12   | Unnamed: 13   |
|:-------------|:---------------------------------------------------------------------------------------------|:-------------|:-------------|:-------------|:-------------|:-------------|:-------------|:-------------|:--------------|:--------------|:--------------|:--------------|
| コマンド         | 70                                                                                           | nan          | nan          | nan          | nan          | nan          | nan          | nan          | nan           | nan           | nan           | nan           |
| サブコマンド       | 00                                                                                           | nan          | nan          | nan          | nan          | nan          | nan          | nan          | nan           | nan           | nan           | nan           |
| 機能           | サービスデータ自動送信制御　設定                                                                             | nan          | nan          | nan          | nan          | nan          | nan          | nan          | nan           | nan           | nan           | nan           |
| nan          | システムコントローラーが室外ユニットに対し、自動的に70コマンドを自動発行してもらうためのコマンド                                            | nan          | nan          | nan          | nan          | nan          | nan          | nan          | nan           | nan           | nan           | nan           |
| nan          | 本コマンドで"開始"を受信した室外ユニットは受信から60分間、定期的にサービスデータ(70コマンド)を自動送信する。                                   | nan          | nan          | nan          | nan          | nan          | nan          | nan          | nan           | nan           | nan           | nan           |
| nan          | 本コマンドを"開始"で受信しない場合、室外ユニットは自動送信しない。                                                           | nan          | nan          | nan          | nan          | nan          | nan          | nan          | nan           | nan           | nan           | nan           |
| nan          | 本コマンドの受信の有無にかかわらず、システムコントローラーから各サブコマンド毎にRequestを送信したらResponse応答する。(自動送信中でもRequestに対しては応答する。) | nan          | nan          | nan          | nan          | nan          | nan          | nan          | nan           | nan           | nan           | nan           |
| nan          | nan                                                                                          | b7           | b6           | b5           | b4           | b3           | b2           | b1           | b0            | 旧データ 70 DN    | 新データ 70 DN    | 備考            |
| nan          | CMD                                                                                          | コマンド         | nan          | nan          | nan          | nan          | nan          | nan          | nan           | -             | -             | nan           |
| nan          | nan                                                                                          | 70           | nan          | nan          | nan          | nan          | nan          | nan          | nan           | -             | -             | nan           |
| nan          | D0                                                                                           | サブコマンド       | nan          | nan          | nan          | nan          | nan          | nan          | nan           | -             | -             | nan           |
| nan          | nan                                                                                          | 00           | nan          | nan          | nan          | nan          | nan          | nan          | nan           | nan           | nan           | nan           |
| nan          | D1                                                                                           | 予備           | nan          | nan          | nan          | nan          | nan          | nan          | 開始/停止         | nan           | nan           | nan           |
| nan          | nan                                                                                          | 0固定          | nan          | nan          | nan          | nan          | nan          | nan          | 開始(1)/停止(0)   | nan           | nan           | nan           |
| nan          | D2                                                                                           | 予備           | nan          | nan          | nan          | nan          | nan          | nan          | nan           | nan           | nan           | nan           |
| nan          | nan                                                                                          | 0固定          | nan          | nan          | nan          | nan          | nan          | nan          | nan           | nan           | nan           | nan           |
| nan          | D3                                                                                           | 予備           | nan          | nan          | nan          | nan          | nan          | nan          | nan           | nan           | nan           | nan           |
| nan          | nan                                                                                          | 0固定          | nan          | nan          | nan          | nan          | nan          | nan          | nan           | nan           | nan           | nan           |
| nan          | D4                                                                                           | 予備           | nan          | nan          | nan          | nan          | nan          | nan          | nan           | nan           | nan           | nan           |
| nan          | nan                                                                                          | 0固定          | nan          | nan          | nan          | nan          | nan          | nan          | nan           | nan           | nan           | nan           |
| nan          | D5                                                                                           | 予備           | nan          | nan          | nan          | nan          | nan          | nan          | nan           | nan           | nan           | nan           |
| nan          | nan                                                                                          | 0固定          | nan          | nan          | nan          | nan          | nan          | nan          | nan           | nan           | nan           | nan           |