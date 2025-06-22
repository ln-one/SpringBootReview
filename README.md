# Spring Boot + JDBC 单表CRUD模板

## 1. 实体类

```java
// Student.java
@Data
public class Student {
    private Integer id;
    private String name;
    private Integer age;
}
```

## 2. Repository接口

```java
// StudentRepository.java
public interface StudentRepository {
    void save(Student student);
    void update(Student student);
    void deleteById(Integer id);
    Student findById(Integer id);
    List<Student> findAll();
}
```

## 3. JDBC实现类

```java
// JdbcStudentRepository.java
@Repository
public class JdbcStudentRepository implements StudentRepository {

    @Autowired
    private JdbcTemplate jdbc;
    
    // 增
    @Override
    public void save(Student student) {
        jdbc.update(
            "INSERT INTO student (name, age) VALUES (?, ?)",
            student.getName(), student.getAge());
    }
    
    // 改
    @Override
    public void update(Student student) {
        jdbc.update(
            "UPDATE student SET name=?, age=? WHERE id=?",
            student.getName(), student.getAge(), student.getId());
    }
    
    // 删
    @Override
    public void deleteById(Integer id) {
        jdbc.update("DELETE FROM student WHERE id=?", id);
    }
    
    // 查-单个
    @Override
    public Student findById(Integer id) {
        return jdbc.queryForObject(
            "SELECT id, name, age FROM student WHERE id=?",
            (rs, rowNum) -> {
                Student student = new Student();
                student.setId(rs.getInt("id"));
                student.setName(rs.getString("name"));
                student.setAge(rs.getInt("age"));
                return student;
            },
            id);
    }
    
    // 查-所有
    @Override
    public List<Student> findAll() {
        return jdbc.query(
            "SELECT id, name, age FROM student",
            (rs, rowNum) -> {
                Student student = new Student();
                student.setId(rs.getInt("id"));
                student.setName(rs.getString("name"));
                student.setAge(rs.getInt("age"));
                return student;
            });
    }
}
```

## 4. Controller类

```java
// StudentController.java
@RestController
@RequestMapping("/students")
public class StudentController {

    @Autowired
    private StudentRepository studentRepo;
    
    // 增
    @PostMapping
    public void addStudent(@RequestBody Student student) {
        studentRepo.save(student);
    }
    
    // 删
    @DeleteMapping("/{id}")
    public void deleteStudent(@PathVariable Integer id) {
        studentRepo.deleteById(id);
    }
    
    // 改
    @PutMapping
    public void updateStudent(@RequestBody Student student) {
        studentRepo.update(student);
    }
    
    // 查-单个
    @GetMapping("/{id}")
    public Student getStudentById(@PathVariable Integer id) {
        return studentRepo.findById(id);
    }
    
    // 查-所有
    @GetMapping
    public List<Student> getAllStudents() {
        return studentRepo.findAll();
    }
}
```
## 考试记忆要点

1. **最关键的四个类**：实体类、Repository接口、JDBC实现类、Controller

2. **JDBC核心方法**：
   - `jdbc.update(SQL, 参数...)` - 执行增删改
   - `jdbc.queryForObject(SQL, RowMapper, 参数...)` - 查询单个对象
   - `jdbc.query(SQL, RowMapper)` - 查询多个对象

3. **RowMapper简写形式**：
   ```java
   (rs, rowNum) -> {
       Student s = new Student();
       s.setId(rs.getInt("id"));
       s.setName(rs.getString("name"));
       s.setAge(rs.getInt("age"));
       return s;
   }
   ```
