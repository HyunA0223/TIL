# API   
## ☝️ API 란?  

클라이언트-서버 간의 약속, 클라이언트가 서버에게 요청(Request)를 보내면 서버가 요구사항을 처리하여 응답(Response)를 반환

<br>

## ☝️ REST 란?  

주소에 명사, 요청 방식에 동사를 사용함으로써 의도를 명확히 드러냄을 의미  
- 여기에서 동사는 CRUD를 지칭 > 생성(POST), 조회(GET), 수정(PUT), 삭제(DELETE) 요청을 하는 것  
(ex) 
    - GET /courses
    → 강의 전체 목록 조회 요청
    - GET /courses/1
    → ID가 1번인 녀석 조회 요청
    - POST /courses 
    → 강의 생성 요청
    - PUT /courses/3 
    → ID가 3번인 녀석 수정 요청
    - DELETE /courses/2 
    → ID 2번인 녀석 삭제 요청
- 주의사항
    - 주소에 들어가는 명사들은 복수형을 사용
    - 주소에 동사는 가급적 사용하지 않음

<br>

-----------------------

## 1. API 

### ✏️ 1-1. **GET** - 데이터 조회 API
1. src > main > java > com.hyuna.jpaPrac 아래에 **controller** 폴더 생성
2. CourseController.java 파일 생성 (테이블 이름 + Controller)
    ```java
    @RequiredArgsConstructor
    @RestController
    public class CourseController {

        private final CourseRepository courseRepository;

        @GetMapping("/api/courses")
        public List<Course> getCourses() {
            return courseRepository.findAll();
        }
    }
    ```
3. jpaPracApplication.java
    ```java
    @EnableJpaAuditing
    @SpringBootApplication
    public class jpaPracApplication {

        public static void main(String[] args) {
            SpringApplication.run(jpaPracApplication.class, args);
        }

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

                CourseRequestDto requestDto = new CourseRequestDto("웹개발의 봄, Spring", "임민영");
                courseService.update(1L, requestDto);
                courseList = courseRepository.findAll();
                for (int i=0; i<courseList.size(); i++) {
                    Course course = courseList.get(i);
                    System.out.println(course.getId());
                    System.out.println(course.getTitle());
                    System.out.println(course.getTutor());
                }
            };
        }
    }
    ```

### ✏️ 1-2. **POST** - 데이터 생성 API 
1. CourseController.java
    ```java
    private final CourseService courseService;

    // PostMapping을 통해서, 같은 주소라도 방식이 다름을 구분합니다.
    @PostMapping("/api/courses") 
    public Course createCourse(@RequestBody CourseRequestDto requestDto) {
            // requestDto 는, 생성 요청을 의미합니다.
            // 강의 정보를 만들기 위해서는 강의 제목과 튜터 이름이 필요하잖아요?
            // 그 정보를 가져오는 녀석입니다.
        
            // 저장하는 것은 Dto가 아니라 Course이니, Dto의 정보를 course에 담아야 합니다.
            // 잠시 뒤 새로운 생성자를 만듭니다.
            Course course = new Course(requestDto);
            
            // JPA를 이용하여 DB에 저장하고, 그 결과를 반환합니다.
        return courseRepository.save(course);
    }
    ```
2. Course 클래스 생성자 추가
    ```java
    public Course(CourseRequestDto requestDto) {
       this.title = requestDto.getTitle();
        this.tutor = requestDto.getTutor();
    }
    ```


### ✏️ 1-3. **PUT** - 데이터 수정 API
1. CourseController.java
    ```java
    @PutMapping("/api/courses/{id}")
    public Long updateCourse(@PathVariable Long id, @RequestBody CourseRequestDto requestDto) {
        return courseService.update(id, requestDto);
    }
    ```

### ✏️ 1-4. **DELETE** - 데이터 수정 API
1. CourseController.java
    ```java
    @DeleteMapping("/api/courses/{id}")
    public Long deleteCourse(@PathVariable Long id) {
        courseRepository.deleteById(id);
        return id;
    }
```