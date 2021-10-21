## Crawling and Indexing setup on NextJS | SEO basics with NextJS

Let's implement whatever we learned about [Crawling and indexing](https://ramankarki.hashnode.dev/crawling-and-indexing-or-search-engines-basics) on NextJS. We will work on following factors. 

- HTTP status codes
- `robots.txt` file
- Sitemaps
- Special meta tags
- Canonical tags

If you are unknown about these topics, I highly recommend you go through my [previous article](https://ramankarki.hashnode.dev/crawling-and-indexing-or-search-engines-basics) and familiarize yourselves with these topics.

## HTTP status code setup

### 200

This is the default code that will be set when NextJS renders a page successfully.

### 301/308

Next.js permanent redirects use 308 by default instead of 301 as it is the newer version and considered the better option.

You can trigger a `308` redirect in Next.js by returning a redirect instead of props in the `getStaticProps()` function.

```
//  pages/about.js
export async function getStaticProps(context) {
  return {
    redirect: {
      destination: '/',
      permanent: true // triggers 308
    }
  }
}
```

You can also use the `permanent: true` key in redirects set in `next.config.js`.

```
//next.config.js

module.exports = {
  async redirects() {
    return [
      {
        source: '/about',
        destination: '/',
        permanent: true // triggers 308
      }
    ]
  }
}
```

## 404

Next.js will automatically return a `404` status code for URLs that do not exist in your application.

In some cases, you might also want to return a `404` status code from page. You can do this by returning the following in place of props: 

```
export async function getStaticProps(context) {
  return {
    notFound: true // triggers 404
  }
}
```

You can create a custom 404 page that is statically generated at build time by creating `pages/404.js`.

**Example:** 

```
// pages/404.js
export default function Custom404() {
  return <h1>404 - Page Not Found</h1>
}
```

## 500

Next.js will automatically return a `500` status code for an unexpected application error. You can create a custom `500` error page that is statically generated a build time by creating `pages/500.js`

**Example:** 

```
// pages/500.js
export default function Custom500() {
  return <h1>500 - Server-side error occurred</h1>
}
```

## How to add a robots.txt file to a Next.js project

Thanks to static file serving in Next.js, we can easily add a `robots.txt` file. We would create a new file named `robots.txt` the public folder in the root directory.

An example of what you could put in this file would be:

```
//robots.txt

# Block all crawlers for /accounts
User-agent: *
Disallow: /accounts

# Allow all crawlers
User-agent: *
Allow: /
```

When you run your app with `yarn dev` or `npm run dev`, it will now be available at `http://localhost:3000/robots.txt`. Note that the public folder name is not part of the URL.

Do not name the public directory anything else. The name cannot be changed and is the only directory used to serve static assets.

## How to Add Sitemaps to a Next.js Project

There are two options: 

1. **Manual**

  If you have a relatively simple and static site, you can manually create a `sitemap.xml` in the `public` directory of your project:

  ```xml
   <!-- public/sitemap.xml -->
   <xml version="1.0" encoding="UTF-8">
   <urlset xmlns="http://www.sitemaps.org/schemas/sitemap/0.9">
     <url>
       <loc>http://www.example.com/foo</loc>
       <lastmod>2021-06-01</lastmod>
     </url>
   </urlset>
   </xml>
  ```

2. **getServerSideProps**

  It's more likely your site will be dynamic. In this case, we can leverage `getServerSideProps` to generate an XML sitemap on-demand.

  We can create a new page inside the pages directory such as `pages/sitemap.xml.js`. The goal of this page will be to hit our API to get data that will allow us to know the URLs of our dynamic pages. We will then write an XML file as the response for `/sitemap.xml`.

  **Here is an example:**

  ```javascript
  // pages/sitemap.xml.js
  const EXTERNAL_DATA_URL = 'https://jsonplaceholder.typicode.com/posts';

  function generateSiteMap(posts) {
    return `<?xml version="1.0" encoding="UTF-8"?>
    <urlset xmlns="http://www.sitemaps.org/schemas/sitemap/0.9">
    <!--We manually set the two URLs we know already-->
    <url>
      <loc>https://jsonplaceholder.typicode.com</loc>
    </url>
    <url>

  <loc>https://jsonplaceholder.typicode.com/guide</loc>
    </url>
    ${posts
      .map(({ id }) => {
        return `
      <url>
          <loc>${`${EXTERNAL_DATA_URL}/${id}`}</loc>
      </url>
    `;
      })
      .join('')}
  </urlset>
  `;
  }

  function SiteMap() {
    // getServerSideProps will do the heavy lifting
  }

  export async function getServerSideProps({ res }) {
    // We make an API call to gather the URLs for our site
    const request = await fetch(EXTERNAL_DATA_URL);
    const posts = await request.json();

    // We generate the XML sitemap with the posts data
    const sitemap = generateSiteMap(posts);

    res.setHeader('Content-Type', 'text/xml');
    // we send the XML to the browser
    res.write(sitemap);
    res.end();

    return {
      props: {},
    };
  }

  export default SiteMap;
  ```

## Special Meta Tags for Search Engines example

```
import Head from 'next/head'

function IndexPage() {
  return (
    <div>
      <Head>
        <title>Meta Tag Example</title>
        <meta name="google" content="nositelinkssearchbox" key="sitelinks" />
        <meta name="google" content="notranslate" key="notranslate" />
      </Head>
      <p>Here we show some meta tags off!</p>
    </div>
  )
}

export default IndexPage
```

As you can see in the example, we are using `next/head` which is a built-in component for appending elements to the `head` of a page.

To avoid duplicate tags in your `head` you can use the `key` property, which will make sure the tag is only rendered once.

## Canonical Tags example

```
import Head from 'next/head'

function IndexPage() {
  return (
    <div>
      <Head>
        <title>Canonical Tag Example</title>
        <link
          rel="canonical"
          href="https://example.com/blog/original-post"
          key="canonical"
        />
      </Head>
      <p>This post exists on two URLs.</p>
    </div>
  )
}

export default IndexPage
```

## Conclusion

So, these were the factors that can highly impact **Crawling and indexing**. We now have the knowledge about **Crawling and Indexing** and a way to implement these on the **NextJS**. Let me know if I missed something. I would appreciate your feedback.

Thank you for reading my blog/article. ❤️❤️