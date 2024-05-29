## Next.js App Router Course - Starter

This is the starter template for the Next.js App Router Course. It contains the starting code for the dashboard application.

For more information, see the [course curriculum](https://nextjs.org/learn) on the Next.js Website.

### Test 계정

- Id: user@nextmail.com
- Password: 123456

## Chapter 6 Setting DB

### Seeding

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
const revenue = await fetchRevenue();
const latestInvoices = await fetchLatestInvoices();
const {
  numberOfCustomers,
  numberOfInvoices,
  totalPaidInvoices,
  totalPendingInvoices,
} = await fetchCardData();
```

위와 같은 코드 구조는 **waterfull** 현상을 유발합니다. 이전 요청의 완료 여부에 따라 달라지는 일련의 네트워크 요청을 의미합니다. 데이터 가져오기의 경우, 각 요청은 이전 요청이 데이터를 반환한 후에만 시작할 수 있습니다.

해당 효과가 필요한 경우도 있지만, 각각의 api가 독립적이라면 waterfull 현상을 유발할 필요는 없습니다. 해당 방법을 해결하는 방법으로 javascript의 `Promise.all()`, `Promise.allSettled()`를 활용합니다.

```ts
const [
  revenueResult,
  invoicesResult,
  {
    totalPaidInvoices,
    totalPendingInvoices,
    numberOfInvoices,
    numberOfCustomers,
  },
] = await Promise.all([fetchRevenue(), fetchLatestInvoices(), fetchCardData()]);
```

## Chapter 8 Static and Dynamaic Rendering

Next.js의 static rendeeing 기본적인 Fetching 전략은 cacheing을 활용합니다. 따라서 실시간으로 데이터를 반영하지 못합니다. dynamic rendering의 경우에는
실시간 데이터 반영, 쿠키 반영이 가능하다는 장점이 있습니다. 위의 api 요청 방식은 static rendering 방식으로 최초 fetching 한 데이터를 cacheing 합니다. 따라서 실시간 데이터 변경사항을 반영하지 못합니다. 그렇기에 실시간 데이터 반영을 위해서는 추가적인 조치가 필요합니다. `import { unstable_noStore as noStore } from 'next/cache';` 해당 메서드를 활용하여 fetching 전력이 caching 되지 않도록 합니다.

혹은 Page, Layout에서 fetching 전략을 어떻게 사용할지 정의 합니다. 실시간 데이터 반영을 위해 page에 해당 코드를 추가합니다. `export const dynamic = "force-dynamic"`

## Chapter 9 Streaming

`Suspense`를 활용해서 fetching waterfull현상을 유의미하게 처리할 수 있습니다. API 요청을 `Suspense`내부에서 처리하여 각API에 대응하는 로딩화면을 보여줌으로서 더욱 사용자 친화적인 화면을 구성할 수 있으며, 부분 로딩을 구현할 수 있습니다.

`Susepnse`를 설정하는 것은 몇가지 조건에 따라 달라집니다.

1. 사용자가 스트리밍할 때 페이지를 경험하는 방식.
2. 우선순위를 지정할 콘텐츠.
3. 컴포넌트가 데이터 가져오기에 의존하는 경우.

또한 `Suspense`를 사용하는 경우 아래의 조건이나 설정을 고려해야 합니다.

- `loading.tsx`에서 했던 것처럼 전체 페이지를 스트리밍할 수도 있지만 구성 요소 중 하나에 데이터 가져오기가 느린 경우 로딩 시간이 길어질 수 있습니다.
- 모든 컴포넌트를 개별적으로 스트리밍할 수도 있지만, 준비되는 대로 UI가 화면에 튀어나올 수 있습니다.
- 페이지 섹션을 스트리밍하여 시차를 두는 효과를 만들 수도 있습니다. 하지만 래퍼 컴포넌트를 만들어야 합니다.

## Chapter11 Adding Search and Pagination

- SearchParams와 useSearchParams를 활용하여 pagenation과 search를 구현합니다. url query string을 활용해서 실시간으로 페이지 경로 정보와 검색 정보를 활용할 수 있도록 합니다.

1. query: 검색어 입력, page: pagenation 정보
2. 전체 row 개수를 fetching 합니다.
3. 하나의 페이지에서 보여줄 수 있는 row 페이지 개수와 전체 column 개수를 계산하여 pagenation을 구현합니다.
4. pagenation의 버튼을 클릭하면 Link 컴포넌트와 searchparams를 활용하여 page의 query 값을 업데이트 해서 다이나믹 랜더링이 발생하도록 합니다.

## Chapter12 Mutating Data

- Server Action
  React 서버 액션을 사용하면 서버에서 직접 비동기 코드를 실행할 수 있습니다. 데이터를 변경하기 위해 API 엔드포인트를 만들 필요가 없습니다. 대신 서버에서 실행되는 비동기 함수를 작성하고 **클라이언트 또는 서버 컴포넌트에서 호출**할 수 있습니다.

  서버 액션을 통해 제출되면 해당 액션을 사용하여 데이터를 변경할 수 있을 뿐만 아니라 `revalidatePath` 및 `revalidateTag`와 같은 API를 사용하여 관련 캐시의 유효성을 재검증할 수도 있습니다. 해당 경로와 연관된 caching 정보를 최신화 할 수 있습니다.

  form의 action을 활용해서 서버 action을 form과 연결해서 서버에서 form 입력값을 처리할 수 있습니다.

## Chapter13 Handling Errors

### error.tsx

`error.tsx` 파일은 route segment의 UI 경계를 정의하는 데 사용할 수 있습니다. 이 파일은 예기치 않은 오류를 확인하며 사용자에게 대체 UI를 표시할 수 있습니다.

```tsx
'use client';

import { useEffect } from 'react';

export default function Error({
  error,
  reset,
}: {
  error: Error & { digest?: string };
  reset: () => void;
}) {
  useEffect(() => {
    // Optionally log the error to an error reporting service
    console.error(error);
  }, [error]);

  return (
    <main className="flex h-full flex-col items-center justify-center">
      <h2 className="text-center">Something went wrong!</h2>
      <button
        className="mt-4 rounded-md bg-blue-500 px-4 py-2 text-sm text-white transition-colors hover:bg-blue-400"
        onClick={
          // Attempt to recover by trying to re-render the invoices route
          () => reset()
        }
      >
        Try again
      </button>
    </main>
  );
}
```

- Client Component입니다.
- Error: 이 객체는 자바스크립트의 기본 Error 객체의 인스턴스입니다.
- reset: Error 경계를 재설정하는 함수입니다. 이 함수가 실행되면 route segment를 다시 렌더링하려고 시도합니다.

### not-found.tsx

- status 404 Error에 대응해서 좀더 특수한 상황에 대응한 page입니다.
- next/navigation에서 제공하는 NotFound 메서드와 not-found.tsx를 조합해서 404에 대응하는 페이지를 만들 수 있습니다.
- 해당 페이지와 기능은 모두 서버 컴포넌트에서 수행되어야 합니다.

## Chapter14 ServerSide validation

### 웹 접근성

Next.js의 eslint는 `eslint-plugin-jsx-ally` 패키지를 포함하고 있습니다. 따라서 lint 검사를 통해서 alt, aria-\*, role에 대해서 검사를 수행할 수 있습니다. 이러한 웹 접근성을 높이는 방법은 다음과 같습니다.

- Semantic HTML
- Labelling
- Focus Outline

### Form validation

Server-side validation을 활용하기 위해서는 react-dom에서 제공하는 api 중 하나인 `useFormState`를 활용합니다.

```tsx
'use client';

// ...
import { useFormState } from 'react-dom';
```

zod의 safeParse() 메서드와 함께 활용합니다. useFormState는 아래와 같이 사용됩니다.

```tsx
'use client';
// ...
import { useFormState } from 'react-dom';

export default function Form({ customers }: { customers: CustomerField[] }) {
  const initialState = { message: null, errors: {} };
  // action을 수행할 함수와 초기 객체 데이터를 인자로 받습니다.
  // state는 errors 객체를 가지며, action에서 반환한 error 메시지와 erros에 대한 정의가 저장됩니다.
  const [state, dispatch] = useFormState(createInvoice, initialState);

  return <form action={dispatch}>...</form>;
}
```

```tsx
// useFormState에서 활용하는 action 함수는 아래와 같이 정의합니다. input 요소의 name에 맞추어 errors내부에 각기 다른 객체를 정의합니다.
export type State = {
  errors?: {
    customerId?: string[];
    amount?: string[];
    status?: string[];
  };
  message?: string | null;
};

export async function createInvoice(prevState: State, formData: FormData) {
  const validatedFields = CreateInvoice.safeParse({
    customerId: formData.get('customerId'),
    amount: formData.get('amount'),
    status: formData.get('status'),
  });

  // If form validation fails, return errors early. Otherwise, continue.
  if (!validatedFields.success) {
    return {
      errors: validatedFields.error.flatten().fieldErrors,
      message: 'Missing Fields. Failed to Create Invoice.',
    };
  }
}
```

- `safeParse()` 메서드는 success와 error에 관한 객체를 담고 있습니다. 반환된 객체는 공통된 속성으로 sucess 프로퍼티를 가지며 이를 통해 유효성 검사 통과 여부를 알 수 있습니다.
- 반환된 state에서 표시되는 error를 표기하기 위해 다음과 같은 처리를 합니다.

```tsx
<form action={dispatch}>
  <div className="rounded-md bg-gray-50 p-4 md:p-6">
    {/* Customer Name */}
    <div className="mb-4">
      <label htmlFor="customer" className="mb-2 block text-sm font-medium">
        Choose customer
      </label>
      <div className="relative">
        <select
          id="customer"
          name="customerId"
          className="peer block w-full rounded-md border border-gray-200 py-2 pl-10 text-sm outline-2 placeholder:text-gray-500"
          defaultValue=""
          aria-describedby="customer-error"
        >
          <option value="" disabled>
            Select a customer
          </option>
          {customers.map((name) => (
            <option key={name.id} value={name.id}>
              {name.name}
            </option>
          ))}
        </select>
        <UserCircleIcon className="pointer-events-none absolute left-3 top-1/2 h-[18px] w-[18px] -translate-y-1/2 text-gray-500" />
      </div>
      <div id="customer-error" aria-live="polite" aria-atomic="true">
        {state.errors?.customerId &&
          state.errors.customerId.map((error: string) => (
            <p className="mt-2 text-sm text-red-500" key={error}>
              {error}
            </p>
          ))}
      </div>
    </div>
    // ...
  </div>
</form>
```

- `aria-describedby="customer-error"`: 선택 요소와 오류 메시지 컨테이너 간의 관계를 설정합니다.` id="customer-error"`인 컨테이너가 선택 요소를 설명한다는 것을 나타냅니다. 화면 리더는 사용자가 선택 상자와 상호 작용할 때 이 설명을 읽고 오류를 알립니다.
- `id="customer-error"`: 이 id 속성은 선택 입력에 대한 오류 메시지를 담고 있는 HTML 요소를 고유하게 식별합니다. 이는 `aria-describedby`가 관계를 설정하는 데 필요합니다.
- `aria-live="polite"`: 스크린 리더는 div 내부의 오류가 업데이트되면 사용자에게 정중하게 알려야 합니다. 콘텐츠가 변경되면(예: 사용자가 오류를 수정할 때) 화면 리더는 이러한 변경 사항을 알리되, 사용자가 방해받지 않도록 유휴 상태일 때만 알립니다.

## Chapter 15 Adding Authentication

- **Authentication** : Authentication은 사용자가 본인이 맞는지 확인하는 것입니다. 사용자 아이디와 비밀번호와 같은 정보를 통해 신원을 증명하는 것입니다.
- **Authorization** : 사용자의 신원이 확인되면 권한 부여를 통해 애플리케이션의 어떤 부분을 사용할 수 있는지 결정합니다.

### NextAuth.js

Authenication을 위해서 NextAuth를 활용합니다.
