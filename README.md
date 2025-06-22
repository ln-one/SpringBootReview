# SpringBootReview
# JPA实现单表CRUD最简化模板（包含注解）

这是一个精简但包含必要注解的JPA单表CRUD模板：

## 1. 实体类

```java
@Entity
@Data
class Student {
    @Id
    @GeneratedValue(strategy = GenerationType.IDENTITY)
    private Long id;
    
    private String name;
    
    private Integer age;
}
```

## 2. Repository接口

```java
@Repository
interface StudentRepository extends CrudRepository<Student, Long> {
    // 自定义查询方法,可以不写
    List<Student> findByName(String name);
    List<Student> findByAgeGreaterThan(int age);
}
```
```java
@Repository
interface StudentRepository extends CrudRepository<Student, Long> {

}
```
## 3. Service类

```java
@Service
class StudentService {
    @Autowired
    private StudentRepository repo;
    
    // 增
    public Student add(Student s) {
        return repo.save(s);
    }
    
    // 删
    public void delete(Long id) {
        repo.deleteById(id);
    }
    
    // 改
    public Student update(Student s) {
        return repo.save(s);
    }
    
    // 查
    public Student findById(Long id) {
        return repo.findById(id).orElse(null);
    }
    
    public List<Student> findAll() {
        return (List<Student>) repo.findAll();
    }
}
```

## 4. Controller类

```java
@RestController
@RequestMapping("/students")
class StudentController {
    @Autowired
    private StudentService service;
    
    // 增
    @PostMapping
    public Student add(@RequestBody @Valid Student s) {
        return service.add(s);
    }
    
    // 删
    @DeleteMapping("/{id}")
    public void delete(@PathVariable Long id) {
        service.delete(id);
    }
    
    // 改
    @PutMapping
    public Student update(@RequestBody @Valid Student s) {
        return service.update(s);
    }
    
    // 查-单个
    @GetMapping("/{id}")
    public Student getById(@PathVariable Long id) {
        return service.findById(id);
    }
    
    // 查-全部
    @GetMapping
    public List<Student> getAll() {
        return service.findAll();
    }
}
```

这个模板包含了所有必要的注解，同时保持代码精简，适合考试手写。

为了更便于记忆，你可以记住以下关键点：
1. 实体类：`@Entity`, `@Data`, `@Id`, `@GeneratedValue`
2. 仓库：`extends CrudRepository<实体类, ID类型>`
3. Service：`@Service`, `@Autowired`
4. Controller：`@RestController`, `@RequestMapping`, `@GetMapping`等
### 附

CrudRepository自带的基本方法：

- `save(实体)` - 保存或更新实体
- `findById(ID)` - 根据ID查找
- `findAll()` - 查找所有
- `count()` - 计算总数
- `delete(实体)` - 删除一个实体
- `existsById(ID)` - 检查ID是否存在
