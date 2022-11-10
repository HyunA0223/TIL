# JPA   
## ☝️ JPA 란?  

JPA는, SQL을 사용하지 않고 데이터를 생성,조회, 수정, 삭제할 수 있도록 해주는 번역기   

<br>

-----------------------

## 1. RDBMS  

### ✏️ 1-1. **RDBMS (Relational DatabBase Management System)**
- RDBMS란? 컴퓨터에 정보를 저장하고 관리하는 기술!
    -> 성능/관리 면에서 매우 고도화된 엑셀 같은 것
- RDBMS의 종류
    -> MySQL, PostgreSQL, Oracle Database  

<br>

------------------------------------------------------------------

## 2. SQL 연습하기   

### ✏️ 2-1. **H2 웹콘솔**
- H2 웹콘솔이란? RDBMS의 한 종류로, 서버가 켜져있는 동안에만 작동하는 RDB
- 데이터베이스
    - 데이터베이스를 엑셀로 비유해서 살펴보면,
        1. 엑셀 파일 하나 = 데이터베이스
        2. 엑셀 시트 하나 = 테이블
        3. 엑셀 행 하나 = 데이터

- 데이터 생성하기
    ```java
    CREATE TABLE IF NOT EXISTS courses (
        id bigint NOT NULL AUTO_INCREMENT, 
        title varchar(255) NOT NULL,
        tutor varchar(255) NOT NULL,
        PRIMARY KEY (id)
    );
    ```
- 데이터 삽입하기
    ```java
    INSERT INTO courses (title, tutor) VALUES
        ('웹개발의 봄, Spring', '남병관'), ('웹개발 종합반', '이범규');
    ```
- 데이터 조회하기
    ```java
    SELECT * FROM courses;
    ```

<br>

------------------------------------------------------------------

## 3. JPA  

### **✏️ 3-1. JPA 시작하기**
- Domain, Repository 소개
    - JPA : 자바로 DB를 사용하도록 도와줌
    - Domain : DB의 테이블
    - Repository : SQL (생성, 삽입, 조회 명령문)
- Domain, Repository 도입
    ex) Course라는 테이블에 title, tutor 컬럼 만들기
    1. *src > main > java > com.hyuna.jpaPrac*에 *domain* 패키지 생성
    2. *Course.java*, *CourseRepository.java* 파일 생성
        - ***Course.java***
            ```java
            @NoArgsConstructor // 기본생성자를 대신 생성해줍니다.
            @Entity // 테이블임을 나타냅니다.
            public class Course {

                @Id // ID 값, Primary Key로 사용하겠다는 뜻입니다.
                @GeneratedValue(strategy = GenerationType.AUTO) // 자동 증가 명령입니다.
                private Long id;

                @Column(nullable = false) // 컬럼 값이고 반드시 값이 존재해야 함을 나타냅니다.
                private String title;

                @Column(nullable = false)
                private String tutor;

                public String getTitle() {
                    return this.title;
                }

                public String getTutor() {
                    return this.tutor;
                }

                public Course(String title, String tutor) {
                    this.title = title;
                    this.tutor = tutor;
                }
            }
            ```
        - ***CourseRepository.java*** 인터페이스
            ```java
            public interface CourseRepository extends JpaRepository<Course, Long> {
            }
            ```
            - **Interface 란?** 클래스에서 멤버가 빠진, 메소드 모음집
                (JPA는 Repository를 통해서만 사용 가능)

### **✏️ 3-2. JPA 사용해보기**
- SQL이 보이도록 **application.properties** 세팅
    ```java
    spring.jpa.show-sql=true
    spring.datasource.url=jdbc:h2:mem:testdb;MODE=MYSQL
    ```
- JPA 사용해보기
    - JPA 실행 코드
        ```java
        // main 함수 아래에 위치
        @Bean
        public CommandLineRunner demo(CourseRepository repository) {
            return (args) -> {

            };
        }
        ```
    - 웹콘솔 접속해서 확인해보기
        ```
        SELECT * FROM course;
        ```

### **✏️ 3-3. JPA 심화**
- **데이터 저장하기 (Create)** & **조회하기 (Read)**
    -> **Repository의** **save**와 **findAll** 등을 이용
    - create & read
        ```java
        // 데이터 저장하기
        repository.save(new Course("프론트엔드의 꽃, 리액트", "임민영"));

        // 데이터 전부 조회하기
        List<Course> courseList = repository.findAll();
        for (int i=0; i<courseList.size(); i++) {
            Course course = courseList.get(i);
            System.out.println(course.getId());
            System.out.println(course.getTitle());
            System.out.println(course.getTutor());
        }

        // 데이터 하나 조회하기
        Course course = repository.findById(1L).orElseThrow(
                () -> new IllegalArgumentException("해당 아이디가 존재하지 않습니다.")
        );
        ```
- **데이터 수정하기 (Update)**
    - Service란?      
        - 스프링의 구조
            1. Controller : 가장 바깥 부분, 요청/응답을 처리
            2. Service : 중간 부분, 실제 중요한 작동이 많이 일어남
            3. Repo : 가장 안쪽 부분, DB와 맞닿아 있음 (Repository, Entity)
    - Service 만들기
        1. Course 클래스에 update 메소드 추가
            ```java
            public void update(Course course) {
                this.title = course.title;
                this.tutor = course.tutor;
            }
            ```
        2. src > main > java > com.hyuna.jpaPrac > service 패키지 생성
        3. CourseService.java 만들기
            ```java
            @Service // 스프링에게 이 클래스는 서비스임을 명시
            public class CourseService {

                    // final: 서비스에게 꼭 필요한 녀석임을 명시
                private final CourseRepository courseRepository;

                    // 생성자를 통해, Service 클래스를 만들 때 꼭 Repository를 넣어주도록
                    // 스프링에게 알려줌
                public CourseService(CourseRepository courseRepository) {
                    this.courseRepository = courseRepository;
                }

                @Transactional // SQL 쿼리가 일어나야 함을 스프링에게 알려줌
                public Long update(Long id, Course course) {
                    Course course1 = courseRepository.findById(id).orElseThrow(
                            () -> new IllegalArgumentException("해당 아이디가 존재하지 않습니다.")
                    );
                    course1.update(course);
                    return course1.getId();
                }
            }
            ```
        4. update 실행해보기
            ```java
            // main 메소드 아래에 작성
            @Bean
            public CommandLineRunner demo(CourseRepository courseRepository, CourseService courseService) {
                return (args) -> {
                    courseRepository.save(new Course("프론트엔드의 꽃, 리액트", "임민영"));

                    System.out.println("데이터 인쇄");
                    List<Course> courseList = courseRepository.findAll();
                    for (int i=0; i<courseList.size(); i++) {
                        Course course = courseList.get(i);
                        System.out.println(course.getId());
                        System.out.println(course.getTitle());
                        System.out.println(course.getTutor());
                    }

                    Course new_course = new Course("웹개발의 봄, Spring", "임민영");
                    courseService.update(1L, new_course);
                    courseList = courseRepository.findAll();
                    for (int i=0; i<courseList.size(); i++) {
                        Course course = courseList.get(i);
                        System.out.println(course.getId());
                        System.out.println(course.getTitle());
                        System.out.println(course.getTutor());
                    }
                };
            }
            ```
    - **데이터 삭제하기 (Delete)**
        ```java
        // main 메소드 아래에 작성
        @Bean
        public CommandLineRunner demo(CourseRepository courseRepository, CourseService courseService) {
            return (args) -> {
                courseRepository.save(new Course("프론트엔드의 꽃, 리액트", "임민영"));

                System.out.println("데이터 인쇄");
                List<Course> courseList = courseRepository.findAll();
                for (int i=0; i<courseList.size(); i++) {
                    Course course = courseList.get(i);
                    System.out.println(course.getId());
                    System.out.println(course.getTitle());
                    System.out.println(course.getTutor());
                }

                Course new_course = new Course("웹개발의 봄, Spring", "임민영");
                courseService.update(1L, new_course);
                courseList = courseRepository.findAll();
                for (int i=0; i<courseList.size(); i++) {
                    Course course = courseList.get(i);
                    System.out.println(course.getId());
                    System.out.println(course.getTitle());
                    System.out.println(course.getTutor());
                }

                courseRepository.deleteAll();
            };
        }
        ```

### **✏️ 3-3. 생성일자, 수정일자**
- Timestamped
    DB 기본 중의 기본은, **생성일자**와 **수정일자**를 필드로 가지는 것
    - **Timestamped.java**
        ```java
        @MappedSuperclass // 상속했을 때, 컬럼으로 인식하게 합니다.
        @EntityListeners(AuditingEntityListener.class) // 생성/수정 시간을 자동으로 반영하도록 설정
        public class Timestamped {

            @CreatedDate // 생성일자임을 나타냅니다.
            private LocalDateTime createdAt;

            @LastModifiedDate // 마지막 수정일자임을 나타냅니다.
            private LocalDateTime modifiedAt;
        }
        ```
    - Course.java에 추가
        ```java
        class Course extends Timestamped { }
        ```
    - jpaPracApplication 클래스에 추가
        ```java
        @EnableJpaAuditing
        @SpringBootApplication
        public class Week02Application { }
        ```