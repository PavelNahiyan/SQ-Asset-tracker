# SQ-Asset-tracker
IT Asset tracking and inventory management system For SQ Agriculture


# SQ Asset Tracker - Complete Deployment Guide

## 🚀 Pre-Deployment Checklist

### System Requirements
- Node.js 18+ installed
- npm or yarn package manager
- Firebase account
- Modern web browser
- Text editor/IDE (VS Code recommended)

---

## 📦 Step 1: Project Setup

### 1.1 Create React Project
```bash
# Create new React app
npx create-react-app sq-asset-tracker
cd sq-asset-tracker

# Install required dependencies
npm install firebase qrcode.react jspdf html2canvas uuid lucide-react

# Install shadcn/ui components
npx shadcn-ui@latest init
npx shadcn-ui@latest add card button input
```

### 1.2 Project Structure
```
sq-asset-tracker/
├── public/
├── src/
│   ├── components/
│   │   └── ui/
│   ├── firebase.js
│   ├── App.js
│   └── index.js
├── package.json
└── README.md
```

---

## 🔥 Step 2: Firebase Configuration

### 2.1 Create Firebase Project
1. Go to [Firebase Console](https://console.firebase.google.com/)
2. Click "Create a project"
3. Enter project name: `sq-asset-tracker`
4. Enable Google Analytics (optional)
5. Click "Create project"

### 2.2 Enable Firebase Services

#### Authentication Setup:
1. In Firebase Console → Authentication
2. Click "Get started"
3. Go to "Sign-in method" tab
4. Enable "Email/Password"
5. Save changes

#### Firestore Database Setup:
1. In Firebase Console → Firestore Database
2. Click "Create database"
3. Choose "Start in test mode" (we'll secure it later)
4. Select location closest to your users
5. Click "Done"

#### Storage Setup:
1. In Firebase Console → Storage
2. Click "Get started"
3. Start in test mode
4. Choose same location as Firestore
5. Click "Done"

### 2.3 Get Firebase Configuration
1. In Firebase Console → Project Settings (gear icon)
2. Scroll to "Your apps" section
3. Click "Web" icon (</>) to add web app
4. Register app name: `SQ Asset Tracker`
5. Copy the configuration object

### 2.4 Create Firebase Configuration File
Create `src/firebase.js`:

```javascript
import { initializeApp } from 'firebase/app';
import { getFirestore } from 'firebase/firestore';
import { getStorage } from 'firebase/storage';
import { getAuth } from 'firebase/auth';

// Your web app's Firebase configuration
const firebaseConfig = {
  apiKey: "your-api-key",
  authDomain: "your-project.firebaseapp.com",
  projectId: "your-project-id",
  storageBucket: "your-project.appspot.com",
  messagingSenderId: "123456789",
  appId: "your-app-id"
};

// Initialize Firebase
const app = initializeApp(firebaseConfig);

// Initialize Firebase services
export const db = getFirestore(app);
export const storage = getStorage(app);
export const auth = getAuth(app);

export default app;
```

---

## 💻 Step 3: Application Code Setup

### 3.1 Update App.js
Replace the content in `src/App.js` with your asset tracker component code.

### 3.2 Install Additional Dependencies
```bash
# If using Tailwind CSS
npm install -D tailwindcss postcss autoprefixer
npx tailwindcss init -p

# Update tailwind.config.js
module.exports = {
  content: [
    "./src/**/*.{js,jsx,ts,tsx}",
  ],
  theme: {
    extend: {},
  },
  plugins: [],
}

# Add to src/index.css
@tailwind base;
@tailwind components;
@tailwind utilities;
```

---

## 🔒 Step 4: Firebase Security Rules

### 4.1 Firestore Security Rules
In Firebase Console → Firestore Database → Rules:

```javascript
rules_version = '2';
service cloud.firestore {
  match /databases/{database}/documents {
    // Assets collection - only authenticated users
    match /assets/{document} {
      allow read, write, delete: if request.auth != null;
    }
    
    // Deny all other access
    match /{document=**} {
      allow read, write: if false;
    }
  }
}
```

### 4.2 Storage Security Rules
In Firebase Console → Storage → Rules:

```javascript
rules_version = '2';
service firebase.storage {
  match /b/{bucket}/o {
    match /assets/{allPaths=**} {
      // Allow authenticated users to upload/download asset images
      allow read, write: if request.auth != null;
    }
  }
}
```

---

## 👤 Step 5: Create Admin User

### 5.1 Add Admin User via Firebase Console
1. Go to Firebase Console → Authentication → Users
2. Click "Add user"
3. Enter email: `admin@yourcompany.com`
4. Enter password: (use strong password)
5. Click "Add user"

### 5.2 Alternative: Create via Code
Add this temporary code to create admin user:

```javascript
// Temporary code in your component for first-time setup
const createAdminUser = async () => {
  try {
    await createUserWithEmailAndPassword(auth, 'admin@yourcompany.com', 'your-secure-password');
    console.log('Admin user created successfully');
  } catch (error) {
    console.error('Error creating admin user:', error);
  }
};
```

---

## 🧪 Step 6: Local Testing

### 6.1 Test Application Locally
```bash
# Start development server
npm start

# Test in browser at http://localhost:3000
```

### 6.2 Testing Checklist
- [ ] Admin login works
- [ ] Can add new assets
- [ ] Image upload works
- [ ] QR codes generate properly
- [ ] PDF download works
- [ ] CSV export works
- [ ] Asset deletion works
- [ ] Logout works

---

## 🚀 Step 7: Production Deployment Options

## Option A: Firebase Hosting (Recommended)

### 7.1 Install Firebase CLI
```bash
npm install -g firebase-tools
firebase login
```

### 7.2 Initialize Firebase Hosting
```bash
# In your project directory
firebase init hosting

# Select options:
# - Use existing project: sq-asset-tracker
# - Public directory: build
# - Single-page app: Yes
# - Overwrite index.html: No
```

### 7.3 Build and Deploy
```bash
# Build production version
npm run build

# Deploy to Firebase
firebase deploy --only hosting

# Your app will be available at:
# https://your-project-id.web.app
```

## Option B: Netlify Deployment

### 7.1 Build Project
```bash
npm run build
```

### 7.2 Deploy to Netlify
1. Go to [Netlify](https://netlify.com)
2. Drag and drop the `build` folder
3. Or connect GitHub repository for auto-deployment

### 7.3 Environment Variables
In Netlify dashboard → Site settings → Environment variables:
```
REACT_APP_FIREBASE_API_KEY=your-api-key
REACT_APP_FIREBASE_AUTH_DOMAIN=your-project.firebaseapp.com
REACT_APP_FIREBASE_PROJECT_ID=your-project-id
REACT_APP_FIREBASE_STORAGE_BUCKET=your-project.appspot.com
REACT_APP_FIREBASE_MESSAGING_SENDER_ID=123456789
REACT_APP_FIREBASE_APP_ID=your-app-id
```

## Option C: Vercel Deployment

### 7.1 Install Vercel CLI
```bash
npm install -g vercel
```

### 7.2 Deploy
```bash
# Build and deploy
npm run build
vercel --prod
```

---

## 🔧 Step 8: Environment Configuration

### 8.1 Create Environment Files

Create `.env.local`:
```env
REACT_APP_FIREBASE_API_KEY=your-api-key
REACT_APP_FIREBASE_AUTH_DOMAIN=your-project.firebaseapp.com
REACT_APP_FIREBASE_PROJECT_ID=your-project-id
REACT_APP_FIREBASE_STORAGE_BUCKET=your-project.appspot.com
REACT_APP_FIREBASE_MESSAGING_SENDER_ID=123456789
REACT_APP_FIREBASE_APP_ID=your-app-id
```

### 8.2 Update Firebase Config
```javascript
// src/firebase.js
const firebaseConfig = {
  apiKey: process.env.REACT_APP_FIREBASE_API_KEY,
  authDomain: process.env.REACT_APP_FIREBASE_AUTH_DOMAIN,
  projectId: process.env.REACT_APP_FIREBASE_PROJECT_ID,
  storageBucket: process.env.REACT_APP_FIREBASE_STORAGE_BUCKET,
  messagingSenderId: process.env.REACT_APP_FIREBASE_MESSAGING_SENDER_ID,
  appId: process.env.REACT_APP_FIREBASE_APP_ID
};
```

---

## 📱 Step 9: Progressive Web App (PWA) Setup

### 9.1 Enable PWA Features
```bash
# Install workbox
npm install --save workbox-webpack-plugin
```

### 9.2 Update public/manifest.json
```json
{
  "short_name": "Asset Tracker",
  "name": "SQ Asset Tracker",
  "icons": [
    {
      "src": "favicon.ico",
      "sizes": "64x64 32x32 24x24 16x16",
      "type": "image/x-icon"
    }
  ],
  "start_url": ".",
  "display": "standalone",
  "theme_color": "#000000",
  "background_color": "#ffffff"
}
```

---

## 🔍 Step 10: Monitoring & Analytics

### 10.1 Enable Firebase Analytics
```javascript
// src/firebase.js
import { getAnalytics } from "firebase/analytics";

const analytics = getAnalytics(app);
export { analytics };
```

### 10.2 Add Performance Monitoring
```bash
npm install firebase
```

```javascript
// src/firebase.js
import { getPerformance } from 'firebase/performance';

const perf = getPerformance(app);
```

---

## 🛠️ Step 11: Maintenance & Updates

### 11.1 Regular Updates
```bash
# Update dependencies monthly
npm update

# Check for security vulnerabilities
npm audit
npm audit fix
```

### 11.2 Backup Strategy
- Firebase automatically backs up your data
- Export Firestore data periodically:
  ```bash
  gcloud firestore export gs://your-bucket-name
  ```

### 11.3 Monitoring Checklist
- [ ] Monitor Firebase usage quotas
- [ ] Check application performance
- [ ] Review security rules
- [ ] Update dependencies
- [ ] Test critical features monthly

---

## 🆘 Troubleshooting Guide

### Common Issues:

**Firebase Connection Error:**
- Check internet connection
- Verify Firebase configuration
- Check Firebase project status

**Authentication Failed:**
- Verify email/password in Firebase Console
- Check Authentication provider settings
- Clear browser cache

**Image Upload Failed:**
- Check Firebase Storage rules
- Verify file size limits
- Check network connectivity

**Build Errors:**
- Clear node_modules: `rm -rf node_modules && npm install`
- Clear build cache: `rm -rf build`
- Check for missing dependencies

---

## 📞 Support & Resources

- **Firebase Documentation:** https://firebase.google.com/docs
- **React Documentation:** https://reactjs.org/docs
- **GitHub Issues:** Create issues for bugs/features
- **Community Support:** Stack Overflow, Discord

---

## ✅ Deployment Completion Checklist

- [ ] Firebase project created and configured
- [ ] Authentication enabled and admin user created
- [ ] Firestore database set up with security rules
- [ ] Storage configured with proper rules
- [ ] Application code deployed
- [ ] Environment variables configured
- [ ] Security rules implemented
- [ ] Testing completed
- [ ] Monitoring set up
- [ ] Backup strategy in place
- [ ] Documentation updated

**🎉 Congratulations! Your SQ Asset Tracker is now live and ready for production use!**
