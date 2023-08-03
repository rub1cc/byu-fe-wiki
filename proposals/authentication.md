# Proposed Solution

**Session object**
```js
{
  token?: {
    ruby: string
    lexi: string
  }
  user?: User
}
```

**User object**
```js
{
  id: string
  name: string
  account_status: PAID 
    | ACTIVATED 
    | EXPIRED 
    | ORDERED 
    | REGISTERED 
    | PENDING_PAYMENT 
    | SUSPENDED
  msisdn: string
}
```

## useSession()

- client side: Yes
- server side: No

The `useSession()` React Hook is the easiest way to check if someone is signed in.

```js
import { useSession } from "@byu/auth"
import { useRouter } from "next/router"

export default function ProtectedPage() {
  const router = useRouter()
  const { data: session } = useSession()

  useEffect(() => {
    if(!session) {
      router.push('/login')
    }
  }, [session])

  return <p>Hi, welcome ${session.user.name}</p>
}
```

`useSession()` returns an object containing one value: `data`:

- **`data`**: This can be three values: **`Session`** / `undefined` / `null`.
    - when the session hasn't been fetched yet, `data` will be `undefined`
    - in case it failed to retrieve the session, `data` will be `null`
    - in case of success, `data` will be **`Session`**.
 
## getSession()

- client side: Yes
- server side: Yes

The `getSession()` helper should be called client side only to return current active session.

```js
async function getServerSideProps() {
  const session = await getSession()
  /* ... */
}
```

## Protected routes

### Scenario 1:

- user access `/v2/home` page
- user must authenticated, if not redirect to `/login` page
- user will get different content based on their `account_status`

```js
import { useSession } from "@byu/auth"
import { useRouter } from "next/router"

export default function Home() {
  const router = useRouter()
  const { data: session } = useSession()

  useEffect(() => {
    if(!session) {
      router.push("/login")
      return
    }
  }, [session])

  const renderContent = (account_status, router) => {
    switch(account_status) {
      case REGISTERED:
      /* ... */
      case ORDERED:
      /* ... */
      case EXPIRED:
      /* ... */
      case PAID:
      /* ... */
      case PENDING_PAYMENT:
      /* ... */
    }
  }
  
  return renderContent()
}
```
