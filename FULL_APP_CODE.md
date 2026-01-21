# Calculator with Hidden Messaging App - Complete Code

## 📋 Project Overview
This is a React Native Expo app that appears as a calculator but contains a hidden messaging app with phone/SMS capabilities via SignalWire.

---

## 📦 Package Dependencies

### package.json
```json
{
  "name": "expo-app",
  "main": "expo-router/entry",
  "version": "1.0.0",
  "scripts": {
    "start": "expo start",
    "android": "expo start --android",
    "ios": "expo start --ios",
    "web": "expo start --web"
  },
  "dependencies": {
    "@expo/vector-icons": "^15.0.3",
    "@nkzw/create-context-hook": "^1.1.0",
    "@react-native-async-storage/async-storage": "2.2.0",
    "@rork-ai/toolkit-sdk": "^0.2.51",
    "@stardazed/streams-text-encoding": "^1.0.2",
    "@tanstack/react-query": "^5.83.0",
    "@ungap/structured-clone": "^1.3.0",
    "expo": "~54.0.27",
    "expo-auth-session": "~7.0.10",
    "expo-blur": "~15.0.8",
    "expo-camera": "~17.0.10",
    "expo-constants": "~18.0.11",
    "expo-contacts": "~15.0.11",
    "expo-document-picker": "~14.0.8",
    "expo-font": "~14.0.10",
    "expo-haptics": "~15.0.8",
    "expo-image": "~3.0.11",
    "expo-image-picker": "~17.0.10",
    "expo-linear-gradient": "~15.0.8",
    "expo-linking": "~8.0.10",
    "expo-location": "~19.0.8",
    "expo-router": "~6.0.17",
    "expo-splash-screen": "~31.0.12",
    "expo-status-bar": "~3.0.9",
    "expo-symbols": "~1.0.8",
    "expo-system-ui": "~6.0.9",
    "expo-web-browser": "~15.0.10",
    "lucide-react-native": "^0.475.0",
    "react": "19.1.0",
    "react-dom": "19.1.0",
    "react-native": "0.81.5",
    "react-native-gesture-handler": "~2.28.0",
    "react-native-maps": "1.20.1",
    "react-native-safe-area-context": "~5.6.0",
    "react-native-screens": "~4.16.0",
    "react-native-svg": "15.12.1",
    "react-native-web": "^0.21.0",
    "react-native-worklets": "0.5.1",
    "zod": "^4.3.4",
    "zustand": "^5.0.2"
  },
  "devDependencies": {
    "@babel/core": "^7.25.2",
    "@expo/ngrok": "^4.1.0",
    "@types/react": "~19.1.10",
    "eslint": "^9.31.0",
    "eslint-config-expo": "~10.0.0",
    "typescript": "~5.9.2"
  },
  "private": true
}
```

---

## ⚙️ Configuration Files

### app.json
```json
{
  "expo": {
    "name": "Calculator with Hidden Message",
    "slug": "calculator-hidden-message",
    "version": "1.0.0",
    "orientation": "portrait",
    "icon": "./assets/images/icon.png",
    "scheme": "myapp",
    "userInterfaceStyle": "automatic",
    "newArchEnabled": true,
    "splash": {
      "image": "./assets/images/splash-icon.png",
      "resizeMode": "contain",
      "backgroundColor": "#ffffff"
    },
    "ios": {
      "supportsTablet": false,
      "bundleIdentifier": "com.yourcompany.calculator",
      "infoPlist": {
        "NSPhotoLibraryUsageDescription": "Allow app to access your photos",
        "NSCameraUsageDescription": "Allow app to access your camera",
        "NSMicrophoneUsageDescription": "Allow app to access your microphone",
        "NSContactsUsageDescription": "Allow app to access your contacts",
        "NSLocationWhenInUseUsageDescription": "Allow app to use your location"
      }
    },
    "android": {
      "adaptiveIcon": {
        "foregroundImage": "./assets/images/adaptive-icon.png",
        "backgroundColor": "#ffffff"
      },
      "package": "com.yourcompany.calculator",
      "permissions": [
        "CAMERA",
        "READ_EXTERNAL_STORAGE",
        "WRITE_EXTERNAL_STORAGE",
        "READ_CONTACTS",
        "ACCESS_FINE_LOCATION",
        "RECORD_AUDIO"
      ]
    },
    "web": {
      "favicon": "./assets/images/favicon.png"
    },
    "plugins": [
      "expo-router",
      "expo-font",
      "expo-web-browser",
      "expo-image-picker",
      "expo-document-picker",
      "expo-contacts",
      "expo-location",
      "expo-camera"
    ]
  }
}
```

### tsconfig.json
```json
{
  "extends": "expo/tsconfig.base",
  "compilerOptions": {
    "strict": true,
    "paths": {
      "@/*": ["./*"]
    }
  }
}
```

---

## 🗂️ File Structure
```
project/
├── app/
│   ├── _layout.tsx
│   └── index.tsx
├── services/
│   └── twilio.ts
├── constants/
│   └── colors.ts
├── assets/
│   └── images/
├── package.json
├── app.json
└── tsconfig.json
```

---

## 📱 Main Application Files

### app/_layout.tsx
```typescript
import { QueryClient, QueryClientProvider } from "@tanstack/react-query";
import { Stack } from "expo-router";
import * as SplashScreen from "expo-splash-screen";
import React, { useEffect } from "react";
import { GestureHandlerRootView } from "react-native-gesture-handler";

SplashScreen.preventAutoHideAsync();

const queryClient = new QueryClient();

function RootLayoutNav() {
  return (
    <Stack screenOptions={{ headerBackTitle: "Back" }}>
      <Stack.Screen name="index" options={{ headerShown: false }} />
    </Stack>
  );
}

export default function RootLayout() {
  useEffect(() => {
    SplashScreen.hideAsync();
  }, []);

  return (
    <QueryClientProvider client={queryClient}>
      <GestureHandlerRootView>
        <RootLayoutNav />
      </GestureHandlerRootView>
    </QueryClientProvider>
  );
}
```

### constants/colors.ts
```typescript
const tintColorLight = "#2f95dc";

export default {
  light: {
    text: "#000",
    background: "#fff",
    tint: tintColorLight,
    tabIconDefault: "#ccc",
    tabIconSelected: tintColorLight,
  },
};
```

---

## 🔧 Services

### services/twilio.ts (SignalWire Integration)
```typescript
// SignalWire credentials - Sign up at https://signalwire.com for FREE $5 credit
// 1. Go to https://signalwire.com/signup
// 2. Create free account (no credit card needed for trial)
// 3. Get your Space URL, Project ID, and API Token from the dashboard
// 4. Buy a phone number for $1/month (you get $5 free credit)

const SIGNALWIRE_SPACE_URL = 'YOUR_SPACE_URL.signalwire.com';
const SIGNALWIRE_PROJECT_ID = 'YOUR_PROJECT_ID';
const SIGNALWIRE_API_TOKEN = 'YOUR_API_TOKEN';
const SIGNALWIRE_PHONE_NUMBER = '+1XXXXXXXXXX';

function formatPhoneNumber(phone: string): string {
  let cleaned = phone.replace(/\D/g, '');
  
  if (cleaned.length === 10) {
    return `+1${cleaned}`;
  }
  
  if (cleaned.length === 11 && cleaned.startsWith('1')) {
    return `+${cleaned}`;
  }
  
  if (phone.startsWith('+')) {
    return phone;
  }
  
  return `+${cleaned}`;
}

const SIGNALWIRE_API_BASE = `https://${SIGNALWIRE_SPACE_URL}/api/laml/2010-04-01/Accounts/${SIGNALWIRE_PROJECT_ID}`;

const getAuthHeader = () => {
  const credentials = `${SIGNALWIRE_PROJECT_ID}:${SIGNALWIRE_API_TOKEN}`;
  return `Basic ${btoa(credentials)}`;
};

export const signalWireService = {
  async sendSMS(to: string, message: string): Promise<{ success: boolean; sid?: string; error?: string }> {
    try {
      const formattedTo = formatPhoneNumber(to);
      console.log('Sending SMS via SignalWire');
      
      if (!SIGNALWIRE_PROJECT_ID || !SIGNALWIRE_API_TOKEN || !SIGNALWIRE_PHONE_NUMBER) {
        return { 
          success: false, 
          error: 'SignalWire not configured. Sign up free at https://signalwire.com/signup' 
        };
      }

      const formData = new URLSearchParams();
      formData.append('To', formattedTo);
      formData.append('From', SIGNALWIRE_PHONE_NUMBER);
      formData.append('Body', message);

      const response = await fetch(`${SIGNALWIRE_API_BASE}/Messages.json`, {
        method: 'POST',
        headers: {
          'Authorization': getAuthHeader(),
          'Content-Type': 'application/x-www-form-urlencoded',
        },
        body: formData.toString(),
      });

      const data = await response.json();

      if (response.ok) {
        console.log('✅ SMS sent successfully:', data.sid);
        return { success: true, sid: data.sid };
      } else {
        console.error('❌ SignalWire SMS error:', data);
        return { success: false, error: data.message || 'Failed to send SMS' };
      }
    } catch (error) {
      console.error('❌ SMS exception:', error);
      return { success: false, error: error instanceof Error ? error.message : 'Unknown error' };
    }
  },

  async makeCall(to: string): Promise<{ success: boolean; sid?: string; error?: string }> {
    try {
      const formattedTo = formatPhoneNumber(to);
      console.log('Making call via SignalWire');
      
      if (!SIGNALWIRE_PROJECT_ID || !SIGNALWIRE_API_TOKEN || !SIGNALWIRE_PHONE_NUMBER) {
        return { 
          success: false, 
          error: 'SignalWire not configured' 
        };
      }

      const formData = new URLSearchParams();
      formData.append('To', formattedTo);
      formData.append('From', SIGNALWIRE_PHONE_NUMBER);
      formData.append('Url', 'http://demo.twilio.com/docs/voice.xml');

      const response = await fetch(`${SIGNALWIRE_API_BASE}/Calls.json`, {
        method: 'POST',
        headers: {
          'Authorization': getAuthHeader(),
          'Content-Type': 'application/x-www-form-urlencoded',
        },
        body: formData.toString(),
      });

      const data = await response.json();

      if (response.ok) {
        console.log('✅ Call initiated successfully:', data.sid);
        return { success: true, sid: data.sid };
      } else {
        console.error('❌ SignalWire call error:', data);
        return { success: false, error: data.message || 'Failed to make call' };
      }
    } catch (error) {
      console.error('Call error:', error);
      return { success: false, error: error instanceof Error ? error.message : 'Unknown error' };
    }
  },

  async endCall(callSid: string): Promise<{ success: boolean; error?: string }> {
    try {
      if (!SIGNALWIRE_PROJECT_ID || !SIGNALWIRE_API_TOKEN) {
        return { success: false, error: 'SignalWire not configured' };
      }

      const formData = new URLSearchParams();
      formData.append('Status', 'completed');

      const response = await fetch(`${SIGNALWIRE_API_BASE}/Calls/${callSid}.json`, {
        method: 'POST',
        headers: {
          'Authorization': getAuthHeader(),
          'Content-Type': 'application/x-www-form-urlencoded',
        },
        body: formData.toString(),
      });

      if (response.ok) {
        console.log('✅ Call ended successfully');
        return { success: true };
      } else {
        const data = await response.json();
        return { success: false, error: data.message || 'Failed to end call' };
      }
    } catch (error) {
      console.error('End call error:', error);
      return { success: false, error: error instanceof Error ? error.message : 'Unknown error' };
    }
  },

  async getMessages(lastCheckTime?: Date): Promise<{ success: boolean; messages?: any[]; error?: string }> {
    try {
      if (!SIGNALWIRE_PROJECT_ID || !SIGNALWIRE_API_TOKEN || !SIGNALWIRE_PHONE_NUMBER) {
        return { success: false, error: 'SignalWire not configured' };
      }

      const params = new URLSearchParams();
      params.append('To', SIGNALWIRE_PHONE_NUMBER);
      params.append('PageSize', '100');

      const url = `${SIGNALWIRE_API_BASE}/Messages.json?${params.toString()}`;

      const response = await fetch(url, {
        method: 'GET',
        headers: {
          'Authorization': getAuthHeader(),
        },
      });

      const data = await response.json();

      if (response.ok) {
        const allMessages = data.messages || [];
        
        const incomingMessages = allMessages.filter((msg: any) => {
          const isInbound = msg.direction === 'inbound';
          const isToMyNumber = msg.to === SIGNALWIRE_PHONE_NUMBER;
          const messageDateSent = new Date(msg.date_sent);
          const isAfterLastCheck = !lastCheckTime || messageDateSent > lastCheckTime;
          
          return isInbound && isToMyNumber && isAfterLastCheck;
        });
        
        return { success: true, messages: incomingMessages };
      } else {
        console.error('Failed to fetch messages:', data);
        return { success: false, error: data.message || 'Failed to fetch messages' };
      }
    } catch (error) {
      console.error('Get messages error:', error);
      return { success: false, error: error instanceof Error ? error.message : 'Unknown error' };
    }
  },
};
```

---

## 🚀 Main App Component

### app/index.tsx
**Note**: This file is 5191 lines. Due to length, the complete code is available in your project at `app/index.tsx`.

Key features include:
- Calculator interface (disguise)
- Hidden messaging app (unlock with 69420)
- Phone/SMS functionality via SignalWire
- AI chat assistant
- Contact management
- Location sharing
- Camera integration
- User authentication with Google Sign-In
- Developer panel

---

## 🛠️ Setup Instructions

### 1. Prerequisites
```bash
# Install Node.js (v18 or higher)
# Install npm or yarn
```

### 2. Create New Expo Project
```bash
npx create-expo-app my-calculator-app
cd my-calculator-app
```

### 3. Install Dependencies
```bash
npm install @expo/vector-icons @nkzw/create-context-hook @react-native-async-storage/async-storage @rork-ai/toolkit-sdk @tanstack/react-query expo-auth-session expo-camera expo-contacts expo-document-picker expo-image-picker expo-location expo-router expo-web-browser lucide-react-native react-native-gesture-handler react-native-maps react-native-safe-area-context react-native-screens
```

### 4. Copy Files
1. Copy all the code from this document into the respective files
2. Create the folder structure as shown above
3. Replace placeholder values in `services/twilio.ts` with your SignalWire credentials

### 5. SignalWire Setup
1. Go to https://signalwire.com/signup
2. Create a free account ($5 credit, no credit card needed)
3. Get your credentials from the dashboard:
   - Space URL
   - Project ID
   - API Token
4. Buy a phone number ($1/month)
5. Update `services/twilio.ts` with your credentials

### 6. Run the App
```bash
# Run on iOS
npx expo start --ios

# Run on Android
npx expo start --android

# Run on Web
npx expo start --web
```

---

## 🔐 Default Login Credentials
- Email: `cruzdev493@gmail.com`
- Password: `Hs4933hs`

---

## 🎯 How to Use

1. **Calculator Mode**: The app opens as a normal calculator
2. **Unlock Hidden App**: Calculate `69420` (or just type it and press =)
3. **Navigate**: Use the bottom navigation bar
4. **Send SMS**: Go to Phone → Enter number → Send SMS
5. **Make Calls**: Enter number in dialer and press green call button
6. **AI Chat**: Chat with "Cruz's Helper" for assistance
7. **Profile**: Sign in to save your data and access features

---

## 📝 Important Notes

- Replace SignalWire credentials before deploying
- The app requires various permissions (Camera, Contacts, Location)
- Message polling runs every 5 seconds when in Phone/SMS mode
- Google Sign-In requires proper OAuth setup
- Developer panel is restricted to the default dev account

---

## 🐛 Troubleshooting

### Can't send SMS/make calls
- Check SignalWire credentials are correct
- Verify phone number is in E.164 format (+1XXXXXXXXXX)
- Check you have credits in your SignalWire account

### Permissions not working
- Check `app.json` has all required permissions
- On iOS, rebuild the app after adding permissions
- On Android, grant permissions manually in Settings

### App crashes on startup
- Clear cache: `npx expo start -c`
- Reinstall dependencies: `rm -rf node_modules && npm install`
- Check all imports are correct

---

## 📧 Support
For issues, contact: harrishayden0809@gmail.com

---

## 📄 License
This code is provided as-is for educational purposes.
