# DB schema
**スケジュール管理ツール**のDBです。
## 今後考えること　型の定義の妥当性、正規化、null、default,外部キー

### apps
| Field            | Type       | Null | Key | Default           | Extra          | 説明など                                                                                                       |
| ---------------- | ---------- | ---- | --- | ----------------- | -------------- | -------------------------------------------------------------------------------------------------------------- |
| password          | int(11) | NO   | PRI | _NULL_  | auto_increment |1レコードのみ|

### app_admin_users
アプリ全体のadmin
| Field            | Type       | Null | Key | Default           | Extra          | 説明など                                                                                                       |
| ---------------- | ---------- | ---- | --- | ----------------- | -------------- | -------------------------------------------------------------------------------------------------------------- |
| user_id          | varchar(32)| PRI   | MULL | _NULL_  || userのuuid |




### shedules
shedulesレコードはsmall_group_idに対応してadminをつける。

| Field            | Type       | Null | Key | Default           | Extra          | 説明など                                                                                                       |
| ---------------- | ---------- | ---- | --- | ----------------- | -------------- | -------------------------------------------------------------------------------------------------------------- |
| id          | int(11) | NO   | PRI | _NULL_  | auto_increment |
| date       | Date  | NO   |     |  _NULL_ |  | 予定日 |
| start_time       | Time  | YES   |     |  _NULL_ |  | 開始時刻 |
| end_time       | Time  | YES   |     |  _NULL_ |       | 終了時刻 |
| small_group_id          | int(11) | NO   | MUL | _NULL_  || 集合の形態（弦、Vn等) |
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

### large_groups


| Field            | Type       | Null | Key | Default           | Extra          | 説明など                                                                                                       |
| ---------------- | ---------- | ---- | --- | ----------------- | -------------- | -------------------------------------------------------------------------------------------------------------- |
| id          | int(11) | NO   | PRI | _NULL_  |uuid|large_groupid |
| name          | text | NO   | MUL | _NULL_  || グループ名 |
| password          | text | NO   | MUL | _NULL_  || グループパスワード(グループ名に対しつけるが、同一グループがある場合、説明文を加えて選択させる) |
| create_user_id      | varchar(32) | NO   | MUL | _NULL_  |           | 作成者のuser_id |
| description          | text | NO   | UNI　| _NULL_  || 説明(これは最終識別手段としてuniqである必要がある。) |
| image          | binary | YES   |  |  | |プロフィール画的な |
| created_at          | timestamp | YES   |  | CURRENT_TIMESTAMP | | |
| deleted_at          | timestamp | YES   |  | CURRENT_TIMESTAMP | | |

### small_groups(弦、Vn、演奏喫茶等)


| Field            | Type       | Null | Key | Default           | Extra          | 説明など                                                                                                       |
| ---------------- | ---------- | ---- | --- | ----------------- | -------------- | -------------------------------------------------------------------------------------------------------------- |
| id          | int(11) | NO   | PRI | _NULL_  |auto_increment|small_groupid(どの団体においても0は必ず全員とする。)  |
| name          | text | NO   | MUL | _NULL_  || グループ名 |
| large_group_id          | int(11) | NO   | PRI | _NULL_  |uuid|large_groupid |
| create_large_group_user_id      | varchar(32) | NO   | MUL | _NULL_  |           | 作成者のlarge_groupe_user_id |
| created_at          | timestamp | YES   |  | CURRENT_TIMESTAMP | | |
| deleted_at          | timestamp | YES   |  | CURRENT_TIMESTAMP | | |


### users
アプリ全体で共通するuser一人一人に対し一つのuser情報

| Field            | Type       | Null | Key | Default           | Extra          | 説明など                                                                                                       |
| ---------------- | ---------- | ---- | --- | ----------------- | -------------- | -------------------------------------------------------------------------------------------------------------- |
| id          | int(11) | NO   | PRI | _NULL_  |auto_increment|  |
| user_id          | varchar(32)| PRI   | MULL | _NULL_  || userのuuid |
| name | text | NO   |     |_NULL_       |       | userの名前   |
| password          | text| NO   | MULL | _NULL_  || userのpassword |

### large_group_users
団体の中でのuserの位置づけ
| Field            | Type       | Null | Key | Default           | Extra          | 説明など                                                                                                       |
| ---------------- | ---------- | ---- | --- | ----------------- | -------------- | -------------------------------------------------------------------------------------------------------------- |
| id          | varchar(32) | NO   | PRI | _NULL_  |uuid| large_group_user_id |
| user_id          | varchar(32)| PRI   | MULL | _NULL_  || userのuuid |
| large_grope_user_name | text | NO   |     |_NULL_       |       | userの表示名   |
| generation          | int(11) | NO   | PRI | _NULL_  ||userの代（例：19）|
| state          | tinyint(4) | NO   |  |0  ||userの状態(活動中、休団中、ゲスト)  |
| large_groupe_admin  | boolean | PRI   | MULL | false|| large_groupに対するadmin |




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
| music_id          | int(11) | NO   | PRI | _NULL_  ||  |
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




### user_small_groups

| Field            | Type       | Null | Key | Default           | Extra          | 説明など                                                                                                       |
| ---------------- | ---------- | ---- | --- | ----------------- | -------------- | -------------------------------------------------------------------------------------------------------------- |
| large_group_user_id          | ivarchar(32)| NO   | MULL | _NULL_  || userのuuid**parents:** |
| small_group_id          | int(11) | NO   | MUL | _NULL_  || グループのid **parents:small_groups.id**|
| small_group_admin  | boolean | PRI   | MULL | false|| small_groupに対するadmin |


### user_instruments

| Field            | Type       | Null | Key | Default           | Extra          | 説明など                                                                                                       |
| ---------------- | ---------- | ---- | --- | ----------------- | -------------- | -------------------------------------------------------------------------------------------------------------- |
| large_group_user_id          | ivarchar(32)| NO   | MULL | _NULL_  || userのuuid**parents:large_group_users.id** |
| instrument_id          | int(11) | NO   | MUL | _NULL_  || 楽器のid **parents:instrument.id**|



### large_groupe_notifications
アプリで上のほうに表示させたいお知らせ
updated_atからある程度日がたったら表示はしないようにする。変更は作成者のみ可
adminのみつくれる
| Field            | Type       | Null | Key | Default           | Extra          | 説明など                                                                                                       |
| ---------------- | ---------- | ---- | --- | ----------------- | -------------- | -------------------------------------------------------------------------------------------------------------- |
| id          | int(11) | NO   | PRI | _NULL_  |auto_increment|  |
| large_groupe_user_id          | varchar(32)| NO   | MULL | _NULL_  || userのuuid**parents:users.user_id** |
| name | text | NO   |     | _NULL_    |       | userの名前   |
| created_at       | timestamp  | NO   |     | CURRENT_TIMESTAMP |       | 予定が作成された日時 |
| updated_at       | timestamp  | NO   |     | CURRENT_TIMESTAMP |       |予定が変更された日時 |
| deleted_at       | timestamp  | NO   |     | CURRENT_TIMESTAMP |       | 予定が日時 |

## all_notifications
アプリ運営側からのお知らせ
| Field            | Type       | Null | Key | Default           | Extra          | 説明など                                                                                                       |
| ---------------- | ---------- | ---- | --- | ----------------- | -------------- | -------------------------------------------------------------------------------------------------------------- |
| id          | int(11) | NO   | PRI | _NULL_  |auto_increment|  |
| large_groupe_user_id          | varchar(32)| NO   | MULL | _NULL_  || userのuuid**parents:users.user_id** |
| name | text | NO   |     | _NULL_    |       | userの名前   |
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
| small_group_id          | int(11) | NO   | MUL | _NULL_  || small_group_id  |
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
small_groupは例外的処置がめんどいのでこっちにはあえてつけなかった。
| Field            | Type       | Null | Key | Default           | Extra          | 説明など                                                                                                       |
| ---------------- | ---------- | ---- | --- | ----------------- | -------------- | -------------------------------------------------------------------------------------------------------------- |
| id          | int(11) | NO   | PRI | _NULL_  |auto_increment| コンサート名id(第n回を想定)  |
| large_group_id          | int(11) | NO   | PRI | _NULL_  |uuid|large_groupid |
| concert_name   | text | NO   |  | _NULL_  || コンサート名id(第n回を想定) |