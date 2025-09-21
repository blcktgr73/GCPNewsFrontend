# GCP News Portal Frontend - Project Documentation

## 📖 목차
1. [프로젝트 개요](#프로젝트-개요)
2. [현재 아키텍처](#현재-아키텍처)
3. [Web 플랫폼 확장](#web-플랫폼-확장)
4. [Google Auth 연동 확장](#google-auth-연동-확장)
5. [구현 로드맵](#구현-로드맵)
6. [개발 가이드](#개발-가이드)

## 📱 프로젝트 개요

### 현재 상태
- **플랫폼**: React Native (Mobile)
- **프레임워크**: Expo Router v4.0.21
- **인증**: Firebase Email/Password Auth
- **상태관리**: Zustand
- **주요 기능**: 키워드 기반 뉴스 요약 포털

### 핵심 기능
1. **사용자 인증**: 이메일/비밀번호 기반 회원가입/로그인
2. **키워드 관리**: 뉴스 키워드 추가/삭제
3. **뉴스 요약**: 키워드별 뉴스 요약 조회 (페이지네이션)
4. **다크/라이트 모드**: 자동 테마 지원

## 🏗️ 현재 아키텍처

### 디렉토리 구조
```
GCPNewsPortalFrontend/
├── app/                    # Expo Router 페이지
│   ├── _layout.tsx        # 루트 레이아웃
│   ├── index.tsx          # 로그인/회원가입
│   ├── summary.tsx        # 뉴스 요약 목록
│   └── keyword.tsx        # 키워드 관리
├── src/
│   └── firebaseConfig.ts  # Firebase 설정
├── components/            # 재사용 컴포넌트
├── assets/               # 이미지, 폰트 등
└── constants/            # 상수 정의
```

### 기술 스택
```json
{
  "core": {
    "expo": "~52.0.47",
    "react-native": "0.76.9",
    "expo-router": "~4.0.21"
  },
  "auth": {
    "firebase": "^11.9.1"
  },
  "state": {
    "zustand": "^5.0.6"
  },
  "http": {
    "axios": "^1.10.0"
  },
  "development": {
    "typescript": "~5.3.3",
    "jest": "^29.2.1"
  }
}
```

## 🌐 Web 플랫폼 확장

### 1. 현재 Web 지원 상태
Expo는 기본적으로 Web을 지원하지만 다음 최적화가 필요합니다:

#### 설정 검토
```typescript
// app.config.ts - 이미 웹 설정 포함
web: {
  favicon: './assets/images/favicon.png',
}
```

### 2. Web 최적화 구현 계획

#### 2.1 반응형 디자인
```typescript
// components/ResponsiveLayout.tsx (신규)
import { useWindowDimensions } from 'react-native';

export const useResponsiveLayout = () => {
  const { width } = useWindowDimensions();
  
  return {
    isMobile: width < 768,
    isTablet: width >= 768 && width < 1024,
    isDesktop: width >= 1024,
  };
};
```

#### 2.2 웹 전용 컴포넌트
```typescript
// components/WebNavigation.tsx (신규)
import { Platform } from 'react-native';

export const Navigation = () => {
  if (Platform.OS === 'web') {
    return <WebSidebar />; // 데스크톱 사이드바
  }
  return null; // 모바일은 기존 네비게이션 사용
};
```

#### 2.3 SEO 최적화
```typescript
// app/+html.tsx - 이미 존재, 확장 필요
export default function Root({ children }: { children: React.ReactNode }) {
  return (
    <html lang="ko">
      <head>
        <meta charSet="utf-8" />
        <meta httpEquiv="X-UA-Compatible" content="IE=edge" />
        <meta name="viewport" content="width=device-width, initial-scale=1" />
        
        {/* SEO 메타 태그 추가 */}
        <title>GCP 뉴스 포털 - 키워드별 뉴스 요약</title>
        <meta name="description" content="키워드 기반 뉴스 요약 서비스" />
        <meta name="keywords" content="뉴스, 요약, 키워드, AI" />
        
        {/* PWA 지원 */}
        <link rel="manifest" href="/manifest.json" />
        <meta name="theme-color" content="#000000" />
      </head>
      <body>{children}</body>
    </html>
  );
}
```

### 3. 웹 전용 기능 추가

#### 3.1 키보드 단축키
```typescript
// hooks/useKeyboardShortcuts.ts (신규)
import { useEffect } from 'react';
import { Platform } from 'react-native';

export const useKeyboardShortcuts = () => {
  useEffect(() => {
    if (Platform.OS !== 'web') return;
    
    const handleKeyPress = (e: KeyboardEvent) => {
      // Ctrl+K: 키워드 검색
      if (e.ctrlKey && e.key === 'k') {
        e.preventDefault();
        // 키워드 입력 포커스
      }
      // F5: 새로고침
      if (e.key === 'F5') {
        // 데이터 리프레시
      }
    };
    
    window.addEventListener('keydown', handleKeyPress);
    return () => window.removeEventListener('keydown', handleKeyPress);
  }, []);
};
```

#### 3.2 브라우저 히스토리 관리
```typescript
// hooks/useBrowserHistory.ts (신규)
import { useRouter } from 'expo-router';
import { Platform } from 'react-native';

export const useBrowserHistory = () => {
  const router = useRouter();
  
  const pushState = (path: string, title?: string) => {
    if (Platform.OS === 'web' && window.history) {
      window.history.pushState(null, title || '', path);
      document.title = title || 'GCP 뉴스 포털';
    }
    router.push(path);
  };
  
  return { pushState };
};
```

## 🔐 Google Auth 연동 확장

### 1. 현재 인증 구조
```typescript
// src/firebaseConfig.ts - 현재 이메일/비밀번호만 지원
const auth = initializeAuth(app, {
  persistence: getReactNativePersistence(AsyncStorage),
});
```

### 2. Google Auth 확장 구현

#### 2.1 Firebase 설정 업데이트
```typescript
// src/firebaseConfig.ts - 확장
import { 
  initializeAuth, 
  getReactNativePersistence,
  GoogleAuthProvider 
} from 'firebase/auth';

// Google Auth Provider 설정
export const googleProvider = new GoogleAuthProvider();
googleProvider.addScope('email');
googleProvider.addScope('profile');
```

#### 2.2 플랫폼별 Google Auth 구현

##### Mobile (React Native)
```typescript
// hooks/useGoogleAuth.native.ts (신규)
import { GoogleSignin } from '@react-native-google-signin/google-signin';
import { signInWithCredential, GoogleAuthProvider } from 'firebase/auth';
import { auth } from '../src/firebaseConfig';

// Google Sign-In 설정
GoogleSignin.configure({
  webClientId: 'YOUR_WEB_CLIENT_ID',
});

export const useGoogleAuth = () => {
  const signInWithGoogle = async () => {
    try {
      await GoogleSignin.hasPlayServices();
      const { idToken } = await GoogleSignin.signIn();
      
      const googleCredential = GoogleAuthProvider.credential(idToken);
      return await signInWithCredential(auth, googleCredential);
    } catch (error) {
      throw error;
    }
  };
  
  return { signInWithGoogle };
};
```

##### Web
```typescript
// hooks/useGoogleAuth.web.ts (신규)
import { signInWithPopup } from 'firebase/auth';
import { auth, googleProvider } from '../src/firebaseConfig';

export const useGoogleAuth = () => {
  const signInWithGoogle = async () => {
    try {
      return await signInWithPopup(auth, googleProvider);
    } catch (error) {
      throw error;
    }
  };
  
  return { signInWithGoogle };
};
```

#### 2.3 통합 로그인 컴포넌트
```typescript
// components/AuthButtons.tsx (신규)
import React from 'react';
import { View, Button, Platform } from 'react-native';
import { useGoogleAuth } from '../hooks/useGoogleAuth';

export const AuthButtons = ({ onSuccess }: { onSuccess: () => void }) => {
  const { signInWithGoogle } = useGoogleAuth();
  
  const handleGoogleSignIn = async () => {
    try {
      await signInWithGoogle();
      onSuccess();
    } catch (error) {
      console.error('Google 로그인 실패:', error);
    }
  };
  
  return (
    <View>
      <Button title="Google로 로그인" onPress={handleGoogleSignIn} />
      {Platform.OS === 'web' && (
        <Button title="계속 둘러보기 (게스트)" onPress={() => {/* 게스트 모드 */}} />
      )}
    </View>
  );
};
```

### 3. 사용자 프로필 확장
```typescript
// types/user.ts (신규)
export interface UserProfile {
  uid: string;
  email: string;
  displayName?: string;
  photoURL?: string;
  provider: 'email' | 'google';
  createdAt: string;
  lastLoginAt: string;
}

// hooks/useUserProfile.ts (신규)
import { useState, useEffect } from 'react';
import { onAuthStateChanged } from 'firebase/auth';
import { auth } from '../src/firebaseConfig';

export const useUserProfile = () => {
  const [profile, setProfile] = useState<UserProfile | null>(null);
  
  useEffect(() => {
    const unsubscribe = onAuthStateChanged(auth, (user) => {
      if (user) {
        setProfile({
          uid: user.uid,
          email: user.email!,
          displayName: user.displayName,
          photoURL: user.photoURL,
          provider: user.providerData[0]?.providerId === 'google.com' ? 'google' : 'email',
          createdAt: user.metadata.creationTime!,
          lastLoginAt: user.metadata.lastSignInTime!,
        });
      } else {
        setProfile(null);
      }
    });
    
    return unsubscribe;
  }, []);
  
  return { profile };
};
```

## 🚀 구현 로드맵

### Phase 1: Web 플랫폼 기본 지원 (2-3주)
- [ ] 반응형 레이아웃 구현
- [ ] 웹 전용 네비게이션 컴포넌트
- [ ] SEO 메타 태그 최적화
- [ ] PWA 매니페스트 설정
- [ ] 웹 빌드 테스트 및 배포

### Phase 2: Google Auth 통합 (1-2주)
- [ ] Firebase Google Provider 설정
- [ ] 모바일용 Google Sign-In 구현
- [ ] 웹용 Google Auth 팝업 구현
- [ ] 사용자 프로필 UI 확장
- [ ] 기존 이메일 인증과 통합

### Phase 3: 고급 웹 기능 (2-3주)
- [ ] 키보드 단축키 지원
- [ ] 브라우저 히스토리 관리
- [ ] 데스크톱 알림 지원
- [ ] 오프라인 지원 (Service Worker)
- [ ] 웹 최적화 (번들 크기, 로딩 속도)

### Phase 4: 사용자 경험 개선 (1-2주)
- [ ] 소셜 로그인 연동 (Facebook, Apple)
- [ ] 사용자 설정 저장
- [ ] 테마 커스터마이징
- [ ] 접근성(a11y) 개선

## 📝 개발 가이드

### 환경 설정

#### 1. Google OAuth 설정
```bash
# .env에 추가
GOOGLE_WEB_CLIENT_ID=your_web_client_id
GOOGLE_IOS_CLIENT_ID=your_ios_client_id  
GOOGLE_ANDROID_CLIENT_ID=your_android_client_id
```

#### 2. 필요한 패키지 설치
```bash
# Google Sign-In (React Native)
npx expo install @react-native-google-signin/google-signin

# Web 최적화
npm install react-helmet-async
npm install workbox-webpack-plugin
```

### 빌드 및 배포

#### Web 빌드
```bash
# 개발 서버
npx expo start --web

# 프로덕션 빌드
npx expo export --platform web

# 정적 호스팅 배포 (Vercel, Netlify 등)
```

#### 모바일 빌드
```bash
# EAS 빌드
eas build --platform android --profile production
eas build --platform ios --profile production
```

### 테스트 전략

#### 1. 크로스 플랫폼 테스트
```typescript
// __tests__/auth.test.ts (신규)
describe('Authentication', () => {
  it('should work on mobile', () => {
    // React Native 테스트
  });
  
  it('should work on web', () => {
    // 웹 브라우저 테스트
  });
});
```

#### 2. E2E 테스트
```typescript
// e2e/auth.spec.ts (신규)
import { test, expect } from '@playwright/test';

test('Google login flow', async ({ page }) => {
  await page.goto('/');
  await page.click('[data-testid="google-login"]');
  // Google OAuth 플로우 테스트
});
```

## 🔧 개발 팁

### 1. 플랫폼별 코드 분리
```typescript
// utils/platform.ts (신규)
import { Platform } from 'react-native';

export const isWeb = Platform.OS === 'web';
export const isMobile = Platform.OS !== 'web';
export const isIOS = Platform.OS === 'ios';
export const isAndroid = Platform.OS === 'android';
```

### 2. 환경변수 관리
```typescript
// config/env.ts (신규)
import Constants from 'expo-constants';

const getEnvVar = (name: string): string => {
  const value = Constants.expoConfig?.extra?.[name] || process.env[name];
  if (!value) {
    throw new Error(`Environment variable ${name} is required`);
  }
  return value;
};

export const config = {
  firebase: {
    apiKey: getEnvVar('FIREBASE_API_KEY'),
    authDomain: getEnvVar('FIREBASE_AUTH_DOMAIN'),
    // ...
  },
  google: {
    webClientId: getEnvVar('GOOGLE_WEB_CLIENT_ID'),
  },
  backendUrl: getEnvVar('BACKEND_URL'),
};
```

### 3. 디버깅
```typescript
// utils/debug.ts (신규)
export const debugInfo = {
  platform: Platform.OS,
  version: Constants.expoConfig?.version,
  buildNumber: Constants.expoConfig?.ios?.buildNumber,
};

// 개발 모드에서만 로그 출력
export const devLog = (message: string, ...args: any[]) => {
  if (__DEV__) {
    console.log(`[${Platform.OS}] ${message}`, ...args);
  }
};
```

이 문서를 통해 단계적으로 웹 플랫폼 지원과 Google Auth 연동을 구현할 수 있습니다. 각 단계별로 테스트를 진행하며 안정성을 확보하는 것을 권장합니다.