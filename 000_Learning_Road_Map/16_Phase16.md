# **Phase 16 – Metadata, SEO and Open Graph**



Next.js 16.x

App Router

TypeScript

Intermediate



## **1. Learning Objectives**

By the end of this phase, you will understand how to:

1. Understand SEO and how search engines discover web pages.
2. Configure page titles and meta descriptions.
3. Use Next.js static metadata and dynamic metadata.
4. Configure title templates and metadata inheritance.
5. Set Open Graph metadata for social media sharing.
6. Configure Twitter/X card metadata.
7. Set canonical URLs and `metadataBase`.
8. Generate dynamic Open Graph images.
9. Create `sitemap.xml` and `robots.txt`.
10. Configure favicons and application icons.
11. Implement practical SEO for a production Next.js application.

**Version note:** This phase uses the Next.js 16 App Router Metadata API. In this API, `metadata` and `generateMetadata` are Server Component exports. You do not manually create `<head>` tags for ordinary metadata. Next.js generates them for you.&#x20;

[image](https://www.google.com/s2/favicons?domain=https://nextjs.org\&sz=32)

Next.js+1



## **2. What Is SEO?**

SEO stands for **Search Engine Optimization**.

It is the process of improving a website so that search engines can discover, understand, and potentially show its pages in relevant search results.

For example, suppose you have a website that sells laptops.

A user searches Google:

```
Best gaming laptop under 80000 in India
```

Google may show pages from websites that provide relevant content.

Your goal is to help search engines understand:

- What your page is about.
- Which users might find it useful.
- What information the page contains.
- Which URL represents the original page.
- What image and description should appear when someone shares the page.

### **2.1 How does SEO work?**

1. **Crawling**

   Search engine bots discover URLs and fetch their content.
2. **Rendering**

   Search engines process page content, including supported JavaScript-rendered content.
3. **Indexing**

   Search engines evaluate and may store information about the page in their index.
4. **Ranking**

   Search engines decide which indexed pages are relevant to a particular search query.

### **2.2 Important SEO terminology**

| Term             | Meaning                                                            |
| ---------------- | ------------------------------------------------------------------ |
| SEO              | Search Engine Optimization                                         |
| SERP             | Search Engine Results Page                                         |
| Crawler / Bot    | Automated program that discovers and fetches pages                 |
| Indexing         | Storing and organizing information about pages                     |
| Ranking          | Ordering results for a search query                                |
| Meta title       | Title associated with a webpage                                    |
| Meta description | Short summary of a webpage                                         |
| Canonical URL    | Preferred URL for a page                                           |
| Sitemap          | File listing URLs you want search engines to discover              |
| Robots.txt       | File containing crawler access rules                               |
| Open Graph       | Metadata protocol used for rich link previews                      |
| Structured data  | Machine-readable information, commonly using Schema.org vocabulary |

**Important:** Adding metadata does not guarantee that Google will index or rank your page. SEO also depends on content quality, relevance, technical accessibility, performance, links, and many other factors.

## **3. What Is Metadata?**

Metadata is information that describes your webpage.

It is generally placed in the HTML document's `<head>` section.

For example:

```
<head>
  <title>Best Gaming Laptops | Tech Store</title>

  <meta
    name="description"
    content="Explore gaming laptops with detailed specifications and prices."
  />
</head>
```

This information helps browsers, search engines, and social platforms understand the page.

### **3.1 Common metadata elements**

| Element            | Purpose                                                               |
| ------------------ | --------------------------------------------------------------------- |
| `<title>`          | Document title shown in browser tabs and often used in search results |
| `meta description` | Summary that may be displayed in search results                       |
| `canonical`        | Preferred URL for a page                                              |
| `robots`           | Indexing and crawling directives                                      |
| `og:title`         | Title for social sharing previews                                     |
| `og:description`   | Description for social sharing previews                               |
| `og:image`         | Image for social sharing previews                                     |
| `twitter:card`     | X/Twitter card presentation type                                      |

### **3.2 Where does Next.js generate metadata?**

You can inspect generated metadata through:

1. Browser Developer Tools.
2. The Elements panel.
3. The document's `<head>` section.
4. View Page Source, where the server-rendered HTML is available.

Next.js automatically manages the relevant metadata tags from your configuration.

## **4. Static Metadata Using the&#x20;**`metadata`**&#x20;Object**

Next.js provides a built-in Metadata API.

You can export a `metadata` object from a `page.tsx` or `layout.tsx` file.

### **4.1 Basic example**

```
// app/about/page.tsx

import type { Metadata } from "next";

export const metadata: Metadata = {
  title: "About Us",
  description: "Learn more about our company and team.",
};

export default function AboutPage() {
  return (
    <main>
      <h1>About Us</h1>
    </main>
  );
}
```

Next.js generates the corresponding HTML metadata.

Conceptually, the resulting HTML includes:

```
<head>
  <title>About Us</title>

  <meta
    name="description"
    content="Learn more about our company and team."
  />
</head>
```

You do not need to manually write these tags.

### **4.2 Metadata in the root layout**

For site-wide metadata, define it in the root layout.

```
// app/layout.tsx

import type { Metadata } from "next";

export const metadata: Metadata = {
  title: "Tech Store",
  description:
    "Discover laptops, computers and accessories at Tech Store.",
};

export default function RootLayout({
  children,
}: Readonly<{
  children: React.ReactNode;
}>) {
  return (
    <html lang="en">
      <body>{children}</body>
    </html>
  );
}
```

This metadata provides defaults for pages beneath the root layout.

### **4.3 Metadata in nested pages**

```
// app/contact/page.tsx

import type { Metadata } from "next";

export const metadata: Metadata = {
  title: "Contact Us",
  description: "Contact the Tech Store support team.",
};

export default function ContactPage() {
  return <h1>Contact Us</h1>;
}
```

The contact page has its own title and description.

### **Important rules**

- `metadata` is supported only in Server Components.
- You cannot export both `metadata` and `generateMetadata` from the same route segment.
- Metadata can be defined in layouts and pages.
- Next.js resolves the metadata and generates the relevant document head tags.

[image](https://www.google.com/s2/favicons?domain=https://nextjs.org\&sz=32)

Next.js+1

## **5. Title Templates and Metadata Inheritance**

This is especially useful for applications with many pages.

Suppose your website has these pages:

```
/
├── about
├── contact
├── products
│   ├── laptops
│   └── mobiles
└── blog
```

You want the browser titles to look like this:

```
Tech Store
About Us | Tech Store
Contact Us | Tech Store
Laptops | Tech Store
Mobiles | Tech Store
```

Instead of repeating `| Tech Store` on every page, use a title template.

### **5.1 Configure a title template**

```
// app/layout.tsx

import type { Metadata } from "next";

export const metadata: Metadata = {
  title: {
    template: "%s | Tech Store",
    default: "Tech Store",
  },
};
```

### **5.2 Define a child page title**

```
// app/about/page.tsx

import type { Metadata } from "next";

export const metadata: Metadata = {
  title: "About Us",
};

export default function AboutPage() {
  return <h1>About Us</h1>;
}
```

Result:

```
About Us | Tech Store
```

### **5.3 Understand&#x20;**`default`**,&#x20;**`template`**, and&#x20;**`absolute`

| Property   | Purpose                                                 |
| ---------- | ------------------------------------------------------- |
| `default`  | Fallback title for child routes without their own title |
| `template` | Adds a prefix or suffix to child page titles            |
| `absolute` | Uses a title without applying a parent template         |

Example:

```
export const metadata: Metadata = {
  title: {
    default: "Tech Store",
    template: "%s | Tech Store",
  },
};
```

A child page can override the template:

```
export const metadata: Metadata = {
  title: {
    absolute: "Special Announcement",
  },
};
```

Result:

```
Special Announcement
```

It does not become `Special Announcement | Tech Store`.

**Important rules:**

1. A title template needs a default title.
2. A template applies to child route segments, not to a title defined in the same layout.
3. A `title.template` in a page has no effect on child pages because pages are terminal route segments.

[image](https://www.google.com/s2/favicons?domain=https://nextjs.org\&sz=32)

Next.js

### **5.4 Metadata inheritance and merging**

Consider this structure:

```
app/
├── layout.tsx
├── page.tsx
└── blog/
    ├── layout.tsx
    └── page.tsx
```

Root layout:

```
export const metadata: Metadata = {
  title: {
    default: "Tech Store",
    template: "%s | Tech Store",
  },
  description: "Your trusted technology store.",
  openGraph: {
    title: "Tech Store",
    description: "Technology products and accessories.",
    images: ["/og-default.png"],
  },
};
```

Blog page:

```
export const metadata: Metadata = {
  title: "Blog",
  openGraph: {
    title: "Tech Store Blog",
  },
};
```

The resulting metadata uses:

- The blog page's title.
- The blog page's Open Graph title.
- The root description.
- The root Open Graph image is not automatically retained when the child defines its own `openGraph` object.

Why?

Metadata merging is shallow for nested objects. A child `openGraph` object replaces the parent's `openGraph` object rather than recursively merging every nested property.

If you want to retain selected parent values, explicitly spread them:

```
const openGraphDefaults = {
  description: "Technology products and accessories.",
  images: ["/og-default.png"],
};

export const metadata: Metadata = {
  title: "Blog",
  openGraph: {
    ...openGraphDefaults,
    title: "Tech Store Blog",
  },
};
```

This is a common source of missing Open Graph descriptions and images.&#x20;

[image](https://www.google.com/s2/favicons?domain=https://nextjs.org\&sz=32)

Next.js



## **6. Dynamic Metadata Using&#x20;**`generateMetadata()`

Static metadata is suitable when every visitor sees the same page title and description.

But consider an e-commerce website.

You have:

```
/products/iphone-17
/products/samsung-s26
/products/macbook-pro
```

Each product needs its own:

- Title
- Description
- Image
- Open Graph information

You cannot conveniently hardcode all these values into one static metadata object.

Use `generateMetadata()`.

### **6.1 Basic dynamic metadata**

```
// app/products/[id]/page.tsx

import type { Metadata } from "next";

type Props = {
  params: Promise<{
    id: string;
  }>;
};

export async function generateMetadata({
  params,
}: Props): Promise<Metadata> {
  const { id } = await params;

  return {
    title: `Product ${id}`,
    description: `Explore product ${id} at Tech Store.`,
  };
}

export default async function ProductPage({
  params,
}: Props) {
  const { id } = await params;

  return (
    <main>
      <h1>Product {id}</h1>
    </main>
  );
}
```

**Notice:** In Next.js 16, route `params` are asynchronous. You must await them.

### **6.2 Dynamic metadata from a database**

Let's implement a realistic example.

Suppose you have a MongoDB product collection:

```
{
  _id: "123",
  name: "Gaming Laptop",
  description: "High performance gaming laptop.",
  image: "https://example.com/laptop.jpg"
}
```

Create a reusable data-access function:

```
// lib/products.ts

import "server-only";

import { connectDB } from "@/lib/db";
import Product from "@/models/Product";

export async function getProduct(id: string) {
  await connectDB();

  return Product.findById(id).lean();
}
```

Now generate metadata:

```
// app/products/[id]/page.tsx

import type { Metadata } from "next";
import { notFound } from "next/navigation";
import { getProduct } from "@/lib/products";

type Props = {
  params: Promise<{
    id: string;
  }>;
};

export async function generateMetadata({
  params,
}: Props): Promise<Metadata> {
  const { id } = await params;

  const product = await getProduct(id);

  if (!product) {
    return {
      title: "Product Not Found",
    };
  }

  return {
    title: product.name,
    description: product.description,

    openGraph: {
      title: product.name,
      description: product.description,
      images: [
        {
          url: product.image,
          alt: product.name,
        },
      ],
    },
  };
}

export default async function ProductPage({
  params,
}: Props) {
  const { id } = await params;

  const product = await getProduct(id);

  if (!product) {
    notFound();
  }

  return (
    <main>
      <h1>{product.name}</h1>
      <p>{product.description}</p>
    </main>
  );
}
```

### **6.3 Avoid duplicate database work**

Notice that `getProduct(id)` is called in both `generateMetadata()` and the page.

If you use the same `fetch()` request, Next.js automatically memoizes matching fetch requests across metadata generation, pages, layouts and related rendering work.

For database functions that do not use `fetch`, React's `cache()` can be used to memoize calls during a server render.

Example:

```
// lib/products.ts

import "server-only";

import { cache } from "react";
import { connectDB } from "@/lib/db";
import Product from "@/models/Product";

export const getProduct = cache(async (id: string) => {
  await connectDB();

  return Product.findById(id).lean();
});
```

This helps avoid repeated database lookups for the same product during that render.

[image](https://www.google.com/s2/favicons?domain=https://nextjs.org\&sz=32)

Next.js

### **6.4 Accessing parent metadata**

`generateMetadata()` can also receive resolved metadata from its parent.

```
import type {
  Metadata,
  ResolvingMetadata,
} from "next";

type Props = {
  params: Promise<{
    id: string;
  }>;
};

export async function generateMetadata(
  { params }: Props,
  parent: ResolvingMetadata
): Promise<Metadata> {
  const { id } = await params;

  const previousImages =
    (await parent).openGraph?.images || [];

  return {
    title: `Product ${id}`,
    openGraph: {
      images: [
        "/product-default.png",
        ...previousImages,
      ],
    },
  };
}
```

This is useful when you want to add page-specific metadata without losing selected parent values.

### **When should you use each approach?**

| Situation                                      | Use                                 |
| ---------------------------------------------- | ----------------------------------- |
| Static About page                              | `metadata`                          |
| Contact page                                   | `metadata`                          |
| Blog post based on slug                        | `generateMetadata()`                |
| Product page based on database                 | `generateMetadata()`                |
| User-specific or request-dependent information | Carefully designed dynamic metadata |

**Important:** Metadata exports are not supported in Client Components. Keep the page or layout as a Server Component and place interactive UI in a separate Client Component.&#x20;

[image](https://www.google.com/s2/favicons?domain=https://nextjs.org\&sz=32)

Next.js



## **7. Meta Title and Meta Description**

These two are fundamental SEO fields.

### **7.1 Meta title**

The title describes the page's main topic.

Example:

```
export const metadata: Metadata = {
  title: "Best Gaming Laptops in India",
};
```

A good title should:

- Describe the actual page.
- Be unique and meaningful.
- Include the main topic naturally.
- Avoid keyword stuffing.
- Help users understand what they will find.

### **7.2 Meta description**

The description summarizes the page.

```
export const metadata: Metadata = {
  title: "Best Gaming Laptops in India",

  description:
    "Compare gaming laptops in India by price, GPU, processor, display and performance.",
};
```

A good description should:

- Clearly explain the page.
- Be relevant to its content.
- Be unique across important pages.
- Encourage the user to visit.
- Avoid misleading information.

### **7.3 Does Google always display our description?**

No.

Google may generate a search result snippet from page content instead of using the supplied meta description. It may also rewrite the displayed title link.

Your metadata provides useful signals, but does not guarantee the exact text shown in search results.&#x20;

[image](https://www.google.com/s2/favicons?domain=https://developers.google.com\&sz=32)

Google for Developers+1



## **8. Open Graph Metadata**

Open Graph is a protocol that allows websites to describe their content when links are shared on social platforms and messaging applications.

For example, when someone shares your website URL on:

- Facebook
- WhatsApp
- LinkedIn
- Other platforms that support Open Graph

The platform may display a rich preview.

**Example: Link preview**

[Gokil Banget! 9 Ulasan Laptop Gaming yang Cocok untuk Para Gamers Sejati – Rakcer.ID](https://images.openai.com/static-rsc-4/j88OMknx4u3apktAZEwS8Q6HHYro08YfLKoRX_20G35NHkQyEAdHNqztkHA3CE-rCD1dunt-X7ppIb3qYSuJFmFiu6udFvg6O94favrXYHq1Fx6fDFwlXVujlLXFAJlde5joVhjoXSk_smtgCZnJPbpTQKRyP3rQSlGXZKUoCuA?purpose=inline)

[rakcer.id](https://rakcer.id/2023/09/28/gokil-banget-9-ulasan-laptop-gaming-yang-cocok-untuk-para-gamers-sejati/?utm_source=chatgpt.com)

**Best Gaming Laptops in India**

Compare gaming laptops by processor, GPU, display and price.

techstore.example

The preview typically contains:

- Image
- Title
- Description
- Website URL or domain

### **8.1 Open Graph metadata example**

```
// app/layout.tsx

import type { Metadata } from "next";

export const metadata: Metadata = {
  title: "Tech Store",
  description: "Shop the latest technology products.",

  openGraph: {
    title: "Tech Store",
    description:
      "Shop laptops, mobiles and accessories.",

    url: "https://example.com",

    siteName: "Tech Store",

    images: [
      {
        url: "https://example.com/og-image.jpg",
        width: 1200,
        height: 630,
        alt: "Tech Store products",
      },
    ],

    locale: "en_IN",
    type: "website",
  },
};
```

This generates metadata conceptually similar to:

```
<meta property="og:title" content="Tech Store" />

<meta
  property="og:description"
  content="Shop laptops, mobiles and accessories."
/>

<meta
  property="og:image"
  content="https://example.com/og-image.jpg"
/>

<meta property="og:type" content="website" />
```

### **8.2 Important Open Graph properties**

| Property      | Purpose                                      |
| ------------- | -------------------------------------------- |
| `title`       | Shared page title                            |
| `description` | Shared page description                      |
| `url`         | Canonical page URL used for sharing          |
| `siteName`    | Name of the website                          |
| `images`      | Images shown in the preview                  |
| `locale`      | Content locale                               |
| `type`        | Content type, such as `website` or `article` |

For many website previews, an image of 1200 × 630 pixels is a practical design target.

### **8.3 Different Open Graph metadata for each page**

For a product:

```
export const metadata: Metadata = {
  title: "Gaming Laptop",
  description: "High-performance gaming laptop.",

  openGraph: {
    title: "Gaming Laptop",
    description: "High-performance gaming laptop.",
    type: "website",
    images: ["/products/gaming-laptop.jpg"],
  },
};
```

For a blog article:

```
export const metadata: Metadata = {
  title: "Understanding Computer Processors",
  description: "Learn how CPU architecture works.",

  openGraph: {
    title: "Understanding Computer Processors",
    description: "Learn how CPU architecture works.",
    type: "article",
    publishedTime: "2026-11-25T10:00:00.000Z",
    authors: ["Tech Store"],
    images: ["/blog/cpu-guide.jpg"],
  },
};
```

Next.js supports Open Graph fields through its Metadata API.&#x20;

[image](https://www.google.com/s2/favicons?domain=https://nextjs.org\&sz=32)

Next.js



## **9. Twitter / X Card Metadata**

X (formerly Twitter) supports card metadata that controls how links are presented when shared.

For example, a shared link can display:

- A large image.
- A title.
- A description.
- A link to the website.

### **9.1 Configure Twitter metadata**

```
// app/layout.tsx

import type { Metadata } from "next";

export const metadata: Metadata = {
  title: "Tech Store",
  description: "Explore the latest technology products.",

  twitter: {
    card: "summary_large_image",
    title: "Tech Store",
    description:
      "Explore laptops, mobiles and accessories.",

    images: ["/twitter-image.jpg"],
  },
};
```

### **9.2 Twitter card types**

| Type                  | Purpose                          |
| --------------------- | -------------------------------- |
| `summary`             | Smaller preview with a thumbnail |
| `summary_large_image` | Large image preview              |

The most commonly used type for promotional content is:

```
card: "summary_large_image"
```

### **9.3 Open Graph vs Twitter metadata**

| Open Graph       | Twitter/X             |
| ---------------- | --------------------- |
| `openGraph`      | `twitter`             |
| `og:title`       | `twitter:title`       |
| `og:description` | `twitter:description` |
| `og:image`       | `twitter:images`      |
| `og:type`        | `twitter:card`        |

You can define both in the same metadata object.

**Important:** Social platforms may use different fallback behavior. Define the metadata explicitly when you need predictable previews.

## **10. Understanding&#x20;**`metadataBase`

This is a particularly useful Next.js feature.

Suppose you write:

```
export const metadata: Metadata = {
  openGraph: {
    images: ["/og-image.jpg"],
  },
};
```

The image URL is relative.

Social platforms generally need an absolute URL so they can fetch the image.

For example:

```
https://example.com/og-image.jpg
```

You can configure `metadataBase` in the root layout.

### **Example**

```
// app/layout.tsx

import type { Metadata } from "next";

export const metadata: Metadata = {
  metadataBase: new URL("https://example.com"),

  title: "Tech Store",

  openGraph: {
    title: "Tech Store",
    images: ["/og-image.jpg"],
  },
};
```

Now the relative image URL can be resolved against the base URL.

Conceptually:

```
/og-image.jpg
        ↓
https://example.com/og-image.jpg
```

### **10.1 Dynamic metadataBase using an environment variable**

You can use the public website URL from your environment configuration.

```
// app/layout.tsx

import type { Metadata } from "next";

const siteUrl =
  process.env.NEXT_PUBLIC_SITE_URL ??
  "http://localhost:3000";

export const metadata: Metadata = {
  metadataBase: new URL(siteUrl),

  title: {
    default: "Tech Store",
    template: "%s | Tech Store",
  },

  description: "Shop technology products online.",
};
```

Production:

```
NEXT_PUBLIC_SITE_URL=https://example.com
```

Development:

```
NEXT_PUBLIC_SITE_URL=http://localhost:3000
```

### **Why is&#x20;**`metadataBase`**&#x20;important?**

Without it, relative metadata URLs that require absolute URLs can cause build errors.

You can use it for:

- Open Graph images
- Twitter images
- Canonical URLs
- Other URL-based metadata

An explicitly absolute URL is used as-is.&#x20;

[image](https://www.google.com/s2/favicons?domain=https://nextjs.org\&sz=32)

Next.js



## **11. Canonical URLs**

Suppose the same content is accessible through multiple URLs:

```
https://example.com/products/laptop

https://example.com/products/laptop?source=facebook

https://example.com/products/laptop?campaign=summer
```

These URLs may represent the same underlying product page.

You generally want search engines to recognize one preferred URL.

That preferred URL is called the **canonical URL**.

### **11.1 Why canonical URLs are useful**

They help search engines understand which URL you consider the primary version of a page.

This is particularly useful for:

- Tracking query parameters.
- Filtered product pages.
- Duplicate content.
- Multiple URL variants.
- Pages accessible through different routes.

### **11.2 Configure canonical URLs in Next.js**

```
// app/products/laptop/page.tsx

import type { Metadata } from "next";

export const metadata: Metadata = {
  alternates: {
    canonical: "/products/laptop",
  },
};

export default function LaptopPage() {
  return <h1>Gaming Laptop</h1>;
}
```

If your root layout has:

```
metadataBase: new URL("https://example.com")
```

The canonical URL resolves to:

```
https://example.com/products/laptop
```

### **11.3 Dynamic canonical URL**

For a dynamic blog route:

```
// app/blog/[slug]/page.tsx

import type { Metadata } from "next";

type Props = {
  params: Promise<{
    slug: string;
  }>;
};

export async function generateMetadata({
  params,
}: Props): Promise<Metadata> {
  const { slug } = await params;

  return {
    alternates: {
      canonical: `/blog/${slug}`,
    },
  };
}

export default async function BlogPage({
  params,
}: Props) {
  const { slug } = await params;

  return <h1>{slug}</h1>;
}
```

**Important:** Do not automatically canonicalize every URL to your homepage. Each indexable page should generally have its own appropriate canonical URL.

## **12. Robots Metadata and Indexing**

Search engines need instructions about whether a page should be indexed or whether links on it should be followed.

Next.js supports robots metadata.

### **12.1 Allow indexing**

```
import type { Metadata } from "next";

export const metadata: Metadata = {
  robots: {
    index: true,
    follow: true,
  },
};
```

Conceptual HTML:

```
<meta name="robots" content="index, follow" />
```

### **12.2 Prevent indexing**

For an internal dashboard:

```
// app/dashboard/layout.tsx

import type { Metadata } from "next";

export const metadata: Metadata = {
  robots: {
    index: false,
    follow: false,
  },
};

export default function DashboardLayout({
  children,
}: {
  children: React.ReactNode;
}) {
  return <>{children}</>;
}
```

Conceptual output:

```
<meta name="robots" content="noindex, nofollow" />
```

### **12.3 Important robots options**

| Option      | Meaning                                                    |
| ----------- | ---------------------------------------------------------- |
| `index`     | Allows indexing                                            |
| `noindex`   | Requests that the page not be indexed                      |
| `follow`    | Allows following links                                     |
| `nofollow`  | Requests that links not be followed                        |
| `noarchive` | Requests that a cached copy not be shown                   |
| `nocache`   | A supported Next.js metadata option for crawler directives |

Example:

```
export const metadata: Metadata = {
  robots: {
    index: false,
    follow: false,
    googleBot: {
      index: false,
      follow: false,
      noimageindex: true,
    },
  },
};
```

### **12.4 Robots metadata is not security**

This is critical.

Suppose you have:

```
/admin
/dashboard
/private-data
```

Adding `noindex` does not protect these pages.

A user can still access them if your application does not enforce authentication and authorization.

Use proper server-side authorization and access control.

Robots directives are instructions for crawlers, not access-control mechanisms.

## **13. Favicon, Icons and Apple Icons**

A favicon is a small icon associated with your website.

It is commonly displayed in:

- Browser tabs.
- Bookmarks.
- Browser history.
- Some search result presentations.

Next.js supports special file conventions for icons.

### **13.1 Add a favicon**

Place the file here:

```
app/
├── favicon.ico
├── layout.tsx
└── page.tsx
```

Next.js automatically recognizes it.

### **13.2 Add an application icon**

```
app/
├── icon.png
├── apple-icon.png
├── layout.tsx
└── page.tsx
```

Common purposes:

| File             | Purpose                  |
| ---------------- | ------------------------ |
| `favicon.ico`    | Browser favicon          |
| `icon.png`       | General application icon |
| `apple-icon.png` | Apple touch icon         |

### **13.3 Use a custom icon filename**

You can also use:

```
app/
├── icon.png
├── apple-icon.png
└── page.tsx
```

Or define icons through the Metadata API:

```
import type { Metadata } from "next";

export const metadata: Metadata = {
  icons: {
    icon: "/icon.png",
    shortcut: "/favicon.ico",
    apple: "/apple-icon.png",
  },
};
```

Next.js recommends file-based metadata for icons where possible because it automatically generates the relevant tags.&#x20;

[image](https://www.google.com/s2/favicons?domain=https://nextjs.org\&sz=32)

Next.js+1



## **14. Viewport and Theme Color**

The viewport controls how the page is initially displayed on different screen sizes.

Next.js automatically includes a suitable viewport meta tag for most applications.

You usually do not need to configure it manually.

However, you can configure the browser theme color.

### **14.1 Use the&#x20;**`viewport`**&#x20;export**

```
// app/layout.tsx

import type { Viewport } from "next";

export const viewport: Viewport = {
  themeColor: "#111827",
};
```

This generates:

```
<meta name="theme-color" content="#111827" />
```

### **14.2 Different theme colors for light and dark modes**

```
import type { Viewport } from "next";

export const viewport: Viewport = {
  themeColor: [
    {
      media: "(prefers-color-scheme: light)",
      color: "#ffffff",
    },
    {
      media: "(prefers-color-scheme: dark)",
      color: "#111827",
    },
  ],
};
```

### **14.3&#x20;**`viewport`**&#x20;vs&#x20;**`metadata`

These are separate exports.

| `metadata`    | `viewport`       |
| ------------- | ---------------- |
| Title         | Theme color      |
| Description   | Viewport width   |
| Open Graph    | Initial scale    |
| Twitter cards | Color scheme     |
| Canonical URL | User scalability |

The old `viewport`, `themeColor`, and `colorScheme` fields inside `metadata` are deprecated. Use the separate `viewport` object or `generateViewport()` instead.&#x20;

[image](https://www.google.com/s2/favicons?domain=https://nextjs.org\&sz=32)

Next.js+1



## **15. Creating&#x20;**`sitemap.xml`

A sitemap is an XML file containing URLs that you want search engines to discover.

Example:

```
https://example.com/sitemap.xml
```

It can contain information such as:

- Page URL.
- Last modification date.
- Change frequency.
- Priority.
- Language alternatives.

### **15.1 Static sitemap**

For a small website, you can create:

```
app/
└── sitemap.xml
```

Example:

```
<?xml version="1.0" encoding="UTF-8"?>

<urlset
  xmlns="http://www.sitemaps.org/schemas/sitemap/0.9"
>
  <url>
    <loc>https://example.com/</loc>
  </url>

  <url>
    <loc>https://example.com/about</loc>
  </url>

  <url>
    <loc>https://example.com/contact</loc>
  </url>
</urlset>
```

### **15.2 Dynamic sitemap using&#x20;**`sitemap.ts`

For real-world applications, you often need to generate URLs dynamically.

Create:

```
app/
├── sitemap.ts
├── layout.tsx
└── page.tsx
```

Example:

```
// app/sitemap.ts

import type { MetadataRoute } from "next";

export default function sitemap(): MetadataRoute.Sitemap {
  const baseUrl = "https://example.com";

  return [
    {
      url: baseUrl,
      lastModified: new Date(),
      changeFrequency: "daily",
      priority: 1,
    },

    {
      url: `${baseUrl}/about`,
      lastModified: new Date(),
      changeFrequency: "monthly",
      priority: 0.8,
    },

    {
      url: `${baseUrl}/contact`,
      lastModified: new Date(),
      changeFrequency: "yearly",
      priority: 0.5,
    },
  ];
}
```

Next.js automatically exposes this at:

```
https://example.com/sitemap.xml
```

### **15.3 Dynamic sitemap using database records**

Imagine you have 1,000 blog posts in MongoDB.

You want to include all published posts in the sitemap.

```
// app/sitemap.ts

import type { MetadataRoute } from "next";

import { connectDB } from "@/lib/db";
import Blog from "@/models/Blog";

export default async function sitemap(): Promise<MetadataRoute.Sitemap> {
  await connectDB();

  const blogs = await Blog.find(
    { status: "published" },
    {
      slug: 1,
      updatedAt: 1,
    }
  ).lean();

  const baseUrl = "https://example.com";

  const blogUrls = blogs.map((blog) => ({
    url: `${baseUrl}/blog/${blog.slug}`,
    lastModified: blog.updatedAt,
  }));

  return [
    {
      url: baseUrl,
      lastModified: new Date(),
    },
    {
      url: `${baseUrl}/about`,
      lastModified: new Date(),
    },
    ...blogUrls,
  ];
}
```

Now the sitemap contains the published blog URLs from your database.

### **15.4 Important sitemap rules**

- Include canonical URLs.
- Include pages you actually want search engines to discover.
- Exclude private, irrelevant, and duplicate URLs.
- Do not include URLs that return errors.
- Keep `lastModified` accurate.
- For very large sites, split the sitemap into multiple files.

A sitemap can contain up to 50,000 URLs per sitemap file. Next.js supports multiple sitemaps and sitemap generation using `generateSitemaps()`.&#x20;

[image](https://www.google.com/s2/favicons?domain=https://nextjs.org\&sz=32)

Next.js



## **16. Creating&#x20;**`robots.txt`

The `robots.txt` file gives crawler instructions about which URL paths they may crawl.

It is generally available at:

```
https://example.com/robots.txt
```

### **16.1 Static robots.txt**

Create:

```
app/
└── robots.txt
```

Example:

```
User-Agent: *

Allow: /

Disallow: /admin/
Disallow: /dashboard/

Sitemap: https://example.com/sitemap.xml
```

### **16.2 Dynamic robots.txt using&#x20;**`robots.ts`

Next.js also supports generating this file programmatically.

```
// app/robots.ts

import type { MetadataRoute } from "next";

export default function robots(): MetadataRoute.Robots {
  return {
    rules: {
      userAgent: "*",
      allow: "/",
      disallow: [
        "/admin/",
        "/dashboard/",
        "/api/private/",
      ],
    },

    sitemap: "https://example.com/sitemap.xml",
  };
}
```

Next.js automatically serves:

```
https://example.com/robots.txt
```

[image](https://www.google.com/s2/favicons?domain=https://nextjs.org\&sz=32)

Next.js+1

### **16.3 Important difference: robots.txt vs noindex**

| robots.txt                                                     | `noindex`                                                          |
| -------------------------------------------------------------- | ------------------------------------------------------------------ |
| Controls crawler access instructions                           | Requests that a page not be indexed                                |
| Tells crawlers which paths they may crawl                      | Tells supported crawlers not to include the page in search results |
| Does not protect confidential data                             | Does not protect confidential data                                 |
| Can prevent a crawler from seeing a page's `noindex` directive | Requires the crawler to access the page to see the directive       |

**Very important:** Do not rely on `robots.txt` to hide sensitive pages. Use authentication and authorization. Also, blocking a URL in `robots.txt` can prevent a crawler from seeing a `noindex` directive on that page.

## **17. Dynamic Open Graph Images**

Now let's learn a more advanced Next.js feature.

Suppose you publish hundreds of blog posts.

You want each blog post to have its own social sharing image.

Creating hundreds of static images manually is not convenient.

Next.js supports generating images dynamically using `ImageResponse`.

### **17.1 Static Open Graph image**

For a simple website, create:

```
app/
├── opengraph-image.png
├── twitter-image.png
├── layout.tsx
└── page.tsx
```

Next.js automatically recognizes these files and generates the appropriate metadata.

### **17.2 Dynamic Open Graph image**

Create:

```
app/
└── blog/
    └── [slug]/
        ├── opengraph-image.tsx
        └── page.tsx
```

Example:

```
// app/blog/[slug]/opengraph-image.tsx

import { ImageResponse } from "next/og";

export const alt = "Blog article preview";

export const size = {
  width: 1200,
  height: 630,
};

export const contentType = "image/png";

type Props = {
  params: Promise<{
    slug: string;
  }>;
};

export default async function Image({
  params,
}: Props) {
  const { slug } = await params;

  return new ImageResponse(
    (
      <div
        style={{
          width: "100%",
          height: "100%",
          display: "flex",
          flexDirection: "column",
          justifyContent: "center",
          alignItems: "center",
          background: "#111827",
          color: "white",
          padding: "60px",
        }}
      >
        <div
          style={{
            fontSize: 32,
            color: "#9ca3af",
          }}
        >
          TECH STORE BLOG
        </div>

        <div
          style={{
            fontSize: 64,
            fontWeight: 700,
            marginTop: 30,
            textAlign: "center",
          }}
        >
          {slug.replaceAll("-", " ")}
        </div>
      </div>
    ),
    size
  );
}
```

The generated image can be used as the social preview image for that route.

### **17.3 How does it work?**

**User visits or shares**

`/blog/nextjs-seo-guide`

**Next.js dynamic image handler**

`opengraph-image.tsx`

**ImageResponse**

Generates a 1200 × 630 image

**Social sharing preview**

Dynamic image generated

[Engineering an AI App · Resend](https://images.openai.com/static-rsc-4/WwsYs4a-in4Gv8iTWygillcDjegIfHBYypkYwRaRkSUTBkQZxqfvNpvxlr4fXYd1iSuqeTBFah9K9AxAPK-4cFF7yfo68JwwqzCyRDQwG0veV9iBf7Z55j9AKkE3ECqK954W_hAkk9PZMSghVIna2l9x6gUxeC_O0NhgBOvgz7I?purpose=inline)

[resend.com](https://resend.com/blog/engineering-an-ai-app?utm_source=chatgpt.com)

### **17.4 Why use dynamic OG images?**

You can automatically generate:

- Blog article images.
- Product sharing images.
- User profile sharing images.
- Event promotion images.
- Dynamic page titles and branding.

The `opengraph-image.tsx` convention automatically adds the relevant image metadata. You can also use `twitter-image.tsx` for a separate X/Twitter image.&#x20;

[image](https://www.google.com/s2/favicons?domain=https://nextjs.org\&sz=32)

Next.js+1



## **18. Structured Data and JSON-LD**

This is another important SEO topic.

Metadata describes a webpage, but structured data provides additional machine-readable information about the content.

For example, suppose you have a product page.

A human visitor sees:

```
Gaming Laptop

Price: ₹75,000

Brand: ExampleTech

Availability: In Stock
```

Structured data allows search engines to understand these facts in a standardized format.

### **18.1 What is JSON-LD?**

JSON-LD stands for **JavaScript Object Notation for Linked Data**.

It is a way of embedding structured information into a webpage.

Google recommends JSON-LD as a convenient format for structured data in many cases.&#x20;

[image](https://www.google.com/s2/favicons?domain=https://developers.google.com\&sz=32)

Google for Developers



### **18.2 Example: Organization structured data**

You can add JSON-LD to your root layout.

```
// app/layout.tsx

import type { Metadata } from "next";

export const metadata: Metadata = {
  title: "Tech Store",
  description: "Shop technology products.",
};

const jsonLd = {
  "@context": "https://schema.org",
  "@type": "Organization",
  name: "Tech Store",
  url: "https://example.com",
  logo: "https://example.com/logo.png",
};

export default function RootLayout({
  children,
}: Readonly<{
  children: React.ReactNode;
}>) {
  return (
    <html lang="en">
      <body>
        <script
          type="application/ld+json"
          dangerouslySetInnerHTML={{
            __html: JSON.stringify(jsonLd).replace(
              /</g,
              "\\u003c"
            ),
          }}
        />

        {children}
      </body>
    </html>
  );
}
```

The replacement of `<` with `\u003c` helps prevent unsafe script termination when serializing data into an inline script. For dynamic content, validate and sanitize data from external sources as well.

### **18.3 Product structured data example**

```
const productJsonLd = {
  "@context": "https://schema.org",
  "@type": "Product",

  name: "Gaming Laptop",

  image: [
    "https://example.com/laptop.jpg",
  ],

  description:
    "High-performance gaming laptop.",

  brand: {
    "@type": "Brand",
    name: "ExampleTech",
  },

  offers: {
    "@type": "Offer",
    priceCurrency: "INR",
    price: "75000",
    availability:
      "https://schema.org/InStock",
    url: "https://example.com/products/gaming-laptop",
  },
};
```

This describes the product and its offer in a machine-readable format.

### **18.4 Where can structured data be useful?**

| Schema type      | Example                    |
| ---------------- | -------------------------- |
| `Organization`   | Company information        |
| `LocalBusiness`  | Local business information |
| `Product`        | Product details            |
| `Article`        | Blog articles              |
| `BreadcrumbList` | Navigation hierarchy       |
| `Event`          | Events                     |
| `Recipe`         | Food recipes               |
| `Person`         | Person profile             |

**Important:** Valid structured data does not guarantee rich results. Google has eligibility requirements for each supported feature. The structured data must accurately represent visible page content and follow Google's guidelines.&#x20;

[image](https://www.google.com/s2/favicons?domain=https://developers.google.com\&sz=32)

Google for Developers



## **19. Complete SEO Configuration Example**

Let's combine the concepts into a realistic Next.js application.

Imagine you are building an e-commerce website.

### **19.1 Recommended folder structure**

```
src/
└── app/
    ├── layout.tsx
    ├── page.tsx
    │
    ├── about/
    │   └── page.tsx
    │
    ├── products/
    │   ├── page.tsx
    │   └── [id]/
    │       ├── page.tsx
    │       └── opengraph-image.tsx
    │
    ├── blog/
    │   └── [slug]/
    │       └── page.tsx
    │
    ├── sitemap.ts
    ├── robots.ts
    ├── favicon.ico
    ├── icon.png
    └── apple-icon.png
```

### **19.2 Root layout metadata**

```
// src/app/layout.tsx

import type {
  Metadata,
  Viewport,
} from "next";

const siteUrl =
  process.env.NEXT_PUBLIC_SITE_URL ??
  "http://localhost:3000";

export const metadata: Metadata = {
  metadataBase: new URL(siteUrl),

  title: {
    default: "Tech Store",
    template: "%s | Tech Store",
  },

  description:
    "Discover laptops, mobiles and accessories at Tech Store.",

  applicationName: "Tech Store",

  keywords: [
    "laptops",
    "gaming laptops",
    "mobiles",
    "technology",
  ],

  openGraph: {
    type: "website",
    siteName: "Tech Store",
    title: "Tech Store",
    description:
      "Discover the latest technology products.",
    images: ["/og-image.jpg"],
  },

  twitter: {
    card: "summary_large_image",
    title: "Tech Store",
    description:
      "Discover the latest technology products.",
    images: ["/twitter-image.jpg"],
  },
};

export const viewport: Viewport = {
  themeColor: "#111827",
};

export default function RootLayout({
  children,
}: Readonly<{
  children: React.ReactNode;
}>) {
  return (
    <html lang="en">
      <body>{children}</body>
    </html>
  );
}
```

### **19.3 Homepage metadata**

The homepage inherits the root metadata.

You do not need to define everything again.

### **19.4 About page metadata**

```
// src/app/about/page.tsx

import type { Metadata } from "next";

export const metadata: Metadata = {
  title: "About Us",
  description:
    "Learn more about Tech Store and our products.",
};

export default function AboutPage() {
  return <h1>About Tech Store</h1>;
}
```

Resulting title:

```
About Us | Tech Store
```

### **19.5 Sitemap**

```
// src/app/sitemap.ts

import type { MetadataRoute } from "next";

const siteUrl =
  process.env.NEXT_PUBLIC_SITE_URL ??
  "https://example.com";

export default function sitemap(): MetadataRoute.Sitemap {
  return [
    {
      url: siteUrl,
      priority: 1,
    },
    {
      url: `${siteUrl}/about`,
      priority: 0.8,
    },
    {
      url: `${siteUrl}/products`,
      priority: 0.9,
    },
  ];
}
```

### **19.6 Robots**

```
// src/app/robots.ts

import type { MetadataRoute } from "next";

const siteUrl =
  process.env.NEXT_PUBLIC_SITE_URL ??
  "https://example.com";

export default function robots(): MetadataRoute.Robots {
  return {
    rules: {
      userAgent: "*",
      allow: "/",
      disallow: [
        "/dashboard/",
        "/admin/",
      ],
    },

    sitemap: `${siteUrl}/sitemap.xml`,
  };
}
```

This provides a solid foundation for a production application's technical SEO configuration.

## **20. Common SEO Mistakes**

| Mistake                                       | Problem                                                  | Solution                             |
| --------------------------------------------- | -------------------------------------------------------- | ------------------------------------ |
| Same title on every page                      | Pages are not clearly differentiated                     | Use unique page titles               |
| Missing descriptions                          | Less control over search snippets                        | Write relevant descriptions          |
| Duplicate canonical URLs                      | Search engines may select an unintended URL              | Configure appropriate canonical URLs |
| Missing Open Graph image                      | Link previews may be incomplete                          | Add OG images                        |
| Wrong `metadataBase`                          | Relative URLs resolve incorrectly                        | Configure the correct domain         |
| Missing sitemap                               | Search engines may have less assistance discovering URLs | Generate a sitemap                   |
| Blocking important pages in robots.txt        | Crawlers may not discover their content                  | Review crawler rules                 |
| Using `noindex` on public pages accidentally  | Important pages may be excluded from search results      | Check indexing directives            |
| Exposing private pages without authentication | Sensitive information may be accessible                  | Implement server-side authorization  |
| Repeating keywords excessively                | Creates poor-quality content                             | Write naturally                      |
| Defining only root metadata                   | All pages may have generic descriptions                  | Define metadata for important pages  |
| Forgetting dynamic metadata                   | Product and blog previews may be generic                 | Use `generateMetadata()`             |

## **21. SEO Testing and Debugging Tools**

Once you implement metadata, you should verify the output.

Here are useful tools:

[Google Search Console Logo PNG Vector, Icon Free Download](https://images.openai.com/static-rsc-4/o9u8dtCgOeDvLKCCJHo2pylV7GUyHAyI8UKcSGteLB0eVZOWrccslV91OJX2hBPlVv0zGraa_3Kr3O31mU9cnErYTkDEy6SYWOHYDPYEFri-2Wp81JR2g82h0Mdkb4U3XD-2mZbkPEfRbNUTfB-onZ77ceGUSI-nTdCWYrZqdmI?purpose=inline)

**Google Search Console**

Monitor indexing, search performance, sitemap submission and search-related issues.

[Open Search Console](https://search.google.com/search-console/?utm_source=chatgpt.com)

[Rich Snippets: A Complete Beginner's Guide](https://images.openai.com/static-rsc-4/7zaqNbbiWZpRRljSb7Loby_krov-IM4V1hjnW-DLM8Gbkvhe71JRBMDSC2uEZZtZ2A1Xm3G4DoLZBxm1_kqUWYjQUIdJvYWsWaPcGlsLe5LDGPskmSylTFxnXG0T7QoGqGHdugnjUQK9PllKX7fnVLpwMlYMSccMnRE9Sj6D16I?purpose=inline)

**Google Rich Results Test**

Validate structured data and check eligibility for supported rich results.

[Open Rich Results Test](https://search.google.com/test/rich-results?utm_source=chatgpt.com)

[Facebook Debugger: Der ultimative Guide, um Links und Bilder zu aktualisieren - acquisa](https://images.openai.com/static-rsc-4/FAmnD0Kx3Q-ACBce1l_b1yunPlH0-H8g9J1J6579XPhNI5rGcIhy2vCijmAMIRLdn_HXszwrn3EAXcLICFowSi16dpyD0dlYOKF9-GXjJtD5YxtQ0_-DfVuA9n6owZe_yrsn6clJvCmaoxfGDb-avajtRA8mDdBc43QycZhJOOw?purpose=inline)

**Facebook Sharing Debugger**

Inspect and refresh Facebook link preview information.

[Open Sharing Debugger](https://developers.facebook.com/tools/debug/?utm_source=chatgpt.com)

[Website & Funnel Builder | Custom Websites & High-Converting Funnels](https://images.openai.com/static-rsc-4/O3v_wKndiY0WuG8LI_81v5dnUU3WCPul7_h7TrxSYiGhnGK_JWc3GSrklXxZOMFELYh-hNOTCUTVrYQqvW2kEn6DIiCOx09cNzH3n3XsFpzdB74phNb7l-yYlROwJ-4yXdvoi0JX-O4lwdA60M_rSCvoZU1zbnj0xhvp8zrBDVA?purpose=inline)

**PageSpeed Insights**

Analyze performance and Core Web Vitals.

[Open PageSpeed Insights](https://pagespeed.web.dev/?utm_source=chatgpt.com)

### **How to test your Next.js metadata manually**

Run your application:

```
npm run dev
```

Open:

```
http://localhost:3000
```

Then:

1. Open Developer Tools.
2. Select the Elements tab.
3. Expand the `<head>` element.
4. Inspect the generated title and meta tags.
5. Verify Open Graph and Twitter metadata.
6. Open `/sitemap.xml`.
7. Open `/robots.txt`.
8. Test dynamic routes with different IDs or slugs.

You can also inspect the server-rendered HTML using View Page Source.

## **22. Interview Questions and Answers**

### **Basic Level**

**Q1. What is SEO?**

SEO stands for Search Engine Optimization. It is the process of improving a website's visibility in search engine results.

**Q2. What is metadata?**

Metadata is information describing a webpage, such as its title, description, canonical URL and social sharing information.

**Q3. What is a meta title?**

It is the document title, usually represented by the HTML `<title>` element.

**Q4. What is a meta description?**

It is a short summary of a webpage that search engines may use in search result snippets.

**Q5. What is Open Graph?**

Open Graph is a protocol that allows websites to provide rich link preview information to social platforms.

**Q6. What is a sitemap?**

A sitemap is a file listing URLs that website owners want search engines to discover.

### **Intermediate Level**

**Q7. What is the difference between&#x20;**`metadata`**&#x20;and&#x20;**`generateMetadata()`**?**

- `metadata` defines static metadata.
- `generateMetadata()` generates metadata dynamically using route parameters, external data or other available information.

**Q8. What is&#x20;**`metadataBase`**?**

It defines a base URL used to resolve relative URLs in metadata fields.

**Q9. What is a canonical URL?**

It identifies the preferred URL for a webpage when multiple URLs represent similar or duplicate content.

**Q10. What is the difference between&#x20;**`robots.txt`**&#x20;and robots metadata?**

`robots.txt` provides crawler access instructions, while robots metadata provides indexing and link-following directives for supported crawlers.

**Q11. Can we export&#x20;**`metadata`**&#x20;from a Client Component?**

No. The Metadata API is supported only in Server Components.

**Q12. What is a title template?**

A title template automatically adds a prefix or suffix to titles defined by child route segments.

### **Advanced Level**

**Q13. Why should we use&#x20;**`generateMetadata()`**&#x20;for product pages?**

Because product metadata depends on dynamic information, such as the product name, description and image retrieved from a database.

**Q14. What happens when child metadata overrides parent metadata?**

Metadata objects are merged across route segments, but nested objects are shallowly replaced when the child defines the same key.

**Q15. What is the purpose of Open Graph images?**

They provide images for rich link previews when a webpage is shared on supported social platforms.

**Q16. What is JSON-LD?**

JSON-LD is a structured data format used to provide machine-readable information about webpage content.

**Q17. What is&#x20;**`ImageResponse`**?**

It is a Next.js API used to generate images dynamically, including Open Graph and social sharing images.

**Q18. Does adding metadata guarantee a higher Google ranking?**

No. Metadata helps search engines understand a page, but rankings depend on many factors, including relevance, content quality, technical accessibility and other signals.

**Q19. Can robots.txt protect private pages?**

No. It is not an authentication or authorization mechanism.

**Q20. Why should we use a canonical URL?**

To communicate the preferred URL for content that may be accessible through multiple URL variations.

## **23. Practical Assignment**

Hands-on project

Build a complete SEO implementation for a Next.js e-commerce application.

### **Task 1: Root metadata**

- Configure a default title.
- Configure a title template.
- Add a default description.
- Configure `metadataBase`.
- Add Open Graph metadata.
- Add Twitter metadata.
- Configure viewport theme color.

### **Task 2: Page-specific metadata**

Implement metadata for:

```
/
 /about
 /contact
 /products
 /products/[id]
 /blog/[slug]
```

- Use static metadata where appropriate.
- Use `generateMetadata()` for dynamic routes.
- Generate product metadata from MongoDB.
- Generate blog metadata from MongoDB.
- Configure canonical URLs.
- Handle missing products and blog posts.

### **Task 3: Social sharing**

- Create an Open Graph image.
- Create a Twitter image.
- Implement a dynamic `opengraph-image.tsx`.
- Verify image URLs are absolute and publicly accessible.

### **Task 4: Search engine configuration**

- Create `sitemap.ts`.
- Include static pages.
- Include published product pages.
- Include published blog pages.
- Create `robots.ts`.
- Exclude appropriate private paths from crawler access.
- Verify that confidential routes are still protected by authentication.

### **Task 5: Structured data**

- Add Organization JSON-LD.
- Add Product JSON-LD.
- Ensure structured data matches visible page content.
- Validate it using Google's Rich Results Test.

### **Task 6: Final testing**

- Inspect the HTML `<head>`.
- Check page titles.
- Check descriptions.
- Check canonical URLs.
- Check Open Graph metadata.
- Open `/sitemap.xml`.
- Open `/robots.txt`.
- Test dynamic product pages.
- Test the production deployment.

## **24. Quick Revision Notes**

| Concept              | Remember                                             |
| -------------------- | ---------------------------------------------------- |
| SEO                  | Improve search engine visibility                     |
| Metadata             | Information describing a webpage                     |
| `metadata`           | Static metadata export                               |
| `generateMetadata()` | Dynamic metadata generation                          |
| `title.template`     | Automatically formats child page titles              |
| `title.default`      | Default title for child segments                     |
| `title.absolute`     | Ignores parent title templates                       |
| `metadataBase`       | Resolves relative metadata URLs                      |
| Open Graph           | Social sharing metadata                              |
| Twitter card         | X/Twitter sharing metadata                           |
| Canonical URL        | Preferred page URL                                   |
| Robots metadata      | Indexing and link-following instructions             |
| Sitemap              | Lists URLs for search engine discovery               |
| Robots.txt           | Crawler access instructions                          |
| Favicon              | Browser/site icon                                    |
| `viewport`           | Controls initial viewport configuration              |
| `ImageResponse`      | Dynamically generates images                         |
| JSON-LD              | Structured data format                               |
| `server-only`        | Helps keep data-access modules out of client imports |

## **25. Phase 16 Completion Checklist**

**Your learning progress**

### **0/18**

Understand SEO, crawling, indexing and ranking

Understand metadata and HTML head elements

Configure static metadata

Use title templates and metadata inheritance

Implement generateMetadata()

Generate metadata from MongoDB records

Configure meta titles and descriptions

Configure Open Graph metadata

Configure Twitter/X cards

Understand metadataBase and canonical URLs

Configure robots metadata

Add favicon, icon and apple icon

Understand viewport configuration

Create sitemap.ts

Create robots.ts

Generate dynamic Open Graph images

Implement JSON-LD structured data

Test SEO configuration and complete the assignment

Reset checklist

## **26. Next Phase Preview**

Next: Phase 17

# **Caching, Revalidation and Performance Optimization**

**Topics we will cover:**

1. What is caching and why is it important?
2. Browser cache vs server cache.
3. Next.js Data Cache and Full Route Cache concepts.
4. Request memoization.
5. `use cache` and Cache Components.
6. `cacheLife()` and `cacheTag()`.
7. `revalidateTag()` and `updateTag()`.
8. `revalidatePath()`.
9. Time-based and on-demand revalidation.
10. Cache invalidation strategies.
11. React `cache()` vs Next.js caching.
12. Practical caching with MongoDB and MySQL.
13. Performance profiling and optimization.

**When you're ready, say&#x20;**`OK, Phase 17`**.**