# Crawlee.dev – The Ultimate Web Scraping & Automation Framework
![image](https://github.com/user-attachments/assets/5efb7a44-482c-4e37-9b3a-6fb4a9c07586)


## Introduction
Crawlee is an open-source web scraping and automation framework developed by Apify. It provides built-in support for Puppeteer and Playwright for JavaScript-heavy websites, as well as HTTP-based crawling for simpler scraping tasks. With powerful features such as automatic request queuing, proxy rotation, and anti-bot mechanisms, Crawlee makes large-scale data extraction efficient and reliable.

## Key Features
- **Headless Browser Support** – Works seamlessly with Puppeteer and Playwright.
- **Anti-Bot Protection** – Built-in handling of rate limits, retries, and CAPTCHA-solving integrations.
- **Scalability** – Efficient queue-based architecture for running multiple crawlers.
- **Extensibility** – Middleware support for custom logic and data transformation.
- **Community & Documentation** – Well-supported by Apify with extensive documentation.

## Use Cases
Crawlee is ideal for:
- **Data Collection** – Market research, academic studies, AI training datasets.
- **SEO Monitoring** – Track rankings, keywords, and competitor strategies.
- **E-commerce & Price Tracking** – Monitor product prices and availability.
- **Lead Generation** – Automate business contact extraction.
- **Content Aggregation** – Gather and structure data from multiple sources.

## Installation
Install Crawlee using npm or yarn:
```sh
npm install crawlee playwright child_process.execSync
```
OR
```sh
yarn add crawlee playwright child_process.execSync
```

## Basic Usage
Here's a simple example using PlayWrightCrawler:
```javascript
import { PlaywrightCrawler, Dataset } from 'crawlee';
import { chromium } from 'playwright';
import { execSync } from 'child_process';

async function ensureBrowser() {
    try {
        await chromium.launch();
    } catch (error) {
        console.log('Installing browser dependencies...');
        execSync('npx playwright install chromium', { stdio: 'inherit' });
    }
}

const config = {
    maxRequestsPerCrawl: 10,
    maxConcurrency: 10,
    launchContext: {
        launchOptions: {
            headless: false,
            args: ['--no-sandbox', '--disable-setuid-sandbox']
        },
    },
};

const crawler = new PlaywrightCrawler({
    ...config,
    async requestHandler({ page, request, enqueueLinks, log }) {
        log.info(`Processing ${request.url}`);
        try {
            await page.waitForLoadState('networkidle', { timeout: 30000 });
            const data = await page.evaluate(() => {
                return {
                    title: document.querySelector('h1')?.innerText,
                    description: document.querySelector('meta[name="description"]')?.content,
                    paragraphs: Array.from(document.querySelectorAll('p')).map(p => p.innerText),
                    links: Array.from(document.querySelectorAll('a')).map(a => a.href),
                };
            });
            await Dataset.pushData({
                url: request.url,
                ...data,
                timestamp: new Date().toISOString(),
            });
            await enqueueLinks({
                strategy: 'same-domain',
                transformRequestFunction: (req) => {
                    if (req.url.match(/\.(jpg|jpeg|png|gif|pdf)$/i)) {
                        return false;
                    }
                    return req;
                },
            });
        } catch (error) {
            log.error(`Error processing ${request.url}: ${error.message}`);
        }
    },
    failedRequestHandler({ request, log }) {
        log.error(`Request ${request.url} failed`);
    },
});

async function startCrawling(startUrl) {
    try {
        console.log('Ensuring browser is installed...');
        await ensureBrowser();
        console.log('Starting the crawler...');
        await crawler.run([startUrl]);
        console.log('Crawler finished');
    } catch (error) {
        console.error('Crawler failed:', error);
    }
}

const startUrl = 'https://en.wikipedia.org/wiki/Web_scraping';
startCrawling(startUrl);
```

## Disadvantages
While Crawlee is powerful, it has some limitations:
- **Steeper Learning Curve** – May be overwhelming for beginners.
- **Resource Intensive** – Using headless browsers consumes more memory and CPU.
- **Not Ideal for API-Based Data Extraction** – If an API is available, direct access is often preferable.

## Comparisons & Alternatives
| Feature | Crawlee | Scrapy | Selenium | BeautifulSoup |
|---------|--------|--------|----------|---------------|
| JavaScript Handling | ✅ | ❌ | ✅ | ❌ |
| Large-Scale Scraping | ✅ | ✅ | ❌ | ❌ |
| GUI Automation | ❌ | ❌ | ✅ | ❌ |
| Ease of Use | Medium | Medium | Hard | Easy |

### When to Use Alternatives
- **For Simple HTML Scraping** – Use BeautifulSoup or Requests.
- **If an API Exists** – Direct API calls are more efficient.
- **For UI Testing & Automation** – Selenium or Playwright may be more suitable.

## Conclusion
Crawlee is a powerful and flexible tool for web scraping and automation, particularly when dealing with JavaScript-heavy websites. It offers scalability, anti-bot protection, and headless browser support, making it a great choice for large-scale data extraction. However, for simpler tasks, alternative tools like BeautifulSoup or API calls may be more efficient.

For more details, check out the [official Crawlee documentation](https://crawlee.dev/).

