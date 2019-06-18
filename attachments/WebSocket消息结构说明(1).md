# WebSocket消息结构说明

服务推送的web socket消息结构固定字段如下:

```json
{
  "id":"<本次推送的消息ID>",
  "action":"<业务动作，详细说明见下表>",
  "type":"<与业务动作相匹配的消息类型，action不同，type的内容也不一样>",
  "from":"<消息推送方Id 0为系统>",
  "way":"消息发送方向，0.b2c 1.c2b 2.s2b 3.s2c",
  "content":"消息内容",
  "time":"<本次推送的时间>"
}
```

`action`及`type`对应说明表(该部分内容会随业务不定时更新)

| `action`  | 说明 | `type`                                                       |
| --------- | ---- | ------------------------------------------------------------ |
| `chat`    | 聊天 | `txt`文本消息 `image`图片 `voice`语音 `video`视频            |
| `notify`  | 通知 | `msg_arrived`消息到达 `msg_read`消息已读 `refresh_contact`更新联系人信息 |
| `request` | 请求 | `phone`电话号码 `wechat`微信号 `information`个人资料         |

