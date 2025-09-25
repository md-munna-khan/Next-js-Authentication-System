
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

## 54-2 Implement Google authentication system
Google
Documentation
https://developers.google.com/identity/protocols/oauth2

- ✅first click configuration link and fullfil google cloud api


Configuration
https://console.developers.google.com/apis/credentials

The "Authorized redirect URIs" used when creating the credentials must include your full domain and end in the callback path. For example;

For production: https://{YOUR_DOMAIN}/api/auth/callback/google
- ✅ add this callback url for development
For development: http://localhost:3000/api/auth/callback/google

- /components/Auth/loginForm.tsx
- handle google button 
```ts
"use client";

import React from "react";
import { useForm } from "react-hook-form";
import { Button } from "@/components/ui/button";
import { Input } from "@/components/ui/input";
import Link from "next/link";
import {
  Form,
  FormControl,
  FormField,
  FormItem,
  FormLabel,
  FormMessage,
} from "@/components/ui/form";
import Image from "next/image";
import { signIn } from "next-auth/react";

type LoginFormValues = {
  email: string;
  password: string;
};

export default function LoginForm() {
  const form = useForm<LoginFormValues>({
    defaultValues: {
      email: "",
      password: "",
    },
  });

  const onSubmit = (values: LoginFormValues) => {
    console.log("Login submitted:", values);
  };

  const handleSocialLogin = (provider: "google" | "github") => {
    console.log(`Login with ${provider}`);

  };

  return (
    <div className="flex justify-center items-center min-h-screen bg-gray-50">
      <div className="space-y-6 w-full max-w-md bg-white p-8 rounded-lg shadow-md">
        <Form {...form}>
          <form
            onSubmit={form.handleSubmit(onSubmit)}
            className="space-y-6 w-full max-w-md"
          >
            <h2 className="text-3xl font-bold text-center">Login</h2>

            {/* Email */}
            <FormField
              control={form.control}
              name="email"
              render={({ field }) => (
                <FormItem>
                  <FormLabel>Email</FormLabel>
                  <FormControl>
                    <Input
                      type="email"
                      placeholder="Enter your email"
                      {...field}
                    />
                  </FormControl>
                  <FormMessage />
                </FormItem>
              )}
            />

            {/* Password */}
            <FormField
              control={form.control}
              name="password"
              render={({ field }) => (
                <FormItem>
                  <FormLabel>Password</FormLabel>
                  <FormControl>
                    <Input
                      type="password"
                      placeholder="Enter your password"
                      {...field}
                    />
                  </FormControl>
                  <FormMessage />
                </FormItem>
              )}
            />

            <Button type="submit" className="w-full mt-2">
              Login
            </Button>

            <div className="flex items-center justify-center space-x-2">
              <div className="h-px w-16 bg-gray-300" />
              <span className="text-sm text-gray-500">or continue with</span>
              <div className="h-px w-16 bg-gray-300" />
            </div>
          </form>
        </Form>
        {/* Social Login Buttons */}
        <div className="flex flex-col gap-3 mt-4">
          <Button
            variant="outline"
            className="flex items-center justify-center gap-2"
            onClick={() => handleSocialLogin("github")}
          >
            {/* GitHub */}
            <Image
              src="https://img.icons8.com/ios-glyphs/24/github.png"
              alt="GitHub"
              className="w-5 h-5"
              width={20}
              height={20}
            />
            Login with GitHub
          </Button>

          <Button
            variant="outline"
            className="flex items-center justify-center gap-2"
            onClick={() => signIn("google",{
              callbackUrl:"/dashboard"
            })}
          >
            {/* Google */}
            <Image
              src="https://img.icons8.com/color/24/google-logo.png"
              alt="Google"
              className="w-5 h-5"
              width={20}
              height={20}
            />
            Login with Google
          </Button>
        </div>
        <p className="text-center text-sm text-gray-500 mt-4">
          Don’t have an account?{" "}
          <Link href="/register" className="text-blue-500 hover:underline">
            Register
          </Link>
        </p>
      </div>
    </div>
  );
}
```