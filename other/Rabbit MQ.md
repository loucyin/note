# Rabbit MQ

## Exchange 几种模式

### simple 模式：

- 使用 rabbitMQ 自带的 Exchange： ""
- 不需要将 Exchange 进行 binding 操作
- 消息传递时，需要 routingKey 指定队列名，将消息发生到指定队列
- 如果 vhost 中不存在 routingKey ，消息会被抛弃

### Fanout 模式：

- 这种模式不需要 routingKey
- 需要提前将 Exchange 与 Queue 绑定，Exchange 与 Queue 可以是多对多的关系
- 如果 Exchange 与 Queue 绑定，消息会被抛弃

### Topic 模式：

- 需要绑定 Exchange Queue routingKey
- 项 Exchange 发布的消息，会根据 routingKey 将消息发布到相匹配的 Queue
- 在 Queue 绑定 routingKey 时，可以使用通配符： * 代表一个关键字，# 表示 0 个或多个关键字
- 匹配不到 Queue，消息会被抛弃
