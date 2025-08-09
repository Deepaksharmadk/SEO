# Portfolio SEO Pro Max — Commented Version

> हर file के important lines पर comment है — जो भी line क्या करती है, उसे हिंदी में समझाया गया है।
> File को project में paste करने से पहले comments TypeScript/JSX में रहींगी — production में आप comments रख सकते हो, या हटाकर use कर सकते हो।

---

## 1. lib/seo-config.ts (Comments explain key fields)

```typescript
// lib/seo-config.ts
export const seoConfig = {
  defaultTitle: "Deepak Sharma - Full Stack Developer", // 👈 Default title used when page-specific title missing
  defaultDescription: "Portfolio of Deepak Sharma, a Full Stack Developer specializing in Next.js, React, and Node.js. Check my projects and contact me for collaborations.", // 👈 Default meta description
  siteUrl: "https://deepaksharma.dev", // 👈 आपकी साइट का canonical base URL
  siteName: "Deepak Sharma", // 👈 Website / Organization का नाम
  locale: "en_US", // 👈 default locale, SEO और OG में उपयोग होगा
  type: "website", // 👈 OpenGraph type

  twitter: {
    card: "summary_large_image", // 👈 Twitter card type (large image preview)
    site: "@deepaksharma", // 👈 Twitter handle for site
    creator: "@deepaksharma" // 👈 Twitter handle of author/creator
  },

  images: {
    default: "/images/og-portfolio.jpg", // 👈 Default OG image path (used by social shares)
    projects: "/images/og-projects.jpg" // 👈 Page-specific OG image example
  },

  keywords: [
    "Deepak Sharma",
    "Full Stack Developer",
    "Next.js Portfolio" // 👈 सेम्पल keywords — meta keywords अब कम relevant हैं पर रखे जा सकते हैं
  ],

  languages: {
    'en-US': 'https://deepaksharma.dev', // 👈 hreflang mapping — क्या URL किस language के लिए है
    'hi-IN': 'https://deepaksharma.dev/hi'
  }
};

export const pageConfigs = {
  home: {
    title: "Deepak Sharma - Full Stack Developer", // 👈 page-specific title (override default)
    description: "Welcome to my portfolio! Explore my work in web development and design.", // 👈 page-specific description
    keywords: ["Deepak Sharma", "Portfolio"]
  },
  // about/projects/contact similar...
};
```

**सार:** `seoConfig` में global defaults रखो — हर page की metadata इनसे inherit कर सकती है। `pageConfigs` में page-specific overrides डालो।

---

## 2. app/layout.tsx (Head setup, preconnect, Json-LD)

```typescript
import type { Metadata } from 'next';
import { Inter } from 'next/font/google';
import { seoConfig } from '../lib/seo-config';
import JsonLd from '../components/seo/JsonLd'; // 👈 JSON-LD component जो structured data inject करेगा

const inter = Inter({ subsets: ['latin'] }); // 👈 Google font preload के लिए Next.js helper

export const metadata: Metadata = {
  metadataBase: new URL(seoConfig.siteUrl), // 👈 base URL used by Next metadata system
  title: {
    default: seoConfig.defaultTitle, // 👈 default title
    template: `%s | ${seoConfig.siteName}` // 👈 title template — %s will be page title
  },
  description: seoConfig.defaultDescription, // 👈 default description
  keywords: seoConfig.keywords, // 👈 meta keywords (optional)
  alternates: {
    canonical: seoConfig.siteUrl, // 👈 default canonical
    languages: seoConfig.languages // 👈 hreflang entries for <link rel="alternate" hreflang=...>
  },
  openGraph: {
    type: 'website',
    locale: seoConfig.locale,
    url: seoConfig.siteUrl,
    siteName: seoConfig.siteName,
    images: [seoConfig.images.default], // 👈 OG image default
  },
  twitter: seoConfig.twitter // 👈 Twitter card metadata
};

export default function RootLayout({ children }: { children: React.ReactNode }) {
  return (
    <html lang="en">
      <head>
        {/* Preconnect helps browser establish early connection to fonts / analytics */}
        <link rel="preconnect" href="https://fonts.gstatic.com" crossOrigin="" /> 
        <link rel="dns-prefetch" href="https://www.googletagmanager.com" />
      </head>
      <body className={inter.className}>
        <JsonLd /> {/* ← Page पर structured data add करता है (Organization, Website, etc.) */}
        {children}
      </body>
    </html>
  );
}
```

**सार:** `metadata` Next.js App Router को बताता है कि default meta क्या हों — page-level metadata इसे override कर सकता है। `preconnect` और `dns-prefetch` से fonts और analytics जल्दी load होते हैं — बेहतर CLS/LCP के लिए helpful।

---

## 3. app/projects/page.tsx (Page-level metadata explained)

```typescript
import type { Metadata } from 'next';
import { pageConfigs, seoConfig } from '../../lib/seo-config';

export const metadata: Metadata = {
  title: pageConfigs.projects.title, // 👈 page-specific title
  description: pageConfigs.projects.description, // 👈 page description used in meta description
  keywords: pageConfigs.projects.keywords, // 👈 (optional) keywords meta tag
  openGraph: {
    title: pageConfigs.projects.title, // 👈 OG title for social shares
    description: pageConfigs.projects.description, // 👈 OG description
    url: `${seoConfig.siteUrl}/projects`, // 👈 canonical URL for OG
    images: [
      {
        url: seoConfig.images.projects, // 👈 image used when sharing on social
        width: 1200,
        height: 630,
        alt: 'Deepak Sharma Projects' // 👈 image alt for accessibility and SEO
      }
    ]
  },
  alternates: {
    canonical: `${seoConfig.siteUrl}/projects` // 👈 explicit canonical — helps avoid duplicates
  }
};

export default function ProjectsPage() {
  return (
    <main>
      <h1>My Projects</h1>
      <p>Here are some of my recent works...</p>
    </main>
  );
}
```

**सार:** Page-level `metadata` जरूरी है ताकि हर page search results और social shares में सही दिखे। `openGraph` images और titles social preview control करते हैं।

---

## 4. app/sitemap.xml/route.ts (Sitemap + hreflang)

```typescript
import { seoConfig } from '../../lib/seo-config';

export async function GET() {
  const baseUrl = seoConfig.siteUrl;
  const routes = [
    { url: baseUrl, changeFrequency: 'monthly', priority: 1 }, // 👈 homepage
    { url: `${baseUrl}/about`, changeFrequency: 'yearly', priority: 0.8 },
    { url: `${baseUrl}/projects`, changeFrequency: 'weekly', priority: 0.9 },
    { url: `${baseUrl}/contact`, changeFrequency: 'yearly', priority: 0.7 }
  ];

  // 👇 hreflang tags — search engines को मदद करते हैं language-specific pages समझने में
  const hreflangTags = Object.entries(seoConfig.languages)
    .map(([lang, href]) => `<xhtml:link rel="alternate" hreflang="${lang}" href="${href}" />`)
    .join('');

  const sitemap = `<?xml version="1.0" encoding="UTF-8"?>
    <urlset xmlns="http://www.sitemaps.org/schemas/sitemap/0.9"
            xmlns:xhtml="http://www.w3.org/1999/xhtml">
      ${routes.map(route => `
        <url>
          <loc>${route.url}</loc>
          ${hreflangTags}  <!-- 👈 हर URL के अंदर alternate language links डालते हैं -->
          <changefreq>${route.changeFrequency}</changefreq>
          <priority>${route.priority}</priority>
        </url>`).join('')}
    </urlset>`;

  return new Response(sitemap, { status: 200, headers: { 'Content-Type': 'application/xml' } });
}
```

**सार:** Sitemap dynamic generate करने से तुम्हें हर बार manual update नहीं करना पड़ता — और `hreflang` entries multiple languages को signal करते हैं।

---

## 5. components/seo/JsonLd.tsx (Structured data short explain)

```typescript
import { seoConfig } from '../../lib/seo-config';

export default function JsonLd() {
  const organizationSchema = {
    '@context': 'https://schema.org',
    '@type': 'Organization',
    name: seoConfig.siteName, // 👈 organization/site name
    url: seoConfig.siteUrl, // 👈 canonical URL
    logo: `${seoConfig.siteUrl}/logo.png`, // 👈 logo URL — Google prefers absolute URLs
    description: seoConfig.defaultDescription,
  };

  const websiteSchema = {
    '@context': 'https://schema.org',
    '@type': 'WebSite',
    name: seoConfig.siteName,
    url: seoConfig.siteUrl,
    description: seoConfig.defaultDescription
  };

  return (
    <>
      {/* JSON-LD scripts — search engines read these to create rich results */}
      <script type="application/ld+json" dangerouslySetInnerHTML={{ __html: JSON.stringify(organizationSchema) }} />
      <script type="application/ld+json" dangerouslySetInnerHTML={{ __html: JSON.stringify(websiteSchema) }} />
    </>
  );
}
```

**सार:** JSON-LD structured data से Google को यह पता चलता है कि साइट किस बारे में है — इससे rich snippets मिलने का chance बढ़ता है।

---

### Quick Tips (छोटे नोट्स)

- `canonical` tag हर dynamic/filtered page पर ज़रूरी है — इससे duplicate content problem नहीं आएगी।  
- OG images absolute URLs होने चाहिए (i.e., `https://.../og.jpg`) — social preview ठीक से दिखाने के लिए।  
- Performance: fonts को preload/preconnect करो और non-critical JS को defer/async लगाओ।  
- हर नया page add करने पर `pageConfigs` और `sitemap` में entry add कर दो।

---

अगर चाहो तो मैं अब इस commented.md फ़ाइल को `/mnt/data/Portfolio-SEO-Setup-Commented.md` के रूप में save कर दूँ और डाउनलोड link दे दूँ।  
कह दो, मैं save कर दूँ?