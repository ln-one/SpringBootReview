# Spring Boot + JDBC 单表CRUD极简版（只有UPDATE操作）

下面是一个极简的只包含"改"操作的Spring Boot + JDBC实现，特别精简易记：

## 1. 实体类

```java
@Data
class Student {
    private Integer id;
    private String name;
    private Integer age;
}
```

## 2. Repository接口

```java
interface StudentRepository {
    void update(Student student);
}
```

## 3. JDBC实现类

```java
@Repository
class JdbcStudentRepository implements StudentRepository {
    @Autowired
    private JdbcTemplate jdbc;
    @Override
    public void update(Student student) {
        jdbc.update(
            "UPDATE student SET name=?, age=? WHERE id=?",
            student.getName(), student.getAge(), student.getId());
    }
}
```

## 4. Controller类

```java
@RestController
@RequestMapping("/students")
class StudentController {
    @Autowired
    private StudentRepository repo;
    @PutMapping
    void updateStudent(@RequestBody Student student) {
        repo.update(student);
    }
}
```
