
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
- ✅ add this callback url 
For development: http://localhost:3000/api/auth/callback/google

- add client api and secret key 

GOOGLE_CLIENT_ID=
GOOGLE_CLIENT_SECRET=

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
           ✅ onClick={() => signIn("google",{
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

## 54-3 Access session user & logout safely

- Next auth has set the user information as token. For getting the user information next auth gives us a function
- Update in Env

```
NEXT_PUBLIC_BASE_API=
#  as we are not using in component level so we do not need to write NEXT_PUBLIC at initial
GOOGLE_CLIENT_ID=
GOOGLE_CLIENT_SECRET=
AUTH_SECRET=
```

- Add Secret to Auth Options

```tsx
import GoogleProvider from "next-auth/providers/google";

export const authOptions = {
  // Configure one or more authentication providers
  providers: [
    GoogleProvider({
      clientId: process.env.GOOGLE_CLIENT_ID as string,
      clientSecret: process.env.GOOGLE_CLIENT_SECRET as string,
    }),
  ],
  secret: process.env.AUTH_SECRET,
};
```

- now grab the user Info

```tsx
import { authOptions } from "@/helpers/authOptions";
import { getServerSession } from "next-auth";

export default async function DashboardHome() {
  const quote = "The secret of getting ahead is getting started. – Mark Twain";

  const session = await getServerSession(authOptions);

  console.log(session);

  return (
    <div className="min-h-screen flex flex-col justify-center items-center bg-gray-50 p-6 w-full">
      <h1 className="text-4xl font-bold text-gray-800 mb-4">
        Welcome, {session?.user?.name}!
      </h1>
      <h1 className="text-4xl font-bold text-gray-800 mb-4">
        {session?.user?.email}
      </h1>
      <p className="text-lg text-gray-600 italic text-center">{quote}</p>
    </div>
  );
}
```

#### Lets Do the Signout

- For signout next auth has also gave us a function

```tsx
"use client";

import Link from "next/link";
import { Button } from "@/components/ui/button";
import { Home, PlusCircle, LogOut } from "lucide-react";
import { signOut } from "next-auth/react";

export default function Sidebar() {
  return (
    <aside className="flex h-screen w-64 flex-col border-r bg-black text-white">
      {/* Top navigation */}
      <nav className="flex-1 space-y-2 p-4">
        <Link
          href="/"
          className="flex items-center gap-2 rounded-lg px-3 py-2 text-sm font-medium hover:bg-gray-100 hover:text-black"
        >
          <Home className="h-4 w-4" />
          Home
        </Link>

        <Link
          href="/dashboard/create-blog"
          className="flex items-center gap-2 rounded-lg px-3 py-2 text-sm font-medium hover:bg-gray-100 hover:text-black"
        >
          <PlusCircle className="h-4 w-4" />
          Create Blog
        </Link>
      </nav>

      {/* Bottom action */}
      <div className="p-4 border-t border-gray-500">
        <Button
          variant="destructive"
          className="w-full justify-start gap-2 cursor-pointer"
          onClick={() => {
            signOut();
          }}
        >
          <LogOut className="h-4 w-4" />
          Logout
        </Button>
      </div>
    </aside>
  );
}
```

- just `signOut()` call it and done logout

#### Now lets see how we can get the session in client component?

- For getting the session in client component we will create a provider and wrap the entire application
- src -> providers -> AuthProviders

```tsx
"use client";
import { SessionProvider } from "next-auth/react";
import React from "react";

const AuthProviders = ({ children }: { children: React.ReactNode }) => {
  return <SessionProvider>{children}</SessionProvider>;
};

export default AuthProviders;
```

- app -> layout.tsx

```tsx
import AuthProviders from "@/providers/AuthProviders";
import type { Metadata } from "next";
import { Geist, Geist_Mono } from "next/font/google";
import "./globals.css";

const geistSans = Geist({
  variable: "--font-geist-sans",
  subsets: ["latin"],
});

const geistMono = Geist_Mono({
  variable: "--font-geist-mono",
  subsets: ["latin"],
});

export const metadata: Metadata = {
  title: "Next Blog",
  description: "A simple blog built with Next.js, Tailwind CSS, and shadcn/ui.",
};

export default function RootLayout({
  children,
}: Readonly<{
  children: React.ReactNode;
}>) {
  return (
    <html lang="en">
      <body
        className={`${geistSans.variable} ${geistMono.variable} antialiased`}
      >
        <AuthProviders>{children}</AuthProviders>
      </body>
    </html>
  );
}
```

- here auth provider is a client component. we are wrapping all pages with a client component that does not mean all are becoming client component. Relax !

- now lets get the user info in a client component

```tsx
"use client";

import Link from "next/link";
import { Button } from "@/components/ui/button";
import { Home, PlusCircle, LogOut } from "lucide-react";
import { signOut, useSession } from "next-auth/react";

export default function Sidebar() {
  const session = useSession();

  return (
    <aside className="flex h-screen w-64 flex-col border-r bg-black text-white">
      {/* Top navigation */}
      <nav className="flex-1 space-y-2 p-4">
        <Link
          href="/"
          className="flex items-center gap-2 rounded-lg px-3 py-2 text-sm font-medium hover:bg-gray-100 hover:text-black"
        >
          <Home className="h-4 w-4" />
          Home
        </Link>

        <Link
          href="/dashboard/create-blog"
          className="flex items-center gap-2 rounded-lg px-3 py-2 text-sm font-medium hover:bg-gray-100 hover:text-black"
        >
          <PlusCircle className="h-4 w-4" />
          Create Blog
        </Link>
      </nav>

      {/* Bottom action */}
      <div className="p-4 border-t border-gray-500">
        {session.status === "authenticated" && (
          <Button
            variant="destructive"
            className="w-full justify-start gap-2 cursor-pointer"
            onClick={() => signOut()}
          >
            <LogOut className="h-4 w-4" />
            Logout
          </Button>
        )}
      </div>
    </aside>
  );
}
```

#### For server component lets separate the function

- helpers -> getUserSession.ts

```ts
import { getServerSession } from "next-auth";
import { authOptions } from "./authOptions";

export const getUserSession = async () => await getServerSession(authOptions);
```

- dashboard -> page.tsx

```tsx
import { getUserSession } from "@/helpers/getUserSession";

export default async function DashboardHome() {
  const quote = "The secret of getting ahead is getting started. – Mark Twain";

  const session = await getUserSession();

  return (
    <div className="min-h-screen flex flex-col justify-center items-center bg-gray-50 p-6 w-full">
      <h1 className="text-4xl font-bold text-gray-800 mb-4">
        Welcome, {session?.user?.name ?? "Guest"}!
      </h1>
      <h1 className="text-4xl font-bold text-gray-800 mb-4">
        {session?.user?.email ?? ""}
      </h1>
      <p className="text-lg text-gray-600 italic text-center">{quote}</p>
    </div>
  );
}
```

## 54-4 Protect a route from unauthorized users using middleware

- The middleware.js|ts file is used to write Middleware and run code on the server before a request is completed. Then, based on the incoming request, you can modify the response by `rewriting`, `redirecting`, `modifying` the request or `response headers`, or `responding directly`.

- Middleware executes before routes are rendered. It's particularly useful for implementing custom server-side logic like authentication, logging, or handling redirects.

- Create a middleware.ts (or .js) file in the project root, or inside src if applicable, so that it is located at the same level as pages or app.
- src -> middleware.ts

```ts
export const middleware = async () => {
  console.log("Hello From Middleware!");
};
```

- For each and every route this will be triggered and consoled.
- But if we want to trigger for specific routes

```ts
export const middleware = async () => {
  console.log("Hello From Middleware!");
};

export const config = {
  matcher: ["/about"],
};
```

- will be triggered in about page only

```ts
import { NextResponse, NextRequest } from "next/server";

// This function can be marked `async` if using `await` inside
export function middleware(request: NextRequest) {
  return NextResponse.redirect(new URL("/", request.url));
}

export const config = {
  matcher: "/about",
};
```

- next auth made us own middleware [Next-auth-middleware](https://next-auth.js.org/configuration/nextjs#middleware)
- middleware.ts

```ts
export { default } from "next-auth/middleware";

export const config = { matcher: ["/dashboard"] };
```

- here dashboard route has become private and behind the scene the token works are done by next auth.
- for redirecting the to the login page we have to mention THE PAGE `/login`
- helpers -> authOptions.ts

```ts
import GoogleProvider from "next-auth/providers/google";

export const authOptions = {
  // Configure one or more authentication providers
  providers: [
    GoogleProvider({
      clientId: process.env.GOOGLE_CLIENT_ID as string,
      clientSecret: process.env.GOOGLE_CLIENT_SECRET as string,
    }),
  ],
  secret: process.env.AUTH_SECRET,
  pages: {
    signIn: "/login",
  },
};
```
## 54-5 Register users using Next.js server actions and a custom server

- registerForm.tsx

```tsx
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

type RegisterFormValues = {
  name: string;
  email: string;
  phone: string;
  password: string;
};

export default function RegisterForm() {
  const form = useForm<RegisterFormValues>({
    defaultValues: {
      name: "",
      email: "",
      phone: "",
      password: "",
    },
  });

  const onSubmit = (values: RegisterFormValues) => {
    console.log("Form submitted:", values);
  };

  return (
    <div className="flex justify-center items-center min-h-screen bg-gray-50">
      <Form {...form}>
        <form
          onSubmit={form.handleSubmit(onSubmit)}
          className="space-y-6 w-full max-w-md bg-white p-8 rounded-lg shadow-md"
        >
          <h2 className="text-3xl font-bold text-center">Register Now</h2>
          {/* Name */}
          <FormField
            control={form.control}
            name="name"
            render={({ field }) => (
              <FormItem>
                <FormLabel>Name</FormLabel>
                <FormControl>
                  <Input placeholder="Enter your name" {...field} />
                </FormControl>
                <FormMessage />
              </FormItem>
            )}
          />
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
          {/* Phone */}
          <FormField
            control={form.control}
            name="phone"
            render={({ field }) => (
              <FormItem>
                <FormLabel>Phone</FormLabel>
                <FormControl>
                  <Input placeholder="Enter your phone number" {...field} />
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
            Register
          </Button>

          <p className="text-center text-sm text-gray-500 mt-4">
            Already have an account?{" "}
            <Link href="/login" className="text-blue-500 hover:underline">
              Login
            </Link>
          </p>
        </form>
      </Form>
    </div>
  );
}
```

- src -> actions -> auth.ts

```ts
"use server";

import { FieldValues } from "react-hook-form";

export const register = async (data: FieldValues) => {
  const res = await fetch(`${process.env.NEXT_PUBLIC_BASE_API}/user`, {
    method: "POST",
    headers: {
      "Content-Type": "application/json",
    },
    body: JSON.stringify(data),
  });

  if (!res?.ok) {
    console.log("User Registration Failed!", await res.text());
  }

  return await res.json();
};
```
- wrap with sonner toast in main layout 

```tsx 
import AuthProviders from "@/providers/AuthProviders";
import type { Metadata } from "next";
import { Geist, Geist_Mono } from "next/font/google";
import "./globals.css"
import { Toaster } from "sonner";


const geistSans = Geist({
  variable: "--font-geist-sans",
  subsets: ["latin"],
});

const geistMono = Geist_Mono({
  variable: "--font-geist-mono",
  subsets: ["latin"],
});

export const metadata: Metadata = {
  title: "Next Blog",
  description: "A simple blog built with Next.js, Tailwind CSS, and shadcn/ui.",
};

export default function RootLayout({
  children,
}: Readonly<{
  children: React.ReactNode;
}>) {
  return (
    <html lang="en">
      <body
        className={`${geistSans.variable} ${geistMono.variable} antialiased`}
      >
        <AuthProviders>\
          {/* using toster sonner */}
          <Toaster richColors position="top-center" />
          {children}
        </AuthProviders>
      </body>
    </html>
  );
}

```
- using the auth function and route redirect works done in RegisterForm.tsx
```tsx 
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
import { register } from "@/actions/auth";
import { toast } from "sonner";
import { useRouter } from "next/navigation";

type RegisterFormValues = {
  name: string;
  email: string;
  phone: string;
  password: string;
};

export default function RegisterForm() {
  const form = useForm<RegisterFormValues>({
    defaultValues: {
      name: "",
      email: "",
      phone: "",
      password: "",
    },
  });
  const router = useRouter()
  const onSubmit = async (values: RegisterFormValues) => {
    try {
      const res = await register(values)
      if(res?.id){
        toast.success("User Registered Successfully!")
        router.push("/login")
      }
    } catch (error) {
      console.log(error)
    }
  };

  return (
    <div className="flex justify-center items-center min-h-screen bg-gray-50">
      <Form {...form}>
        <form
          onSubmit={form.handleSubmit(onSubmit)}
          className="space-y-6 w-full max-w-md bg-white p-8 rounded-lg shadow-md"
        >
          <h2 className="text-3xl font-bold text-center">Register Now</h2>
          {/* Name */}
          <FormField
            control={form.control}
            name="name"
            render={({ field }) => (
              <FormItem>
                <FormLabel>Name</FormLabel>
                <FormControl>
                  <Input placeholder="Enter your name" {...field} />
                </FormControl>
                <FormMessage />
              </FormItem>
            )}
          />
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
          {/* Phone */}
          <FormField
            control={form.control}
            name="phone"
            render={({ field }) => (
              <FormItem>
                <FormLabel>Phone</FormLabel>
                <FormControl>
                  <Input placeholder="Enter your phone number" {...field} />
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
            Register
          </Button>

          <p className="text-center text-sm text-gray-500 mt-4">
            Already have an account?{" "}
            <Link href="/login" className="text-blue-500 hover:underline">
              Login
            </Link>
          </p>
        </form>
      </Form>
    </div>
  );
}
```