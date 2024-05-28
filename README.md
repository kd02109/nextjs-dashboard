## Next.js App Router Course - Starter

This is the starter template for the Next.js App Router Course. It contains the starting code for the dashboard application.

For more information, see the [course curriculum](https://nextjs.org/learn) on the Next.js Website.

## Chapter 6 Setting DB
###  Seeding

1. 초기 데이터 설정

개발 초기 단계에서 기본적인 데이터를 데이터베이스에 설정하여 애플리케이션이 정상적으로 작동하도록 합니다. 예를 들어, 사용자 역할, 설정 값, 기본 카테고리 등의 데이터를 미리 삽입합니다.
개발 및 테스트 환경 설정:

개발자와 테스터들이 애플리케이션을 개발하고 테스트하는 과정에서 일관된 데이터를 사용할 수 있도록 도와줍니다. 이는 일관성 있는 테스트를 가능하게 하여 버그를 쉽게 재현하고 수정할 수 있게 합니다.

2. 데모 및 프레젠테이션 용도

애플리케이션을 시연하거나 프레젠테이션할 때 필요한 샘플 데이터를 제공합니다. 이를 통해 기능을 쉽게 설명하고 보여줄 수 있습니다.

3. 데이터 무결성 유지

시딩을 통해 데이터베이스에 필요한 기본 데이터 구조와 관계를 설정하여 데이터 무결성을 유지할 수 있습니다. 이는 데이터베이스의 올바른 동작을 보장하는 데 중요합니다.

4. 자동화된 배포

CI/CD 파이프라인에서 데이터베이스를 자동으로 설정하고 배포할 때 시딩 스크립트를 사용하여 데이터베이스를 초기화합니다. 이는 배포 과정에서 일관성을 유지하고 수작업을 최소화하는 데 도움을 줍니다.

## Chapter 7 Fetching Data
### 서버와 소통하기
- API 엔드포인트를 만들 때는 데이터베이스와 상호 작용하는 로직을 작성해야 합니다.
- React 서버 컴포넌트(서버에서 데이터 불러오기)를 사용하는 경우 API 계층을 건너뛰고 데이터베이스 비밀을 클라이언트에 노출할 위험 없이 데이터베이스를 직접 쿼리할 수 있습니다.
- 서버컴포넌트 사용의 장점
    - 서버 컴포넌트는 `Promise`를 지원하여 데이터 가져오기와 같은 비동기 작업을 위한 더 간단한 솔루션을 제공합니다. `useEffect`, `useState` 또는 fetching 라이브러리를 사용하지 않고도 `async/await` 구문을 사용할 수 있습니다.
    - 서버 컴포넌트는 서버에서 실행되므로 비용이 많이 드는 데이터 가져오기 및 로직을 서버에 보관하고 결과만 클라이언트로 전송할 수 있습니다.
    - 앞서 언급했듯이 서버 컴포넌트는 서버에서 실행되므로 추가 API 계층 없이 데이터베이스를 직접 쿼리할 수 있습니다.

### request waterfulls
```ts
  const revenue = await fetchRevenue()
  const latestInvoices = await fetchLatestInvoices()
  const {      
      numberOfCustomers,
      numberOfInvoices,
      totalPaidInvoices,
      totalPendingInvoices
    } = await fetchCardData()
```
위와 같은 코드 구조는 __waterfull__ 현상을 유발합니다. 이전 요청의 완료 여부에 따라 달라지는 일련의 네트워크 요청을 의미합니다. 데이터 가져오기의 경우, 각 요청은 이전 요청이 데이터를 반환한 후에만 시작할 수 있습니다.

해당 효과가 필요한 경우도 있지만, 각각의 api가 독립적이라면 waterfull 현상을 유발할 필요는 없습니다. 해당 방법을 해결하는 방법으로 javascript의 `Promise.all()`, `Promise.allSettled()`를 활용합니다.
```ts
  const [revenueResult, invoicesResult, {
    totalPaidInvoices,
    totalPendingInvoices,
    numberOfInvoices,
    numberOfCustomers
  }] = await Promise.all([
    fetchRevenue(),
    fetchLatestInvoices(),
    fetchCardData(),
  ])

```