## Next.js App Router Course - Starter

This is the starter template for the Next.js App Router Course. It contains the starting code for the dashboard application.

For more information, see the [course curriculum](https://nextjs.org/learn) on the Next.js Website.

## Create a new project

next.js 공식문서에서는 pnpm 패키지 매니저 사용을 권장,  
npm, yarn에 비해 빠르고 효율적이라는 이유.

```bash
npm install -g pnpm
```

이를 활용해서 Next.js app 프로젝트를 생성하는데  
`create-next-app` 이라는 CLI 도구와 `--example` 플래그와 함께 시작 예제 생성

```bash
npx create-next-app@latest nextjs-dashboard --example "https://github.com/vercel/next-learn/tree/main/dashboard/starter-example" --use-pnpm
```

---

## Exploring the project

### 폴더 구조

예제 프로젝트의 폴더 구조를 보면  
`/app` : 애플리케이션의 모든 route, components 등 로직을 포함  
`/app/lib` : 재사용 가능한 유틸리티 함수와 데이터 fetch 함수 같은 애플리케이션에서 사용되는 함수를 포함  
`/app/ui` : 카드, 테이블, 폼과 같은 애플리케이션의 모든 UI 컴포넌트를 포함  
`/public` : 이미지와 같은 애플리케이션의 모든 정적 파일을 포함

### Placeholder Data

DB나 API가 준비되지 않은 경우 JSON 형식의 Javascript 객체로 placeholder data를 사용할 수 있음.

### Typescript

타입스크립트를 사용함으로서 컴포넌트나 DB에 잘못된 데이터 형식을 전달하는 실수를 방지할 수 있음.

> 수동으로 데이터 타입을 선언해도 되지만 더 나은 타입 안정성을 위해 Prisma 또는 Drizzle 과 같은 도구를 사용하는 것을 권장.  
> 이 도구들은 데이터 베이스 스키마에 기반하여 타입을 자동으로 생성해줌.

---

### Running the development server

`pnpm i` 명령어를 통해 프로젝트 패키지를 설치한 후,  
`pnmp dev` 로 개발 서버를 실행.

---

## CSS Styling

### Global styles

`/app/ui` 폴더를 살펴보면 `global.css` 파일을 볼 수 있는데,  
이 파일을 이용해서 _CSS reset rules_ 와 같은 프로젝트 전반에 적용되는 CSS 룰을 설정할 수 있음.

> `global css`는 어느 컴포넌트에서든지 import 할 수 있지만, 일반적으로 최상위 컴포넌트에 추가하는 것이 좋은 관행.

### Using the `clsx` library to toggle class names

Next.js 프로젝트 생성 시 tailwind css를 사용할거냐고 물어보는데 대부분의 경우 tailwind css를 활용하기도 하고 개인적으로 선호하기도 하니 tailwind css 를 사용하는데

이때, state나 기타 조건에 따라 요소를 조건부로 스타일링해야 하는 경우가 종종 있음.  
`clsx` 는 클래스 이름을 쉽게 toggle 할 수 있게 해주는 라이브러리로서 자세한 내용은 공식문서에 잘 나와있지만 기본 사용법은 다음과 같다.

`InvoiceStatus` 라는 컴포넌트를 만들어서 `status`를 전달한다고 가정했을 때, 이 `status`는 _pending_ 또는 _paid_ 일 수 있다.

만약 _paid_ 일 경우 색상을 green으로, _pending_ 일 경우, gray로 하고 싶다면 아래와 같이 작성할 수 있다.

```javascript
import clsx from 'clsx';

export default function InvoiceStatus({ status }: { status: string }) {
  return (
    <span
      className={clsx(
        'inline-flex items-center rounded-full px-2 py-1 text-sm',
        {
          'bg-gray-100 text-gray-500': status === 'pending',
          'bg-green-500 text-white': status === 'paid',
        },
      )}
    >
    // ...
)}
```

---

## Optimizing Fonts and Images

### Why optimize fonts?

폰트는 웹페이지 디자인에서 중요한 역할을 하지만 프로젝트에서 커스텀 폰트를 사용한다면 성능에 영향을 미칠 수 있기에 폰트 최적화가 필요하다.

구글에서 웹사이트의 성능과 사용자 경험을 평가하는데 사용하는 지표인 누적 레이아웃 이동(Cumulative Layout Shift, CLS)에서 폰트와 관련하여 레이아웃 이동은 브라우저가 처음에 텍스트를 fallback 또는 시스템 폰트로 렌더링 한 후,  
로드된 커스텀 폰트로 교체할 때 발생하는데, 이 교체는 텍스트 크기, 간격, 또는 레이아웃의 변화를 일으켜 주변 요소들을 이동시킬 수 있음.

![next-font-module](https://nextjs.org/_next/image?url=%2Flearn%2Fdark%2Ffont-layout-shift.png&w=1920&q=75)

Next.js에서는 `next/font` 모듈을 이용해서 폰트를 자동으로 최적화 해준다.  
빌드 시 폰트 파일을 다운로드하고 다른 정적 파일과 함께 호스팅함으로서  
사용자가 애플리케이션을 방문할 때, 폰트에 대한 추가적인 네트워크 요청이 발생하지 않아 성능에 영향을 주지 않음을 의미.

### 기본 폰트 추가

커스텀 구글 폰트를 내 프로젝트에 적용하기 위해  
`/app/ui` 폴더 안에 `fonts.ts` 라느 파일을 생성하고 이 파일을 애플리케이션 전반에 걸쳐 사용할 폰트를 저장할 목적으로 사용할 것이다.

먼저, `next/font/google` 모듈에서 `Inter` 폰트를 가져온 다음, 로드할 서브셋을 지정한다.

```javascript
/app/iu / fonts.ts;

import { Inter } from "next/font/google";

export const inter = Inter({ subsets: ["latin"] });
```

마지막으로 `/app/layout.tsx` 파일에서 `<body>` 요소에 폰트를 추가한다.

```javascript
/app/layout.tsx

import '@/app/ui/global.css';
import { inter } from '@/app/ui/fonts';

export default function RootLayout({
  children,
}: {
  children: React.ReactNode;
}) {
  return (
    <html lang="en">
      <body className={`${inter.className} antialiased`}>{children}</body>
    </html>
  );
}

```

`Inter` 폰트를 `<body>` 에 추가함으로서 폰트가 애플리케이션 전반에 걸쳐 적용됨.  
이때, Tailwind의 **`antialiased`** 클래스를 추가하여 폰트를 부드럽게 처리하고 있는데  
필수적으로 사용해야 하는건 아니지만 권장!

#### Practice : Adding a secondary font

`fonts.ts` 파일에서 `Lusitana` 라는 보조 폰트를 가져와서 컴포넌트에 적용하기!

```javascript
import { Inter, Lusitana } from "next/font/google";

export const inter = Inter({ subsets: ["latin"] });

export const lusitana = Lusitana({
  weight: ["400", "700"],
  subsets: ["latin"],
});
```

```javascript
import AcmeLogo from "@/app/ui/acme-logo";
import { ArrowRightIcon } from "@heroicons/react/24/outline";
import Link from "next/link";
import { lusitana } from "@/app/ui/fonts";

export default function Page() {
  return (
    // ...
    <p
      className={`${lusitana.className} text-xl text-gray-800 md:text-3xl md:leading-normal`}
    ></p>
    // ...
  );
}
```

### Why optimize Images?

Next.js는 이미지와 같은 정적 assets을 최상위 `/public` 폴더 아래에서 제공한다.  
일반적으로 다음과 같이 이미지를 추가할 수 있는데

```html
<img
  src="/hero.png"
  alt="대시보드 프로젝트의 스크린샷을 보여주는 데스크탑 버전"
/>
```

이런 방식을 사용하면 다음을 수동으로 처리해야 하는 불편함이 있다.

1. 이미지가 다양한 화면 크기에서 반응형으로 동작하도록 설정
2. 다양한 기기에 대한 이미지 크기 지정
3. 이미지 로드 시 레이아웃 이동 방지
4. 사용자의 뷰포트 밖에 있는 이미지 등은 lazy loading

그래서 Next.js는 이러한 최적화를 수동으로 구현하는 대신 **`next/image`** **컴포넌트**을 사용하여 이미지를 자동으로 최적화 할 수 있다.

#### Image Component

**`<Image>`** 컴포넌트는 HTML의 `<img>` 태그의 확장판으로, 다음과 같은 자동 이미지 최적화 기능이 포함되어 있다.

1. 이미지 로드 시 레이아웃 이동 방지
2. 작은 뷰포트를 가진 기기에 큰 이미지를 제공하지 않도록 크기 조정
3. 기본적으로 이미지를 lazy loading
4. 브라우저가 지원할 경우 webp, avif와 같은 최신 포맷으로 이미지 제공

```javascript
/app/page.tsx

import AcmeLogo from '@/app/ui/acme-logo';
import { ArrowRightIcon } from '@heroicons/react/24/outline';
import Link from 'next/link';
import { lusitana } from '@/app/ui/fonts';
import Image from 'next/image';

export default function Page() {
  return (
    // ...
    <div className="flex items-center justify-center p-6 md:w-3/5 md:px-28 md:py-12">
      {/* 여기에 히어로 이미지를 추가하세요 */}
      <Image
        src="/hero-desktop.png"
        width={1000}
        height={760}
        className="hidden md:block"
        alt="대시보드 프로젝트의 스크린샷을 보여주는 데스크탑 버전"
      />
    </div>
    //...
  );
}

```

레이아웃 이동 방지를 위해 `width`와 `height` 설정해주는 것은 좋은 습관이며, 이 값은 소스 이미지와 동일한 가로/세로 비율을 가져야 함.

또한, `hidden`, `md:block` 클래스를 사용해서 모바일 화면에서 이미지를 DOM에서 제거하고, 데스크탑 화면에서 이미지를 표시하였음.

---

## Creating Layouts and Pages

### 중첩 (Nested) Routing

Next.js는 **폴더**를 사용하여 중첩된 경로를 만드는 파일 시스템 라우팅을 사용함.  
각 폴더는 **URL 세그먼트**에 매핑되는 **경로 세그먼트**를 나타낸다.

![next-nested-routing](https://nextjs.org/_next/image?url=%2Flearn%2Fdark%2Ffolders-to-url-segments.png&w=1920&q=75)

**`layout.tsx`** 및 **`page.tsx`** 파일을 사용하여 각 경로에 대한 별도의 UI를 만들 수 있음.

**`page.tsx`** 는 React 컴포넌트를 내보내는 특수한 Next.js 파일이며, 경로에 엑세스할 수 있도록 하려면 반드시 필요하다.

이미 예시 프로젝트에 `page.tsx` 가 있는데 `/app/page.tsx` 는 `/` 경로와 연결된 홈 페이지이며,

중첩된 경로를 만드려면 각 폴더를 서로 중첩시키고 내부에 `page.tsx` 파일을 추가하면 되는데,  
예를 들어  
![next-nested-routing2](https://nextjs.org/_next/image?url=%2Flearn%2Fdark%2Fdashboard-route.png&w=1920&q=75)

`/app/dashboard/page.tsx` 는 `/dashboard` 경로와 연결된다.

### Creating the dashboard page

`/app` 폴더 안에 `dashboard` 라는 폴더를 생성하고 그 안에 `page.tsx` 파일을 생성한 후 아래와 같이 입력해보자

```javascript
export default function Page() {
  return <p>Dashboard Page</p>;
}
```

그런 다음, 서버를 실행시켜서 [http://localhost:3000/dashboard](http://localhost:3000/dashboard) 로 접근하면 `page.tsx` 에 작성한 `Dashboard Page`라는 텍스트를 볼 수 있다.

이것이 Next.js가 다양한 페이지를 만드는 방법으로, 새로운 경로를 만들기 위해 폴더를 생성하고 그 안에 `page` 파일을 추가하는 것이다.

> `page` 라는 특별한 이름을 사용함으로서 Next.js는 UI 구성요소, 테스트 파일 및 다른 관련 코드를 경로와 함께 colocation 할 수 있다.

> page 파일 내부의 콘텐츠에만 공개적으로 엑세스할 수 있으며,

> 예를 들어 `/ui`, `/lib` 폴더는 `/app` 폴더 내에 colocate 되어 있다.

![next-colocate](https://nextjs.org/_next/image?url=%2Fdocs%2Fdark%2Fproject-organization-colocation.png&w=1920&q=75)

### Practice : Creating the dashboard page

1.`Customer page`와 `invoices page`를 만들건데 2. `/dashboard/customers`, `/dashboard/invoices` 와 같은 경로로 접근이 가능해야 한다.

정답은 아래와 같은 폴더 구조를 만들어주면 된다.
![next-create-page](https://nextjs.org/_next/image?url=%2Flearn%2Fdark%2Frouting-solution.png&w=1920&q=75)

### Creating the dashboard layout

dashboard 에는 일반적으로 여러 페이지에서 공유되는 고유한 형태의 네비게이션이 있다.

Next.js에서는 이렇게 여러 페이지 간에 공유되는 UI를 만들기 위해 `layout.tsx` 라는 특별한 파일을 사용할 수 있는데

`/dashboard` 폴더 내에 `layout.tsx` 라는 파일을 추가하고 아래와 같이 입력해보자.

```javascript
import SideNav from "@/app/ui/dashboard/sidenav";

export default function Layout({ children }: { children: React.ReactNode }) {
  return (
    <div className="flex h-screen flex-col md:flex-row md:overflow-hidden">
      <div className="w-full flex-none md:w-64">
        <SideNav />
      </div>
      <div className="flex-grow p-6 md:overflow-y-auto md:p-12">{children}</div>
    </div>
  );
}
```

위 코드를 살펴보면 먼저, `<SideNav>` 컴포넌트를 `Layout`에 import 하고 있는데,  
이 파일에 임포트한 모든 컴포넌트는 레이아웃의 일부가 된다.

`<Layout />` 컴포넌트는 `children` prop을 받으며, 이 `children`은 페이지이거나 다른 레이아웃일 수 있다.

현재 프로젝트의 경우엔 `/dashboard` 내부의 페이지들이 자동으로 `<Layout />` 안에 중첩된다.

![next-layout](https://nextjs.org/_next/image?url=%2Flearn%2Fdark%2Fshared-layout.png&w=1920&q=75)

이처럼 Next.js에서 `Layout`의 장점은 `Page` 컴포넌트만 업데이트 되고 `Layout` 은 다시 렌더링되지 않는다는 것이며,

이를 `부분 렌더링(partial rendering)` 이라고 한다.
![next-partial-rendering](https://nextjs.org/_next/image?url=%2Flearn%2Fdark%2Fpartial-rendering-dashboard.png&w=1920&q=75)

### Root Layout

이전에 `Inter` 폰트를 다른 레이아웃 `/app/layout.tsx`에 가져왔는데

이를 **`Root Layout`** 이라고 하며, 필수로 존재해야 한다.  
루트 레이아웃에 추가한 모든 UI는 애플리케이션의 모든 페이지에서 공유되며,  
`<html>`, `<body>` 태그를 수정하고 메타데이터를 추가할 수 있다.

---

## Navigating Between Pages

### Why optimize navigation?

페이지 간 링크를 만드려면 전통적인 방식으로는 `<a>` 태그를 사용했었다.  
이전에 추가한 `SideNav` 에는 이런 전통적 방식에 따라 `<a>` 태그로 페이지 이동을 하고 있는데

클릭 했을 때, 무슨 일이 일어나는지 보면,  
페이지 전체가 **`refresh`** 되는 것을 볼 수 있다.

---

Next.js에선 페이지 간에 링크를 만들기 위해 **`<Link />`** 컴포넌트를 사용할 수 있다.

`<Link />` 컴포넌트를 사용하면 자바스크립트를 사용하여 **_<u>클라이언트-사이드 페이지 이동</u>_** 이 가능하다

> Client-side Navigation은 Next.js에서 사용자가 페이지 간 이동 시 페이지를 다시 로드하지 않고 자바스크립트를 사용하여 빠르게 페이지를 전환하는 방법을 뜻함.

> 🔥 주요 특징
>
> 1. 자바스크립트를 이용한 페이지 전환 : Link 컴포넌트를 사용하여 필요한 부분만 업데이트하므로 전환이 빠름.
> 2. 코드 스플리팅과 prefetching : 브라우저의 뷰포트에 `<Link />` 가 나타나면 Next.js는 링크된 페이지의 코드를 백그라운드에서 `prefetching` 함.

기존 `/app/ui/dashboard/nav-links.tsx` 파일의 `<a>` 태그를 `next/link` 모듈의 `<Link />` 태그로 변경하는데  
`<a href="...">` 대신 `<Link href="...">`로 대체해주면 된다.

그리고 다시 페이지 전환을 해보면 페이지 전체 refresh 없이 페이지 이동이 가능하다.

즉, 애플리케이션의 일부가 서버에서 렌더링되기는 하지만 전체 페이지 새로고침이 없어 웹앱처럼 느껴지는데 이유는

#### Automatic code-splitting and prefetching 때문이다.

페이지 이동 경험을 향상시키기 위해 Next.js는 라우트 세그먼트별로 애플리케이션 코드를 자동으로 분할한다.  
이는 브라우저가 초기 로드 시 모든 애플리케이션 코드를 로드하는 전통적인 React **_<u>SPA</u>_** 와는 다른데,

라우트 별로 코드를 분할하면 페이지가 격리되고 특정 페이지에서 오류가 발생하더라도 나머지 애플리케이션은 여전히 작동한다.

또한, production 환경에서 브라우저 뷰포트에 `<Link>` 컴포넌트가 나타나면 Next.js가 자동으로 연결된 경로의 코드를 백그라운드에서 `prefetching(사전로드)` 함으로서  
사용자가 링크를 클릭할 때 대상 페이지의 코드는 이미 백그라운드에서 로드되어 페이지 전환이 거의 즉시 발생한다.

### Pattern : Showing active links

### 패턴 : 활성화된 링크 표시

사용자에게 현재 어떤 페이지에 있는지 나타내기 위해 활성화된 링크를 표시하는 것은 일반적인 UI 패턴이다.

이를 위해서 사용자의 현재 경로를 URL에서 가져와야 하는데  
Next.js는 **`usePathname()`** 이라는 훅을 제공하며, 이를 사용하여 경로를 확인하고 해당 패턴을 구현할 수 있다.

`usePathname()` 은 훅이므로 `nav-links.tsx` 를 클라이언트 컴포넌트로 변환해야 하는데,  
이를 위해 파일 최상단에 React의 **`"use client"`** 지시어를 추가한 다음,  
`next/navigtion`에서 `usePathname()` 을 가져오면 된다.

```javascript
"use client";

import {
  UserGroupIcon,
  HomeIcon,
  InboxIcon,
} from "@heroicons/react/24/outline";
import Link from "next/link";
import { usePathname } from "next/navigation";

// ...
```

그리고 `<NavLinks />` 컴포넌트 내에서 `pathname`이라는 변수에 경로를 할당한다.

```javascript
export default function NavLinks() {
  const pathname = usePathname();
  // ...
}
```

`clsx` 라이브러리는 이전에 소개한 것처럼 클래스 이름을 조건부로 적용하는데 사용할 수 있으므로,  
`link.href`가 `pathname`과 일치하는지 여부에 따라 색상을 다르게 표현할 수 있다.

```javascript
"use client";

import {
  UserGroupIcon,
  HomeIcon,
  DocumentDuplicateIcon,
} from "@heroicons/react/24/outline";
import Link from "next/link";
import { usePathname } from "next/navigation";
import clsx from "clsx";

// ...

export default function NavLinks() {
  const pathname = usePathname();

  return (
    <>
      {links.map((link) => {
        const LinkIcon = link.icon;
        return (
          <Link
            key={link.name}
            href={link.href}
            className={clsx(
              "flex h-[48px] grow items-center justify-center gap-2 rounded-md bg-gray-50 p-3 text-sm font-medium hover:bg-sky-100 hover:text-blue-600 md:flex-none md:justify-start md:p-2 md:px-3",
              {
                "bg-sky-100 text-blue-600": pathname === link.href,
              }
            )}
          >
            <LinkIcon className="w-6" />
            <p className="hidden md:block">{link.name}</p>
          </Link>
        );
      })}
    </>
  );
}
```

---

## Setting Up Your Database

지금까지 대시보드에서 작업을 해왔는데 앞으로 계속 작업을 하기 위해선 데이터가 필요하다.  
이번 챕터에서는 `@vercel/postgres`를 활용하여 `PostgreSQL` 데이터베이스를 설정해보려 한다.

### Create a Github repository

시작하기에 앞서 아직 레포지토리를 깃허브에 푸쉬하지 않았다면 푸쉬를 하자.  
이렇게 하면 데이터베이스를 설정하고 배포하는 것이 더 쉬워진다.

### Vercel 계정 생성

계정을 만드려면 [vercel](vecel.com/signup)에 방문해서 `hobby` 플랜을 선택하여 계정을 생성하고 github와 연동 또한 설정해야 한다.

### Connect and deploy your project

그 다음, 아래와 같은 화면으로 이동할 텐데 여기서 방금 만든 깃허브 레포를 선택하고 import 할 수 있다.  
![next-vercel-project](https://nextjs.org/_next/image?url=%2Flearn%2Fdark%2Fimport-git-repo.png&w=1080&q=75)

그리고 `Deploy` 버튼을 클릭하면 아래 화면처럼 프로젝트 배포가 완료된 것을 볼 수 있다.
![next-vercel-project1](https://nextjs.org/_next/image?url=%2Flearn%2Fdark%2Fdeployed-project.png&w=1080&q=75)

Vercel과 깃허브 repo와 연동을 함으로서 변경사항을 **main** 에 push하면 언제든지 자동으로 재배포를 수행하며, 별도 설정이 필요 없다.

pull request를 열 때에도 미리보기가 가능하여 배포 오류를 미리 확인하고 프로젝트의 미리보기를 팀원들과 공유할 수 있다.

---

### Create a Postgres database

다음으로 데이터베이스를 설정하기 위해 <u>**Continue to Dashboard**</u>를 클릭하고 프로젝트 대시보드에서 `Storage` 탭을 선택한다.

그런 다음, **`Connect Store`** → **`Create New`** → **`Postgres`** → **`Continue`** 를 선택.  
![connect-database](https://nextjs.org/_next/image?url=%2Flearn%2Fdark%2Fcreate-database.png&w=1080&q=75)

약관 수락 후 데이터베이스의 이름을 지정하고 데이터베이스 지역을  
\*_Washington D.C(iad1)_ 로 설정한다.

> 이는 모든 Vercel 프로젝트의 기본 지역이므로  
> 데이터베이스를 응용프로그램 코드와 같은 지역이나 가까운 곳으로 설정하는 것이  
> 데이터 요청의 지연 시간을 줄일 수 있음.  
> 한국 기준으로는 `icn1(서울)` > ![vercel-database-region](https://nextjs.org/_next/image?url=%2Flearn%2Fdark%2Fdatabase-region.png&w=1080&q=75)

연결하면 `.env.local` 탭으로 이동, `Show secret` 및 `Copy Snippet`을 클릭하여
비밀키를 복사한다.  
![database-key](https://nextjs.org/_next/image?url=%2Flearn%2Fdark%2Fdatabase-dashboard.png&w=1080&q=75)

그리고 코드 에디터 (VSCode 등)로 이동하여 `.env.example` 파일을 `.env`로 이름을 변경하고  
Vercel에서 복사한 내용을 붙여넣는다.

> **!중요** : `.gitignore` 파일로 이동하여 `.env` 가 무시되도록 해서 비밀키가 노출되지 않도록 해야 한다.

마지막으로 터미널에서 `pnpm i @vercel/postgres`를 실행하여 [Vercel Postgres SDK](https://vercel.com/docs/storage/vercel-postgres/sdk)를 설치한다.

### Seed your database

데이터베이스를 생성했으면, 이제 초기 데이터인 시드를 심을 차례이다.  
이번 예제의 경우 대시보드를 구축하는 동안 사용할 데이터를 가져오게 할거고

프로젝트의 `/app` 폴더 안에 `seed` 라는 폴더가 있다.  
이 폴더엔 `route.ts` 라는 Next.js 라우트 핸들러가 포함되어 있으며, 이 핸들러를 사용해서 데이터베이스에 데이터를 저장할 수 있다.

이 과정은 데이터베이스를 채우기 위해 브라우저에서 접근할 수 있는 서버 사이드 엔드포인트를 생성한다.

그리고 나서 `pnpm run dev` 가 실행되어 있는지 확인한 후,  
`localhost:3000/seed`로 접근하면 `"Database seeded successfully"` 라는 text가 브라우저에 표시되면 해당 파일은 삭제해도 무방하다.

> !참고 : `seeding` 이란,  
> 데이터베이스에 초기 데이터 집합을 채우는 것을 의미한다.

> 🐛 문제 해결  
> 스크립트는 사용자의 비밀번호를 hash 하기 위해 `bcrypt`를 사용하는데,  
> `bcrypt`가 환경과 호환되지 않을 경우 스크립트를 `bcryptjs`로 업데이트 할 수 있음.

> 데이터베이스 시드 중에 문제가 발생해서 스크립트를 다시 실행하고 싶다면  
> 데이터베이스 쿼리 인터페이스에서 `DROP TABLE tablename`을 실행하여 기존 테이블을 삭제할 수 있음!

### Exploring your database
