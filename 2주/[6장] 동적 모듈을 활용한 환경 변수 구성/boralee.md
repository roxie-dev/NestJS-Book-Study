## @nest/config  패키지를 사용하지 않고 동적으로 모듈 생성하기

```ts
// app.module.ts
import { Module } from '@nestjs/common';
import { AppController } from './app.controller';
import { AppService } from './app.service';
import { SampleConfigModule } from './sampleConfig.module';

// dotenv를 이용한 동적 모듈 주입
@Module({
  imports: [
    SampleConfigModule.forRoot(
      process.env.NODE_ENV === 'production'
        ? '.product.env'
        : process.env.NODE_ENV === 'stage'
          ? '.stage.env'
          : '.dev.env',
    ),
  ],
  controllers: [AppController],
  providers: [AppService],
})
export class AppModule {}
```

```ts
// sampleConfig.module.ts
import { DynamicModule } from '@nestjs/common';
import * as dotenv from 'dotenv';
import * as path from 'path';
import { SampleConfigService } from './sampleConfig.service';

export class SampleConfigModule {
  static forRoot(envPath: string): DynamicModule {
    dotenv.config({ path: path.resolve(envPath) });

    return {
      module: SampleConfigModule,
      providers: [SampleConfigService],
      exports: [SampleConfigService],
    };
  }
}
```


```ts
// sampleConfig.service.ts
import { Get, Injectable } from '@nestjs/common';

@Injectable()
export class SampleConfigService {
  @Get(`/getSampleConfig`)
  get() {
    return process.env.DATABASE_HOST ;
  }
}
```


```ts
// app.controller.ts
import { Controller, Get } from '@nestjs/common';
import { SampleConfigService } from './sampleConfig.service';

@Controller()
export class AppController {
  constructor(private readonly sampleConfigService: SampleConfigService) {}

  @Get('/getSampleConfig')
  getDatabaseHostFromSampleService(): string {
    return this.sampleConfigService.get() ?? 'DATABASE_HOST undefined';
  }
}
```
