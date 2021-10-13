## How to render Notion pages content in React apps | Make your site content dynamic

## Situation

I was working on a web project where most contents were static and were given to me by my client. Some pages were pretty plain and simple that could be written using markdown. But the problem was, my client frequently asked me to change those static content and could be updated shortly again. So, I had to change those hard-coded contents frequently from React app, which was a bit boring.

I have been using **Notion** to keep my notes and documents for a long time. I wanted to use this tool like a **CMS (Content Management System)** to write those contents on the **Notion**, fetch contents using **Notion API**, and render them in my React app. By doing that, I could be free to frequently update my React code and push my code to GitHub.

I could hand over that **Notion** page, and they would have to write those contents themselves and change it whenever they want. Every time they change those contents, it would reflect in React app.

### 💡 Quick information about [Notion](https://notion.so) if you are new to this

> The **Notion** is an application that provides components such as notes, databases, kanban boards, wikis, calendars, and reminders. Users can connect these components to create their systems for knowledge management, note-taking, data management, project management, among others. It's the all-in-one workspace for you and your team.

So I started reading the **Notion API** documentation, and I tried using it. But the problem is, the **API** is in the **Beta version**, and the **CORS (Cross Origin Resource Sharing)** wasn't enabled either.

I started looking for alternate solutions, and I finally found one. I wasn't expecting this solution to be like this. But in the end, it worked, and my problem got solved.

## Let's render our **Notion** pages in our React apps. 🤩🤩

### Create React project

I have used **Vite** to create my React project instead of **CRA (Create React App)**. Learn [more](https://ramankarki.hashnode.dev/react-project-setup-with-vite-for-faster-development-or-blazing-fast-server-startup-and-updates) about Vite.

### Install `react-notion` npm package

```bash
npm install react-notion
```

A React renderer for **Notion** pages. We can use the **Notion** as a **CMS (Content Management System) **for our blog, documentation or personal site.

### Create a Notion account and a page on the Notion

After you signup on the Notion, create a **New page** from the bottom-left of your screen and add title whatever you want.

![How to render Notion pages in React apps _ Make your site content dynamic.png](https://cdn.hashnode.com/res/hashnode/image/upload/v1634135707244/p30aPETy1.png)

### Make your **Notion** page from private to public

It is an important step for fetching page content from our React app.

![How to render Notion pages in React apps _ Make your site content dynamic.png](https://cdn.hashnode.com/res/hashnode/image/upload/v1634136165931/YYy1hWW_N.png)

### Add some content

Add anything you want like paragraphs, images, lists, etc. We will fetch those content and render in our React app.

### Copy the page **UUID (Universally Unique Identity)**

It is required while making **GET** request for our page content. It should be at the end of your page **URL**.

![How to render Notion pages in React apps _ Make your site content dynamic.png](https://cdn.hashnode.com/res/hashnode/image/upload/v1634138881593/KlgTpOl1i.png)

### Change your `App.jsx` code

```javascript
import { useState, useEffect } from 'react';

// We installed earlier. This will render content data fetched from the Notion.
import { NotionRenderer } from 'react-notion';

// For styling markdown content
import 'react-notion/src/styles.css';
import './App.css';

function App() {
  const [data, setData] = useState({});

  useEffect(() => {
    // notion-api-worker
    fetch(
      'https://notion-api.splitbee.io/v1/page/<your page UUID>'
    )
      .then((res) => res.json())
      .then((data) => setData(data));
  }, []);

  return (
    <div className="App">
      <h1>
        How to render Notion pages in React apps | Make your site content
        dynamic
      </h1>

      {/* Mount NotionRenderer and pass in data to render */}
      <NotionRenderer blockMap={data} />
    </div>
  );
}

export default App;
```

We have used [notion-api-worker](https://github.com/splitbee/notion-api-worker) that has **CORS (Cross Origin Resource Sharing)** enabled to fetch the **Notion** page content. And `react-notion` npm package to render those fetched data into our React app.

[`notion-api-worker`](https://github.com/splitbee/notion-api-worker) is a **serverless** wrapper for the private **Notion API**. It provides fast and easy access to your **Notion** content. Ideal to make **Notion** your **CMS**.

### Change your `App.css` code

Just for basic styling.

```css
.App {
  width: 100%;
  max-width: 700px;
  margin: auto;
  padding: 1.5rem;
}

h1 {
  margin: 2rem 0;
}
```

### Start your development server

```
// if you used CRA
npm start

// if you used Vite
npm run dev
```

You should have a page running that looks like this,

![How to render Notion pages in React apps _ Make your site content dynamic.png](https://cdn.hashnode.com/res/hashnode/image/upload/v1634140808747/Bbn1c7K9k.png)

### Update your content on the Notion
 
Try updating your **Notion** page content, save it, and refresh your browser tab once or twice. You should have updated content on your React app.

## Conclusion

So, now we have the **Notion** as our **CMS (Content Management System)**. And our content is rendering on our React app dynamically. Let me know if I missed something. 

Thank you for reading my blog/article. ❤️❤️