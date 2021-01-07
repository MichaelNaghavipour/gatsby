---
title: Using the beta Gatsby Image plugin
---

The Gatsby Image plugin gives you the tools you need to add high-performance, responsive images to your site. This gives the best experience for your users and helps you achieve high performance scores. This can be hard to do manually, as you need to produce images in multiple sizes and formats, but the Gatsby Image plugin handles the hard parts for you.

> Want to dig deeper into using high-performance images with Gatsby? Read the conceptual guide.

The new Gatsby Image plugin is currently in beta, but you can try it out now and see what it can do for the performance of your site.

## Getting started

First you need to install the following packages:

```shell
npm install gatsby-plugin-image gatsby-plugin-sharp gatsby-transformer-sharp
```

You then need to add the plugins to your `gatsby-config.js`:

```js
module.exports = {
  plugins: [
    `gatsby-plugin-image`,
    `gatsby-plugin-sharp`,
    `gatsby-transformer-sharp`,
  ],
}
```

If you already have some of these plugins installed, please check that they're updated to the latest version.

## Using the Gatsby Image components

### Decide which component to use

The Gatsby Image plugin includes two image components: one for static and one for dynamic images. The simplest way to decide which you need to is to ask yourself: _"will this image be the same every time the component or template is used?"_. If it will always be the same, then use `StaticImage`. If it will change, whether through data coming from a CMS or different values passed to a component each time you use it, then it is a dynamic image and you should use the `GatsbyImage` component.

### Static images

If you are using an image that will be the same each time the component is used, such as a logo or hero image, you can use the `StaticImage` component. The image can be either a local file in your project, or an image hosted on a remote server. Any remote images are downloaded and resized at build time.

1. **Add the image to your project**

   If you are using a local image, copy it into the project. A folder such as `src/images` is a good choice.

2. **Add the `StaticImage` component to your template**

   Import the component, then set the `src` prop to point to the image you added earlier, or to the URL of the image if hosted elsewhere. The path is relative to the source file itself. If your component file was `src/components/dino.js`, then you would load the image like this:

   ```jsx
   import { StaticImage } from "gatsby-plugin-image"

   export function Dino() {
     return (
       <section>
         <StaticImage src="../images/dino.png" alt="A dinosaur" />
         <StaticImage src="https://placekitten.com/800/600" alt="A kitten" />
       </section>
     )
   }
   ```

   When you build your site, this will load the image from your filesystem or from the remote URL, and generate all the sizes and formats that you need to support a responsive image. Because the image is loaded at build time, you cannot pass the filename in as a prop, or otherwise generate it outside of the component. It should either be a static string, or a local variable in the component's scope.

   > **Important:** remote images are downloaded and resized at build time. If the image is changed on the other server, it will not be updated on your site until you rebuild

3. **Configure the image**:

   You configure the image by passing props to the `<StaticImage />` component. You can change the size and layout, as well as settings such as the type of placeholder used when lazy loading. There are also advanced image processing options available. You can find the full list of options in the API docs.

   ```jsx
   import { StaticImage } from "gatsby-plugin-image"

   export function Dino() {
     return (
       <StaticImage
         src="../images/dino.png"
         alt="A dinosaur"
         placeholder="blurred"
         layout="fixed"
         width={200}
         height={200}
       />
     )
   }
   ```

### Dynamic images

If you need to have dynamic images, such as if they are coming from a CMS, they are loaded via GraphQL and displayed using the `GatsbyImage` component.

1. **Add the image to your page query**:

   Any GraphQL File object that includes an image will have a `childImageSharp` field that you can use to query the image data. The exact data structure will vary according to your data source, but the syntax is like this:

   ```graphql
   query {
     blogPost(id: { eq: $Id }) {
       title
       body
       avatar {
         childImageSharp {
           gatsbyImageData(maxWidth: 200)
         }
       }
     }
   }
   ```

2. **Configure your image**:

   You configure the image by passing arguments to the `gatsbyImageData` resolver. You can change the size and layout, as well as settings such as the type of placeholder used when lazy loading. There are also advanced image processing options available. You can find the full list of options in the API docs.

   ```graphql
   query {
     blogPost(id: { eq: $Id }) {
       title
       body
       author
       avatar {
         childImageSharp {
           gatsbyImageData(
             maxWidth: 200
             placeholder: BLURRED
             formats: [AUTO, WEBP, AVIF]
           )
         }
       }
     }
   }
   ```

3. **Display the image:**

   You can then use the `GatbsyImage` component to display the image on the page. The `getImage()` function is an optional helper to make your code easier to read. It takes a `File` and returns `file.childImageSharp.gatsbyImageData`, which can be passed to the `GatsbyImage` component.

   ```jsx
   import { GatsbyImage, getImage } from "gatsby-plugin-image"

   function BlogPost({ data }) {
     const image = getImage(data.blogPost.avatar)
     return (
       <section>
         <h2>{data.blogPost.title}</h2>
         <GatsbyImage image={image} alt={data.blogPost.author} />
         <p>{data.blogPost.body}</p>
       </section>
     )
   }
   ```

## Migrating

If your site uses the old `gatsby-image` component, you can use a codemod to help you migrate to the new Gatsby Image components. This can update the code for most sites. To use the codemod, run this command in the root of your site:

```shell
npx gatsby-codemods gatsby-plugin-image
```

This will convert all GraphQL queries and components to use the new plugin. For more information see the full migration guide.