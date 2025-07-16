# APKRoja - High-Speed APK Download Website

A modern, high-performance APK download website similar to APKPure and APKCombo, built with React frontend and Flask backend. Optimized for Google AdSense approval and SEO performance.

## 🚀 Live Demo

**Website URL:** https://apkroja.com/
**Admin Panel:** https://apkroja.com/admin (admin/admin)

## ✨ Features

### Core Features
- **Modern UI/UX**: Clean, responsive design optimized for mobile and desktop
- **8-Second Download Timer**: Countdown timer before showing final download link
- **Dark Mode Support**: Toggle between light and dark themes
- **Fast Loading**: Optimized for Google PageSpeed score 90+
- **Mobile-First Design**: Responsive layout that works on all devices

### SEO & AdSense Optimization
- **Google Schema Markup**: Structured data for articles, reviews, FAQs, breadcrumbs
- **SEO-Ready Structure**: Optimized meta tags, titles, and descriptions
- **Clean URLs**: SEO-friendly URLs like `/apps/spotify-premium-mod`
- **Auto-Generated Sitemap**: XML sitemap and robots.txt
- **Ad Placement Areas**: Header, sidebar, content, and footer ad zones

### Content Management
- **Easy Admin Panel**: No-code interface for managing content
- **App Management**: Add/edit apps with descriptions, screenshots, versions
- **Category Management**: Organize apps into categories
- **Review System**: User comments and ratings with moderation
- **Article/Tutorial System**: Write and publish articles

### Pages Included
- **Homepage**: Featured apps, categories, recent updates
- **App Detail Pages**: Full app information with screenshots
- **Download Pages**: Secure download with countdown timer
- **Category Pages**: Browse apps by category
- **Search Page**: Advanced search functionality
- **Legal Pages**: Privacy Policy, Terms of Service, About Us, Contact
- **Admin Panel**: Complete CMS for content management

## 🛠 Tech Stack

### Frontend
- **React 19**: Modern React with hooks and functional components
- **Tailwind CSS**: Utility-first CSS framework
- **Lucide Icons**: Beautiful, customizable icons
- **React Router**: Client-side routing
- **React Helmet**: SEO meta tag management

### Backend
- **Flask**: Python web framework
- **SQLAlchemy**: Database ORM
- **Flask-CORS**: Cross-origin resource sharing
- **SQLite**: Database (easily upgradeable to PostgreSQL)

### Deployment
- **Production Ready**: Built and optimized for deployment
- **Static Assets**: Optimized CSS and JavaScript bundles
- **API Integration**: RESTful API for frontend-backend communication

## 📁 Project Structure

```
apkroja-website/
├── apkroja-frontend/          # React frontend application
│   ├── src/
│   │   ├── components/        # Reusable UI components
│   │   │   ├── Header.jsx     # Navigation header
│   │   │   ├── Footer.jsx     # Site footer
│   │   │   ├── AdBanner.jsx   # AdSense integration
│   │   │   ├── SEO.jsx        # SEO meta tags
│   │   │   ├── Breadcrumb.jsx # Navigation breadcrumbs
│   │   │   └── ui/            # UI components (buttons, forms, etc.)
│   │   ├── pages/             # Page components
│   │   │   ├── HomePage.jsx   # Main landing page
│   │   │   ├── AppDetailPage.jsx # App information page
│   │   │   ├── DownloadPage.jsx  # Download with timer
│   │   │   ├── CategoryPage.jsx  # Category listings
│   │   │   ├── SearchPage.jsx    # Search functionality
│   │   │   ├── AdminPanel.jsx    # Admin interface
│   │   │   ├── PrivacyPage.jsx   # Privacy policy
│   │   │   ├── TermsPage.jsx     # Terms of service
│   │   │   ├── AboutPage.jsx     # About us
│   │   │   └── ContactPage.jsx   # Contact form
│   │   ├── services/          # API service layer
│   │   │   └── api.js         # API communication
│   │   ├── utils/             # Utility functions
│   │   │   └── sitemap.js     # SEO utilities
│   │   └── App.jsx            # Main app component
│   ├── dist/                  # Built production files
│   └── package.json           # Dependencies
├── apkroja-backend/           # Flask backend application
│   ├── src/
│   │   ├── models/            # Database models
│   │   │   ├── app.py         # App model
│   │   │   └── user.py        # User model
│   │   ├── routes/            # API routes
│   │   │   ├── api.py         # Public API endpoints
│   │   │   └── admin.py       # Admin API endpoints
│   │   ├── static/            # Static files (built frontend)
│   │   └── main.py            # Flask application
│   ├── venv/                  # Python virtual environment
│   └── requirements.txt       # Python dependencies
└── README.md                  # This documentation
```

## 🚀 Quick Start

### Prerequisites
- Node.js 20+ and pnpm
- Python 3.11+
- Git

### Installation

1. **Clone the repository**
```bash
git clone <repository-url>
cd apkroja-website
```

2. **Setup Backend**
```bash
cd apkroja-backend
python -m venv venv
source venv/bin/activate  # On Windows: venv\Scripts\activate
pip install -r requirements.txt
```

3. **Setup Frontend**
```bash
cd ../apkroja-frontend
pnpm install
```

### Development

1. **Start Backend Server**
```bash
cd apkroja-backend
source venv/bin/activate
python src/main.py
```
Backend will run on http://localhost:5000

2. **Start Frontend Development Server**
```bash
cd apkroja-frontend
pnpm run dev
```
Frontend will run on http://localhost:5173

### Production Build

1. **Build Frontend**
```bash
cd apkroja-frontend
pnpm run build
```

2. **Copy to Backend**
```bash
cp -r dist/* ../apkroja-backend/src/static/
```

3. **Deploy**
The Flask backend will serve both API and frontend from http://localhost:5000

## 🔧 Configuration

### Environment Variables
Create a `.env` file in the backend directory:
```env
SECRET_KEY=your-secret-key-here
DATABASE_URL=sqlite:///apkroja.db
ADMIN_USERNAME=admin
ADMIN_PASSWORD=your-secure-password
```

### API Configuration
Update the API base URL in `apkroja-frontend/src/services/api.js`:
```javascript
const API_BASE_URL = process.env.NODE_ENV === 'production' 
  ? 'https://yourdomain.com/api' 
  : 'http://localhost:5000/api'
```

### AdSense Integration
Add your AdSense code in `apkroja-frontend/src/components/AdBanner.jsx`:
```javascript
// Replace with your actual AdSense code
<ins className="adsbygoogle"
     style={{display: 'block'}}
     data-ad-client="ca-pub-XXXXXXXXXXXXXXXX"
     data-ad-slot="XXXXXXXXXX"
     data-ad-format="auto"></ins>
```

## 📊 Admin Panel

### Access
- URL: `/admin`
- Default credentials: `admin` / `admin`
- Change credentials in the backend configuration

### Features
- **Dashboard**: Overview of apps, downloads, and statistics
- **App Management**: Add, edit, delete apps with full details
- **Category Management**: Create and manage app categories
- **Review Moderation**: Approve or reject user reviews
- **Analytics**: View download statistics and popular apps

### Adding New Apps
1. Login to admin panel
2. Go to "Apps" section
3. Click "Add New App"
4. Fill in details:
   - Name, developer, version
   - Description and features
   - Screenshots (URLs)
   - Download URL
   - Category and tags
5. Save and publish

## 🎨 Customization

### Branding
- Update logo and colors in `apkroja-frontend/src/components/Header.jsx`
- Modify theme colors in `apkroja-frontend/src/App.css`
- Change site name and description in `index.html`

### Adding New Pages
1. Create new component in `src/pages/`
2. Add route in `src/App.jsx`
3. Update navigation in `src/components/Header.jsx`

### Database Schema
The database includes these main tables:
- **apps**: App information and metadata
- **categories**: App categories
- **reviews**: User reviews and ratings
- **downloads**: Download tracking
- **users**: Admin users

## 🔍 SEO Features

### Structured Data
- Article schema for app pages
- Review schema for ratings
- FAQ schema for help pages
- Breadcrumb navigation

### Meta Tags
- Dynamic titles and descriptions
- Open Graph tags for social sharing
- Twitter Card support
- Canonical URLs

### Sitemap Generation
Automatic XML sitemap generation including:
- Static pages
- App detail pages
- Category pages
- Image sitemaps for screenshots

## 📱 Mobile Optimization

- **Responsive Design**: Works on all screen sizes
- **Touch-Friendly**: Large tap targets and gestures
- **Fast Loading**: Optimized images and code splitting
- **PWA Ready**: Can be extended to Progressive Web App

## 🔒 Security Features

- **Input Validation**: All user inputs are validated
- **SQL Injection Protection**: Using SQLAlchemy ORM
- **XSS Prevention**: Proper output encoding
- **CSRF Protection**: Built-in Flask security
- **Admin Authentication**: Secure admin panel access

## 📈 Performance Optimization

- **Code Splitting**: Lazy loading of components
- **Image Optimization**: Responsive images with proper sizing
- **Caching**: Browser caching for static assets
- **Minification**: Compressed CSS and JavaScript
- **CDN Ready**: Static assets can be served from CDN

## 🚀 Deployment Options

### Option 1: Traditional Hosting
1. Build the frontend: `pnpm run build`
2. Copy files to Flask static directory
3. Deploy Flask app to your hosting provider
4. Configure domain and SSL

### Option 2: Separate Deployment
1. Deploy frontend to Netlify/Vercel
2. Deploy backend to Heroku/DigitalOcean
3. Update API URLs in frontend
4. Configure CORS for cross-origin requests

### Option 3: Docker Deployment
Create `Dockerfile` for containerized deployment:
```dockerfile
FROM python:3.11-slim
COPY apkroja-backend /app
WORKDIR /app
RUN pip install -r requirements.txt
EXPOSE 5000
CMD ["python", "src/main.py"]
```

## 📋 AdSense Approval Checklist

✅ **Content Requirements**
- Original, high-quality content
- Regular updates with new apps
- Detailed app descriptions
- User-generated reviews

✅ **Technical Requirements**
- Fast loading speed (90+ PageSpeed score)
- Mobile-responsive design
- HTTPS enabled
- Clean, professional design

✅ **Legal Pages**
- Privacy Policy (comprehensive)
- Terms of Service
- About Us page
- Contact information

✅ **Navigation & UX**
- Clear site navigation
- Search functionality
- Breadcrumb navigation
- User-friendly URLs

✅ **Ad Placement**
- Strategic ad placement areas
- Non-intrusive ad integration
- Proper ad-to-content ratio
- Mobile-optimized ad sizes

## 🐛 Troubleshooting

### Common Issues

**Frontend not loading data:**
- Check API URL configuration
- Verify backend server is running
- Check browser console for errors

**Admin panel not accessible:**
- Verify admin credentials
- Check Flask session configuration
- Ensure database is initialized

**Slow loading:**
- Optimize images
- Enable gzip compression
- Use CDN for static assets
- Implement caching

### Debug Mode
Enable debug mode in Flask for development:
```python
app.run(debug=True, host='0.0.0.0', port=5000)
```

## 📞 Support

For technical support or questions:
- Email: support@apkroja.com
- Documentation: This README file
- Issues: Create GitHub issues for bugs

## 📄 License

This project is licensed under the MIT License. See LICENSE file for details.

## 🙏 Acknowledgments

- Inspired by APKPure and APKCombo
- Built with modern web technologies
- Optimized for performance and SEO
- Designed for AdSense approval

---

**Note**: Remember to update all placeholder URLs, credentials, and configuration values before deploying to production. Always use secure passwords and keep your dependencies updated.

