# What Is React and Next

React is a Java Script Library for building interactive user interfaces. A user interface can be broken down into smaller building blocks called components. Components serve as the basis to React.

Next is a framework build on top of React that gives you building blocks to create fast web application.

To setup a React project you need to have Node installed because React is a Java Script library. Node is the **JavaScript runtime environment** that allows you to run JavaScript **outside of a web browser**. You can download Node from the [node.js](https://nodejs.org/en) website.

Setting up a React and Next project. Next.js comes with React built-in, so you don’t need to install React separately.
```bash
npx create-next-app@latest projectName
```

The CLI will ask you some questions, choose according to your needs. For a beginner, the defaults are fine.

Go into your project folder and run the development server to start your project. Every time you want to run your app you can use this command.
```bash
npm run dev
```

This will start the Next.js dev server. Open your browser and go to [localhost](http://localhost:3000),  you should see your Next.js app running.

---

## React Project Structure

Lets go through the folder structure of a React and Next project. This will help with understanding everything else.
```
React Project
├── app
|	├── layout.tsx
|	└── page.tsx
│
├── assets
|	└── images
|
├── components
│   └── Component.tsx
|
├── public
|
├── services
│   └── ComponentService.ts
|
├── styles
│   └── global.css
│	
└──types
	└── index.ts
```

---

## Components Basics

Each component is its own sperate Type Script function in its own separate file. Components have properties called props which are passed to the function.

Component file in components directory.
```tsx
type Props = {
	name: string;
};

const Greeting: React.FC<Props> = ({name}: Props) => {
	return (
		<h1> Hello, {name}! </h1>
	);
};

export default Greeting;
```

The return value of this function is in JSX format which basically allows you to write html code inside a Type Script function and then Type Script inside html code.

The example above uses object destructuring like we learned in JavaScript. If you want to use the same method without object destructuring, this is what it would look like.
```tsx
const Greeting: React.FC<Props> = (props: Props) => {
  return <h1>Hello, {props.name}!</h1>;
};
```

Rendering a component is the process where react converts the component into HTML. React renders a component the first time it comes on screen.

React determines when to re-render a component based only on specific triggers.
- A change in state.
- A change in props, data passed from a parent component.  
- A parent component re-rendering.

---

## Components With Global Types

You can also pass prespecified global types as properties or props to a component function. The type should be declared in the index file in the types directory.

Index file in the types directory
```ts
export type Person = {
  id: number;
  name: string;
  age: number;
};
```

Component file in components directory.
```tsx
import React from 'react';
import { Person } from '@types';

type Props = {
	person: Person;
};

const Greeting: React.FC<Props> = ({person}: Props) {
	return (
		<h1> Hello, {person.name}! </h1>
	);
};

export default Greeting;
```

---
## Home Page

Our home page is the page file in the app directory, this basically acts as `index.html`. So now we need to render our component on our homepage.

Page file in the app directory.
```tsx
import Greeting from '@components/Greeting';

const Home = () => {
	return (
	    <div>
	      <Greeting person={data} />
	    </div>
	);
};

export default Home;
```

---

## Layout Page

The layout page is a built in page in React and Next. It basically represents what stays the same on all pages like the header or footer.

The header and footer should still be written as component functions like this.
```tsx
import Link from 'next/link';
import Image from "next/image";
import logo from '@assets/images/logo.png';

const Header: React.FC = () => {
	return (
		<header>
			<div>
				<Image src={logo} alt="Logo" />
			</div>
			<nav>
				<Link href="/" /> Name </Link>
				<Link href="/" /> Name </Link>
			</nav>
		</header>
	);
};
```

When making navigation menus in React, we use the Next `<Link>` tag. The tag automatically knows you are trying to call another page in app, so all you can to do is just call the page using `/page` without specifying the whole path.

When using images we use the Next `<Image>` tag. When using images in React, we use the assets directory. You can also use the public directory but it is not considered best practice.

Layout file in app directory.
```tsx
import Header from '@components/header';

const RootLayout = async ({ children }: { children: React.ReactNode }) => {
	return (
		<html lang="en">
			<body>
				<Header />
			</body>
		</html>
	);
};

export default RootLayout;
```

---

## Global CSS In React

You can create a global CSS file for your global styles in the styles directory. Then in your component you can call your classes using `className="name"`.

Global.css file in the styles directory.
```css
.greeting {
  color: teal;
  font-size: 24px;
  background-color: lightgray;
  padding: 10px;
}
```

Component file in the components directory.
```tsx
import '@styles/global.css';

const Greeting = ({ name }: { name: string }) => {
  return <h1 className="greeting">Hello, {name}!</h1>;
};

export default Greeting;
```

---

## Module CSS In React

You can also create page specific CSS styles. You just need to call the file `page.module.css`. Then in your component you can call your classes using `className={styles.name}`.

Greetings.module.css file in the styles directory.
```css
.title {
  color: teal;
  font-size: 24px;
  background-color: lightgray;
  padding: 10px;
}
```

Component file in the components directory.
```tsx
import '@styles/greeting.module.css';

const Greeting = ({ name }: { name: string }) => {
  return <h1 className={styles.title}>Hello, {name}!</h1>;
};

export default Greeting;
```

This is more scoped and avoids global conflicts. It also supports **pseudo-classes** like `:hover`.

---

## Tail Wind CSS

**Tailwind CSS** is a utility-first CSS framework, and it works very well with React. Instead of writing separate CSS files, you use **predefined class names** directly in your JSX.

Installing  Tailwind.
```bash
npm install -D tailwindcss postcss autoprefixer
npx tailwindcss init -p
```

Add Tailwind to your global.css file in the styles directory.
```css
@tailwind base;
@tailwind components;
@tailwind utilities;
```

Component file in the components directory.
```tsx
const Greeting = ({ name }: { name: string }) => {
  return <h1 className="write your tailwind here">Hello, {name}!</h1>;
};

export default Greeting;
```

Tailwind is really fast once you get used to it, because **no extra CSS files** are needed and everything is in JSX.

---

## Client Side Vs Server Side Components

**Client-Side Components are components that run **in the browser**. They handle user interactions, state, and lifecycle methods. A client component always has the `"use client"` annotation on top of the file.
- Rendered **on the client**.
- Can use **hooks** like `useState`, `useEffect`, `useContext`.
- Can handle **interactivity** (clicks, forms, animations, etc.).
- Are bundled in the client JavaScript, which increases the download size.

**Server-Side Components are components that run on the server**. They render to HTML before being sent to the client. The components are typically faster and do not display  `"use client"`  on top of the file.
- Rendered **on the server** during the initial request.
- Cannot use **client hooks** like `useState` and `useEffect`, because the server doesn’t handle interactivity.
- Can fetch data directly from a **database or API** without exposing secrets to the client.
- Reduces client bundle size because the JS for the component **doesn’t get sent to the browser**.

In modern React the common pattern is to  combine both components. **Server Components** to fetch data, render static content. and **Client Components** to add interactivity on top of server rendered content.
- If the component **needs user interaction** → client.
- If it **just fetches and displays data** → server.
- Mix them wisely: **server for performance + client for interactivity**

---

## Client Side Component Hooks 

Hooks are **special functions** in React that let you “hook into” React features like state, lifecycle, and context **without using class components**. Only usable in **client side components**. Hooks are only called on the top level, never inside loops or conditions.

Useful hooks in React.
- `useState` Adds **state** to functional components. Returns `[value, setter]`.
- `useEffect` Tells the code inside it to run when a component is mounted. Is used for interaction with an external system like browser storage.
- `useInterval` Is a custom hook to implement polling.
- `useRouter` Is used to help you with page redirection.
- `usePathname` Is used to return the value of your current URL as a string.
- `useSWR` Is used for client-side API requests and should be imported.

### Use State Hook
In React, when creating a local variable in a component, we should always give it a state using the `useState` hook. This is because React is a stateful language. Giving variables states, helps the component to remember and keep track of a variable. 

Syntax.
```tsx
const [variable, setVariable] = useState<returnValues>(initialValues);
```

Example.
```tsx
const [name, setName] = useState<String | null>('');
```

### Use Effect Hook

On the other hand `useEffect` is a hook to perform side effects that are outside the scope of the  
component rendering like reading and updating browser storage . Only call `useEffect` to interact with an external system, otherwise use state and event handlers.

This call of `useEffect` has no dependency array as second parameter, so it is called  
after every render during mount and update. The function inside executes when the side effect occurs.
```tsx
useEffect(() => {console.log("Component rendered"); });
```

With an empty dependency array as second parameter, only executed once, after the first  
render, so in the mount phase.
```tsx
useEffect(() => {console.log("Component mounted"); }, []);
```

With one or more elements in the dependency array, executed in mount phase and after  
every render in the update phase, but only when there is a change in one of the elements  
of the array like username in the example. This does not work across components, so basically `username` must be a **state variable (`useState`) or prop** inside that component.
```tsx
useEffect(() => {console.log("UserName changed so run effect"); }, [username]);
```

Helpful summary of all cases.
- `useEffect(() => {}, [])` → mount only.
- `useEffect(() => {}, [x])` → mount + when `x` changes or updates.
- `useEffect(() => {})` → every render.
- `return () => {}` → runs on unmount.

### Use Interval Hook

The `useInterval` hook is a custom hook that leverages java Script `setInterval` behind the screens.

The first parameter of `useInterval` is a callback function that is  executed after every interval specified by the second parameter.

Installing the hook.
```bash
npm install use-interval
```

Syntax.
```tsx
useInterval(function, intervalInMiliseconds);
```

### Use Router Hook

The hook `useRouter` helps you navigate to other places in your app. So lets say a button was pushed and you need to get sent to another page you need this hook.

Syntax.
```tsx
const router = userRouter();
```

Then you can use it in this way. The example below pushes you to the home page.
```tsx
rounter.push("/");
```

### Use Path Name Hook

The `usePathname` hook returns a pathname that returns the value of your current url as a string.

Syntax.
```tsx
const pathname = usePathname();
```

This doesn’t sound very useful, but you can use it to trigger a `useEffect` on the url change for example.
```tsx
useEffect(() => {console.log("Pathname changed to:" pathname); }, [pathname]);
```

### Use SWR Hook

The `useSWR` hook stands for **“stale-while-revalidate”**. Think of it as a **smart alternative to `useEffect` combined with `useState` for fetching data**. The` useSWR` hook is used for client-side API requests. The `useEffect` hook is used for interaction with an external system like browser storage.

Installing the hook.
```bash
npm install swr
```

SWR automatically caches data for the given key `"/api/data"`, and re-fetches it in the background when needed.
```tsx
const fetcher = async () => {
  const res = await fetch("/api/data");
  return res.json();
};

const { data, error, isLoading } = useSWR("/api/data", fetcher);
```

Function options:
- `data` → the fetched data.
- `error` → any error during fetch.
- `isLoading` → whether the data is still loading.

---

## Login Forms And Binding State To An Input Field

States are useful for forms like login pages, when someone types in an input field, you want to save what he typed into a stateful variable to then be processed. Below is an example of a typical login form and its functionality.

Example of a login form.
```tsx
const LoginForm: React.FC = () => {
	const [name, setName] = useState<String | null>('');
	const [error, setError] = useState("");
	
	const handleSubmit = async (event: React.FormEvent<HTMLFormElement>) => {
		event.preventDefault();
		    
		if (!name.trim()) {
			setError("Please fill in the name field.");
			return;
		}
		
		const role = await loginUser({ username, password });
		
		if (role) {
			setError("");
			sessionStorage.setItem("userRole", role);
		} else {
			setError("Invalid username or password");
		}
	};
	
	retrun (
		<form onSubmit = {handlesSubmit}>
			<label>Name:</label>
			<input
				type = "text"
				value = {name}
				placeholder="Your Name"
				onChange = {(event) => setName(event.target.value)}
			/>
			{error && <p>{error}</p>}
			<button type="submit"> Login </button>
		</form>
	);
};

export default LoginForm;
```

In react we call that event function a callback function. The example above shows how we can set the value of an input field to a stateful variable in React.

The actual logic comes form the User Service file in the services directory. The User Service file contains the function `loginUser` which we use in our `handleSubmit` function. Data handling should always be done in the services directory. Below is an example of a services file with hard coded data. 

Example of a service file for a login form.
```ts
export type UserRole = "admin" | "user" | null;

type Credentials = {
	name: string;
};

// Hard Coded user data, could later come from an API or backend
const users = [
	{name: "admin", role: "admin"},
	{name: "user", role: "user"},
];

const loginUser = async ({name}: Credentials): Promise<UserRole> => {
	
	const res = await fetch(`url`);
    
    if (!res.ok) {
      throw new Error("Failed to fetch user data");
    }

    const users: { name: string; role: UserRole }[] = await res.json();

    // Find the user from fetched data
    const foundUser = users.find((user) => user.name === name);

    return foundUser ? foundUser.role : null;
};

export default loginUser;
```

Example of a valid url for the fetch method above.
`https://api.example.com/users?name=${name}`

---

## Client Side Storage

Browser storage is a mechanism to store client-side data instead of only relying on server-side storage. We did this in the login form component above. Store data that is safe to store client-side and that can be used across your entire React application with all its components (no passwords). You can only interact with browser storage in client components like in the examples below.

Adding an entry.
```tsx
sessionStorage.setItem("username", name);
```

Adding an entry.
```tsx
setUsername(sessionStorage.getItem("username"));
```

Deleting an entry.
```tsx
sessionStorage.removeItem("username");
```

Deleting everything.
```tsx
sessionStorage.clear();
```

Cookies are also a mechanism to store client-side data. You can use them to store small pieces of data and not just single values. In contrast to browser storage, cookies are sent back and forth to the server with every request: network performance impact. Cookies can have a custom expiration time. Cookies can be subject to security and privacy concerns like cross-site scripting, session hijacking, tracking and theft. You can secure cookies with `HttpOnly` and `SameSite`.

---

## App Router 

In the App directory some file names are reserved.
- `page.tsx` is the main entry point for each folder.  
- `layout.tsx` contains the wrapping layout. If we don't provide one, the autogenerated layout.js in the root app folder is used. 
- `head.tsx` used to customize the head of the page including metatags and the title.  
- `error.tsx` is used in data fetching.  
- `not-found.tsx` is used to throw a 404 error .
- `loading.tsx` is used to display loading.

In the app directory you can include dynamic routing, just like how we add params to a path in spring boot making it dynamic. This servers as a good transition to Back end development. In the app router to add params to your paths all you have to do is add the params as folders between brackets.

Example `/folder/param1/param2` will open the specified `page.tsx`.
```
React Project
└── app
	├── folder
	|	└── [param1]
	|		└── [param2]
	|			└── page.tsx
	|			
	├── layout.tsx
	└── page.tsx
```

In order to use the values of the params in your component you will need to assign the params as props to pass to your component.
```tsx
type Props = {
	params: Promise<{
		param1: string;
		param2: string;
	}>;
};

const Greeting = async ({ params }: Props) => {
  // Example: fetch data from an API based on params
  const fetchData = async () => {
    const res = await fetch(`url`);
    if (!res.ok) {
      throw new Error("Failed to fetch data");
    }
    return res.json();
  };

  const data = await fetchData();

  return (
    <div>
      <h1>
        Hello, {params.param1} & {params.param2}!
      </h1>
      <p>Fetched Data: {JSON.stringify(data)}</p>
    </div>
  );
};

export default Greeting;
```

Example of a valid URL for the fetch method above.
`https://api.example.com/data?param1=${params.param1}&param2=${params.param2}`

---

## Parent And Child Component Communication.

In React, **data flows downwards** from a parent component to a child component using **props**. Think of props like function arguments for components.
```tsx
// Parent component
const Parent = () => {
  const message = "Hello from parent!";
  return <Child text={message} />;
};

// Child component
const Child = ({ text }: { text: string }) => {
  return <p>{text}</p>;
};
```

Additionally, a **parent passes a callback function to a child via props**, and the child calls that callback function to **send information back**. 
```tsx
// Parent component
const Parent = () => {
	const handleChildClick = () => {
	    alert("Child clicked the button!");
    };
    
    return <Child onClick={handleChildClick} />;
};

// Child component
const Child = ({ onClick }: { onClick: () => void }) => {
    return <button onClick={onClick}>Click Me</button>;
};

```

---

## Event Listeners

**Event listeners** are how JavaScript **reacts to user actions** like clicks, typing, hovering, and submitting forms.
```tsx
function Button() {
  const handleClick = () => {
    console.log("Clicked!");
  };

  return <button onClick={handleClick}>Click me</button>;
}

```

---

## React Context

**React Context** is a mechanism for sharing data across multiple components without passing props manually at every level. It is used to avoid _prop drilling_, where props must be passed through many intermediate components.  

Context provides a global-like state that can be accessed by any component within a specific part of the component tree. A Context consists of a **Provider**, which stores the data, and **Consumers**, which read the data.  

When the context value changes, all subscribed components automatically re-render. React Context is commonly used for authentication state, theme settings, and language preferences. It is best suited for data that changes infrequently and is needed by many components.

This is a simple example of how react context can be used for authentication as it is a global procedure you will need in most files. The following is a react context file.
```tsx
export const AuthContext = createContext(null);

export const AuthProvider = ({ children }) => {
  const [user, setUser] = useState(null);

  return (
    <AuthContext.Provider value={{ user, setUser }}>
      {children}
    </AuthContext.Provider>
  );
};
```

Now you have to wrap your root layout in that react context file.
```tsx
export const RootLayout = async ({ children }: { children: React.ReactNode }) => {
	return (
		<html lang="en">
			<body>
				<AuthProvider>
					<Header />
				</AuthProvider>
			</body>
		</html>
	);
};
```

Finally, here is an example of using the context in a component.
```tsx
import { useContext } from "react";
import { AuthContext } from "../context/AuthContext";

const Profile = () => {
  const { user, setUser } = useContext(AuthContext);

  return (
    <div>
      <p>{user ? user.name : "Not logged in"}</p>
      <button onClick={() => setUser({ name: "Bernard" })}>
        Login
      </button>
    </div>
  );
};

export default Profile;
```

---

## Security 

This brings us to front end security. Our backend expects an authentication JWT Token whenever an API request is made from the front end. 
### Client Components 

When you fetch in your services file you have to specify `credentials: 'include'`.
```ts
const authenticate = async (user: User): Promise<User> => {
  const response = await fetch("/users/login", {
    method: "POST",
    headers: {
      "Content-Type": "application/json"
    },
    body: JSON.stringify(user),
    credentials: 'include'
  });

  const data = await response.json();
  console.log(data);
  return data;
}
```

Then in the component call you service.
```tsx
const user = await authenticate({ username, password });
```
### Server Components

Server components are rendered on the server so they have no access to browser cookie storage. We need to manually send the JWT token in an Authorization header. 
The java back-end supports both scenarios, authentication using an Authorization  
header or secure cookies.
```ts
const getAllLecturers = async (cookies: ReadonlyRequestCookies) => {
  const token = cookies.get('authToken')?.value;
  
  const response = await fetch("/lecturers", {
    method: "GET",
    headers: {
      "Content-Type": "application/json"
      Authorization: "Bearer ${token}"
    },
    credentials: 'include'
  });

  const data = await response.json();
  console.log(data);
  return data;
}
```

Then in the component call your service.
```tsx
const cookieStore = await cookies();
const lecturers = await LecturerService.getAllLecturers(cookieStore);
```

---
