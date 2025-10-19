# eTax Mobile PWA - Architecture Documentation

## 🏗️ **SYSTEM ARCHITECTURE**

### **High-Level Overview**
```
┌─────────────────┐    ┌─────────────────┐    ┌─────────────────┐
│   User Device   │    │   Admin Panel   │    │  Firebase Cloud │
│                 │    │                 │    │                 │
│ ┌─────────────┐ │    │ ┌─────────────┐ │    │ ┌─────────────┐ │
│ │   Browser   │ │◄──►│ │   Browser   │ │◄──►│ │  Firestore  │ │
│ │             │ │    │ │             │ │    │ │  Database   │ │
│ │ localStorage│ │    │ │ localStorage│ │    │ │             │ │
│ │ sessionStore│ │    │ │ sessionStore│ │    │ │             │ │
│ └─────────────┘ │    │ └─────────────┘ │    │ └─────────────┘ │
└─────────────────┘    └─────────────────┘    └─────────────────┘
```

---

## 🔄 **DATA FLOW**

### **1. User Registration Flow**
```
Admin Panel → Create MST → Save to localStorage → Upload to Firestore → Broadcast to other tabs
```

### **2. User Login Flow**
```
Login Page → Load from Firestore → Sync to localStorage → Authenticate → Redirect to Index
```

### **3. Cross-Device Sync Flow**
```
Device A (Admin) → Firestore → Device B (User) → localStorage → UI Update
```

---

## 📱 **COMPONENT ARCHITECTURE**

### **Frontend Components**
```
┌─────────────────────────────────────────────────────────────┐
│                    eTax Mobile PWA                          │
├─────────────────────────────────────────────────────────────┤
│  ┌─────────────┐  ┌─────────────┐  ┌─────────────┐         │
│  │   Login     │  │    Admin    │  │    User     │         │
│  │   System    │  │    Panel    │  │   Pages     │         │
│  └─────────────┘  └─────────────┘  └─────────────┘         │
├─────────────────────────────────────────────────────────────┤
│  ┌─────────────┐  ┌─────────────┐  ┌─────────────┐         │
│  │   Theme     │  │    UI       │  │   Service   │         │
│  │  Manager    │  │  Manager    │  │   Worker    │         │
│  └─────────────┘  └─────────────┘  └─────────────┘         │
├─────────────────────────────────────────────────────────────┤
│  ┌─────────────┐  ┌─────────────┐  ┌─────────────┐         │
│  │   Auth      │  │   Storage   │  │   Sync      │         │
│  │   System    │  │   Manager   │  │   Manager   │         │
│  └─────────────┘  └─────────────┘  └─────────────┘         │
└─────────────────────────────────────────────────────────────┘
```

---

## 🔐 **AUTHENTICATION ARCHITECTURE**

### **Multi-Layer Authentication**
```
┌─────────────────────────────────────────────────────────────┐
│                    Authentication Layers                    │
├─────────────────────────────────────────────────────────────┤
│  Layer 1: Session Management (localStorage/sessionStorage)  │
│  Layer 2: MST + Password Validation                         │
│  Layer 3: Firestore Data Verification                       │
│  Layer 4: Admin Panel Access Control                       │
└─────────────────────────────────────────────────────────────┘
```

### **Session Flow**
```javascript
// Login Process
1. User enters MST + Password
2. Check localStorage for user data
3. If not found, load from Firestore
4. Validate credentials
5. Create session in localStorage
6. Redirect to appropriate page
```

---

## 💾 **DATA STORAGE ARCHITECTURE**

### **Dual Storage System**
```
┌─────────────────────────────────────────────────────────────┐
│                    Storage Architecture                      │
├─────────────────────────────────────────────────────────────┤
│  ┌─────────────┐  ┌─────────────┐  ┌─────────────┐         │
│  │ localStorage│  │ Firestore   │  │ sessionStore│         │
│  │             │  │             │  │             │         │
│  │ • User Data │  │ • User Data │  │ • Session   │         │
│  │ • Settings  │  │ • Real-time │  │ • Temp Data │         │
│  │ • Cache     │  │ • Cross-dev │  │ • State     │         │
│  └─────────────┘  └─────────────┘  └─────────────┘         │
└─────────────────────────────────────────────────────────────┘
```

### **Data Synchronization**
```javascript
// Sync Strategy
1. Write to localStorage (fast access)
2. Write to Firestore (cross-device sync)
3. Broadcast to other tabs (same-browser sync)
4. Handle conflicts with Firestore as source of truth
```

---

## 🌐 **DEPLOYMENT ARCHITECTURE**

### **Firebase Hosting**
```
┌─────────────────────────────────────────────────────────────┐
│                    Firebase Hosting                         │
├─────────────────────────────────────────────────────────────┤
│  ┌─────────────┐  ┌─────────────┐  ┌─────────────┐         │
│  │   CDN       │  │   SSL       │  │   Custom    │         │
│  │  Global     │  │   HTTPS     │  │   Domain    │         │
│  └─────────────┘  └─────────────┘  └─────────────┘         │
├─────────────────────────────────────────────────────────────┤
│  URL: https://anhbao-373f3.web.app                          │
│  Redirect: /* → /login.html                                 │
│  Status: ✅ Production Ready                                │
└─────────────────────────────────────────────────────────────┘
```

---

## 🔄 **SYNC ARCHITECTURE**

### **Real-time Synchronization**
```
┌─────────────────────────────────────────────────────────────┐
│                Synchronization Layers                       │
├─────────────────────────────────────────────────────────────┤
│  Layer 1: BroadcastChannel (Same Browser)                  │
│  Layer 2: Firestore (Cross Device)                         │
│  Layer 3: localStorage (Local Cache)                        │
│  Layer 4: Conflict Resolution (Firestore Wins)             │
└─────────────────────────────────────────────────────────────┘
```

### **Sync Flow**
```javascript
// Data Change Flow
1. User action in Admin Panel
2. Update localStorage
3. Upload to Firestore
4. BroadcastChannel notifies other tabs
5. Other devices receive Firestore update
6. Update local localStorage
7. Refresh UI
```

---

## 📱 **PWA ARCHITECTURE**

### **Progressive Web App Features**
```
┌─────────────────────────────────────────────────────────────┐
│                    PWA Architecture                         │
├─────────────────────────────────────────────────────────────┤
│  ┌─────────────┐  ┌─────────────┐  ┌─────────────┐         │
│  │   Service   │  │   Web App   │  │   Push      │         │
│  │   Worker    │  │   Manifest  │  │   Notif.    │         │
│  │             │  │             │  │             │         │
│  │ • Offline   │  │ • Install   │  │ • Real-time │         │
│  │ • Cache     │  │ • Icon      │  │ • Updates   │         │
│  │ • Sync      │  │ • Theme     │  │ • Alerts    │         │
│  └─────────────┘  └─────────────┘  └─────────────┘         │
└─────────────────────────────────────────────────────────────┘
```

---

## 🔧 **DEVELOPMENT ARCHITECTURE**

### **File Organization**
```
src/
├── pages/           # Main application pages
├── services/        # Business logic services
├── components/      # Reusable UI components
└── utils/          # Utility functions

css/
├── critical.min.css     # Critical above-the-fold CSS
├── etax-common.min.css # Common styles
└── [page-specific].css  # Page-specific styles

assets/
├── images/         # Static images
├── icons/          # App icons
└── fonts/          # Custom fonts
```

---

## 🚀 **SCALABILITY CONSIDERATIONS**

### **Current Limitations**
- Single Firebase project
- No user authentication system
- Limited offline capabilities
- No push notifications

### **Future Enhancements**
- Multi-tenant architecture
- User authentication with Firebase Auth
- Advanced offline sync
- Push notification system
- Mobile app store deployment

---

## 📊 **PERFORMANCE METRICS**

### **Current Performance**
- **First Load**: ~2-3 seconds
- **Subsequent Loads**: ~1 second (cached)
- **Sync Latency**: ~500ms (Firestore)
- **Offline Support**: Basic (Service Worker)

### **Optimization Targets**
- **First Load**: <2 seconds
- **Sync Latency**: <200ms
- **Offline Support**: Full functionality
- **Bundle Size**: <1MB

---

**Last Updated:** 2025-01-19  
**Architecture Version:** 1.0.0  
**Status:** ✅ Production Ready
