# 🏦 Saudi Bank Transaction Parser API

<div align="center">

![Google I/O Extended](https://img.shields.io/badge/Google%20I%2FO-Extended-4285F4?style=for-the-badge&logo=google&logoColor=white)

*Built with Firebase Cloud Functions for parsing Saudi bank transaction messages*

[![Firebase](https://img.shields.io/badge/Firebase-Functions-FFA000?style=flat-square&logo=firebase)](https://firebase.google.com/products/functions)
[![TypeScript](https://img.shields.io/badge/TypeScript-4.9-3178C6?style=flat-square&logo=typescript)](https://www.typescriptlang.org/)
[![Express](https://img.shields.io/badge/Express-4.18-000000?style=flat-square&logo=express)](https://expressjs.com/)
[![Node.js](https://img.shields.io/badge/Node.js-18+-339933?style=flat-square&logo=node.js)](https://nodejs.org/)

</div>

---

## 🎯 Overview

A comprehensive **Saudi Bank Transaction Parser API** built with Firebase Cloud Functions that intelligently parses transaction messages from all major Saudi Arabian banks and financial institutions. This API automatically extracts transaction details, categorizes expenses, detects recurring payments, and stores transaction history in Firestore.

### ✨ Key Features

- 🏦 **Multi-Bank Support**: Supports 15+ Saudi banks (Al Rajhi, NCB/SNB, Riyad Bank, SAMBA, SAIB, etc.)
- 💳 **Payment Methods**: MADA, VISA, Mastercard, Apple Pay, Samsung Pay, STC Pay, Mobily Pay
- 📊 **Auto-Categorization**: Intelligent expense categorization using rule-based ML
- 🔄 **Recurrence Detection**: Identifies subscription and recurring payments
- 📱 **BNPL Support**: Tamara and Tabby Buy Now Pay Later integration
- 🔍 **Merchant Normalization**: Cleans and standardizes merchant names
- 📈 **Batch Processing**: Handle multiple transactions simultaneously
- 🗄️ **Firestore Integration**: Automatic transaction history storage
- ⚡ **High Performance**: Optimized for Middle East region (me-central1)

---

## 🏛️ Supported Financial Institutions

### 🏦 Traditional Banks
- **الراجحي** (Al Rajhi Bank) - Most popular Saudi bank
- **البنك الأهلي السعودي** (Saudi National Bank - SNB/NCB)
- **بنك الرياض** (Riyad Bank)
- **سامبا** (SAMBA Financial Group)
- **البنك السعودي للاستثمار** (Saudi Investment Bank - SAIB)
- **البنك السعودي الفرنسي** (Banque Saudi Fransi - BSF)
- **البنك العربي الوطني** (Arab National Bank - ANB)
- **البنك السعودي البريطاني** (Saudi British Bank - SABB)
- **بنك الجزيرة** (Bank AlJazira)
- **بنك البلاد** (Bank Albilad)
- **بنك أبوظبي الأول** (First Abu Dhabi Bank - FAB)
- **بنك الإنماء** (Alinma Bank)

### 💳 Digital Wallets & Payment Systems
- **STC Pay** (stc pay)
- **Mobily Pay** (موبايلي باي)
- **Zain Pay** (زين باي)
- **Apple Pay** (آبل باي)
- **Samsung Pay** (سامسونج باي)
- **MADA** (مدى) - Saudi national payment system

### 🛒 Buy Now Pay Later (BNPL)
- **Tamara** - Installment payments
- **Tabby** - Split payments

---

## 🚀 Quick Start

### Prerequisites

```bash
# Required versions
Node.js >= 18.0.0
Firebase CLI >= 12.0.0
```

### 1. Installation

```bash
# Clone the repository
git clone <your-repo-url>
cd saudi-bank-parser

# Install Firebase CLI globally
npm install -g firebase-tools

# Login to Firebase
firebase login

# Install dependencies
cd functions
npm install
```

### 2. Firebase Setup

```bash
# Initialize Firebase project
firebase init functions

# Select:
# ✅ Use existing project
# ✅ TypeScript
# ✅ Use ESLint
# ✅ Install dependencies now
```

### 3. Enable Firestore

1. Go to [Firebase Console](https://console.firebase.google.com)
2. Navigate to **Cloud Firestore**
3. Click **Create database**
4. Choose **me-central1** region (recommended for Saudi Arabia)
5. Start in **Test mode** for development

### 4. Local Development

```bash
# Build the project
npm run build

# Start local emulator
npm run serve

# Your API will be available at:
# http://localhost:5001/YOUR_PROJECT/me-central1/api/
```

### 5. Deploy to Production

```bash
# Deploy functions
firebase deploy --only functions

# Your production API will be available at:
# https://me-central1-YOUR_PROJECT.cloudfunctions.net/api/
```

---

## 📖 API Documentation

### Base URL
```
Production: https://me-central1-YOUR_PROJECT.cloudfunctions.net/api/
Development: http://localhost:5001/YOUR_PROJECT/me-central1/api/
```

### 🔍 Health Check
```http
GET /transactions/health
```

**Response:**
```json
{
  "success": true,
  "message": "Saudi Bank Transaction Parser API is running",
  "version": "1.0.0",
  "timestamp": "2025-08-03T12:00:00.000Z"
}
```

### 📝 Parse Single Transaction
```http
POST /transactions/parse
Content-Type: application/json
```

**Request Body:**
```json
{
  "userId": "user123", // Optional
  "transaction": "شراء إنترنت\nبـ 21.99 SAR\nمن Spotify AB P3781C3C72\nمدى 3180*\nحساب 0165*\nفي08-06-25",
  "historicalTransactions": [] // Optional for recurrence detection
}
```

**Response:**
```json
{
  "success": true,
  "data": {
    "description": "شراء إنترنت",
    "amount": 21.99,
    "currency": "SAR",
    "merchant": "Spotify",
    "accountMasked": "3180* / 0165*",
    "date": "2025-06-08",
    "category": "Subscriptions",
    "recurrence": {
      "isRecurring": true,
      "period": "monthly",
      "confidence": 0.9
    },
    "rawText": "شراء إنترنت\nبـ 21.99 SAR\nمن Spotify AB P3781C3C72\nمدى 3180* \nحساب 0165*\nفي08-06-25",
    "bankFormat": "generic"
  },
  "metadata": {
    "version": "1.0.0",
    "timestamp": "2025-08-03T12:00:00.000Z",
    "processingTimeMs": 45
  }
}
```

### 📦 Batch Processing
```http
POST /transactions/parse/batch
Content-Type: application/json
```

**Request Body:**
```json
{
  "userId": "user123", // Optional
  "transactions": [
    "شراء إنترنت\nبـ 21.99 SAR\nمن Spotify AB",
    "دفع فاتورة\nبـ 150.00 SAR\nمن STC"
  ]
}
```

### 📊 Get Categories
```http
GET /transactions/categories
```

### 🏪 Get Merchant Patterns
```http
GET /transactions/merchants
```

### 📈 Get User Transaction History
```http
GET /transactions/users/{userId}/transactions?limit=50&offset=0&category=Subscriptions
```

---

## 🛠️ Configuration

### Adding New Bank Patterns

```typescript
// In src/routes/transactions/const.ts
export const BANK_PATTERNS = {
  // Add your new bank pattern
  newbank: {
    description: /^([^\n]+)/,
    amount: /بـ\s*([\d,\.]+)\s*([A-Z]{3})/,
    merchant: /من\s+([^\n]+)/,
    card: /بطاقة\s*(\d+\*)/,
    account: /حساب\s*(\d+\*)/,
    date: /في\s*(\d{2}-\d{2}-\d{1,2})/,
  }
};
```

### Adding New Categories

```typescript
// Add to CATEGORY_RULES array
{
  keywords: ["كلمات", "مفتاحية", "keywords"],
  category: "New Category",
  priority: 80
}
```

### Adding Merchant Patterns

```typescript
// Add to MERCHANT_PATTERNS array
{
  pattern: /merchant_pattern/i,
  normalizedName: "Clean Merchant Name",
  category: "Category"
}
```

---

## 🏗️ Project Structure

```
functions/
├── src/
│   ├── index.ts                 # Main Express app & Firebase function export
│   └── routes/
│       ├── index.ts             # Route aggregator
│       └── transactions/
│           ├── index.ts         # Transaction API endpoints
│           ├── types.ts         # TypeScript interfaces
│           ├── const.ts         # Bank patterns & categorization rules
│           └── utils.ts         # Core parsing logic
├── package.json
└── tsconfig.json
```

---

## 🔧 Advanced Features

### Recurrence Detection

The API includes sophisticated recurrence detection:

- **Pattern Analysis**: Detects monthly, weekly, yearly patterns
- **Amount Matching**: Handles slight variations in recurring amounts
- **Confidence Scoring**: Provides reliability scores for predictions
- **Historical Analysis**: Uses transaction history for better accuracy

```typescript
// Example recurrence detection result
{
  "isRecurring": true,
  "period": "monthly",
  "confidence": 0.95
}
```

### Intelligent Categorization

Rule-based categorization system:

- **Priority-based**: Higher priority rules checked first
- **Keyword Matching**: Arabic and English keyword support
- **Merchant-based**: Specific merchant category overrides
- **Extensible**: Easy to add new categories and rules

### Multi-Language Support

- **Arabic**: Native support for Arabic transaction texts
- **English**: International card and digital wallet transactions
- **Mixed**: Handles transactions with both Arabic and English text

---

## 🔐 Security & Production Setup

### Environment Variables

```bash
# Firebase Configuration (automatically handled)
GOOGLE_APPLICATION_CREDENTIALS=path/to/service-account.json
FIREBASE_PROJECT_ID=your-project-id
```

### Firestore Security Rules

```javascript
// firestore.rules
rules_version = '2';
service cloud.firestore {
  match /databases/{database}/documents {
    // User transactions
    match /users/{userId}/transactions/{transactionId} {
      allow read, write: if request.auth != null && request.auth.uid == userId;
    }
  }
}
```

### Rate Limiting

Built-in rate limiting is configured for production use:

```typescript
// In src/index.ts - Already configured
app.use(
  rateLimit({
    windowMs: 15 * 60 * 1000, // 15 minutes
    max: 100 // limit each IP to 100 requests per windowMs
  })
);
```

---

## 📊 Performance Optimization

### Memory & Timeout Configuration

```typescript
// Optimized for Middle East region
export const api = onRequest(
  {
    timeoutSeconds: 300,
    region: "me-central1",
    memory: "1GiB",
    minInstances: 1, // Keeps function warm
    concurrency: 80
  },
  app
);
```

### Batch Processing Limits

- **Maximum batch size**: 50 transactions per request
- **Processing timeout**: 300 seconds
- **Memory allocation**: 1GiB for complex parsing operations

---

## 🧪 Testing

### Sample Transaction Formats

**Al Rajhi Bank:**
```
عملية شراء
بقيمة 99.99 SAR
من Amazon.sa P12345
بطاقة 1234*
حساب 5678*
بتاريخ 03-08-25
```

**STC Pay:**
```
تحويل مبلغ
بقيمة 50.00 SAR
إلى محمد أحمد
محفظة 9876*
في 03-08-2025
الساعة 14:30
```

**Apple Pay:**
```
Purchase
Amount 129.00 SAR
From Jarir Bookstore
Apple Pay iPhone
Card 4567*
on 03-08-2025
```

### Running Tests

```bash
# Install test dependencies
npm install --save-dev jest @types/jest

# Run tests
npm test

# Run with coverage
npm run test:coverage
```

---

## 🤝 Contributing

We welcome contributions! Please follow these steps:

1. **Fork** the repository
2. **Create** a feature branch (`git checkout -b feature/amazing-feature`)
3. **Add** your bank patterns or improvements
4. **Test** thoroughly with real transaction data
5. **Commit** your changes (`git commit -m 'Add amazing feature'`)
6. **Push** to the branch (`git push origin feature/amazing-feature`)
7. **Open** a Pull Request

### Adding New Banks

When adding support for a new bank:

1. Add the bank pattern to `const.ts`
2. Test with at least 10 real transaction samples
3. Update the documentation
4. Add the bank to the supported institutions list

---

## 📝 License

This project is licensed under the **MIT License** - see the [LICENSE](LICENSE) file for details.

---

## 🌟 Acknowledgments

- **Google I/O Extended** community for inspiration and support
- **Firebase** team for excellent Cloud Functions platform
- **Saudi Arabian Monetary Authority** for standardized payment systems
- **Open source contributors** who make projects like this possible

---

## 📧 Support & Contact

- **Issues**: [GitHub Issues](https://github.com/your-username/saudi-bank-parser/issues)
- **Discussions**: [GitHub Discussions](https://github.com/your-username/saudi-bank-parser/discussions)
- **Email**: your-email@example.com

---

<div align="center">

**Built with ❤️ in Saudi Arabia 🇸🇦**

*Presented at Google I/O Extended Events*

[![Made with Firebase](https://img.shields.io/badge/Made%20with-Firebase-orange?style=flat-square&logo=firebase)](https://firebase.google.com/)
[![Deployed on Cloud Functions](https://img.shields.io/badge/Deployed%20on-Cloud%20Functions-blue?style=flat-square&logo=google-cloud)](https://cloud.google.com/functions)

</div>