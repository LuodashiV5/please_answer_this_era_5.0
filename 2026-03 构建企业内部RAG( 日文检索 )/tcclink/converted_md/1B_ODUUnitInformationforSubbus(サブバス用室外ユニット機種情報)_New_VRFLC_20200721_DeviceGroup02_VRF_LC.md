# Source: 1B_ODUUnitInformationforSubbus(サブバス用室外ユニット機種情報)_New_VRFLC_20200721.xlsx

## Sheet: 改訂履歴

| Unnamed: 1   | Unnamed: 2   | Unnamed: 3   | Unnamed: 4   |
|:-------------|:-------------|:-------------|:-------------|
| ＜変更履歴＞       | nan          | nan          | nan          |
| No.          | 変更日          | 変更者          | 変更内容         |
| 1            | nan          | nan          | nan          |
| 2            | nan          | nan          | nan          |
| 3            | nan          | nan          | nan          |
| 4            | nan          | nan          | nan          |

## Sheet: 1B_Request

| Unnamed: 1   | Unnamed: 2                                                              | Unnamed: 3   | Unnamed: 4   | Unnamed: 5   | Unnamed: 6   | Unnamed: 7   | Unnamed: 8   | Unnamed: 9   | Unnamed: 10   | Unnamed: 11   | Unnamed: 12   | Unnamed: 13   |
|:-------------|:------------------------------------------------------------------------|:-------------|:-------------|:-------------|:-------------|:-------------|:-------------|:-------------|:--------------|:--------------|:--------------|:--------------|
| コマンドコード      | 1B                                                                      | nan          | nan          | nan          | nan          | nan          | nan          | nan          | nan           | nan           | nan           | nan           |
| 機能           | サブバス用室外ユニット機種情報                                                         | nan          | nan          | nan          | nan          | nan          | nan          | nan          | nan           | nan           | nan           | nan           |
| nan          | サブバス用室外ユニット機種情報を取得する。                                                   | nan          | nan          | nan          | nan          | nan          | nan          | nan          | nan           | nan           | nan           | nan           |
| nan          | ※サブバスは電源重畳による通信を行うが、データ内で"0"が連続するとコンデンサ容量が下降し通信成り立たない場合がある。             | nan          | nan          | nan          | nan          | nan          | nan          | nan          | nan           | nan           | nan           | nan           |
| nan          | 室外ユニット機種情報(16コマンド)が使用できない場合があるため、サブバス通信で必要なデータのみを抽出した本コマンドを用意し本問題を回避する。 | nan          | nan          | nan          | nan          | nan          | nan          | nan          | nan           | nan           | nan           | nan           |
| nan          | nan                                                                     | b7           | b6           | b5           | b4           | b3           | b2           | b1           | b0            | 旧データ E8 DN    | 新データ E8 DN    | 備考            |
| nan          | -                                                                       | コマンド         | nan          | nan          | nan          | nan          | nan          | nan          | nan           | -             | -             | nan           |
| nan          | nan                                                                     | 1B           | nan          | nan          | nan          | nan          | nan          | nan          | nan           | -             | -             | nan           |

## Sheet: 1B_Response

| Unnamed: 1   | Unnamed: 2                                                              | Unnamed: 3                                 | Unnamed: 4   | Unnamed: 5   | Unnamed: 6   | Unnamed: 7   | Unnamed: 8   | Unnamed: 9   | Unnamed: 10   | Unnamed: 11   | Unnamed: 12   | Unnamed: 13   |
|:-------------|:------------------------------------------------------------------------|:-------------------------------------------|:-------------|:-------------|:-------------|:-------------|:-------------|:-------------|:--------------|:--------------|:--------------|:--------------|
| コマンドコード      | 1B                                                                      | nan                                        | nan          | nan          | nan          | nan          | nan          | nan          | nan           | nan           | nan           | nan           |
| 機能           | サブバス用室外ユニット機種情報                                                         | nan                                        | nan          | nan          | nan          | nan          | nan          | nan          | nan           | nan           | nan           | nan           |
| nan          | サブバス用室外ユニット機種情報を取得する。                                                   | nan                                        | nan          | nan          | nan          | nan          | nan          | nan          | nan           | nan           | nan           | nan           |
| nan          | ※サブバスは電源重畳による通信を行うが、データ内で"0"が連続するとコンデンサ容量が下降し通信成り立たない場合がある。             | nan                                        | nan          | nan          | nan          | nan          | nan          | nan          | nan           | nan           | nan           | nan           |
| nan          | 室外ユニット機種情報(16コマンド)が使用できない場合があるため、サブバス通信で必要なデータのみを抽出した本コマンドを用意し本問題を回避する。 | nan                                        | nan          | nan          | nan          | nan          | nan          | nan          | nan           | nan           | nan           | nan           |
| nan          | nan                                                                     | b7                                         | b6           | b5           | b4           | b3           | b2           | b1           | b0            | 旧データ E8 DN    | 新データ E8 DN    | 備考            |
| nan          | -                                                                       | コマンド                                       | nan          | nan          | nan          | nan          | nan          | nan          | nan           | -             | -             | nan           |
| nan          | nan                                                                     | 1B                                         | nan          | nan          | nan          | nan          | nan          | nan          | nan           | -             | -             | nan           |
| nan          | D0                                                                      | センター/ターミナル情報                               | nan          | nan          | nan          | nan          | nan          | nan          | nan           | -             | 新規            | nan           |
| nan          | nan                                                                     | 本機設定                                       | nan          | nan          | ターミナル４       | ターミナル３       | ターミナル２       | ターミナル１       | センター※必ず1      | nan           | nan           | nan           |
|              |                                                                         | ｾﾝﾀｰ=1/ﾀｰﾐﾅﾙ1=2/ﾀｰﾐﾅﾙ2=3/ﾀｰﾐﾅﾙ3=4/ﾀｰﾐﾅﾙ4=5 |              |              | 接続=1 未接続=0   | 接続=1 未接続=0   | 接続=1 未接続=0   | 接続=1 未接続=0   | 接続=1 未接続=0    |               |               |               |