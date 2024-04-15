## webFlux

4. Spring-webflux的响应式API
   Spring-webflux框架是基于Reactor这个开源项目开发的。Reactor框架是跟Spring紧密配合的。

里边提供了两种API类型，分别是Mono和Flux；

Mono表示0 或 1个元素，
Flux表示0 至 N个元素，

webflux兼容大部分springmvc的注解，也可以像mvc那样创建controller处理请求。

区别：

WebFlux是完全异步非阻塞的，SpringMVC是同步阻塞的。
WebFlux采用异步响应式编程，SpringMVC采用命令式编程。
WebFlux由于完全异步，所有操作数据库的框架，以及数据库也都要求是支持异步的，所以目前不支持Mybatis、不支持Oracle数据库。

**依赖**
```
<dependency>
	<groupId>org.springframework.boot</groupId>
	<artifactId>spring-boot-starter-web</artifactId>
</dependency>
<dependency>
	<groupId>org.springframework.boot</groupId>
	<artifactId>spring-boot-starter-webflux</artifactId>
</dependency>
```

**启动类注解**
```
@SpringBootApplication
@EnableWebFlux
public class SpringWebfluxSessionApplication implements WebFluxConfigurer {
    public static void main(String[] args) {
        SpringApplication.run(SpringWebfluxSessionApplication.class, args);
    }
}
```

**路由定义**
定义PersonController，响应式风格中不再使用@RequestMapping声明地址映射，而是通过RouterFunctions.route().GET()方法:
```
    @Bean
    public RouterFunction<ServerResponse> personRoutes() {
        return RouterFunctions.route()
                .GET("/person/{id}", RequestPredicates.accept(MediaType.APPLICATION_JSON), personHandler::getPerson)
                .GET("/person", RequestPredicates.accept(MediaType.APPLICATION_JSON), personHandler::listPeople)
                .POST("/person", personHandler::createPerson)
                .build();
    }
```

**在PersonHandler中处理对应的HTTP请求，等同于MVC架构中的Service层**
```
@Component
public class PersonHandler {

    @Resource
    private IPersonDao personDao;

    public Mono<ServerResponse> listPeople(ServerRequest request) {
        Flux<Person> people = personDao.getList();
        return ServerResponse.ok()
                .contentType(MediaType.APPLICATION_JSON)
                .body(people, Person.class);
    }

    public Mono<ServerResponse> createPerson(ServerRequest request) {
        return request.bodyToMono(Person.class)
                .flatMap(i -> personDao.savePerson(i))
                .flatMap(p -> ServerResponse.ok().bodyValue(p));
    }

    public Mono<ServerResponse> getPerson(ServerRequest request) {
        int personId = Integer.parseInt(request.pathVariable("id"));
        return personDao.getPerson(personId)
                .flatMap(person -> ServerResponse.ok().contentType(MediaType.APPLICATION_JSON).bodyValue(person))
                .switchIfEmpty(ServerResponse.notFound().build());
    }
}
```

**dao层定义**
```
@Component
public class PersonDao implements IPersonDao {

    private final List<Person> personList = new ArrayList<>();

    public PersonDao() {
        this.personList.add(new Person(1, 17, "张三"));
        this.personList.add(new Person(2, 18, "李四"));
    }

    @Override
    public Flux<Person> getList() {
        return Flux.fromIterable(personList);
    }

    @Override
    public Mono<Void> savePerson(Person person) {
        personList.add(person);
        System.out.println("personList.size = " + personList);
        return Mono.empty();
    }

    @Override
    public Mono<Person> getPerson(Integer id) {
        return Mono.justOrEmpty(personList.stream().filter(p -> p.getId().equals(id)).findFirst());
    }
}
```