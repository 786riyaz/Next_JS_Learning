# **Phase 18 – Images, Fonts, Scripts and Static Assets Optimization**



Next.js 16.x

App Router

TypeScript

Intermediate–Advanced



## **1. Learning Objectives**

By the end of this phase, you will understand how to:

1. Organize and serve static assets in Next.js.
2. Use the `next/image` component for image optimization.
3. Understand image dimensions, aspect ratios, `fill`, and `sizes`.
4. Configure remote images using `remotePatterns`.
5. Use image placeholders and blur effects.
6. Understand image loading strategies and the `preload` property.
7. Optimize fonts using `next/font`.
8. Load Google Fonts and local fonts.
9. Use font weights, subsets, and CSS variables.
10. Load third-party JavaScript using `next/script`.
11. Understand script loading strategies.
12. Optimize static assets and Core Web Vitals.
13. Build a practical asset optimization setup for a production application.

**Version note:** This phase uses Next.js 16 App Router. One important change: Next.js 16 deprecates the `priority` prop of `next/image` in favor of `preload`. We will use the current API throughout.&#x20;

[image](https://www.google.com/s2/favicons?domain=https://nextjs.org\&sz=32)

Next.js+1



## **2. Understanding Static Assets**

Static assets are files that your application serves without generating their content dynamically for each request.

Examples:

- Images
- Logos
- Icons
- Fonts
- Videos
- PDFs
- CSS files
- JavaScript files
- Favicon files

Suppose you are building an e-commerce website.

Your application might need:

```
Logo
Product images
Category banners
User profile images
Custom fonts
Product demonstration videos
```

Next.js provides different ways to organize and load these files.

The two common approaches are:

1. Store files inside the `public` folder.
2. Import files directly into your application code.

## **3. Understanding the&#x20;**`public`**&#x20;Folder**

Next.js provides a special folder called `public`.

Files placed inside it can be accessed using URLs starting from `/`.

### **3.1 Example folder structure**

```
my-next-app/
│
├── public/
│   ├── logo.png
│   ├── favicon.ico
│   │
│   ├── images/
│   │   ├── banner.jpg
│   │   └── laptop.jpg
│   │
│   ├── videos/
│   │   └── demo.mp4
│   │
│   └── documents/
│       └── brochure.pdf
│
├── src/
│   └── app/
│       ├── page.tsx
│       └── layout.tsx
│
└── package.json
```

### **3.2 How do public file URLs work?**

Suppose you have:

```
public/logo.png
```

You can access it through:

```
http://localhost:3000/logo.png
```

Suppose you have:

```
public/images/laptop.jpg
```

Its URL is:

```
http://localhost:3000/images/laptop.jpg
```

**Important:** Do not include `/public` in the URL.

Incorrect:

```
<img src="/public/logo.png" alt="Logo" />
```

Correct:

```
<img src="/logo.png" alt="Logo" />
```

The same URL convention applies when using `next/image`.

### **3.3 When should you use the public folder?**

Use it for:

- Logos and branding assets.
- Static banners.
- PDFs and downloadable documents.
- Files referenced by a predictable URL.
- Assets that need a stable public path.

Next.js serves these files directly from the root URL path.&#x20;

[image](https://www.google.com/s2/favicons?domain=https://nextjs.org\&sz=32)

Next.js+1



### **3.4 Important caching detail**

The default response header for files in `public` is:

```
Cache-Control: public, max-age=0
```

This means browsers should not assume that these files can remain fresh indefinitely.

For assets that change infrequently, use versioned filenames or an appropriate CDN/deployment caching strategy.

For example:

```
logo-v2.png
banner-2026.jpg
```

[image](https://www.google.com/s2/favicons?domain=https://nextjs.org\&sz=32)

Next.js

## **4. Importing Static Assets Directly**

Instead of putting every image inside `public`, you can import images directly into your components.

Example:

```
src/
├── app/
│   └── page.tsx
│
└── assets/
    ├── logo.png
    └── laptop.jpg
```

Use:

```
import Image from "next/image";

import laptopImage from "@/assets/laptop.jpg";

export default function HomePage() {
  return (
    <main>
      <Image
        src={laptopImage}
        alt="Gaming laptop"
      />
    </main>
  );
}
```

Notice that you do not need to manually specify `width` and `height` when importing a static image. Next.js can infer its dimensions.

### **4.1 Public folder vs static import**

| Public folder                                 | Static import                                                              |
| --------------------------------------------- | -------------------------------------------------------------------------- |
| Referenced using a URL string                 | Imported as a module                                                       |
| URL is predictable                            | Image metadata can be inferred                                             |
| Useful for files referenced by URL            | Useful for component-owned assets                                          |
| File can be replaced without changing imports | Build tracks the imported asset                                            |
| No automatic static-import blur metadata      | Can automatically provide blur metadata for supported static image formats |

### **Which one should you use?**

For most applications:

- Use `public` for logos, fixed URLs, favicons, and general static files.
- Use static imports for images that belong directly to specific components.
- Use remote image URLs for product images or images stored in services such as Cloudinary.

## **5. Understanding&#x20;**`next/image`

The `Image` component is one of the most important Next.js performance features.

Import it using:

```
import Image from "next/image";
```

It extends the standard HTML image element with automatic image optimization.

### **5.1 Why not use a normal&#x20;**`<img>`**&#x20;everywhere?**

Consider:

```
<img
  src="/images/laptop.jpg"
  alt="Gaming laptop"
/>
```

A standard `<img>` element does not automatically provide Next.js image resizing, responsive source generation, or the built-in image optimization pipeline.

With `next/image`:

```
import Image from "next/image";

export default function Product() {
  return (
    <Image
      src="/images/laptop.jpg"
      alt="Gaming laptop"
      width={600}
      height={400}
    />
  );
}
```

Next.js can:

- Generate optimized image sizes.
- Serve modern image formats when supported.
- Generate responsive image sources.
- Help reserve layout space.
- Lazy-load images by default.
- Optimize supported remote images.

[image](https://www.google.com/s2/favicons?domain=https://nextjs.org\&sz=32)

Next.js+1

### **5.2 What happens internally?**

**Image optimization flow**

**Original image**

laptop.jpg — 3000 × 2000 pixels

**Next.js Image Optimization**

Resize, format and quality selection

**Mobile**

Small image

**Tablet**

Medium image

**Desktop**

Larger image

The browser can choose an appropriate generated image source based on the device and layout.

## **6. Understanding&#x20;**`width`**&#x20;and&#x20;**`height`

When using `next/image`, you generally need to provide both `width` and `height`, unless:

- You statically import the image.
- You use the `fill` property.

Example:

```
<Image
  src="/laptop.jpg"
  alt="Gaming laptop"
  width={600}
  height={400}
/>
```

These values represent the image's intrinsic dimensions.

They help the browser reserve space before the image loads, reducing layout shifts.

### **6.1 Do these values control the actual rendered size?**

Not necessarily.

Consider:

```
<Image
  src="/laptop.jpg"
  alt="Gaming laptop"
  width={600}
  height={400}
  style={{
    width: "100%",
    height: "auto",
  }}
/>
```

The image can render responsively while maintaining its aspect ratio.

### **6.2 Aspect ratio**

Aspect ratio describes the relationship between width and height.

\\[ \text{Aspect Ratio}=\frac{\text{Width}}{\text{Height}} \\]

Example:

| Dimensions  | Aspect ratio         |
| ----------- | -------------------- |
| 600 × 400   | 3:2                  |
| 800 × 800   | 1:1                  |
| 1200 × 630  | Approximately 1.91:1 |
| 1920 × 1080 | 16:9                 |

Always use dimensions that represent the actual source image's aspect ratio.&#x20;

[image](https://www.google.com/s2/favicons?domain=https://nextjs.org\&sz=32)

Next.js



## **7. Understanding the&#x20;**`fill`**&#x20;Property**

Sometimes you do not know the exact dimensions of an image.

For example, product images may have different original dimensions, but you want all product cards to have the same visual size.

Use `fill`.

### **7.1 Basic example**

```
import Image from "next/image";

export default function ProductCard() {
  return (
    <div
      style={{
        position: "relative",
        width: "100%",
        height: "300px",
      }}
    >
      <Image
        src="/images/laptop.jpg"
        alt="Gaming laptop"
        fill
        style={{
          objectFit: "cover",
        }}
      />
    </div>
  );
}
```

### **7.2 Why does the parent need&#x20;**`position: relative`**?**

When `fill` is used, the image is positioned absolutely to fill its nearest positioned parent.

Therefore, the parent should have:

```
position: relative;
```

It also needs a defined size.

Otherwise, the image may not occupy the space you expect.

### **7.3 Understanding&#x20;**`objectFit`

There are two common values.

[Gaming laptop computer isolated on white](https://images.openai.com/static-rsc-4/AkU2aToQXTx-_h2pvgSPYwGLjKAHlyu-VdqdE8AFSOeCGjdLkbsjfe1XGD_zktxgWw6RwrpLERa4_mQN2SW9LHtnr7hwgUKgNiMsn37pKSxvNpwVXh_cLojJ9aXFku58or0CO-f_6a-dL566Oy0ztWidsTfkk3VmNzCgV4EoyWHN2F9aXRgsABpJTJxrw6vn?purpose=inline)

`object-fit: contain`

Entire image remains visible. Empty space may appear.

[ASUS ROG STRIX G513IM-716512G0W, Ryzen 7, Win 11 – Zelpac I.T And Printer Cartridges](https://images.openai.com/static-rsc-4/-Mo5LXT1ndIGCWcJUmy_IGJzxli8qUmZVYkEuhKdI9c7B938L37IVYbM1hRtQGQ0L9J1rIyD_J9QSK57plw4q0en8x7Bq6kXEwKzzYbmZ7st6ZUcymRl-LCEeJlbxm050TD8PxLUZvHgpwC0ifDVyMAFo0UyzMXVZYzU2SsLTUs?purpose=inline)

`object-fit: cover`

Fills the container. Some parts may be cropped.

CSS example:

```
<Image
  src="/laptop.jpg"
  alt="Gaming laptop"
  fill
  style={{
    objectFit: "contain",
  }}
/>
```

Or:

```
<Image
  src="/laptop.jpg"
  alt="Gaming laptop"
  fill
  style={{
    objectFit: "cover",
  }}
/>
```

### **When should you use each?**

| Value     | Best use                            |
| --------- | ----------------------------------- |
| `contain` | Product images, logos, diagrams     |
| `cover`   | Banners, thumbnails, profile covers |

You can also use CSS classes instead of inline styles.

[image](https://www.google.com/s2/favicons?domain=https://nextjs.org\&sz=32)

Next.js+1

## **8. Understanding Responsive Images and&#x20;**`sizes`

This is an important performance concept.

Suppose your website displays a product image inside a grid.

On desktop:

```
| Product | Product | Product |
```

On mobile:

```
| Product |
| Product |
| Product |
```

The image should not always download a desktop-sized file.

The `sizes` property tells the browser approximately how much horizontal space the image will occupy at different viewport sizes.

### **8.1 Example**

```
<Image
  src="/laptop.jpg"
  alt="Gaming laptop"
  fill
  sizes="(max-width: 768px) 100vw, 33vw"
/>
```

Understand the value:

```
(max-width: 768px) 100vw, 33vw
```

| Screen width | Expected image width   |
| ------------ | ---------------------- |
| Up to 768px  | 100% of viewport width |
| Above 768px  | 33% of viewport width  |

### **8.2 More detailed example**

```
<Image
  src="/laptop.jpg"
  alt="Gaming laptop"
  fill
  sizes="
    (max-width: 640px) 100vw,
    (max-width: 1024px) 50vw,
    33vw
  "
/>
```

This describes:

- Mobile: full viewport width.
- Tablet: half viewport width.
- Desktop: approximately one-third viewport width.

### **8.3 Why is&#x20;**`sizes`**&#x20;important?**

Without `sizes`, a responsive `fill` image may be treated as if it occupies the full viewport width.

This can cause the browser to download a larger image than necessary.

With a correct `sizes` value, Next.js generates a more suitable `srcset` and the browser can select an appropriate image candidate.

**Rule:** If you use `fill` or CSS-based responsive sizing, provide an accurate `sizes` value.&#x20;

[image](https://www.google.com/s2/favicons?domain=https://nextjs.org\&sz=32)

Next.js



## **9. Loading Strategies: Lazy Loading, Eager Loading and Preloading**

Images do not all need to load at the same time.

For example, consider a product page:

- Main product image is immediately visible.
- Product recommendations are below the fold.
- Footer images are at the bottom.

Loading every image immediately wastes bandwidth.

Next.js provides different loading options.

### **9.1 Lazy loading**

By default, `next/image` uses lazy loading.

```
<Image
  src="/recommendations.jpg"
  alt="Recommended products"
  width={600}
  height={400}
  loading="lazy"
/>
```

The browser delays loading until the image approaches the viewport.

Use this for:

- Images below the fold.
- Product recommendations.
- Long article images.
- Gallery thumbnails that are not initially visible.

### **9.2 Eager loading**

```
<Image
  src="/hero.jpg"
  alt="Homepage banner"
  width={1200}
  height={600}
  loading="eager"
/>
```

This requests the image immediately rather than waiting for lazy-loading proximity.

Use it only when you specifically need immediate loading and are not using `preload`.

### **9.3 Preloading in Next.js 16**

Next.js 16 supports the `preload` property.

```
<Image
  src="/hero.jpg"
  alt="Homepage hero banner"
  width={1200}
  height={600}
  preload
/>
```

This tells Next.js to preload the image.

Next.js 16 change

Older tutorials often show:

```
<Image
  src="/hero.jpg"
  alt="Hero"
  width={1200}
  height={600}
  priority
/>
```

In Next.js 16, `priority` is deprecated. Use `preload` instead.

### **9.4 When should you use&#x20;**`preload`**?**

Use it selectively for an image that is:

- Immediately visible.
- Important to the initial page presentation.
- Likely to be the page's Largest Contentful Paint (LCP) element.

Do not preload every image. Preloading too many resources competes for bandwidth and can slow down more important content.

### **Comparison**

| Property          | Behavior                                   |
| ----------------- | ------------------------------------------ |
| `loading="lazy"`  | Loads when approaching the viewport        |
| `loading="eager"` | Requests the image immediately             |
| `preload`         | Adds a preload hint for an important image |

Generally, let Next.js lazy-load ordinary images and use `preload` only for the critical image when justified.&#x20;

[image](https://www.google.com/s2/favicons?domain=https://nextjs.org\&sz=32)

Next.js+1



## **10. Image Placeholders and Blur Effects**

Sometimes images take a little time to load.

Instead of displaying an empty space, you can show a placeholder.

Next.js supports:

- `placeholder="empty"`
- `placeholder="blur"`
- A custom Data URL placeholder.

### **10.1 Using blur with a static import**

```
import Image from "next/image";

import laptop from "@/assets/laptop.jpg";

export default function ProductImage() {
  return (
    <Image
      src={laptop}
      alt="Gaming laptop"
      placeholder="blur"
    />
  );
}
```

For supported static image imports, Next.js can automatically provide the blur placeholder data.

### **10.2 Using blur with a remote image**

For a remote URL, you generally need to provide `blurDataURL` yourself.

```
import Image from "next/image";

export default function ProductImage() {
  return (
    <Image
      src="https://images.example.com/laptop.jpg"
      alt="Gaming laptop"
      width={600}
      height={400}
      placeholder="blur"
      blurDataURL="data:image/jpeg;base64,REPLACE_WITH_VALID_DATA"
    />
  );
}
```

The example placeholder string is illustrative; replace it with an actual valid, small image Data URL.

### **10.3 When should you use blur?**

Useful for:

- Product galleries.
- Large hero images.
- Blog cover images.
- Profile images.
- Image-heavy pages.

Avoid generating unnecessarily large blur Data URLs because they add data to the page.

## **11. Configuring Remote Images with&#x20;**`remotePatterns`

In real applications, images are often stored outside your Next.js project.

Examples:

- Cloudinary
- AWS S3
- Image hosting services
- External product image providers

Suppose your image URL is:

```
https://res.cloudinary.com/my-cloud/image/upload/laptop.jpg
```

If you use this directly:

```
<Image
  src="https://res.cloudinary.com/my-cloud/image/upload/laptop.jpg"
  alt="Laptop"
  width={600}
  height={400}
/>
```

Next.js may reject the image because its remote host is not configured.

### **11.1 Configure&#x20;**`remotePatterns`

Open:

```
next.config.ts
```

Example:

```
import type { NextConfig } from "next";

const nextConfig: NextConfig = {
  images: {
    remotePatterns: [
      {
        protocol: "https",
        hostname: "res.cloudinary.com",
        pathname: "/my-cloud/**",
      },
    ],
  },
};

export default nextConfig;
```

Now Next.js can optimize images from that approved location.

### **11.2 More restrictive configuration**

You can specify the protocol, hostname, port, path and query string restrictions.

```
const nextConfig: NextConfig = {
  images: {
    remotePatterns: [
      {
        protocol: "https",
        hostname: "images.example.com",
        port: "",
        pathname: "/products/**",
        search: "",
      },
    ],
  },
};

export default nextConfig;
```

This allows URLs under:

```
https://images.example.com/products/...
```

but restricts other paths and query strings.

### **11.3 Why is this configuration necessary?**

It is a security measure.

You do not want your image optimization endpoint to fetch arbitrary external URLs.

Allow only the image hosts and paths your application actually needs.

### **11.4&#x20;**`remotePatterns`**&#x20;vs&#x20;**`domains`

Older tutorials may use:

```
images: {
  domains: ["example.com"],
}
```

The `domains` option is deprecated.

Prefer `remotePatterns`, which provides more precise restrictions.&#x20;

[image](https://www.google.com/s2/favicons?domain=https://nextjs.org\&sz=32)

Next.js+1



## **12. Complete Cloudinary Image Example**

Let's use a realistic example.

Suppose your application stores product images in Cloudinary.

### **12.1 Configure Next.js**

```
// next.config.ts

import type { NextConfig } from "next";

const nextConfig: NextConfig = {
  images: {
    remotePatterns: [
      {
        protocol: "https",
        hostname: "res.cloudinary.com",
        pathname: "/your-cloud-name/**",
      },
    ],
  },
};

export default nextConfig;
```

Replace `your-cloud-name` with your actual Cloudinary cloud name.

### **12.2 Display a Cloudinary image**

```
import Image from "next/image";

export default function ProductCard() {
  return (
    <div className="relative h-64 w-full">
      <Image
        src="https://res.cloudinary.com/your-cloud-name/image/upload/laptop.jpg"
        alt="Gaming laptop"
        fill
        sizes="(max-width: 768px) 100vw, 33vw"
        style={{
          objectFit: "contain",
        }}
      />
    </div>
  );
}
```

### **12.3 Dynamic product images**

Imagine your MongoDB product document contains:

```
{
  name: "Gaming Laptop",
  image: "https://res.cloudinary.com/your-cloud-name/image/upload/laptop.jpg"
}
```

You can display it dynamically:

```
import Image from "next/image";

type Product = {
  name: string;
  image: string;
};

export default function ProductCard({
  product,
}: {
  product: Product;
}) {
  return (
    <div className="relative h-64 w-full">
      <Image
        src={product.image}
        alt={product.name}
        fill
        sizes="(max-width: 768px) 100vw, 33vw"
        style={{
          objectFit: "contain",
        }}
      />
    </div>
  );
}
```

**Important:** Only use image URLs from trusted, expected sources. Do not let arbitrary users submit unrestricted external image URLs and then automatically optimize them.

## **13. Image Quality and Optimization Settings**

Next.js allows you to control the quality of optimized images.

Example:

```
<Image
  src="/laptop.jpg"
  alt="Gaming laptop"
  width={600}
  height={400}
  quality={75}
/>
```

The quality value ranges from 1 to 100.

| Quality | Typical effect                                      |
| ------- | --------------------------------------------------- |
| 40      | Smaller file, potentially visible compression       |
| 60      | Lower file size                                     |
| 75      | Default quality in current Next.js configuration    |
| 85      | Higher visual fidelity                              |
| 100     | Maximum configured quality, potentially much larger |

The actual output depends on the source image and output format.

### **13.1 Configure allowed qualities**

In Next.js 16, you can configure allowed image qualities.

```
// next.config.ts

import type { NextConfig } from "next";

const nextConfig: NextConfig = {
  images: {
    qualities: [60, 75, 85],
  },
};

export default nextConfig;
```

Now the image optimizer restricts requested qualities to your configured values.

### **13.2 Should every image use quality 100?**

No.

A higher quality usually means a larger image file.

For most product images and content images, a moderate quality is sufficient.

Choose quality based on:

- Image type.
- Visual importance.
- Original image quality.
- Required file size.
- Display dimensions.

[image](https://www.google.com/s2/favicons?domain=https://nextjs.org\&sz=32)

Next.js+1

## **14. Understanding&#x20;**`unoptimized`

Sometimes you do not need Next.js image optimization.

For example:

- Small SVG icons.
- Animated GIFs.
- Tiny images.
- Images already optimized by an external image service.

You can use:

```
<Image
  src="/logo.svg"
  alt="Company logo"
  width={150}
  height={50}
  unoptimized
/>
```

This tells Next.js to serve the source image without using its image optimization API.

You can also configure this globally, but doing so disables optimization for all images and is generally not the preferred default.

### **When to use&#x20;**`unoptimized`**?**

Use it when you have a specific reason, not simply because an image is failing to load.

[image](https://www.google.com/s2/favicons?domain=https://nextjs.org\&sz=32)

Next.js

## **15. Understanding Image Accessibility**

The `alt` property is important for accessibility.

Example:

```
<Image
  src="/laptop.jpg"
  alt="Black gaming laptop with an illuminated keyboard"
  width={600}
  height={400}
/>
```

Screen readers can use this text to communicate the image's meaning.

### **15.1 Informative image**

```
<Image
  src="/product.jpg"
  alt="Silver laptop with a 16-inch display"
  width={600}
  height={400}
/>
```

### **15.2 Decorative image**

```
<Image
  src="/decorative-pattern.svg"
  alt=""
  width={500}
  height={200}
/>
```

Use an empty alt value when the image is purely decorative and adds no meaningful information.

### **15.3 Common mistake**

Bad:

```
<Image
  src="/laptop.jpg"
  alt="image"
/>
```

Better:

```
<Image
  src="/laptop.jpg"
  alt="Gaming laptop with RTX graphics"
  width={600}
  height={400}
/>
```

Alt text should describe the image's purpose or content, not repeat unnecessary surrounding text.&#x20;

[image](https://www.google.com/s2/favicons?domain=https://nextjs.org\&sz=32)

Next.js



## **16. Understanding&#x20;**`next/font`

Now let's move to font optimization.

Fonts influence:

- Visual design.
- Readability.
- Brand identity.
- Page layout.
- Loading performance.

A traditional approach is to load Google Fonts through an external stylesheet.

For example:

```
<link
  href="https://fonts.googleapis.com/css2?family=Roboto"
  rel="stylesheet"
/>
```

This can require the browser to make additional network requests.

Next.js provides `next/font` to optimize and self-host fonts.

### **16.1 Why use&#x20;**`next/font`**?**

Next.js:

- Downloads supported Google Fonts at build time.
- Self-hosts the font files with your application.
- Avoids browser requests to Google for those fonts.
- Optimizes font loading.
- Helps reduce layout shifts.

[image](https://www.google.com/s2/favicons?domain=https://nextjs.org\&sz=32)

Next.js+1

### **16.2 Two ways to load fonts**

```
next/font
│
├── next/font/google
│   └── Google Fonts
│
└── next/font/local
    └── Your own font files
```

Let's understand both.

## **17. Loading Google Fonts Using&#x20;**`next/font/google`

Suppose you want to use the Inter font.

### **17.1 Basic example**

```
// src/app/layout.tsx

import { Inter } from "next/font/google";

const inter = Inter({
  subsets: ["latin"],
});

export default function RootLayout({
  children,
}: {
  children: React.ReactNode;
}) {
  return (
    <html lang="en" className={inter.className}>
      <body>{children}</body>
    </html>
  );
}
```

Now the Inter font applies throughout the application.

### **17.2 Understanding&#x20;**`subsets`

Fonts can contain different character sets.

For example:

- Latin
- Latin Extended
- Cyrillic
- Greek
- Devanagari

If your application only needs Latin characters, you can specify:

```
const inter = Inter({
  subsets: ["latin"],
});
```

This helps include only the required font subset.

### **17.3 Understanding font weights**

Suppose you want Roboto.

```
import { Roboto } from "next/font/google";

const roboto = Roboto({
  weight: ["400", "500", "700"],
  subsets: ["latin"],
});
```

These weights represent:

| Weight | Common use  |
| ------ | ----------- |
| 400    | Normal text |
| 500    | Medium text |
| 700    | Bold text   |

If the font is variable, you can often avoid specifying individual weights.

### **17.4 Using&#x20;**`display: "swap"`

```
const inter = Inter({
  subsets: ["latin"],
  display: "swap",
});
```

`swap` allows fallback text to be displayed while the web font is loading, then swaps to the requested font.

This helps avoid invisible text while waiting for the font.

### **17.5 Recommended approach**

```
import { Geist } from "next/font/google";

const geist = Geist({
  subsets: ["latin"],
  display: "swap",
});
```

Next.js recommends variable fonts where available because they provide flexible weights without requiring separate font files for every weight.&#x20;

[image](https://www.google.com/s2/favicons?domain=https://nextjs.org\&sz=32)

Next.js+1



## **18. Loading Local Fonts Using&#x20;**`next/font/local`

Sometimes you have your own font files.

For example:

```
public/
└── fonts/
    ├── Inter-Regular.woff2
    ├── Inter-Medium.woff2
    └── Inter-Bold.woff2
```

You can load them using `next/font/local`.

### **18.1 Basic example**

```
// src/app/layout.tsx

import localFont from "next/font/local";

const myFont = localFont({
  src: "../../public/fonts/Inter-Regular.woff2",
});

export default function RootLayout({
  children,
}: {
  children: React.ReactNode;
}) {
  return (
    <html lang="en" className={myFont.className}>
      <body>{children}</body>
    </html>
  );
}
```

The `src` path is resolved relative to the file where `localFont()` is called.

You can also keep fonts alongside your application code:

```
src/
└── app/
    ├── fonts/
    │   └── Inter-Regular.woff2
    └── layout.tsx
```

Then:

```
const myFont = localFont({
  src: "./fonts/Inter-Regular.woff2",
});
```

### **18.2 Multiple font weights**

Suppose you have three files:

```
fonts/
├── Inter-Regular.woff2
├── Inter-Medium.woff2
└── Inter-Bold.woff2
```

Configure them together:

```
import localFont from "next/font/local";

const inter = localFont({
  src: [
    {
      path: "./fonts/Inter-Regular.woff2",
      weight: "400",
      style: "normal",
    },
    {
      path: "./fonts/Inter-Medium.woff2",
      weight: "500",
      style: "normal",
    },
    {
      path: "./fonts/Inter-Bold.woff2",
      weight: "700",
      style: "normal",
    },
  ],
});
```

Now CSS can use:

```
body {
  font-weight: 400;
}

h2 {
  font-weight: 500;
}

h1 {
  font-weight: 700;
}
```

### **18.3 Local fonts vs Google Fonts**

| Local fonts                   | Google Fonts                                     |
| ----------------------------- | ------------------------------------------------ |
| You provide the font files    | Next.js downloads the selected font during build |
| Uses `next/font/local`        | Uses `next/font/google`                          |
| Useful for custom brand fonts | Useful for commonly available fonts              |
| No Google Fonts dependency    | Requires build-time access to the font source    |
| You manage the font files     | Next.js self-hosts the downloaded files          |

**Important:** Because Google Font files are downloaded during the build, an offline or restricted build environment may need local fonts instead.&#x20;

[image](https://www.google.com/s2/favicons?domain=https://nextjs.org\&sz=32)

Next.js+1



## **19. Using Multiple Fonts**

A website might use:

- One font for headings.
- Another font for body text.
- A separate font for special branding.

You can configure multiple fonts.

```
// src/app/fonts.ts

import { Inter, Roboto_Mono } from "next/font/google";

export const inter = Inter({
  subsets: ["latin"],
  variable: "--font-inter",
});

export const robotoMono = Roboto_Mono({
  subsets: ["latin"],
  variable: "--font-roboto-mono",
});
```

Use them in the root layout:

```
// src/app/layout.tsx

import { inter, robotoMono } from "./fonts";

export default function RootLayout({
  children,
}: {
  children: React.ReactNode;
}) {
  return (
    <html
      lang="en"
      className={`${inter.variable} ${robotoMono.variable}`}
    >
      <body>{children}</body>
    </html>
  );
}
```

Now define CSS:

```
body {
  font-family: var(--font-inter), sans-serif;
}

code,
pre {
  font-family: var(--font-roboto-mono), monospace;
}
```

### **19.1 Why use CSS variables?**

You can easily assign different fonts to different elements without repeatedly importing font classes.

For example:

```
h1,
h2,
h3 {
  font-family: var(--font-inter), sans-serif;
}

.code-editor {
  font-family: var(--font-roboto-mono), monospace;
}
```

### **19.2 When should you use this approach?**

- Applications with a branded design system.
- Dashboards with code or data displays.
- Websites using different typography for headings and body text.
- Applications using Tailwind CSS.

## **20. Applying Fonts with Tailwind CSS**

If your project uses Tailwind CSS, CSS variables can integrate neatly with its font utilities.

For example:

```
// src/app/layout.tsx

import { Inter } from "next/font/google";

const inter = Inter({
  subsets: ["latin"],
  variable: "--font-inter",
});

export default function RootLayout({
  children,
}: {
  children: React.ReactNode;
}) {
  return (
    <html lang="en" className={inter.variable}>
      <body>{children}</body>
    </html>
  );
}
```

Then, depending on your Tailwind version and configuration, define a font family using the CSS variable.

For Tailwind CSS v4, in your CSS:

```
@import "tailwindcss";

@theme inline {
  --font-sans: var(--font-inter);
}
```

Now:

```
<h1 className="font-sans text-3xl font-bold">
  Welcome
</h1>
```

The font is applied using your configured variable.

## **21. Understanding&#x20;**`next/script`

Now let's discuss JavaScript scripts.

Most applications use third-party JavaScript for additional functionality.

Examples:

- Google Analytics.
- Google Tag Manager.
- Payment provider SDKs.
- Live chat widgets.
- Maps.
- Customer support tools.
- Advertising and tracking scripts.

Suppose you directly add a third-party script:

```
<script src="https://example.com/widget.js"></script>
```

If scripts load at inappropriate times, they can compete with your application for network and main-thread resources.

Next.js provides the `Script` component.

Import it using:

```
import Script from "next/script";
```

It allows you to control when external scripts load and execute.

[image](https://www.google.com/s2/favicons?domain=https://nextjs.org\&sz=32)

Next.js+1

### **21.1 Basic example**

```
import Script from "next/script";

export default function Page() {
  return (
    <main>
      <h1>Welcome</h1>

      <Script src="https://example.com/widget.js" />
    </main>
  );
}
```

By default, the strategy is:

```
strategy="afterInteractive"
```

### **21.2 Why use&#x20;**`next/script`**&#x20;instead of a normal script tag?**

It provides:

- Controlled loading strategies.
- Integration with Next.js layouts.
- Script loading management across client-side navigation.
- Callback support.
- Better control over third-party script placement.

Next.js also ensures that a script placed in a shared layout is not repeatedly loaded when navigating between routes in that layout.&#x20;

[image](https://www.google.com/s2/favicons?domain=https://nextjs.org\&sz=32)

Next.js



## **22. Understanding Script Loading Strategies**

Next.js provides four documented strategies:

1. `beforeInteractive`
2. `afterInteractive`
3. `lazyOnload`
4. `worker`

Let's understand each one.

### **22.1&#x20;**`beforeInteractive`

Loads a script before Next.js application code and before page hydration.

Example:

```
import Script from "next/script";

export default function RootLayout({
  children,
}: {
  children: React.ReactNode;
}) {
  return (
    <html lang="en">
      <body>
        {children}

        <Script
          src="https://example.com/critical.js"
          strategy="beforeInteractive"
        />
      </body>
    </html>
  );
}
```

Use it only for scripts that genuinely must execute before the application becomes interactive.

This strategy is intended for scripts that need to run across the application and should be placed in the root layout.

### **22.2&#x20;**`afterInteractive`

This is the default strategy.

```
<Script
  src="https://example.com/analytics.js"
  strategy="afterInteractive"
/>
```

It loads the script early, after some hydration has occurred.

Useful for:

- Analytics.
- Chat widgets.
- Customer support integrations.
- Scripts that are not required before initial rendering.

### **22.3&#x20;**`lazyOnload`

Loads the script later, during browser idle time.

```
<Script
  src="https://example.com/optional-widget.js"
  strategy="lazyOnload"
/>
```

Useful for:

- Optional widgets.
- Non-critical tracking.
- Features that can wait until after initial page loading.

### **22.4&#x20;**`worker`

This strategy is experimental and uses Partytown to offload supported third-party scripts to a Web Worker.

Example:

```
<Script
  src="https://example.com/analytics.js"
  strategy="worker"
/>
```

However, current Next.js documentation warns that the `worker` strategy is not stable and does not yet work with the App Router.

Do not use it as a standard production solution for your App Router applications.&#x20;

[image](https://www.google.com/s2/favicons?domain=https://nextjs.org\&sz=32)

Next.js



### **Comparison**

| Strategy            | When it loads                   | Typical use                     |
| ------------------- | ------------------------------- | ------------------------------- |
| `beforeInteractive` | Before Next.js hydration        | Essential early scripts         |
| `afterInteractive`  | After some hydration            | Analytics and integrations      |
| `lazyOnload`        | During browser idle time        | Non-critical widgets            |
| `worker`            | In a Web Worker, experimentally | Specialized third-party scripts |

Recommended default

Use `afterInteractive` for ordinary third-party scripts and `lazyOnload` for non-critical scripts.

## **23. Where Should You Place Third-Party Scripts?**

You can add scripts in different layouts depending on where they are required.

### **23.1 Script in the root layout**

If a script is required throughout your application:

```
// src/app/layout.tsx

import Script from "next/script";

export default function RootLayout({
  children,
}: {
  children: React.ReactNode;
}) {
  return (
    <html lang="en">
      <body>{children}</body>

      <Script
        src="https://example.com/global-widget.js"
        strategy="afterInteractive"
      />
    </html>
  );
}
```

This loads the script across application routes.

### **23.2 Script in a nested layout**

Suppose a customer support widget is needed only in the dashboard.

```
app/
├── layout.tsx
├── page.tsx
│
└── dashboard/
    ├── layout.tsx
    ├── page.tsx
    └── settings/
        └── page.tsx
```

Add the script in the dashboard layout:

```
// app/dashboard/layout.tsx

import Script from "next/script";

export default function DashboardLayout({
  children,
}: {
  children: React.ReactNode;
}) {
  return (
    <>
      {children}

      <Script
        src="https://example.com/support-widget.js"
        strategy="lazyOnload"
      />
    </>
  );
}
```

Now the script is associated with the dashboard route tree rather than every page of your application.

**Best practice:** Load third-party scripts only on the routes where they are required.&#x20;

[image](https://www.google.com/s2/favicons?domain=https://nextjs.org\&sz=32)

Next.js



### **23.3 Using&#x20;**`onLoad`**&#x20;and&#x20;**`onReady`

Sometimes you need to execute code after a script loads.

`next/script` provides callbacks such as:

- `onLoad`
- `onReady`
- `onError`

Example:

```
"use client";

import Script from "next/script";

export default function AnalyticsScript() {
  return (
    <Script
      src="https://example.com/analytics.js"
      strategy="afterInteractive"
      onLoad={() => {
        console.log("Script loaded");
      }}
      onReady={() => {
        console.log("Script ready");
      }}
      onError={() => {
        console.error("Script failed to load");
      }}
    />
  );
}
```

These callbacks require a Client Component.

Use `onReady` when initialization needs to happen after the script is ready, including after component remounts. Ensure that your initialization code is safe to run more than once.

### **23.4 Inline scripts**

You can also use `next/script` for inline JavaScript.

```
import Script from "next/script";

export default function Page() {
  return (
    <>
      <h1>Welcome</h1>

      <Script id="page-initialization">
        {`
          console.log("Page initialized");
        `}
      </Script>
    </>
  );
}
```

An `id` is required for inline scripts so Next.js can track them.

**Security note:** Avoid inserting untrusted user-generated content into inline scripts. If you use a Content Security Policy (CSP), configure it appropriately for your script requirements.

[image](https://www.google.com/s2/favicons?domain=https://nextjs.org\&sz=32)

Next.js

## **24. Practical Example: Google Analytics Script**

Suppose you want to integrate Google Analytics.

You have a measurement ID:

```
G-XXXXXXXXXX
```

Create a Client Component:

```
// src/app/components/google-analytics.tsx

"use client";

import Script from "next/script";

export default function GoogleAnalytics() {
  const measurementId =
    process.env.NEXT_PUBLIC_GA_ID;

  if (!measurementId) {
    return null;
  }

  return (
    <>
      <Script
        src={`https://www.googletagmanager.com/gtag/js?id=${measurementId}`}
        strategy="afterInteractive"
      />

      <Script
        id="google-analytics"
        strategy="afterInteractive"
      >
        {`
          window.dataLayer = window.dataLayer || [];

          function gtag() {
            window.dataLayer.push(arguments);
          }

          window.gtag = gtag;

          gtag('js', new Date());
          gtag('config', '${measurementId}');
        `}
      </Script>
    </>
  );
}
```

Then include it in your root layout:

```
import GoogleAnalytics from "./components/google-analytics";

export default function RootLayout({
  children,
}: {
  children: React.ReactNode;
}) {
  return (
    <html lang="en">
      <body>
        {children}
        <GoogleAnalytics />
      </body>
    </html>
  );
}
```

Add the public measurement ID to your environment configuration:

```
NEXT_PUBLIC_GA_ID=G-XXXXXXXXXX
```

This is an illustrative integration. For production, use Google's current recommended installation and consent configuration, and ensure that analytics events are not sent before required user consent in jurisdictions where consent is necessary.

## **25. Understanding Static Assets vs Dynamic Assets**

It is useful to distinguish between different ways of serving files.

| Asset type             | Example                       | Recommended approach                           |
| ---------------------- | ----------------------------- | ---------------------------------------------- |
| Static logo            | `logo.png`                    | `public` or static import                      |
| Product image          | Cloudinary URL                | `next/image` with `remotePatterns`             |
| User-uploaded image    | MongoDB record containing URL | Remote image with trusted host configuration   |
| Font                   | `.woff2`                      | `next/font/local`                              |
| Google Font            | Inter                         | `next/font/google`                             |
| PDF                    | Brochure                      | `public` folder or protected download endpoint |
| Video                  | Demo video                    | Appropriate media hosting and HTML video       |
| Third-party JavaScript | Analytics                     | `next/script`                                  |
| SVG icon               | Logo or icon                  | Static asset or inline SVG                     |

### **Important: Where should user uploads go?**

Do not normally store user-uploaded images inside the Next.js `public` folder at runtime.

Why?

In many production hosting environments, the deployment filesystem is not a persistent user-upload storage system.

Instead, use:

- Cloudinary.
- Amazon S3.
- Another object-storage provider.

Store the resulting file URL and metadata in your database.

This is especially useful when you deploy to platforms where application instances may be replaced or scaled independently.

## **26. Core Web Vitals**

Now let's understand how image, font and script optimization affects real user experience.

Google uses Core Web Vitals as part of its page experience evaluation.

The three main metrics are:

1. LCP – Largest Contentful Paint
2. INP – Interaction to Next Paint
3. CLS – Cumulative Layout Shift

### **26.1 LCP – Largest Contentful Paint**

LCP measures how long it takes for the largest visible content element to render.

Examples:

- Hero image.
- Main banner.
- Large heading or text block.

**Example: Slow LCP**

[Flawless Home Website — Hero Section Design Case Study :: Behance](https://images.openai.com/static-rsc-4/oh3Z6m8w5BsJYnNOHo9RM5uTE-1owNUDzW9GQr9QTmjJr4H2KXxCHHL_GJTEavIqYeLUO7cbBda8dp5hzgM4AzdPEYO9KpO3fJ1Qr5gnvfWN0WocgLJM_H-Tf1m_OjxAMdWLVb7gpX7GoGyWjXQZPmh1_J0kALRy8UzVVSHEhwo?purpose=inline)

[behance.net](https://www.behance.net/gallery/241437709/Flawless-Home-Website-Hero-Section-Design-Case-Study?utm_source=chatgpt.com)

The main hero image takes too long to appear.

Ways to improve LCP:

- Optimize the hero image.
- Use the correct image dimensions.
- Use `preload` when appropriate for a critical image.
- Avoid loading unnecessary third-party scripts early.
- Improve server response time.
- Use a suitable CDN.

### **26.2 INP – Interaction to Next Paint**

INP measures how responsive the page is to user interactions.

Examples:

- Clicking a button.
- Opening a dropdown.
- Submitting a form.
- Changing a tab.

Ways to improve INP:

- Reduce unnecessary JavaScript.
- Avoid long-running main-thread tasks.
- Minimize heavy third-party scripts.
- Break up expensive computations.
- Keep Client Components focused.

### **26.3 CLS – Cumulative Layout Shift**

CLS measures unexpected movement of page elements during loading.

Example:

The user is about to click a button, but an image loads above it and pushes the button down.

**Example: Layout shift**

BEFORE IMAGE LOAD

Product information

Buy Now

AFTER IMAGE LOAD

[Asus finally has its own thin-bezel gaming laptop with the ROG GL504 Strix Scar II and Hero II - Notebookcheck News](https://images.openai.com/static-rsc-4/Y6T3YXkY3b94rFGAu8KOxSg-PWSp34NN5P6o079zu8o9oc1lsqodZv9f_xX_tTEGtZXY1xaAwD_roKkS_wT3h6AUa_wW45KPmYb40uhBVWc5u0T1pYY6Z3PAK2ZVHTBPH5wmWLtJ2XM23XtgjPS3aneKTkyE5fA39JfC15O8ASo?purpose=inline)

[notebookcheck.net](https://www.notebookcheck.net/Asus-finally-has-its-own-thin-bezel-gaming-laptop-with-the-ROG-GL504-Strix-Scar-II-and-Hero-II.307590.0.html?utm_source=chatgpt.com)

Product information

Buy Now

The newly loaded image changes the position of content below it.

Ways to reduce CLS:

- Set image dimensions.
- Use `fill` inside a correctly sized container.
- Reserve space for advertisements and embeds.
- Use `next/font` to reduce font-related layout shifts.
- Avoid inserting unexpected content above existing content.

### **26.4 Core Web Vitals thresholds**

| Metric | Good     | Needs improvement | Poor         |
| ------ | -------- | ----------------- | ------------ |
| LCP    | ≤ 2.5 s  | 2.5–4 s           | Above 4 s    |
| INP    | ≤ 200 ms | 200–500 ms        | Above 500 ms |
| CLS    | ≤ 0.1    | 0.1–0.25          | Above 0.25   |

These are field-measured thresholds evaluated at the 75th percentile of page visits.&#x20;

[image](https://www.google.com/s2/favicons?domain=https://nextjs.org\&sz=32)

Next.js+1



## **27. Tools for Testing Performance**

After optimizing your application, you need to measure the results.

These tools are useful:

[Website & Funnel Builder | Custom Websites & High-Converting Funnels](https://images.openai.com/static-rsc-4/O3v_wKndiY0WuG8LI_81v5dnUU3WCPul7_h7TrxSYiGhnGK_JWc3GSrklXxZOMFELYh-hNOTCUTVrYQqvW2kEn6DIiCOx09cNzH3n3XsFpzdB74phNb7l-yYlROwJ-4yXdvoi0JX-O4lwdA60M_rSCvoZU1zbnj0xhvp8zrBDVA?purpose=inline)

**Google PageSpeed Insights**

Analyze page performance, LCP, INP, CLS and optimization opportunities.

[Open PageSpeed Insights](https://pagespeed.web.dev/?utm_source=chatgpt.com)

[File:Google Lighthouse logo.svg - Wikimedia Commons](https://images.openai.com/static-rsc-4/sgZB_rMlyP6h0CdtiUfP6Wadh8zIW6XK-_QXOKW0LrXCItsvewVQvZa3xhntuyPElN6kR-nvYIJ8B5X95jBDbV6Sxvyd9wJYoD-hlu1MEkQNh5onjg3yVX2P3IKmdw_lFJXddPs_RTGQDMeJKJH7EXv3ucZsEyn4s5LSEB5UzSU?purpose=inline)

**Lighthouse**

Audit performance, accessibility, SEO and best practices.

Available through Chrome Developer Tools.

[Chrome Dev Tools Icon, HD Png Download , Transparent Png Image - PNGitem](https://images.openai.com/static-rsc-4/rw3r2D7cAjolcuKc-ySAHz5YsCSS9Oxd3tlz_LWsZItb58cUuXtyMZGpOU1ObTHz3e3YmKtWT725UCkt6cBqOkpLvwhaXea8JVfbcQN8BoO06YF488xcML2cAI5ECHBd8OD6Sqg1V5m2p_OKU3GEHvrYno6HFJk4-4LhT0axQxA?purpose=inline)

**Chrome DevTools**

Inspect image sizes, network requests, JavaScript execution and loading behavior.

[DevTools documentation](https://developer.chrome.com/docs/devtools/?utm_source=chatgpt.com)

[WebPageTest Forums](https://images.openai.com/static-rsc-4/FjgQ5X4KlQzfy1P-3rB2OfK79lAu8LBJwi1oLlD15L-bkgv2vYB0S8EWDOnVzhOG1l4qC8_AHXu_T_XxLNF0yWiqARex4MGH7V8Jmi45jj8CRqZ2H8n9LzLzmlS8BpcG--lR7qXDd0wLbUozBdW0Sfz4AXcJTZESAF-WbGhpCec?purpose=inline)

**WebPageTest**

Test page-loading performance under different network and device conditions.

[Open WebPageTest](https://www.webpagetest.org/?utm_source=chatgpt.com)

### **How to measure your application**

Build your application:

```
npm run build
```

Start production mode:

```
npm run start
```

Then test the production URL using PageSpeed Insights or Lighthouse.

**Important:** Production performance measurements are more representative than relying only on the development server.

## **28. Common Mistakes and Their Solutions**

| Mistake                                       | Problem                                         | Solution                             |
| --------------------------------------------- | ----------------------------------------------- | ------------------------------------ |
| Using `/public/logo.png`                      | Incorrect public URL                            | Use `/logo.png`                      |
| Using `<img>` for every image                 | Misses Next.js optimization features            | Use `next/image` where appropriate   |
| Forgetting image dimensions                   | Can cause layout shifts                         | Provide width and height             |
| Using `fill` without a positioned parent      | Image may not fit correctly                     | Set parent to `position: relative`   |
| Forgetting `sizes`                            | Browser may download unnecessarily large images | Specify responsive sizes             |
| Preloading every image                        | Competes for network bandwidth                  | Preload only critical images         |
| Using `priority` in Next.js 16                | Deprecated property                             | Use `preload` where appropriate      |
| Using remote images without configuration     | Image optimization may fail                     | Configure `remotePatterns`           |
| Allowing arbitrary image hosts                | Unnecessary security and resource exposure      | Restrict allowed image sources       |
| Using huge original images                    | Slower loading                                  | Resize and compress images           |
| Loading all Google Font weights               | Increases font assets                           | Load only required weights           |
| Using external font stylesheets unnecessarily | Additional browser requests                     | Use `next/font`                      |
| Loading analytics before necessary            | Can delay rendering                             | Use appropriate script strategy      |
| Adding scripts globally when not needed       | Unnecessary work on unrelated pages             | Use nested layouts                   |
| Loading too many third-party widgets          | Can hurt responsiveness                         | Remove or defer non-critical scripts |

## **29. Complete Real-World Optimization Example**

Let's combine the concepts into one Next.js application.

Suppose you are building an e-commerce website.

### **29.1 Recommended folder structure**

```
my-next-app/
│
├── public/
│   ├── logo.png
│   ├── favicon.ico
│   └── images/
│       └── hero.jpg
│
├── src/
│   └── app/
│       ├── fonts.ts
│       ├── layout.tsx
│       ├── page.tsx
│       │
│       ├── components/
│       │   ├── hero.tsx
│       │   └── analytics.tsx
│       │
│       └── products/
│           └── page.tsx
│
├── next.config.ts
└── package.json
```

### **29.2 Configure remote images**

```
// next.config.ts

import type { NextConfig } from "next";

const nextConfig: NextConfig = {
  images: {
    remotePatterns: [
      {
        protocol: "https",
        hostname: "res.cloudinary.com",
        pathname: "/your-cloud-name/**",
      },
    ],

    qualities: [60, 75, 85],
  },
};

export default nextConfig;
```

### **29.3 Configure fonts**

```
// src/app/fonts.ts

import { Inter } from "next/font/google";

export const inter = Inter({
  subsets: ["latin"],
  display: "swap",
  variable: "--font-inter",
});
```

### **29.4 Configure root layout**

```
// src/app/layout.tsx

import { inter } from "./fonts";

export default function RootLayout({
  children,
}: {
  children: React.ReactNode;
}) {
  return (
    <html lang="en" className={inter.variable}>
      <body>{children}</body>
    </html>
  );
}
```

### **29.5 Create a hero component**

```
// src/app/components/hero.tsx

import Image from "next/image";

export default function Hero() {
  return (
    <section
      style={{
        position: "relative",
        height: "500px",
        width: "100%",
      }}
    >
      <Image
        src="/images/hero.jpg"
        alt="Latest technology products"
        fill
        sizes="100vw"
        preload
        style={{
          objectFit: "cover",
        }}
      />

      <div
        style={{
          position: "absolute",
          inset: 0,
          display: "flex",
          alignItems: "center",
          justifyContent: "center",
          color: "white",
          zIndex: 1,
        }}
      >
        <h1>Discover New Technology</h1>
      </div>
    </section>
  );
}
```

### **29.6 Create the homepage**

```
// src/app/page.tsx

import Hero from "./components/hero";

export default function HomePage() {
  return (
    <main>
      <Hero />

      <section>
        <h2>Popular Products</h2>
      </section>
    </main>
  );
}
```

### **What did we optimize?**

- Images use `next/image`.
- The hero image uses `preload`.
- Responsive image dimensions are specified.
- The font is optimized using `next/font`.
- Remote images are restricted to an approved host.
- The page uses a Server Component by default.

## **30. Interview Questions and Answers**

### **Basic Level**

**Q1. What is the&#x20;**`public`**&#x20;folder in Next.js?**

It is a special folder used to serve static files from the application's root URL.

**Q2. What is&#x20;**`next/image`**?**

It is the Next.js Image component that extends HTML image functionality with automatic image optimization.

**Q3. What is the purpose of&#x20;**`alt`**?**

It provides alternative text for accessibility and helps communicate the image's meaning.

**Q4. What is&#x20;**`next/font`**?**

It is a Next.js module that optimizes and self-hosts Google and local fonts.

**Q5. What is&#x20;**`next/script`**?**

It is a component for loading and managing third-party scripts with controlled loading strategies.

### **Intermediate Level**

**Q6. What is the difference between&#x20;**`width`**&#x20;and&#x20;**`fill`**&#x20;in&#x20;**`next/image`**?**

- `width` and `height` describe intrinsic image dimensions.
- `fill` makes the image fill its positioned parent container.

**Q7. What is the purpose of&#x20;**`sizes`**?**

It tells the browser how much space a responsive image is expected to occupy at different viewport widths.

**Q8. What is lazy loading?**

Lazy loading delays downloading an image until it approaches the visible viewport.

**Q9. What is the difference between&#x20;**`preload`**&#x20;and&#x20;**`loading="lazy"`**?**

`preload` requests an important resource early. Lazy loading delays an image request until it is near the viewport.

**Q10. What is&#x20;**`remotePatterns`**?**

It is a Next.js image configuration that specifies which external image URLs are permitted for optimization.

**Q11. What is the difference between&#x20;**`next/font/google`**&#x20;and&#x20;**`next/font/local`**?**

Google Fonts are downloaded during the build and self-hosted. Local fonts use font files supplied by the developer.

**Q12. What is the difference between&#x20;**`afterInteractive`**&#x20;and&#x20;**`lazyOnload`**?**

`afterInteractive` loads a script after some hydration has occurred, while `lazyOnload` waits until the browser is idle.

### **Advanced Level**

**Q13. Why should we use&#x20;**`sizes`**&#x20;with&#x20;**`fill`**?**

Without an accurate `sizes` value, the browser may assume the image occupies the full viewport width and download unnecessarily large images.

**Q14. Why should we use&#x20;**`preload`**&#x20;selectively?**

Preloading too many resources creates competition for network bandwidth and may delay more important resources.

**Q15. Why does Next.js use&#x20;**`remotePatterns`**?**

To restrict remote image optimization to approved external hosts, paths and protocols.

**Q16. How does&#x20;**`next/font`**&#x20;improve performance?**

It self-hosts font files and avoids additional browser requests to external font providers, while optimizing font loading.

**Q17. What is LCP?**

Largest Contentful Paint measures when the largest visible content element is rendered.

**Q18. What is INP?**

Interaction to Next Paint measures the responsiveness of a page to user interactions.

**Q19. What is CLS?**

Cumulative Layout Shift measures unexpected visual movement of page elements.

**Q20. Why should we avoid loading third-party scripts globally?**

They may add unnecessary network and main-thread work to routes that do not need them.

## **31. Practical Assignment**

Hands-on project

Build an optimized e-commerce homepage.

### **Task 1: Static assets**

- Create a `public` folder.
- Add a logo.
- Add a hero image.
- Add product images.
- Add a downloadable PDF.
- Access assets using correct root-relative URLs.

### **Task 2: Image optimization**

- Use `next/image`.
- Configure image width and height.
- Implement `fill` in a product card.
- Use `object-fit: cover`.
- Use `object-fit: contain`.
- Configure responsive `sizes`.
- Use lazy loading for below-the-fold images.
- Use `preload` for the critical hero image.
- Add appropriate alt text.
- Configure Cloudinary using `remotePatterns`.
- Test image quality settings.

### **Task 3: Font optimization**

- Add a Google Font using `next/font/google`.
- Add a local font using `next/font/local`.
- Configure multiple font weights.
- Configure a CSS variable.
- Apply fonts globally.
- Verify that the browser does not make unnecessary Google Fonts requests.

### **Task 4: Script optimization**

- Add a third-party script using `next/script`.
- Test `afterInteractive`.
- Test `lazyOnload`.
- Add a script to a nested layout.
- Use `onLoad` and `onError`.
- Avoid loading unnecessary scripts globally.

### **Task 5: Performance testing**

- Run `npm run build`.
- Run the production server.
- Test with Lighthouse.
- Check LCP.
- Check INP.
- Check CLS.
- Compare the page before and after optimization.
- Inspect image sizes in Chrome DevTools.

## **32. Quick Revision Notes**

| Concept               | Remember                                  |
| --------------------- | ----------------------------------------- |
| `public`              | Serves static assets from the root URL    |
| Static import         | Imports an asset into application code    |
| `next/image`          | Optimized image component                 |
| `width` / `height`    | Intrinsic image dimensions                |
| `fill`                | Fills a positioned parent                 |
| `object-fit: contain` | Shows the entire image                    |
| `object-fit: cover`   | Fills and crops the image                 |
| `sizes`               | Describes responsive rendered image width |
| `loading="lazy"`      | Defers image loading                      |
| `loading="eager"`     | Requests image immediately                |
| `preload`             | Preloads a critical image                 |
| `priority`            | Deprecated in Next.js 16                  |
| `placeholder="blur"`  | Shows a blur while the image loads        |
| `remotePatterns`      | Restricts approved external image sources |
| `unoptimized`         | Skips Next.js image optimization          |
| `next/font/google`    | Google Font self-hosting                  |
| `next/font/local`     | Local font optimization                   |
| `variable`            | Exposes a font through a CSS variable     |
| `next/script`         | Optimized script loading                  |
| `beforeInteractive`   | Loads before hydration                    |
| `afterInteractive`    | Loads after some hydration                |
| `lazyOnload`          | Loads during browser idle time            |
| LCP                   | Loading performance                       |
| INP                   | Interaction responsiveness                |
| CLS                   | Visual stability                          |

## **33. Phase 18 Completion Checklist**

**Your learning progress**

### **0/21**

Understand static assets and the public folder

Understand public URLs and static imports

Use next/image correctly

Understand width, height and aspect ratios

Use fill and objectFit

Configure responsive sizes

Understand lazy and eager loading

Use preload correctly in Next.js 16

Implement image placeholders

Configure remotePatterns

Understand image quality and unoptimized

Write meaningful alt text

Load Google Fonts using next/font/google

Load local fonts using next/font/local

Configure font weights and subsets

Use CSS variables for fonts

Understand next/script

Use beforeInteractive, afterInteractive and lazyOnload

Load scripts in nested layouts

Understand LCP, INP and CLS

Complete the practical assignment and test performance

Reset checklist

## **34. Next Phase Preview**

Next: Phase 19

# **Deployment, Production Builds and CI/CD**

**Topics we will cover:**

1. Development vs production environments.
2. Understanding `next dev`, `next build` and `next start`.
3. Production build process.
4. Understanding `.next` build output.
5. Deploying Next.js to Vercel.
6. Deploying Next.js to a VPS.
7. Deploying with Node.js, PM2 and Nginx.
8. Environment variables in production.
9. Custom domains and HTTPS.
10. GitHub integration and CI/CD.
11. Build failures and deployment debugging.
12. Production logs and monitoring.
13. Deployment architecture and best practices.

**When you're ready, say&#x20;**`OK, Phase 19`**.**

## **Official documentation**

- [Next.js Image Component](https://nextjs.org/docs/app/api-reference/components/image?utm_source=chatgpt.com)
- [Next.js Public Folder](https://nextjs.org/docs/app/api-reference/file-conventions/public-folder?utm_source=chatgpt.com)
- [Next.js Font Optimization](https://nextjs.org/docs/app/getting-started/fonts?utm_source=chatgpt.com)
- [Next.js Font API Reference](https://nextjs.org/docs/app/api-reference/components/font?utm_source=chatgpt.com)
- [Next.js Script Optimization](https://nextjs.org/docs/app/guides/scripts?utm_source=chatgpt.com)
- [Web Vitals and performance thresholds](https://web.dev/articles/vitals?utm_source=chatgpt.com)