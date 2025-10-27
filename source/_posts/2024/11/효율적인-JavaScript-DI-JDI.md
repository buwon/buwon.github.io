---
title: 효율적인 JavaScript DI (JDI)
date: 2024-12-12
updated: 2024-12-12
tags: javascript di
category: articles
---

![](image.jpg)

## Dependency Injection은 Java 기준으로 생각하고 있다
프로그래밍에서 종속성 주입(Dependency Injection, DI)은 중요한 설계 패턴으로, 특히 자바(Java) 생태계에서 널리 채택되어 왔습니다. 그러나 자바스크립트(JavaScript) 환경에서는 DI의 적용과 관련하여 다소 복잡한 상황이 존재합니다.
자바에서 DI는 모듈 간 결합도를 낮추고 코드의 재사용성과 테스트 용이성을 높이는 핵심 메커니즘으로 자리 잡았습니다. 반면 자바스크립트에서는 DI 구현 방식에 대한 다양한 접근법과 논쟁이 존재합니다. 이러한 논쟁의 근본 원인은 자바의 DI 기법을 그대로 자바스크립트에 적용하려는 시도에서 비롯됩니다.
자바스크립트는 동적 타이핑과 함수형 프로그래밍 패러다임을 가진 언어로, 자바와는 근본적으로 다른 특성을 가지고 있습니다. 따라서 자바의 DI 패턴을 기계적으로 번역하는 것은 오히려 불필요한 복잡성을 야기할 수 있습니다. 대신 자바스크립트의 고유한 특성에 맞는 경량화되고 유연한 DI 접근법을 고려해야 합니다.
현재 자바스크립트 생태계에는 DI를 지원하는 다양한 라이브러리와 도구가 존재하지만, 개발자들 사이에서 이러한 도구들의 실제 필요성과 효용성에 대한 논쟁이 계속되고 있습니다. 이는 단순히 기술적 문제를 넘어 소프트웨어 설계 철학과 밀접하게 연관되어 있습니다.

## JavaScript의 import는 사실상 의존성 주입이다
JavaScript의 모듈 시스템은 실제로 암묵적인 싱글톤 패턴을 구현합니다. 또한 import한 결과물은 인스턴스입니다. 즉, 모듈에서 필요로 하는 의존성이 있는 모듈의 instance를 초기화 시점에서 주입을 받는 다는 사실입니다. 이것은 사실상 의존성을 주입하는 메커니즘으로 볼 수 있습니다.
```ts
// storeRepository.ts
export const storeRepository = {
  findUser() {
    // 사용자 조회 로직
    return { id: 1, name: 'John Doe' };
  }
};

// userService.ts
// UserService에 StoreRepository를 주입
import { storeRepository as store } from './storeRepository'

class UserService {
  getUser() {
    return store.findUser();
  }
}
```


## 의존 모듈을 교체하는 경우
의존성을 다른 모듈로 교체하여 주입하는 경우에는 의존성을 정의하는 파일을 수정하면 됩니다.
이 파일 또한 하나의 모듈이기 때문에 여러 가지 로직을 추가하여 의존성을 해결할 수도 있습니다.
```ts
// databaseA.ts
export class DatabaseA implements StoreRepository {
  findUser() {
  }
}

// databaseB.ts
export class DatabaseB implements StoreRepository {
  findUser() {
  }
}

// storeRepository.ts
import { DatabaseA } from './databaseA'
import { DatabaseB } from './databaseB'

// 기존 A모듈에서 B로 교체
// export const storeRepository = new DatabaseA()
export const storeRepository = new DatabaseB()
```


## 테스트 코드 작성하는 경우
DI를 사용하는 주된 이유중 하나는 모듈간의 결합도를 낮추고 테스트를 용이하게 하는데 있습니다. 인스턴스를 생성하는 시점에 주입하는 것이 아니라 import 시점에서 주입이 일어나기 때문에 손쉽게 mock 인스턴스로 교체할 수 있습니다.
```ts
import { mock } from 'bun:test'
mock.import('./storeRepository, () => {
  return {
    storeRepository: new MockStoreRepository()
  }
})
```

이 접근법의 핵심은 모듈 로딩 메커니즘을 활용해 런타임 시 의존성을 동적으로 대체한다는 점입니다. 전통적인 DI 프레임워크에서 제공하는 기능을 JavaScript의 모듈 시스템이 내재적으로 지원하고 있는 것이죠. 이로 인해 테스트 작성을 훨씬 더 유연하고 간결하게 만듭니다.