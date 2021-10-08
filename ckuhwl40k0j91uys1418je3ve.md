## A better way to structure your React & Redux projects.

## Situation

While learning React, we all started our journey with this kind of file structure.

```
test
├── README.md
├── node_modules
├── package-lock.json
├── package.json
├── public
│   ├── favicon.ico
│   ├── index.html
│   ├── logo192.png
│   ├── logo512.png
│   ├── manifest.json
│   └── robots.txt
└── src
    ├── App.css
    ├── App.js
    ├── App.test.js
    ├── actions
    │   ├── action1.js
    │   └── action2.js
    ├── components
    │   ├── Contact
    │   │   └── index.jsx
    │   └── Home
    │       └── index.jsx
    ├── index.css
    ├── index.js
    ├── logo.svg
    ├── reducers
    │   └── index.js
    ├── reportWebVitals.js
    └── setupTests.js
```

It could be used for small projects. But it can create a problem for large-scale projects to organize files and make re-usable components. Every component can't be kept in a single `components` folder. It will be hard for everyone to search for several files and follow the **DRY (Do Not Repeat)** principle. I tried multiple ways to organize my file structure for my React projects, and here is what I came up with.

## A better way to structure your React & Redux projects.

We will create PWA (Progressive Web App) with CRA (Create React App) template. [Read more](https://create-react-app.dev/docs/making-a-progressive-web-app/)

```bash
npx create-react-app my-app --template cra-template-pwa
```

[Here](https://github.com/ramankarki/react-project-structure) is my boilerplate for our final file structure. You will probably have to look over the code while reading this article.

Let's see an overview of our final file structure. And then, we will go through each file and folder in detail.

```
react-project-structure
├── package-lock.json
├── package.json
├── public
│   ├── assets
│   │   └── code.jpg
│   ├── favicon.ico
│   ├── index.html
│   ├── logo192.png
│   ├── logo512.png
│   ├── manifest.json
│   └── robots.txt
└── src
    ├── App.js
    ├── App.scss
    ├── Routes
    │   ├── lazyPages.js
    │   ├── PageWrapper.jsx
    │   ├── constants.js
    │   ├── index.jsx
    │   ├── routes.js
    │   └── routesHelper.js
    ├── _variables.scss
    ├── actions
    │   ├── constants.js
    │   ├── helper.js
    │   ├── onDelete.js
    │   ├── onGet.js
    │   ├── onPatch.js
    │   └── onPost.js
    ├── appState
    │   ├── auth.js
    │   ├── index.js
    │   └── user.js
    ├── components
    │   ├── ErrorBoundary
    │   │   ├── index.jsx
    │   │   └── index.scss
    │   └── PageLoadingSpinner
    │       ├── index.jsx
    │       └── index.scss
    ├── configureStore.js
    ├── index.js
    ├── pages
    │   ├── Dashboard
    │   │   └── index.jsx
    │   ├── Home
    │   │   └── index.jsx
    │   ├── Login
    │   │   └── index.jsx
    │   ├── PageNotFound
    │   │   └── index.jsx
    │   └── Signup
    │       └── index.jsx
    ├── reducers
    │   └── HOFreducer.js
    ├── service-worker.js
    ├── serviceWorkerRegistration.js
    ├── templates
    │   ├── Footer
    │   │   └── index.jsx
    │   ├── Form
    │   │   └── index.jsx
    │   └── Header
    │       └── index.jsx
    └── utils
        ├── API.js
        ├── actionLogger.js
        ├── dynamicReducers.js
        ├── getQueryObj.js
        └── monitorReducers.js
```

## public/

Inside `public` folder we create `assets` folder and store static assets like images, fonts, etc. We also keep our `favicon.png`, `logo192.png`, `logo512.png`, `manifest.json`, `robots.txt`, etc. inside our `public` folder.

We setup our manifest to make **PWA** that looks something like this,

```json
{
  "short_name": "Shortcut app name",
  "name": "App name",
  "icons": [
    {
      "src": "favicon.png",
      "sizes": "64x64 32x32 24x24 16x16",
      "type": "image/x-icon"
    },
    {
      "src": "logo192.png",
      "type": "image/png",
      "sizes": "192x192"
    },
    {
      "src": "logo512.png",
      "type": "image/png",
      "sizes": "512x512"
    }
  ],
  "start_url": "./index.html",
  "display": "standalone",
  "theme_color": "#000000",
  "background_color": "#ffffff"
}
```

## src/Routes/

We keep all our files and coding logic related to routes inside this folder. The crucial files here are `constants.js` and `routes.js` in this folder.

### constants.js

We create path constants like this,

```javascript
export const ROOT = '/';

export const SIGNUP_PAGE = '/auth/signup';

export const LOGIN_PAGE = '/auth/login';

export const DASHBOARD_PAGE = '/dashboard';

/*
  Add more path down
*/

/*
  Add more path up
*/

export const PAGE_NOT_FOUND = '/*';
```

These can be used in multiple components to redirect the user to another page or update history. We should do it in this way because if we hard code these path names and make any spelling mistakes, it can be hard for us to debug. But if we import this file and make a typo while using these constant variables, we can find that error more quickly.

### routes.js

we create route object like this,

```javascript
import {
  DASHBOARD_PAGE,
  LOGIN_PAGE,
  PAGE_NOT_FOUND,
  ROOT,
  SIGNUP_PAGE,
} from './constants';

const routes = {
  [ROOT]: {
    private: false,
    pageName: 'Home',
  },

  [SIGNUP_PAGE]: {
    private: false,
    pageName: 'Signup',
  },

  [LOGIN_PAGE]: {
    private: false,
    pageName: 'Login',
  },

  [DASHBOARD_PAGE]: {
    private: true,
    pageName: 'Dashboard',
  },

  // Add your routes here

  [PAGE_NOT_FOUND]: {
    private: false,
    pageName: 'PageNotFound',
  },
};

export default routes;
```

We export a routes object which has `key` as `path` and `value` as `Object`. The `Object` has two fields:

- `private` - true if route is protected.
- `pageName` - name of the folder you created under `pages` while creating new page component.

The point of creating `Routes` folder is to get rid of the code that looks something like this,

```javascript
import { connect } from "react-redux";
import { HashRouter, Route } from "react-router-dom";
import {
  root,
  login,
  signup,
  forgotpassword,
  resetpassword,
  emailConfirmation,
  activateAccount,
  conversations,
  updateProfile,
  changePassword,
} from "../utils/Routes";

import Signup from "./AuthComponent/Signup";
import Login from "./AuthComponent/Login";
import Forgotpassword from "./AuthComponent/Forgotpassword";
import Resetpassword from "./AuthComponent/Resetpassword";
import EmailConfirmation from "./AuthComponent/EmailConfirmation";
import ActivateAccount from "./AuthComponent/ActivateAccount";
import Conversations from "./Conversations";
import Homepage from "./Homepage/Homepage";
import Profile from "./Profile";
import ChangePassword from "./ChangePassword";
import "./App.scss";

function App() {
    return (
      <div>
        <HashRouter>
          <Route path={root} exact component={Homepage} />
          <Route path={login} exact>
            <Login />
          </Route>
          <Route path={signup} exact>
            <Signup />
          </Route>
          <Route path={forgotpassword} exact>
            <Forgotpassword />
          </Route>
          <Route path={resetpassword} exact>
            <Resetpassword />
          </Route>
          <Route path={emailConfirmation} exact component={EmailConfirmation} />
          <Route path={activateAccount} exact>
            <ActivateAccount />
          </Route>
          <Route path={conversations} exact component={Conversations} />
          <Route path={updateProfile} exact component={Profile} />
          <Route path={changePassword} exact component={ChangePassword} />
        </HashRouter>
      </div>
  );
}
```

Every time we needed to add a new route for our new page, we had to add a new `Route` component manually and was tricky to manage. That's why we separated our routing logic into a different folder that will generate those routes for us.

*Let's talk about other files inside `src/Routes/`.*

### lazyPages.js

This file has a default export of an `Object`, which has `key` as `page` name and value as `lazy` loading page component. You can see the [code](https://github.com/ramankarki/react-project-structure/blob/main/src/Routes/lazyPages.js) to know how pages are loaded lazily.

### routesHelper.js

This file also has a default export of an `Object`. It has code to merge object returned from `src/Routes/lazyPages.js` and `src/Routes/routes.js` which will be imported and used by `src/Routes/index.jsx`.

### PageWrapper.jsx

It is a react component that will have the logic to prevent access to protected routes from unauthenticated users. If the user is logged in and private property in route object true, they can access the protected routes, or else they will be redirected to the login page. You will be writing that logic on your own.

### index.jsx

And finally, a react component that will loop over the object returned from `routesHelper.js` and return array of `Routes` component wrapped inside `HashRouter` component. The component will be imported and mounted in the `App.js` component. You will see that in just a moment. 

## Adding new route for new page component

*We will see our way of managing components down below.*

- Create new page component inside `pages`.

```file
react-project-structure
└── src
    └── pages
         └── NewPage
              └── index.jsx
```

- Add new path in `/src/Routes/constants.js`.

```javascript
export const NEW_PAGE = '/new-page' 
```

- import `NEW_PAGE` const variable inside `/src/Routes/routes.js`.

- Add new route object in `/src/Routes/routes.js`.

```javascript
  // Add your routes here
[NEW_PAGE]: {
    private: false,
    pageName: 'NewPage',
  }
```

*`pageName` should be the same as the folder name inside `pages` folder.*

## src/App.js

We don't need to do anything here. Just import `Routes` and wrap with `lazy` and `ErrorBoundary`.

```
import { lazy, Suspense } from 'react';

import ErrorBoundary from './components/ErrorBoundary';
import PageLoadingSpinner from './components/PageLoadingSpinner';
import './App.scss';

const Routes = lazy(() => import('./Routes'));

function App() {
  return (
    <main className="App">
      <ErrorBoundary>
        <Suspense fallback={PageLoadingSpinner()}>
          <Routes />
        </Suspense>
      </ErrorBoundary>
    </main>
  );
}

export default App;
```

This will show `PageLoadingSpinner` until page files gets loaded.

## src/_variables.scss

We add all the variables used in our projects like colors, container width, font sizes, font weights, media breakpoints, box-shadows, etc.

## src/actions/

We will add all the action types in `constants.js` and import them wherever it is required. The reason is the same as adding a new path in `src/Routes/constants.js` as you read above.

#### Some tips for creating action creator:

- Try to write your action creator functions in their own file, i.e. authentication action creators in `auth.js`.

- Try to use single `onGet` action creator to make all the `@GET` request. Same goes for other request methods.

- Try to use `helper.js` to write your common code as a function to make it re-usable for other action creators.

- Try to write **HOF (Higher Order Functions)** if you have similar logic and need to create multiple action creators.

## src/appState/

### context

In large web applications, it is often desirable to split up the app code into multiple JS bundles that can be loaded on-demand. This strategy, called `code splitting`, helps to increase performance of your application by reducing the size of the initial JS payload that must be fetched.

Most applications deal with multiple types of data, which can be broadly divided into three categories:

- **Domain data**: This should be like a mini-database. Data returned from server should be stored in this reducer and [selectors](https://redux.js.org/usage/deriving-data-selectors) can be used to extract required data from the store.
- **App state**: Data that is specific to the application's behavior (such as 'there is a request in progress to fetch Todos').
- **UI state**: Data that represents how the UI is currently displayed (such as 'The EditTodo modal dialog is currently open' or 'state of a login form').

[Read more in detail](https://redux.js.org/usage/structuring-reducers/basic-reducer-structure)

### Back to appState

We have written app state in separate files inside `appState` folder. It will be used as an initial state for `APP_STATE_ACTION_TYPES` while dynamically injecting reducers. If we write all the reducers and combine them while creating store, our JS bundle size will grow more larger on build time. That's why we did this to have dynamic reducers. Only required reducers for `page` will exists on redux store on mount. [See the usage](https://github.com/ramankarki/react-project-structure/blob/main/src/pages/Dashboard/index.jsx).

You can do this with Domain and UI state, Injecting required reducers on `page` component on mount.

## src/reducers/

As an application grows, common patterns in reducer logic will start to emerge. You may find several parts of your reducer logic doing the same kinds of work for different types of data, and want to reduce duplication by reusing the same common logic for each data type. For that, we can use **HOF (Higher Order Function)** reducer. We can also call them as a "reducer factory".

The two most common ways to write a **HOF** reducer are to generate new action constants with a given prefix or suffix, or to attach additional info inside the action object.

[This](https://github.com/ramankarki/react-project-structure/blob/main/src/reducers/HOFreducer.js) is how we wrote our **HOF** reducer, and you can write on your own for your project.

## src/utils/

The crucial files here are `actionLogger.js`, `dynamicReducers.js`, `monitorReducers.js`. Other files are straightforward. You can explore on your own and change if you want.

`actionLogger.js` and `monitorReducers.js` were setup from [Redux official site](https://redux.js.org/usage/configuring-your-store#extending-redux-functionality). These will be used to configure redux store.

`dynamicReducers.js` returns a function that will be used as a redux store enhancer and will be used while configuring redux store.

> NOTE: Middleware adds extra functionality to the Redux dispatch function; enhancers add extra functionality to the Redux store.

## src/configureStore.js

This file was setup with [Redux official site](https://redux.js.org/usage/configuring-your-store#the-solution-configurestore) as well. You can have a look if you want to read in detail.

### Why configure redux store like this?

- Dynamic reducers with **HMR (Hot Module Replacement)**. Helps in reducing JS bundle size.

- Wraps middleware, enhancers, and redux dev tools.

- Logs state each time redux dispatches any action.

## src/service-worker.js & src/serviceWorkerRegistration.js

These two files were generated by **CRA (Create React App) PWA (Progressive Web App)** template. We don't need to touch these files.

*Finally comes the folders where we will write our components.*

## src/components/

Here, we will keep our tiny components which will be used more commonly in our templates or pages. For example: 

- Buttons
- Responsive images
- Input fields
- Badges
- Tabs
- Progress bar
- Spinners
- Tool tips

## src/templates/

Here, we will keep our templates which will be used in multiple pages. For example:

- Header
- Footer
- Form
- Grid layout
- Card
- Modal

## src/pages/

Here, we will keep our final pages that will be passed as a component on `Route` (component of a `react-router-dom`) and will be rendered in our browser. For example:

- Home
- Signup
- Login
- Contact

## Difference between `components`, `templates`, and `pages`

In our file structure, the `components` folder will contain only small components which may have one or two elements inside them and won't have their own state.

On the other hand, the `templates` folder will contain the components which may have multiple elements and tiny components inside them and may or may not have their own state.

And finally, the `pages` folder will contain the components which are made of using multiple elements, templates, and tiny components. It is the last component and will be passed on `Route` and won't be re-used elsewhere.

Think of it as an atoms, molecules, and organs.

## Conclusion

It is not the only file structure and may not fit on every project. You can explore how others have managed their file structure and make changes if you want. Let me know if I missed something.

Thank you for reading my blog/article. ❤️❤️