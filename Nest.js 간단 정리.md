# Nest.js 간단 정리

### Nest.js에 대해 간단하게 정리해보자

- 기본적으로 Nest.js는 Express.js를 기반으로 하지만 다른 여러 프레임워크로부터 영향을 받은 복합적인 프레임워크이다.

- 기본적으로 TS를 사용할 것을 전제하고 만들어졌으며 강력한 의존성 주입 기능을 자랑한다.

<br>

### 주요 개념

- 모듈(Module)은 기본 구성 요소 중 하나로, 관련 기능을 그룹화한다. 기본적으로 최소 하나의 루트 모듈을 가진다.

```ts
@Module({
  imports: [],
  controllers: [MoviesController],
  providers: [MoviesService],
})
export class AppModule {}
```

- 컨트롤러(Controller)는 요청을 처리하고 클라이언트에 응답을 반환하는 역할을 수행한다.

```ts
@Controller('movies')
export class MoviesController {
  @Get()
  getAll(): Movie[] {
    return this.moviesService.getAll();
  }

  @Get('/:id')
  getOne(@Param('id') movieId: string) {
    return this.moviesService.getOne(movieId);
  }

  @Post()
  create(@Body() movieData) {
    return this.moviesService.create(movieData);
  }

  @Delete('/:id')
  remove(@Param('id') movieId: string) {
    return this.moviesService.deleteOne(movieId);
  }

  @Patch('/:id')
  patch(@Param('id') movieId: string, @Body() updateData) {
    return {
      updateMovie: movieId,
      ...updateData,
    };
  }
}
```

- 서비스(Service)는 비즈니스 로직을 처리한다. 컨트롤러에서 호출하고 의존성 주입을 통해 모듈에서 사용된다.

```ts
@Injectable()
export class MoviesService {
  private movies: Movie[] = [];

  getAll(): Movie[] {
    return this.movies;
  }

  getOne(id: string): Movie {
    return this.movies.find((movie) => movie.id === parseInt(id));
  }

  deleteOne(id: string): boolean {
    this.movies.filter((movie) => movie.id !== +id);
    return true;
  }

  create(movieData) {
    this.movies.push({
      id: this.movies.length + 1,
      ...movieData,
    });
  }
}
```

- 의존성 주입은 클래스의 의존성을 관리한다.

```ts
constructor(private readonly moviesService: MoviesService) {}
```

- 데코레이터(decorator)는 클래스, 메서드, 프로퍼티, 매개변수 등을 수식하여 특정 동작을 수행한다.

```ts
@Get()
@Post()
@Patch()
...
```

- DTO는 TS의 타입과도 같은 존재로, 바디의 유효성 검사에 사용된다. class-validator, class-transformer, mapped-types 등의 라이브러리와 병행하여 간단한 유효성 검사 로직의 구현이 가능하다.

```ts
import { IsNumber, IsOptional, IsString } from 'class-validator';

export class CreateMovieDto {
  @IsString()
  readonly title: string;
  @IsNumber()
  readonly year: number;
  @IsOptional()
  @IsString({ each: true })
  readonly genres: string[];
}
```

<br>

### 기초 세팅

- 생성

```bash
npm i -g @nestjs/cli
nest new my-nest-app
cd my-nest-app
```
