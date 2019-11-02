# DBUtils

#### **1 核心对象**

l QueryRunner

l ResultSetHandler

 

 

#### **2 DBUtils实现增删改查**

```java
 @Override
    public List<Message> findAll() {
        List<Message> messages = null;
        QueryRunner queryRunner = new QueryRunner();
        String sql = "select * from td_message";
        try {
            messages = queryRunner.query(getConn(),sql, new BeanListHandler<>(Message.class));
        } catch (SQLException e) {
            e.printStackTrace();
        }
        System.out.println(messages);
        return messages;
    }
@Override
    public void insert(Message message) {
        QueryRunner queryRunner = new QueryRunner();
        String sql = "insert into td_message(user_id,room_id,content)values (?,?,?)";
        try {
            queryRunner.insert(getConn(),sql,
                    new BeanListHandler<>(Message.class),message.getUser_id(),message.getRoom_id(),
                    message.getContent());
        } catch (SQLException e) {
            e.printStackTrace();
        }
    }
```

