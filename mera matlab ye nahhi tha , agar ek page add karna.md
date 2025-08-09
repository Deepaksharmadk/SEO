<img src="https://r2cdn.perplexity.ai/pplx-full-logo-primary-dark%402x.png" style="height:64px;margin-right:32px"/>

# mera matlab ye nahhi tha , agar ek page add karna hoga to kaha kaha change karna hoga seo

अच्छा, अब मैं समझ गया! आप पूछ रहे हैं कि **नया page add करने के लिए कहाँ-कहाँ changes करने होंगे SEO के लिए**। यहाँ complete checklist है:

## नया Page Add करते समय SEO Changes की Complete Checklist

### 1. **नया Page Create करना**

```
app/new-page-name/page.tsx
```


### 2. **Page-Level SEO Setup**

```typescript
// app/new-page-name/page.tsx
import type { Metadata } from 'next';
import { seoConfig } from '../../lib/seo-config';

export const metadata: Metadata = {
  title: "New Page Title - Dhur Converter",
  description: "New page description here...",
  keywords: ["keyword1", "keyword2", "keyword3"],
  
  openGraph: {
    title: "New Page Title",
    description: "New page description...",
    url: `${seoConfig.siteUrl}/new-page-name`,
    images: [
      {
        url: `${seoConfig.siteUrl}/images/og-new-page.jpg`,
        width: 1200,
        height: 630,
        alt: 'New Page Image Alt Text',
      }
    ],
  },
  
  alternates: {
    canonical: `${seoConfig.siteUrl}/new-page-name`,
  },
};

export default function NewPage() {
  return (
    <main>
      <h1>New Page Heading</h1>
      {/* Page content */}
    </main>
  );
}
```


### 3. **SEO Config File में Add करना**

```typescript
// lib/seo-config.ts में यह add करें:
export const pageConfigs = {
  // existing pages...
  
  newPage: {  // 👈 नया page config add करें
    title: "New Page Title - Dhur Converter",
    description: "Complete description for new page...",
    keywords: ["new page keywords", "related terms"]
  }
};
```


### 4. **Sitemap Update करना**

```typescript
// app/sitemap.xml/route.ts में add करें:
const routes = [
  // existing routes...
  
  {  // 👈 नया route add करें
    url: `${baseUrl}/new-page-name`,
    lastModified: new Date(),
    changeFrequency: 'weekly' as const,
    priority: 0.8,
  },
];
```


### 5. **Navigation/Internal Linking**

```typescript
// components/Navigation.tsx या जहाँ menu है
const navigationLinks = [
  // existing links...
  
  {  // 👈 नया navigation link add करें
    href: '/new-page-name',
    label: 'New Page',
    title: 'Go to New Page'
  }
];
```


### 6. **Breadcrumb Schema Update**

```typescript
// अगर breadcrumb use कर रहे हैं
const breadcrumbItems = [
  { name: 'Home', url: seoConfig.siteUrl },
  { name: 'New Page', url: `${seoConfig.siteUrl}/new-page-name` }  // 👈 add करें
];
```


### 7. **Internal Linking Strategy**

```typescript
// अन्य pages में internal links add करें
// app/page.tsx या related pages में:
<Link href="/new-page-name" title="Visit New Page">
  Related: New Page Name
</Link>
```


### 8. **Page-Specific Schema (Optional)**

```typescript
// अगर specific schema चाहिए तो:
// app/new-page-name/page.tsx में add करें:

const pageSchema = {
  '@context': 'https://schema.org',
  '@type': 'WebPage',
  name: 'New Page Name',
  description: 'Page description',
  url: `${seoConfig.siteUrl}/new-page-name`
};

export default function NewPage() {
  return (
    <>
      <script
        type="application/ld+json"
        dangerouslySetInnerHTML={{ __html: JSON.stringify(pageSchema) }}
      />
      <main>
        {/* content */}
      </main>
    </>
  );
}
```


### 9. **Images के लिए OG Image**

```
public/images/og-new-page.jpg  // 👈 नया OG image add करें
```


### 10. **Testing Checklist**

```bash
# Development में test करें:
npm run dev

# Check करें:
# ✅ /new-page-name access हो रहा है
# ✅ Meta tags show हो रहे हैं
# ✅ Sitemap में दिख रहा है: /sitemap.xml
# ✅ OG image load हो रहा है
```


## Quick Reference - File Changes Summary:

**हर नया page add करने पर ये files modify करें:**

1. ✅ `app/new-page-name/page.tsx` - नया page create करें
2. ✅ `lib/seo-config.ts` - page config add करें
3. ✅ `app/sitemap.xml/route.ts` - sitemap में route add करें
4. ✅ Navigation components - menu links add करें
5. ✅ Related pages में internal links add करें
6. ✅ `public/images/` - OG image add करें (optional)

## Automation के लिए Helper Function:

```typescript
// lib/page-generator.ts
export const generatePageSEO = (pageName: string, config: {
  title: string;
  description: string;
  keywords: string[];
  priority?: number;
}) => {
  console.log(`
Add these configurations for page: ${pageName}

1. Page config in seo-config.ts:
${pageName}: {
  title: "${config.title}",
  description: "${config.description}",
  keywords: ${JSON.stringify(config.keywords)}
},

2. Sitemap route:
{
  url: \`\${baseUrl}/${pageName}\`,
  lastModified: new Date(),
  changeFrequency: 'weekly' as const,
  priority: ${config.priority || 0.8},
},

3. Navigation link:
{
  href: '/${pageName}',
  label: '${config.title.split(' - ')[^0]}',
  title: 'Go to ${config.title.split(' - ')[^0]}'
}
  `);
};
```

यह systematic approach follow करके आप easily नये pages add कर सकते हैं complete SEO के साथ! 🚀

<div style="text-align: center">⁂</div>

[^1]: https://nextjs.org/docs/app

[^2]: https://nextjs.org/docs/pages/building-your-application/routing/pages-and-layouts

[^3]: https://dev.to/cre8stevedev/practical-guide-to-implementing-functional-seo-in-nextjs-app-router-static-dynamic-metadata-4ae2

[^4]: https://nextjs.org/docs/app/getting-started

[^5]: https://stackoverflow.com/questions/78356635/where-to-add-seo-metadata-in-next-js-with-app-router-instead-of-page-router

[^6]: https://www.wisp.blog/blog/how-to-choose-between-app-router-and-pages-router-in-nextjs-15-a-complete-guide-for-seo-conscious-developers

[^7]: https://www.youtube.com/watch?v=wTGVHLyV09M

[^8]: https://nextjs.org/learn/dashboard-app/adding-search-and-pagination

[^9]: https://nextjs.org/docs/pages/building-your-application/routing

