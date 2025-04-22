- 자주 접했던 에러
    - `UnknownDependenciesException`
        - 주로 **의존성 주입** 과정에서 모듈 간 설정이 잘못되어 있을 때 발생한다
        `nest g resource Users`  명령어로 CRUD 보일러플레이트를 생성한 후 서버를 실행할 때
        해당 에러가 발생함
        - UserController가 UserService를 정상적으로 주입 받지 않았음을 깨닫고 
        `users.module.ts`  파일을 살펴본 결과, `UserService`를  exports 하지 않고 있었음
        
        ```jsx
        import { Module } from '@nestjs/common';
        import { UsersController } from './users.controller';
        import { UsersService } from './users.service';
        
        @Module({
          controllers: [UsersController], // Controller 등록
          providers: [UsersService],      // Service 등록
          exports: [UsersService],        // 해당 줄 누락. 다른 모듈에서도 UsersService가 필요하므로 export가 필요함
        })
        export class UsersModule {}
        ```
        
- 3.1.5 커스텀 헤더
    - 브라우저 및 툴마다 헤더 이름을 표시하는 방식은 다를 수 있지만
    HTTP 헤더는 대소문자를 구분하지 않으며, 내부적으로 모두 동일하게 처리된다.
        
        ```tsx
        @Header('SampleHeader', 'Test')
        @Get(':id')
        findOne(@Param('id') id: string) {
          return this.usersService.findOne(+id);
        }
        ```
        
    - 위와 같이 커스텀 헤더의 이름을 카멜케이스로 작성했을 때 크롬브라우저와 curl의 헤더 정보가 다르게 나왔다
    - 크롬
        
        ![image1.png](https://github.com/roxie-dev/NestJS-Book-Study/blob/main/assets/boraLee/image1.png)
        
    - curl 명령
        
        ![image2.png](https://github.com/roxie-dev/NestJS-Book-Study/blob/main/assets/boraLee/image2.png)
        
    
- 3.1.6 리디렉션
    - http response status 301와 302의 차이
        - 301 Moved Permanenty
            - 해당 리소스가 영구적으로 이동됨
            - 이로인한 클라이언트의 동작
                - 브라우저는 새로운 URL를 캐싱한다 → 즉, 이전 URL로 재접속을 하면 서버 요청을 거치지 않고 바로 새로운 URL로 이동한다
                - 검색엔진이 **검색 엔진 최적화**를 위해 
                기존페이지의  페이지 랭크, 트래픽, 신뢰도를 신규 페이지로 이전한다
            - 사용 예시
                - 도메인을 변경할 때 (`example.com` → `new-example.com`)
                - URL 구조를 변경할 때 (`/prev-page` → `/new-page`
        - 302
            - 해당 리소스가 일시적으로 이동됨
            - 이로인한 클라이언트의 동작
                - 이전 URL을 다시 요청할 수 있다.
                - 검색엔진은 기존페이지의 페이지랭크,트래픽,신뢰도를 이전하지 않는다
            - 사용 예시
                - 이벤트 페이지를 일시적으로 변경할 때 (`/home` → `/event`)
