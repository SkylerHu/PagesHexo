---
title: fastjson使用public修饰 private转化Object有问题 注意事项
date: 2014-06-11 14:17
tags:
  - fastjson
---
### **版本：fastjson.jar 1.1.33**

字段为非`String` 的`Object`时，若不是使用`public`修饰，则必须有对应的get、set方法，

字段类选为`boolean`时，必须使用`public`;

**综合各种考虑，应当直接全部使用`public`修饰！**

```Java
import java.util.ArrayList;  
import java.util.List;  
  
import com.alibaba.fastjson.JSON;  
  
class User {  
    public Long id;  
    public String name;  
    int age;  
    public String adress;  
    List<String> teachers;  
    public void setTeachers(List<String> teachers) {  
        this.teachers = teachers;  
    }  
    public List<String> getTeachers(){  
        return this.teachers;  
    }  
}  
  
public class Test {  
    public static void main(String[] args) {  
        List<String> teachers = new ArrayList<>();  
        for (int i = 0; i < 5; i++) {  
            teachers.add(i+"");  
        }  
        User rootUser = new User();  
        rootUser.id = 3L;  
        rootUser.name = "root";  
        rootUser.teachers = teachers;  
//      rootUser.setTeachers(teachers);  
          
        String jsonString = JSON.toJSONString(rootUser);  
  
        System.out.println(jsonString);  
          
        User user = JSON.parseObject(jsonString, User.class);  
  
        System.out.println(user.id);  
//      System.out.println(user.getTeachers());  
        System.out.println(user.teachers);  
          
    }  
}
```
