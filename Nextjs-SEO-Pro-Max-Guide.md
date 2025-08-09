# Next.js App Router SEO Pro Max Guide

> **Version:** Production Ready  
> **Includes:** Core SEO Setup + Performance + Rich Snippets + Multi-Region Support  
> **Author:** ChatGPT SEO Merge for DEEPAK SHARMA

---

## 1. lib/seo-config.ts

```typescript
// lib/seo-config.ts
export const seoConfig = {
  defaultTitle: "Dhur Converter - Accurate Land Area Conversion Tool",
  defaultDescription: "Free online Dhur converter tool for accurate land area conversion. Convert Dhur to Square Feet, Acres, and other units instantly with our professional calculator.",
  siteUrl: "https://dhurconverter.com",
  siteName: "Dhur Converter",
  locale: "hi_IN",
  type: "website",
  
  twitter: {
    card: "summary_large_image",
    site: "@dhurconverter",
    creator: "@dhurconverter"
  },
  
  images: {
    default: "/images/og-dhur-converter.jpg",
    dhurToFeet: "/images/og-dhur-to-feet.jpg",
    calculator: "/images/og-dhur-calculator.jpg"
  },
  
  keywords: [
    "dhur converter",
    "dhur to square feet",
    "land measurement",
    "area converter",
    "dhur calculator",
    "bigha dhur conversion",
    "land area calculator"
  ],

  languages: {
    'hi-IN': 'https://dhurconverter.com/hi',
    'en-US': 'https://dhurconverter.com',
    'ne-NP': 'https://dhurconverter.com/ne' // Added Nepalese for expansion
  }
};

export const pageConfigs = {
  home: {
    title: "Dhur Converter - Free Online Land Area Conversion Tool",
    description: "Convert Dhur to Square Feet, Acres, and other land measurement units instantly. Accurate, free, and easy-to-use Dhur converter with detailed conversion tables.",
    keywords: ["dhur converter", "dhur to square feet", "land measurement converter"]
  },
  // ... other pages configs same as before
};
```

---

## 2. components/seo/JsonLd.tsx

```typescript
import { seoConfig } from '../../lib/seo-config';

export default function JsonLd({ breadcrumbItems = [] }: { breadcrumbItems?: Array<{ name: string; url: string }> }) {
  const organizationSchema = {
    '@context': 'https://schema.org',
    '@type': 'Organization',
    name: seoConfig.siteName,
    url: seoConfig.siteUrl,
    logo: `${seoConfig.siteUrl}/logo.png`,
    description: seoConfig.defaultDescription,
    contactPoint: {
      '@type': 'ContactPoint',
      contactType: 'customer service',
      availableLanguage: ['Hindi', 'English']
    }
  };

  const websiteSchema = {
    '@context': 'https://schema.org',
    '@type': 'WebSite',
    name: seoConfig.siteName,
    url: seoConfig.siteUrl,
    description: seoConfig.defaultDescription,
    potentialAction: {
      '@type': 'SearchAction',
      target: `${seoConfig.siteUrl}/search?q={search_term_string}`,
      'query-input': 'required name=search_term_string'
    }
  };

  const breadcrumbSchema = breadcrumbItems.length > 0 ? {
    '@context': 'https://schema.org',
    '@type': 'BreadcrumbList',
    itemListElement: breadcrumbItems.map((item, index) => ({
      '@type': 'ListItem',
      position: index + 1,
      item: {
        '@id': item.url,
        name: item.name,
      },
    })),
  } : null;

  const faqSchema = {
    '@context': 'https://schema.org',
    '@type': 'FAQPage',
    mainEntity: [
      {
        '@type': 'Question',
        name: 'How to convert Dhur to Square Feet?',
        acceptedAnswer: {
          '@type': 'Answer',
          text: 'Simply enter the Dhur value in our converter and get instant square feet conversion results.'
        }
      },
      {
        '@type': 'Question',
        name: 'Is Dhur measurement used in Nepal and India?',
        acceptedAnswer: {
          '@type': 'Answer',
          text: 'Yes, Dhur is used in both Nepal and parts of India for land measurement.'
        }
      }
    ]
  };

  return (
    <>
      <script type="application/ld+json" dangerouslySetInnerHTML={{ __html: JSON.stringify(organizationSchema) }} />
      <script type="application/ld+json" dangerouslySetInnerHTML={{ __html: JSON.stringify(websiteSchema) }} />
      {breadcrumbSchema && <script type="application/ld+json" dangerouslySetInnerHTML={{ __html: JSON.stringify(breadcrumbSchema) }} />}
      <script type="application/ld+json" dangerouslySetInnerHTML={{ __html: JSON.stringify(faqSchema) }} />
    </>
  );
}
```

---

## 3. app/layout.tsx

```typescript
import type { Metadata, Viewport } from 'next';
import { Inter } from 'next/font/google';
import { seoConfig } from '../lib/seo-config';
import JsonLd from '../components/seo/JsonLd';

const inter = Inter({ subsets: ['latin'] });

export const viewport: Viewport = {
  width: 'device-width',
  initialScale: 1,
  themeColor: '#ffffff',
};

export const metadata: Metadata = {
  metadataBase: new URL(seoConfig.siteUrl),
  title: {
    default: seoConfig.defaultTitle,
    template: `%s | ${seoConfig.siteName}`
  },
  description: seoConfig.defaultDescription,
  keywords: seoConfig.keywords,
  alternates: {
    canonical: seoConfig.siteUrl,
    languages: seoConfig.languages
  },
  openGraph: {
    type: 'website',
    locale: seoConfig.locale,
    siteName: seoConfig.siteName,
    url: seoConfig.siteUrl,
    images: [seoConfig.images.default],
  },
  twitter: seoConfig.twitter
};

export default function RootLayout({ children }: { children: React.ReactNode }) {
  return (
    <html lang="hi">
      <head>
        <link rel="preconnect" href="https://fonts.gstatic.com" crossOrigin="" />
        <link rel="dns-prefetch" href="https://www.googletagmanager.com" />
      </head>
      <body className={inter.className}>
        <JsonLd />
        {children}
      </body>
    </html>
  );
}
```

---

## 4. app/sitemap.xml/route.ts

```typescript
import { seoConfig } from '../../lib/seo-config';

export async function GET() {
  const baseUrl = seoConfig.siteUrl;
  const routes = [
    {
      url: baseUrl,
      lastModified: new Date(),
      changeFrequency: 'daily',
      priority: 1,
    },
    // Add more pages dynamically from configs if needed
  ];

  const hreflangTags = Object.entries(seoConfig.languages)
    .map(([lang, href]) => `<xhtml:link rel="alternate" hreflang="${lang}" href="${href}" />`)
    .join('');

  const sitemap = `<?xml version="1.0" encoding="UTF-8"?>
    <urlset xmlns="http://www.sitemaps.org/schemas/sitemap/0.9"
            xmlns:xhtml="http://www.w3.org/1999/xhtml">
      ${routes.map(route => `
        <url>
          <loc>${route.url}</loc>
          ${hreflangTags}
          <lastmod>${route.lastModified.toISOString()}</lastmod>
          <changefreq>${route.changeFrequency}</changefreq>
          <priority>${route.priority}</priority>
        </url>`).join('')}
    </urlset>`;

  return new Response(sitemap, { status: 200, headers: { 'Content-Type': 'application/xml' } });
}
```

---

## 5. Dynamic Canonical Example

```typescript
export async function generateMetadata({ searchParams }: { searchParams: { [key: string]: string | string[] | undefined } }) {
  const canonicalBase = 'https://dhurconverter.com/dhur-calculator';
  const hasParams = Object.keys(searchParams).length > 0;
  
  return {
    title: 'Dhur Calculator',
    description: 'Advanced land area converter',
    alternates: {
      canonical: hasParams ? canonicalBase : `${canonicalBase}?${new URLSearchParams(searchParams as any)}`
    }
  };
}
```

---

## 6. next.config.js Security & Performance Headers

```javascript
/** @type {import('next').NextConfig} */
const nextConfig = {
  compress: true,
  images: { formats: ['image/webp', 'image/avif'] },
  async headers() {
    return [
      {
        source: '/:path*',
        headers: [
          { key: 'X-DNS-Prefetch-Control', value: 'on' },
          { key: 'Strict-Transport-Security', value: 'max-age=63072000; includeSubDomains; preload' },
        ],
      },
    ];
  },
};

module.exports = nextConfig;
```

---

**✅ अब ये setup तुम्हारे project को SEO + Speed + Internationalization के लिए पूरी तरह ready बना देगा।**
