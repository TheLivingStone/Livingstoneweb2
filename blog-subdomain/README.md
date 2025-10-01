# Blog Subdomain Setup Guide

## Overview
This directory contains all files for the `blog.thelivingstonefoundation.com` subdomain.

## Directory Structure

```
blog-subdomain/
├── index.html              # Main blog landing page (homepage for blog.thelivingstonefoundation.com)
├── post/                   # Individual blog post pages
│   ├── ai-agents-enterprise-future.html
│   ├── techcorp-case-study.html
│   ├── llm-business-guide.html
│   └── ... (more articles)
├── category/               # Category archive pages
│   ├── ai-insights.html
│   ├── case-studies.html
│   ├── industry-trends.html
│   ├── tutorials.html
│   └── technical.html
└── README.md              # This file
```

## Features

### Main Blog Page (index.html)
- **Hero Section** with search functionality
- **Featured Article** prominently displayed
- **Category Filtering** (All Posts, AI Insights, Case Studies, Industry Trends, Tutorials, Technical)
- **Blog Grid** with 9+ articles
- **Pagination** for browsing multiple pages
- **Newsletter Signup** for email subscriptions
- **Search Functionality** to find articles by keyword

### Individual Post Pages
- Full article content with rich formatting
- Related articles section
- Author information and metadata
- Share buttons
- Newsletter signup

### Category Pages
- Filtered views showing only articles from specific categories
- Same layout and design as main blog page

## Setup Instructions

### For Local Development
1. Place this `blog-subdomain` folder at the root of your web server
2. Access at: `http://localhost/blog-subdomain/` or configure subdomain locally

### For Production Deployment

#### Option 1: Subdomain Configuration (Recommended)
1. **DNS Setup:**
   - Create an A record for `blog` pointing to your server IP
   - Or create a CNAME record: `blog.thelivingstonefoundation.com` → `thelivingstonefoundation.com`

2. **Server Configuration (Apache):**
   ```apache
   <VirtualHost *:80>
       ServerName blog.thelivingstonefoundation.com
       DocumentRoot /var/www/thelivingstonefoundation.com/blog-subdomain

       <Directory /var/www/thelivingstonefoundation.com/blog-subdomain>
           Options Indexes FollowSymLinks
           AllowOverride All
           Require all granted
       </Directory>
   </VirtualHost>
   ```

3. **Server Configuration (Nginx):**
   ```nginx
   server {
       listen 80;
       server_name blog.thelivingstonefoundation.com;
       root /var/www/thelivingstonefoundation.com/blog-subdomain;
       index index.html;

       location / {
           try_files $uri $uri/ =404;
       }
   }
   ```

#### Option 2: Subdirectory with Rewrite
If you prefer `/blog/` URL structure instead of subdomain:
1. Move `blog-subdomain` contents to `/blog/` directory
2. Update all internal links to use relative paths
3. Access at: `https://thelivingstonefoundation.com/blog/`

## Content Management

### Adding New Blog Posts
1. Create new HTML file in `/post/` directory
2. Use existing post template structure
3. Add entry to `index.html` articles grid:
   ```html
   <article class="blog-card" data-category="your-category">
       <div class="blog-image">
           <i class="fas fa-icon-name"></i>
           <span class="blog-category">Category Name</span>
       </div>
       <div class="blog-content">
           <h3>Your Article Title</h3>
           <p>Article description...</p>
           <div class="blog-meta">
               <span class="blog-date">Date</span>
               <span class="blog-read-time">X min read</span>
           </div>
           <a href="post/your-article.html" class="blog-link">Read More <i class="fas fa-arrow-right"></i></a>
       </div>
   </article>
   ```

### Categories
Available categories:
- `ai-insights` - AI Insights
- `case-studies` - Case Studies
- `industry-trends` - Industry Trends
- `tutorials` - Tutorials
- `technical` - Technical

## SEO Optimization
- Each page has proper meta tags
- Semantic HTML structure
- Alt text for images
- Clean URL structure
- Mobile responsive
- Fast loading with optimized CSS

## Analytics Integration
Add your analytics tracking code to the `<head>` section of index.html and post templates:
```html
<!-- Google Analytics -->
<script async src="https://www.googletagmanager.com/gtag/js?id=YOUR-GA-ID"></script>
<script>
  window.dataLayer = window.dataLayer || [];
  function gtag(){dataLayer.push(arguments);}
  gtag('js', new Date());
  gtag('config', 'YOUR-GA-ID');
</script>
```

## Current Articles
1. ✅ The Future of AI Agents in Enterprise: Beyond Automation (Featured)
2. ✅ How TechCorp Reduced Operational Costs by 40%
3. ✅ Understanding Large Language Models: A Business Leader's Guide
4. 🔄 2025 AI Predictions: What Every Business Should Know
5. 🔄 Getting Started with AI Integration
6. 🔄 Revolutionizing Healthcare with AI
7. 🔄 AI Security and Privacy
8. 🔄 RAG Implementation
9. 🔄 Measuring AI ROI

Legend:
- ✅ = Complete with full content
- 🔄 = Listed but needs full content creation

## Next Steps
1. ✅ Create main blog landing page (index.html)
2. 🔄 Copy article files from main site to `/post/` directory
3. 🔄 Create category archive pages
4. 🔄 Test subdomain configuration
5. 🔄 Set up SSL certificate for blog subdomain
6. 🔄 Configure CDN if needed
7. 🔄 Add sitemap for blog posts
8. 🔄 Set up RSS feed

## Support
For questions or issues, contact: info@thelivingstonefoundation.com
