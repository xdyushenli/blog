---
title: ssr
date: 2020-09-02 19:24:03
tags: [ react ]
---
# What is SSR?
SSR(server-side-render) means the content on your webpage rendered in the server not in the browser using JavaScript. It will return a fully rendered html file that will directly paint in your screen, not like usual React App created by create-react-app that would return a `.js` file.

todo Is it a html file without js at all? A pure static resource?
todo what is ssg?

Traditional SSR language or framework are like `PHP`, `Java`, `ASP.NET` and `Node.js`. Luckily, we have so much choices now, like `Nect.js`, `Gatsby` and ext. 

I choose `Next.js` as my SSR framework in this post, and later I'll show you how to do it.

# Why we should use SSR?
There are two reasons that you should use SSR in your app. The first is Performance, and the other is for SEO.

## performance
In contrast to the usual React app with CRA, 

# How to use SSR in React?

# Difference between React SSR and Easy SSR

todo
> To use CSS Modules, import a CSS file named *.module.css in any component.
To use global CSS, import a CSS file in pages/_app.js.
> NextJS can auto prefetch Link content.
> By default, Next.js pre-renders every page. This means that Next.js generates HTML for each page in advance, instead of having it all done by client-side JavaScript. Pre-rendering can result in better performance and SEO.Each generated HTML is associated with minimal JavaScript code necessary for that page. When a page is loaded by the browser, its JavaScript code runs and makes the page fully interactive. (This process is called hydration.)

todo Will SSR update whole page whenever a request send?
这特么不就是没有 ajax 时的更新逻辑吗？？

two forms of pre-render:
- Static Generation: Generate one page for everyone.
- Server-side Render: Generate page when a request arrives.

> Importantly, Next.js lets you choose which pre-rendering form to use for each page. You can create a "hybrid" Next.js app by using Static Generation for most pages and using Server-side Rendering for others.
> We recommend using Static Generation (with and without data) whenever possible because your page can be built once and served by CDN, which makes it much faster than having a server render the page on every request.

Blog posts should use Static Generation.

> You should ask yourself: "Can I pre-render this page ahead of a user's request?" If the answer is yes, then you should choose Static Generation.

In some case, like your page need to show frequently update data, and the page content will change on every request.In that case, you should use SSR.

In markdown file:
> You might have noticed that each markdown file has a metadata section at the top containing title and date. This is called YAML Front Matter, which can be parsed using a library called `gray-matter`.

> This is possible because `getStaticProps` runs only on the server-side. It will never run on the client-side. It won’t even be included in the JS bundle for the browser. That means you can write code such as direct database queries without them being sent to browsers.

> Because it’s meant to be run at build time, you won’t be able to use data that’s only available during request time, such as query parameters or HTTP headers.

> getStaticProps can only be exported from a page. You can’t export it from non-page files.

> Because getServerSideProps is called at request time, its parameter (context) contains request specific parameters.

`getServerSideProps` will be invoked at every request time, `getStaticProps` will only be invoked at build time once.
In development mode, `getStaticProps` will also be invoked at every request time.

---- dyamic route

<!-- =========================================== -->

# 
- https://blog.logrocket.com/why-you-should-render-react-on-the-server-side-a50507163b79/#:~:text=Server%2Dside%20rendering%20(SSR),comes%20as%20fully%20rendered%20HTML.
-> Abstract: Next.js