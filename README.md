# Next.js Authentication System
GitHub Link: https://github.com/Apollo-Level2-Web-Dev/next-blog-ui/tree/part-2


# Next.js Authentication System 

GitHub Link: https://github.com/Apollo-Level2-Web-Dev/next-blog-ui/tree/part-2

## 54-1 Integrating and setting up NextAuth in Next.js app
- for authentication system we will use `next auth`

```
npm install next-auth
```
- If you're using Next.js 13.2 or above with the new App Router (app/), you can initialize the configuration using the new Route Handlers.

[Guides-For using Route Handler](https://next-auth.js.org/configuration/initialization#route-handlers-app)

- create a folder 

```
/app/api/auth/[...nextauth]/route.ts
```
- same name convention and file name is required. 
- route.ts 

```ts 
import NextAuth from "next-auth"

const handler = NextAuth(
    // authOptions function will be done here. 
)

export { handler as GET, handler as POST }
```

- src -> helpers -> authOptions.ts

```ts 


export const authOptions = {
    // Configure one or more authentication providers
    providers: [],
}

```
- Lets Implement The Google Login First 

[Google-Login-Next Auth Provider](https://next-auth.js.org/providers/google)

- src -> helpers -> authOptions.ts

```ts 
import GoogleProvider from "next-auth/providers/google";

export const authOptions = {
    // Configure one or more authentication providers
    providers: [
        GoogleProvider({
            clientId: process.env.GOOGLE_CLIENT_ID as string,
            clientSecret: process.env.GOOGLE_CLIENT_SECRET as string
        })

    ],
}

```

- /app/api/auth/[...nextauth]/route.ts

```ts 
import { authOptions } from "@/helpers/authOptions"
import NextAuth from "next-auth"

const handler = NextAuth(
    // function will be done here. 
    authOptions
)

export { handler as GET, handler as POST }
```
- .env.local 

```
NEXT_PUBLIC_BASE_API=
#  as we are not using in component level so we do not need to write NEXT_PUBLIC at initial 
GOOGLE_CLIENT_ID=
GOOGLE_CLIENT_SECRET=

```