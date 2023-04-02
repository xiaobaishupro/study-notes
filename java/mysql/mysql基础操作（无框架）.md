

### mysql基础操作（无框架）

createStatement()：

```java
Class.forName("com.mysql.jdbc.Driver");
Connection connection = DriverManager.getConnection(
        "jdbc:mysql://localhost:3306/study_xj",
        "root",
        "root");
String sql = "select * from user";
Statement statement = connection.createStatement();
ResultSet resultSet = statement.executeQuery(sql);
while (resultSet.next()) {
    System.out.println("id:" + resultSet.getInt("user_id") +
            "name:" + resultSet.getString("username"));
}
statement.close();
connection.close();
```

```java
Class.forName("com.mysql.jdbc.Driver");
String url = "jdbc:mysql://localhost:3306/study_xj?useSSL=true";
String username = "root";
String password = "root";
Connection connection = DriverManager.getConnection(url, username, password);
String sql = "select * from user where user_id = ?";
PreparedStatement statement = connection.prepareStatement(sql);
statement.setInt(1, 4);
ResultSet resultSet = statement.executeQuery();
while (resultSet.next()) {
    System.out.println("id:" + resultSet.getInt("user_id") + "\t" +
            "name:" + resultSet.getString("username"));
}
statement.close();
connection.close();
```