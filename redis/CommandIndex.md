## Command Index

### MULTI, EXEC

- 事务块，将几个命令作为同一个事务操作（原子性）
- `MULTI` 用于标记一个事务块的开始
- `EXEC` 用于执行, 标记事务块的结束

```
> MULTI
> COMMAND 1
> COMMAND 2
> EXEC
```

### SADD, SMEMBERS

- `SADD` 将一个或多个元素加入到集合中，不存在集合则创建
- 用 `SMEMBERS` 来查看集合中的元素

```
> SADD myset "hello" "world"
> SMEMBERS myset
```

### RPUSH, LPUSH, LRANGE

- `RPUSH` 将一个或多个值插入到列表的尾部（右边），若不是列表类型，则报错
- `LPUSH` 类同 `RPUSH`，从左边
- `LRANGE` 用于查看列表 （list range）

```
> RPUSH mylist "world"
> LPUSH mylist "hello"
> LRANGE mylist 0 -1

```
