# Calculator with Hidden Messaging App

A React Native Expo app that appears as a calculator but contains a hidden messaging app with SMS/calling via SignalWire.

## Quick Start

```bash
npx create-expo-app my-app
cd my-app
npm install
npx expo start
```

## Dependencies

```bash
npm install @expo/vector-icons @nkzw/create-context-hook @react-native-async-storage/async-storage @rork-ai/toolkit-sdk @tanstack/react-query expo-auth-session expo-camera expo-contacts expo-document-picker expo-image-picker expo-location expo-router expo-web-browser lucide-react-native react-native-gesture-handler react-native-maps react-native-safe-area-context react-native-screens
```

## File: package.json

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
    "@tanstack/react-query": "^5.83.0",
    "expo": "~54.0.27",
    "expo-camera": "~17.0.10",
    "expo-contacts": "~15.0.11",
    "expo-document-picker": "~14.0.8",
    "expo-image-picker": "~17.0.10",
    "expo-location": "~19.0.8",
    "expo-router": "~6.0.17",
    "lucide-react-native": "^0.475.0",
    "react": "19.1.0",
    "react-native": "0.81.5",
    "react-native-maps": "1.20.1",
    "react-native-web": "^0.21.0"
  },
  "devDependencies": {
    "@babel/core": "^7.25.2",
    "@types/react": "~19.1.10",
    "typescript": "~5.9.2"
  }
}
```

## File: app.json

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
    "ios": {
      "supportsTablet": false,
      "bundleIdentifier": "com.yourcompany.calculator",
      "infoPlist": {
        "NSContactsUsageDescription": "Allow app to access your contacts"
      }
    },
    "android": {
      "package": "com.yourcompany.calculator",
      "permissions": ["CAMERA", "READ_CONTACTS"]
    },
    "plugins": ["expo-router"]
  }
}
```

## File: tsconfig.json

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

## File: app/_layout.tsx

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

## File: constants/colors.ts

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

## File: services/twilio.ts

```typescript
const SIGNALWIRE_SPACE_URL = 'YOUR_SPACE_URL.signalwire.com';
const SIGNALWIRE_PROJECT_ID = 'YOUR_PROJECT_ID';
const SIGNALWIRE_API_TOKEN = 'YOUR_API_TOKEN';
const SIGNALWIRE_PHONE_NUMBER = '+1XXXXXXXXXX';

function formatPhoneNumber(phone: string): string {
  let cleaned = phone.replace(/\D/g, '');
  if (cleaned.length === 10) return `+1${cleaned}`;
  if (cleaned.length === 11 && cleaned.startsWith('1')) return `+${cleaned}`;
  if (phone.startsWith('+')) return phone;
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
        return { success: false, error: 'SignalWire not configured' };
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
        return { success: true, sid: data.sid };
      }
      return { success: false, error: data.message || 'Failed to send SMS' };
    } catch (error) {
      return { success: false, error: error instanceof Error ? error.message : 'Unknown error' };
    }
  },

  async makeCall(to: string): Promise<{ success: boolean; sid?: string; error?: string }> {
    try {
      const formattedTo = formatPhoneNumber(to);
      
      if (!SIGNALWIRE_PROJECT_ID || !SIGNALWIRE_API_TOKEN || !SIGNALWIRE_PHONE_NUMBER) {
        return { success: false, error: 'SignalWire not configured' };
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
        return { success: true, sid: data.sid };
      }
      return { success: false, error: data.message || 'Failed to make call' };
    } catch (error) {
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
        headers: { 'Authorization': getAuthHeader() },
      });

      const data = await response.json();
      if (response.ok) {
        const allMessages = data.messages || [];
        const incomingMessages = allMessages.filter((msg: any) => {
          const isInbound = msg.direction === 'inbound';
          const messageDateSent = new Date(msg.date_sent);
          const isAfterLastCheck = !lastCheckTime || messageDateSent > lastCheckTime;
          return isInbound && isAfterLastCheck;
        });
        return { success: true, messages: incomingMessages };
      }
      return { success: false, error: data.message || 'Failed to fetch messages' };
    } catch (error) {
      return { success: false, error: error instanceof Error ? error.message : 'Unknown error' };
    }
  },
};
```

## File: app/index.tsx

**Note:** The main app file (app/index.tsx) is 5191 lines long. It's available in your project directory.

**Key Features:**
- Calculator interface (disguise)
- Hidden messaging app (unlock with 69420)
- Phone/SMS via SignalWire
- AI chat assistant
- Contact management
- Location sharing
- Camera integration
- Developer panel

## Setup SignalWire

1. Go to https://signalwire.com/signup
2. Create free account ($5 credit included)
3. Get credentials from dashboard
4. Buy phone number ($1/month)
5. Update services/twilio.ts with your credentials

## How to Use

1. Open app (shows calculator)
2. Type 69420 and press = to unlock hidden app
3. Navigate using bottom tabs
4. Send SMS via Phone tab
5. Chat with AI assistant

## Default Login

- Email: cruzdev493@gmail.com
- Password: Hs4933hs

## Contact

harrishayden0809@gmail.com

---

For the complete app/index.tsx file, see your project directory or contact for full code.