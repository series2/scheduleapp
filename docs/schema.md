# DB schema
**スケジュール管理ツール**のDBです。

### administrators
万物へのadminがつく

| Field            | Type     | Null | Key | Default | Extra | 説明など |
| ---------------- | -------- | ---- | --- | ------- | ----- | -------- |
| admin_user_id     | varchar(32) | NO   | PRI | _NULL_  |


### group_administrators

groupidに対応したaedmin

| Field            | Type     | Null | Key | Default | Extra | 説明など |
| ---------------- | -------- | ---- | --- | ------- | ----- | -------- |
| admin_user_id     | varchar(32) | NO   | PRI | _NULL_  |
| group_id          | int(11) | NO   | MUL | _NULL_  || 集合の形態（弦、Vn等) |


### shedules
shedulesレコードはgroup_idに対応してadminをつける。

| Field            | Type       | Null | Key | Default           | Extra          | 説明など                                                                                                       |
| ---------------- | ---------- | ---- | --- | ----------------- | -------------- | -------------------------------------------------------------------------------------------------------------- |
| id          | int(11) | NO   | PRI | _NULL_  | auto_increment |
| date       | Date  | NO   |     |  _NULL_ |  | 予定日 |
| start_time       | Time  | YES   |     |  _NULL_ |  | 開始時刻 |
| end_time       | Time  | YES   |     |  _NULL_ |       | 終了時刻 |
| group_id          | int(11) | NO   | MUL | _NULL_  || 集合の形態（弦、Vn等) |
| concert_id      | int(11) | YES   | MUL | _NULL_  |           | なんの本番のためか(演奏喫茶、定期演奏会等) |
| place      | text | YES   | MUL | _NULL_  |           | 場所 |   
| remarks      | text | YES   | MUL | _NULL_  |           | 備考 |           
| type          | int(11) | NO   | MUL | _NULL_  || 特別分けるべき行事(通常練習は0 他にリハ、本番等、打ち上げ) |
| created_at       | timestamp  | NO   |     | CURRENT_TIMESTAMP |       | 予定が作成された日時 |
| updated_at       | timestamp  | NO   |     | CURRENT_TIMESTAMP |       |予定が変更された日時(これはshedule_detailの変更に対応させるか迷う) |
| deleted_at       | timestamp  | NO   |     | CURRENT_TIMESTAMP |       | 予定が日時 |

### shedules_details

sheduleの予定をさらに分割すべき時にshedule.idを外部キーとして対応させる
曲の練習の時を主に想定し、曲名、楽章、時間の入力用とするが、他にも一応、演奏喫茶の内装で買い出し、切り出し、組み立て等と分けることもできそう。

| Field            | Type       | Null | Key | Default           | Extra          | 説明など                                                                                                       |
| ---------------- | ---------- | ---- | --- | ----------------- | -------------- | -------------------------------------------------------------------------------------------------------------- |
| id          | int(11) | NO   | PRI | _NULL_  | auto_increment |
|shedule_id|int(11)|NO|MUL|_NULL_||予定id **parents:shedules.id**|
| start_time       | Time  | YES   |     |  _NULL_ |  | 開始時刻 |
| end_time       | Time  | YES   |     |  _NULL_ |       | 終了時刻 |
| music_id        | int(11)      | YES  |     | _NULL_||     曲名id (practice_musictableの互換性注意）|
| title        | text      | NO  |     | _NULL_||     曲名以外の時 |
| remarks       | text      | YES  |     | _NULL_ |           |  楽章等|
| created_at       | timestamp  | NO   |     | CURRENT_TIMESTAMP |       | 予定が作成された日時 |
| updated_at       | timestamp  | NO   |     | CURRENT_TIMESTAMP |       |予定が変更された日時 |
| deleted_at       | timestamp  | NO   |     | CURRENT_TIMESTAMP |       | 予定が削除された日時 |


### groups


| Field            | Type       | Null | Key | Default           | Extra          | 説明など                                                                                                       |
| ---------------- | ---------- | ---- | --- | ----------------- | -------------- | -------------------------------------------------------------------------------------------------------------- |
| id          | int(11) | NO   | PRI | _NULL_  |auto_increment|groupid  |
| name          | text | NO   | MUL | _NULL_  || グループ名 |
| create_user_id      | varchar(32) | NO   | MUL | _NULL_  |           | 作成者のuser_id |
| created_at          | timestamp | YES   |  | CURRENT_TIMESTAMP | | |


### users

| Field            | Type       | Null | Key | Default           | Extra          | 説明など                                                                                                       |
| ---------------- | ---------- | ---- | --- | ----------------- | -------------- | -------------------------------------------------------------------------------------------------------------- |
| id          | int(11) | NO   | PRI | _NULL_  |auto_increment|  |
| user_id          | varchar(32)| NO   | MULL | _NULL_  || userのuuid |
| name | text | NO   |     |_NULL_       |       | userの名前   |
| generation          | int(11) | NO   | PRI | _NULL_  ||userの代（例：19）|
| state          | boolean | NO   | PRI |true  ||userの状態(活動中、休団中)  |

### instruments

| Field            | Type       | Null | Key | Default           | Extra          | 説明など                                                                                                       |
| ---------------- | ---------- | ---- | --- | ----------------- | -------------- | -------------------------------------------------------------------------------------------------------------- |
| instrument_id          | int(11) | NO   | MUL | _NULL_  || 楽器のid **parents:instrument.id**|
| name | text | NO   |     |_NULL_       |       | 楽器の名前   |

### practice_musics
同じ曲で複数本番があるとき、レコードを追加してください。
musicテーブルを作ったのは同一曲名の異なる作曲者に対応するためそうすれば一意に曲が決まる(編曲、出版社は？)

| Field            | Type       | Null | Key | Default           | Extra          | 説明など                                                                                                       |
| ---------------- | ---------- | ---- | --- | ----------------- | -------------- | -------------------------------------------------------------------------------------------------------------- |
| id          | int(11) | NO   | PRI | _NULL_  |auto_increment|  |
| music_id          | int(11) | NO   | PRI | _NULL_  |auto_increment|  |
| name | text | NO   |     |_NULL_       |       | 曲の名前   |
| concert_id          | int(11) | NO   | PRI | _NULL_  || 控えている本番 |

### musics
曲を一意に決めるためのテーブル
ゆくゆくはmusics_detailsテーブル作って作曲者とかいろいろ入れる？！
(本当はこれはオケに関係がなく作れる表なので外部apiをたたくべきだし、知見アプリのdbで持つべきだが。)

| Field            | Type       | Null | Key | Default           | Extra          | 説明など                                                                                                       |
| ---------------- | ---------- | ---- | --- | ----------------- | -------------- | -------------------------------------------------------------------------------------------------------------- |
| id          | int(11) | NO   | PRI | _NULL_  |auto_increment|  |
| name | text | NO   |     |_NULL_       |       | 曲の名前   |
| composer_id          | varchar(32) | NO   | PRI | _NULL_  || 作曲者id |
| arranger_id          | varchar(32) | NO   | PRI | _NULL_  || 編曲者id |

###　composers
作曲、編曲者(本当はこれはオケに関係がなく作れる表なので外部apiをたたくべきだし、知見アプリのdbで持つべきだが。)

| Field            | Type       | Null | Key | Default           | Extra          | 説明など                                                                                                       |
| ---------------- | ---------- | ---- | --- | ----------------- | -------------- | -------------------------------------------------------------------------------------------------------------- |
| id          | varchar(32)  | NO   | PRI | _NULL_  |uuid|  |
| name | text | NO   |     |_NULL_       |       | 名前   |







### user_groups

| Field            | Type       | Null | Key | Default           | Extra          | 説明など                                                                                                       |
| ---------------- | ---------- | ---- | --- | ----------------- | -------------- | -------------------------------------------------------------------------------------------------------------- |
| user_id          | ivarchar(32)| NO   | MULL | _NULL_  || userのuuid**parents:users.user_id** |
| group_id          | int(11) | NO   | MUL | _NULL_  || グループのid **parents:groups.id**|


### user_instruments

| Field            | Type       | Null | Key | Default           | Extra          | 説明など                                                                                                       |
| ---------------- | ---------- | ---- | --- | ----------------- | -------------- | -------------------------------------------------------------------------------------------------------------- |
| user_id          | ivarchar(32)| NO   | MULL | _NULL_  || userのuuid**parents:users.user_id** |
| instrument_id          | int(11) | NO   | MUL | _NULL_  || 楽器のid **parents:instrument.id**|



### notifications
アプリで上のほうに表示させたいお知らせ
updated_atからある程度日がたったら表示はしないようにする。変更は作成者のみ可
adminのみつくれる
| Field            | Type       | Null | Key | Default           | Extra          | 説明など                                                                                                       |
| ---------------- | ---------- | ---- | --- | ----------------- | -------------- | -------------------------------------------------------------------------------------------------------------- |
| id          | int(11) | NO   | PRI | _NULL_  |auto_increment|  |
| user_id          | ivarchar(32)| NO   | MULL | _NULL_  || userのuuid**parents:users.user_id** |
| name | text | NO   |     |_NULL_       |       | userの名前   |
| created_at       | timestamp  | NO   |     | CURRENT_TIMESTAMP |       | 予定が作成された日時 |
| updated_at       | timestamp  | NO   |     | CURRENT_TIMESTAMP |       |予定が変更された日時 |
| deleted_at       | timestamp  | NO   |     | CURRENT_TIMESTAMP |       | 予定が日時 |

## 以下は将来的にアプリとして分離する可能性がある。
お気持ち
(やっぱない。appとして各々が独立していないのはwebapiの利用と思えば良いのかもしれないがなんかいやだ。情報の共有とかなら良いと思う。例えばコンサート情報を別アプリから読み取って、こちらのconcertsテーブルに代入したり逆に自分のかかわった本番を他のアプリにデータして読みだす。同期していないならば手打ちすればいいだけなのでアプリとしては独立しているとみなせる。例えばツイッターとかの同期も手で打てるし、アマゾンのリンクも行こうと思えばいける。パッケージも外のものを結局取り込んでるし)

### concerts
これは自分のたどってきた軌跡となるので自分の好きな曲みたいなのを作った時に自分の訪れたコンサート情報と共有しても良いかもしれない。曲情報はpractice_musicを参照


| Field            | Type       | Null | Key | Default           | Extra          | 説明など                                                                                                       |
| ---------------- | ---------- | ---- | --- | ----------------- | -------------- | -------------------------------------------------------------------------------------------------------------- |
| id          | int(11) | NO   | PRI | _NULL_  |auto_increment| concertid  |
| group_id          | int(11) | NO   | MUL | _NULL_  || group_id  |
| concert_name_id          | int(11) | NO   |  | _NULL_  || コンサート名id(第n回を想定) |
| count          | int(11) | NO   |  | _NULL_  || 第n回 |
| date       | Date  | YES   |     |  _NULL_ |  | 日付 |
| place       | text  | YES   |     |  _NULL_ |  | 場所 |
| open_time       | Time  | YES   |     |  _NULL_ |  | 開場時刻 |
| start_time       | Time  | YES   |     |  _NULL_ |  | 開演時刻 |
| end_time       | Time  | YES   |     |  _NULL_ |       | 終演時刻 |
| created_at       | timestamp  | NO   |     | CURRENT_TIMESTAMP |       | 作成された日時 |
| updated_at       | timestamp  | NO   |     | CURRENT_TIMESTAMP |       |変更された日時 |
| deleted_at       | timestamp  | NO   |     | CURRENT_TIMESTAMP |       | 削除日時 |

### concert_names
groupは例外的処置がめんどいのでこっちにはあえてつけなかった。
| Field            | Type       | Null | Key | Default           | Extra          | 説明など                                                                                                       |
| ---------------- | ---------- | ---- | --- | ----------------- | -------------- | -------------------------------------------------------------------------------------------------------------- |
| id          | int(11) | NO   | PRI | _NULL_  |auto_increment| コンサート名id(第n回を想定)  |
| concert_name   | text | NO   |  | _NULL_  || コンサート名id(第n回を想定) |