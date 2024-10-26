## 数据库设计

### 前端

继承自 `Dexie` ，管理本地的 `IndexedDB` 数据库，主要用于对应到前端的 `Message` 和 `Conversation` 两个 `interface`。

#### messages

| 属性 | 类型 | 含义 |
| --- | --- | --- |
| id | int | 消息主键 |
| conversation | int | 会话 id |
| sender | str | 发送者 id |
| timestamp | datetime | 消息发送时间 |

---

#### conversations

| 属性 | 类型 | 含义 |
| --- | --- | --- |
| id | int | 主键 |
| type | str | 群聊类型，群聊和私聊 |

---

#### Message

| 属性 | 类型 | 含义 |
| --- | --- | --- |
| id | int | 消息主键 |
| conversation | int | 会话 id |
| sender | str | 发送者 |   
| senderId | str | 发送者 ID |
| content | str | 消息内容 |
| timestamp | datetime | 消息发送时间 |
| sendTime | datetime | 服务器端消息发送时间 |
| avatar | str | 头像 |
| readList | list[str] | 已读用户列表 |
| replyId | int | 回复消息的ID |
| replyCount | int | 回复消息的计数 |
| deleteList | list[str] | 删除用户列表 |

---

#### Conversation

| 属性 | 类型 | 含义 |
| --- | --- | --- |
| id | int | 会话 id |
| type | str | 群聊类型，群聊和私聊 |
| members | list[User] | 会话成员 |
| unreadCount | int | 未读计数 |
| avatarUrl | str | 头像 |
| otherUserId | str | 私聊对方的 ID |
| # 以下为群聊的字段 |  |  |
| groupName | str | 群名 |
| groupNotificationList | list[Notification] | 群公告列表 |
| host | User | 群主 |
| hostId | str | 群主 ID |
| adminList | list[User] | 管理员列表 |
| adminIdList | list[str] | 管理员 ID 列表 |

---

#### User

| 属性 | 类型 | 含义 |
| --- | --- | --- |
| id | int | 主键 |
| userId | str | 用户 id |
| userName | str | 用户名 |
| avatarUrl | str | 头像 |
| isDeleted | bool | 是否已经注销 |

---

### 后端

选用 `SQLite` 作为后端数据库。

#### User

表示用户的账户资料

| 属性 | 类型 | 含义 |
| --- | --- | --- |
| id | int | 主键 |
| userId | str | 账号，唯一，用于标识用户身份 |
| password | str | 密码，加密储存在数据库中 |
| email | str | 邮箱 |
| phoneNumber | str | 电话号码 |
| avatarUrl | str | 表示头像的字符串 |
| isDeleted | bool | 账户状态, True表示已注销，注销的用户保留已有数据，但不再产生新的相关数据 |

---

#### Friendship

表示好友关系，一对好友对应两条记录

| 属性 | 类型 | 含义 |
| --- | --- | --- |
| id | int |  |
| userId | str | 用户账号 |
| friendId | str | 好友账号 |
| tag | str | 对好友的分组（备注） |
| status | bool | False表示已删除，不能在打tag和发消息 |

---

#### FriendshipRequest

| 属性 | 类型 | 含义 |
| --- | --- | --- |
| id | int |  |
| senderId | str | 请求发送者的账号 |
| receiverId | str | 接受请求者的账号 |
| sendTime | 时间戳 | 发送时间 |
| message | str | 请求中打招呼的内容 |
| status | int | 请求状态，0为未处理，1为已接受，2为已过期 |

#### Conversation

| 属性 | 类型 | 含义 |
| --- | --- | --- |
| id | int |  |
| type | str | 群聊类型，群聊和私聊 |
| members | list[User] | 会话成员 |
| status | bool | False表示群聊已失效，主要用于私聊和Friendship的状态保持一致 |
| # 以下为群聊的字段 |  |  |
| host | User | 群主 |
| admins | list[User] | 管理员 |
| avatarUrl | str | 群聊头像，私聊显示对方头像 |
| groupName | str | 群名 |
| groupNotificationList | list[Notification] | 群公告列表 |

---

#### Notification

| 属性 | 类型 | 含义 |
| --- | --- | --- |
| id | int | 主键 |
| userId | str | 发布者账号 |
| userName | str | 发布者呢称 |
| avatarUrl | str | 发布者头像 |
| content | str | 群通知的内容 |
| timestamp | datetime | 发送时间 |

---

#### Message

| 属性 | 类型 | 含义 |
| --- | --- | --- |
| id | int |  |
| conversation | Conversation | 所属会话 |
| sender | User | 发送者 |
| receiver | list[User] | 接收者 |
| sendTime | datetime | 消息发送时间 |
| updateTime | datetime | 消息更新时间 |
| content | str | 消息内容 |
| replyTo | Message | 引用消息的id |
| readUsers | list[User] | 已读名单 |
| deleteUsers | list[User] | 删除这条消息的名单 |
| replyCount | int | 被回复次数 |

---

#### Invitation

| 属性 | 类型 | 含义 |
| --- | --- | --- |
| id | int |  |
| sender | User | 发送者 |
| receiver | User | 接收者，为群聊的群主和管理员，而不是被邀请者 |
| timestamp | datetime | 邀请更新时间 |