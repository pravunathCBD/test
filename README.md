# Training Module:

## Tailwind CSS

### Success criteria:
- Should be able to install and set tailwind in a Next.js and React.js project comfortably.
- Should be able to configure tailwind with the config file.
- Should be able to visualize how an UI can be broken down in to components and can be styled using Tailwind CSS.

### Installation and Setup:
- For Next.js:
  - Follow the guide mentioned in the [Tailwind documentation](https://tailwindcss.com/docs/guides/nextjs)
- For React.js using vite:
  - Follow the guide mentioned in the docs for [React + Vite](https://tailwindcss.com/docs/guides/vite)

### Install the official tailwind extension for VS Code. This gives you autocomplete and suggestions for tailwind classes
- Should have the `tailwind.config.ts` file for this to work.

### Demo project:
- Create a todo app in Next.js with Tailwind CSS.
  - The app should have basic CRUD functionality and should be responsive
- Core concepts that would be required to be covered:
  - [Utility-First Fundamentals](https://tailwindcss.com/docs/utility-first)
  - [Handling Hover, Focus, and Other States](https://tailwindcss.com/docs/hover-focus-and-other-states)
  - [Responsive design](https://tailwindcss.com/docs/responsive-design)
  - [Reusing Styles](https://tailwindcss.com/docs/reusing-styles)
  - [Adding custom styles by extending the config](https://tailwindcss.com/docs/adding-custom-styles)
  - **Advanced** [Functions & Directives](https://tailwindcss.com/docs/functions-and-directives)

The above mentioned concepts should be more than enough to get you started.

### Example of extending the tailwind config:

```ts
import type { Config } from 'tailwindcss';

import { tailwind } from '@iee/theme';

const config: Config = {
  content: [
    './src/pages/**/*.{js,ts,jsx,tsx,mdx}',
    './src/components/**/*.{js,ts,jsx,tsx,mdx}',
    './src/app/**/*.{js,ts,jsx,tsx,mdx}',
  ],
  important: true,
  theme: {
    extend: {
      backgroundImage: {
        'tag-gradient': 'linear-gradient(90deg, #00AEEF 0%, #0090D4 100%)',
      },
      boxShadow: {
        'card': '0px 1px 12px 0px rgba(0,0,0,0.04)',
        'card-hover': '0px 1px 12px 3px rgba(0,0,0,0.06)',
      },
      animation: {
        'ping-alt': 'ping-alt 1.5s cubic-bezier(0, 0, 0.2, 1) infinite',
      },
      keyframes: {
        'ping-alt': {
          '75%, 100%': { transform: 'scale(1.5)', opacity: '0.2' },
        },
      },
      screens: {
        '3xl': '1660px',
        '4xl': '1920px',
      },
    },
  },
  presets: [tailwind],

  plugins: [],
};
export default config;
```

As you can see we have added all our custom styles inside the `extend` object in the `theme` object. The keys you provide here will be directly available as utility classes.
Also if you have a common tailwind config and you want to use it in multiple projects then you can use the `presets` property.

- Common config file
```ts
import type { Config } from 'tailwindcss';
import { fontFamily } from 'tailwindcss/defaultTheme';
import { generateColorRecord, primaryColor, secondaryColor } from './color';

// SEE: https://github.com/tailwindlabs/tailwindcss/blob/master/stubs/config.full.js
const tailwind: Config = {
  important: true,
  content: ['./src/**/*.{js,ts,jsx,tsx,mdx}'],
  theme: {
    container: {
      center: true,
    },
    extend: {
      colors: {
        transparent: 'transparent',
        primary: {
          DEFAULT: primaryColor[6],
          ...generateColorRecord(primaryColor),
        },
        secondary: {
          DEFAULT: secondaryColor[9],
          ...generateColorRecord(secondaryColor),
        },
      },
      fontFamily: {
        inter: ['Inter', ...fontFamily.sans],
      },
      fontSize: {
        xxs: ['0.625rem', { lineHeight: '1rem' }],
      },
      backgroundImage: {
        'btn-gradient': 'linear-gradient(90deg, #3DC1FA 0%, #00B3FF 100%)',
      },
    },
  },
  plugins: [],
};

export default tailwind;
```

- Using it using `presets`:
```ts
import { tailwind } from '@iee/theme';

/** @type {import('tailwindcss').Config} */
export default {
  presets: [tailwind],
  content: ['./index.html', './src/**/*.{js,ts,jsx,tsx}'],
  important: true,
  theme: {
    extend: {
      boxShadow: {
        dropdown: '0px 4px 10px 0px rgba(0, 0, 0, 0.12)',
      },
      colors: {
        'primary-accent': '#DEF3FF',
      },
    },
  },
  plugins: [],
};
```

## Tanstack Query

### Success criteria:
- Should be able to install and setup tanstack query in both Next.js and Vite projects
- Should be able to write queries and mutations comfortably.
- Should be able to understand what various properties like `queryKey`, `mutationKey`, `queryFn` and `mutationFn` are. 
- Should be able to understand how to and when to invalidate queries. 

### Installation and Setup:
- Refer to the [installation guide](https://tanstack.com/query/latest/docs/framework/react/installation)
- Setup: This can be done in two ways:
- [Installing and setting up devtools](https://tanstack.com/query/latest/docs/framework/react/devtools)
```tsx
const App = ({ Component, pageProps }: AppPropsWithLayout) => {
  const [queryClient] = useState(() => new QueryClient());

  return (
  <QueryClientProvider client={queryClient}>
        <HydrationBoundary state={pageProps.dehydratedState}>
          <MantineProvider theme={theme} defaultColorScheme="light">
            <DefaultSeo {...seo} />
            <RouterTransition />
            <Notifications />
            <ReactQueryDevtools initialIsOpen={false} />

            {getLayout(<Component {...pageProps} />)}
          </MantineProvider>
        </HydrationBoundary>
      </QueryClientProvider>
  );
};

export default App;
```

```tsx
const queryClient = new QueryClient({});

const App = () => (
  <QueryClientProvider client={queryClient}>
    <MantineProvider theme={theme} defaultColorScheme="light">
      <ColorSchemeScript defaultColorScheme="light" />
      <Notifications position="bottom-right" />
      <ReactQueryDevtools initialIsOpen={false} />

      <Router />
    </MantineProvider>
  </QueryClientProvider>
);

export default App;
```

The difference between these two methods and when to use what can be found here
The difference between these two approaches is where and when the `QueryClient` is initialized and how React's component lifecycle affects the behavior:

#### 1. **`const [queryClient] = useState(() => new QueryClient());` (Inside Component)**
   - **When it's initialized**: `useState` initializes the `QueryClient` inside the component during the component's initial render.
   - **Lifecycle Impact**: Since `useState` retains its value between renders, the `QueryClient` will only be created once during the component's first render and will persist across re-renders.
   - **Advantages**: This method guarantees that the `QueryClient` is created when the component renders for the first time, and it prevents re-creating the `QueryClient` on every render. It also ensures that if the component is conditionally rendered, the `QueryClient` is only initialized when needed.
   - **Lazy Initialization**: The function `() => new QueryClient()` is a lazy initializer. The `QueryClient` is only instantiated when React first runs the component, not during module execution.

```js
const App = () => {
  const [queryClient] = useState(() => new QueryClient());

  return (
    <QueryClientProvider client={queryClient}>
      {/* other components */}
    </QueryClientProvider>
  );
};
```

#### 2. **`const queryClient = new QueryClient({});` (Outside Component)**
   - **When it's initialized**: `queryClient` is initialized when the module is first imported or executed, i.e., before the component is even rendered.
   - **Lifecycle Impact**: Since the `QueryClient` is created outside the component, it only gets created once, when the module loads, and persists for the entire lifetime of the application. It won't be tied to the lifecycle of any specific component, meaning it’s shared across all renders and instances of the component.
   - **Advantages**: This is useful when you want to share the same `QueryClient` across multiple components or when you don't want to tie its lifecycle to any specific component. Since it's defined outside, it ensures the client is created just once for the whole application, not per component instance.
   
```js
const queryClient = new QueryClient({});

const App = () => (
  <QueryClientProvider client={queryClient}>
    {/* other components */}
  </QueryClientProvider>
);
```

#### Key Differences:

1. **Instance Lifetime**:
   - `useState(() => new QueryClient())`: `QueryClient` is created at the first render of the component and only exists as long as the component is mounted.
   - `const queryClient = new QueryClient()`: `QueryClient` is created once when the module is executed and remains available throughout the app’s lifetime.

2. **Component Re-renders**:
   - `useState(() => new QueryClient())`: Even if the component re-renders, the `QueryClient` is not recreated.
   - `const queryClient = new QueryClient()`: The `QueryClient` instance remains unaffected by re-renders since it’s outside the component.

3. **Conditional Rendering**:
   - If the component using `useState` is conditionally rendered (e.g., based on state or props), the `QueryClient` will only be created when that condition is met.
   - The globally defined `QueryClient` will be created regardless of whether the component using it is rendered or not.

#### When to use which:
- **Use `useState(() => new QueryClient())`** if the `QueryClient` should be tied to the lifecycle of the component and you might conditionally render it.
- **Use `const queryClient = new QueryClient()`** if you need the `QueryClient` to exist independently of any component, like when it should be shared across multiple components or globally throughout the app.

### Demo app:
- Create a todo app in Next.js with React Query.
  - The app should have basic CRUD functionality and should be responsive
  - When creating / updating or deleting a todo, invalidate the fetch query so that new data can be displayed in the UI

- The core concepts that would be required to be covered:
  - `QueryClient`, [docs](https://tanstack.com/query/latest/docs/reference/QueryClient)
  - `useQuery`, [docs](https://tanstack.com/query/latest/docs/framework/react/reference/useQuery)
  - `useMutation`, [docs](https://tanstack.com/query/latest/docs/framework/react/reference/useMutation)
  - `useQueryClient`, [docs](https://tanstack.com/query/latest/docs/framework/react/reference/useQueryClient)
  - `useInfiniteQuery`, [docs](https://tanstack.com/query/latest/docs/framework/react/reference/useInfiniteQuery)

- Please check with code examples in the `queries` folder on how to use these above concepts in the example repos shared with you.

## Zustand

### Success criteria:
- Should be able to install and set Zustand in a Next.js and React.js project comfortably.
- Should be able to create stores using the `slices` pattern
- Should be able to persist data in the store using `persist` middleware

### Installation and Setup:
- Follow this guide to setup Zustand, [installation](https://zustand.docs.pmnd.rs/getting-started/introduction)
- [Setup with Next.js](https://zustand.docs.pmnd.rs/guides/nextjs)
- [Usage with typescript](https://zustand.docs.pmnd.rs/guides/typescript)

### Demo project:
- Integrate Zustand in the todo app you have created such that
  - The user data can be stored and accessed from anywhere in the app
  - If the user logs out, the data should be cleared from the store
  - If the user data is updated, it should be reflected in the UI
  - The user data should be persisted such that on page refresh, the data is still available
  - CONDITION: No mock API should be used for this

- Core concepts that would be required for this to be covered:
  - [Updating state](https://zustand.docs.pmnd.rs/guides/updating-state)
  - [Immutability](https://zustand.docs.pmnd.rs/guides/immutable-state-and-merging)
  - [Reset State](https://zustand.docs.pmnd.rs/guides/how-to-reset-state)
  - **MOST IMPORTANT** [Slices Pattern](https://zustand.docs.pmnd.rs/guides/slices-pattern)
  - [Persisting store data](https://zustand.docs.pmnd.rs/integrations/persisting-store-data)

#### Please check the demo example of stores and how they are being used from any of the example repos shared with you. The slices and stores will be present in the `store` folder in the `src` directory.
