# Java专题——JDBC
java是通过使用jdbc来实现对数据库的curd（增删查改）的，使用jdbc首先需要导入相应的jar包，本文中导入的jar包包括mysql-connector-java-8.0.11.jar, 代码如下：  
```JAVA
package testjdbc;

import java.sql.Connection;
import java.sql.DriverManager;
import java.sql.PreparedStatement;
import java.sql.ResultSet;

public class TestjdbcUtils {
  public static void main(String[] args) {
    try {
      f1();
    } catch (Exception e) {
      // TODO Auto-generated catch block
      e.printStackTrace();
    }
  }

  public static void f1() throws Exception  {
    //注册驱动，在mysql-connector-java-8.0.11.jar里找
    Class.forName("com.mysql.cj.jdbc.Driver");
    
    //获取连接
    Connection con = DriverManager.getConnection("jdbc:mysql://localhost:3306/day14?serverTimezone=UTC&useUnicode=true&characterEncoding=UTF-8","root","password");
    
    //编写sql语言
    String sql = "select * from testtable";
    String sql1 = "insert into testtable(testKind,testScore) values(?,?)";
    
    //创建语句执行者
    PreparedStatement ps = con.prepareStatement(sql);
    PreparedStatement ps2 = con.prepareStatement(sql1);
    
    //设置参数
    ps2.setString(1,"物理");
    ps2.setLong(2,88);
    
    //执行sql
    ResultSet rs = ps.executeQuery();
    int i = ps2.executeUpdate();
    
    //处理结果
    while(rs.next()) {
      System.out.println(rs.getString("testID") + ":" + rs.getString("testKind") + ":" + rs.getString("testScore"));
    }
    

    //释放资源
    rs.close();
    ps.close();
    ps2.close();
    con.close();
  }
  
}
```
调用数据库共有8步：
第一步注册驱动：驱动的路径要在调用的jar包中找<br />
第二部获取连接
>Connection con = DriverManager.getConnection(jdbc:mysql:url?xx&xx&xx,username,password);<br />
>其中url实际上为//localhost:3306/day14<br />
>地址前面表示jdbc协议和mysql协议<br />
>而后面是为了考虑eclipse软件，mysql软件，以及jar包之间的兼容性而设置的<br />
>serverTimezone=UTC 统一软件之间的时区<br />
>useUnicode=true&characterEncoding=UTF-8 则是解决中文乱码问题<br />
  
第三步创建执行语句 将sql命令以字符串的形式存入sql和sql1中  <br/>
第四步创建执行语句采用的是PreparedStatement声明，如：  <br/>
PreparedStatement ps = con.prepareStatement(sql);  <br/>
第五步是设置参数，在第三步的命令中如果存在 ？ 问号，则需要对这些问号赋值，对应的命令有  setString 和 setLong 等  <br/>
第六步执行sql命令  <br/>
如果是查询的话，则会输出对应的结果因此采用：ResultSet rs = ps.executeQuery(); 其中rs会在后续的步骤中输出结果  <br/>
如果是增删改的话，则返回操作是否成功：int i = ps2.executeUpdate();  其中 i=1 表示成功，i=0 表示失败  <br/>
第七步处理结果中展示了如何将查询的结果输出  <br/>
第八步释放资源：完成了与数据库的交互后需要按顺序释放资源，先释放结果rs(如果有的话)，再释放执行语句（PreparedStatement类型），最后释放连接（Connection类型）<br/>

以上便是操作数据库的基础流程。<br/>

附在MySQL中生成初试表格的代码：<br/>
CREATE TABLE IF NOT EXISTS `testtable` (
    `testID` INT(11) PRIMARY KEY AUTO_INCREMENT,
    `testKind` VARCHAR(200),
    `testScore` INT(11)
);

INSERT INTO `testtable` (`testKind`,`testScore`) VALUES ('语文', 89);
INSERT INTO `testtable` (`testKind`,`testScore`) VALUES ('数学', 100);
INSERT INTO `testtable` (`testKind`,`testScore`) VALUES ('英语', 93);
