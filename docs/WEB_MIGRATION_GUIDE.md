# Web 플랫폼 마이그레이션 가이드

## 🎯 목표
React Native 모바일 앱을 웹 플랫폼으로 확장하여 크로스 플랫폼 뉴스 포털 서비스 구현

## 📋 현재 상태 분석

### 이미 지원되는 웹 기능
✅ **Expo Web 기본 설정** - `app.config.ts`에 웹 설정 존재
✅ **웹 빌드 스크립트** - `package.json`에 `web` 스크립트 포함
✅ **HTML 템플릿** - `app/+html.tsx` 존재

### 웹 최적화가 필요한 영역
❌ **반응형 디자인** - 모바일 중심 레이아웃
❌ **웹 네비게이션** - 데스크톱 UI/UX 부재
❌ **키보드 인터랙션** - 웹 사용자 경험 부족
❌ **SEO 최적화** - 검색엔진 최적화 미흡

## 🚀 구현 단계

### Phase 1: 기본 웹 지원 확보

#### 1.1 반응형 디자인 시스템 구축

```bash
# 새 파일 생성
mkdir -p hooks styles
```

**hooks/useResponsiveLayout.ts**
```typescript
import { useState, useEffect } from 'react';
import { useWindowDimensions } from 'react-native';

export interface BreakpointConfig {
  mobile: number;
  tablet: number;
  desktop: number;
}

const defaultBreakpoints: BreakpointConfig = {
  mobile: 768,
  tablet: 1024,
  desktop: 1200,
};

export const useResponsiveLayout = (breakpoints = defaultBreakpoints) => {
  const { width } = useWindowDimensions();
  
  const getDeviceType = () => {
    if (width < breakpoints.mobile) return 'mobile';
    if (width < breakpoints.tablet) return 'tablet';
    if (width < breakpoints.desktop) return 'desktop';
    return 'wide';
  };

  const [deviceType, setDeviceType] = useState(getDeviceType());

  useEffect(() => {
    setDeviceType(getDeviceType());
  }, [width]);

  return {
    width,
    deviceType,
    isMobile: deviceType === 'mobile',
    isTablet: deviceType === 'tablet',
    isDesktop: deviceType === 'desktop' || deviceType === 'wide',
    isWide: deviceType === 'wide',
  };
};
```

**styles/responsive.ts**
```typescript
import { StyleSheet } from 'react-native';

export const createResponsiveStyles = (deviceType: string) => {
  const baseStyles = {
    container: {
      flex: 1,
      padding: 20,
    },
    maxWidth: deviceType === 'mobile' ? '100%' : 1200,
    sidebarWidth: deviceType === 'desktop' ? 250 : 0,
  };

  return StyleSheet.create({
    container: {
      ...baseStyles.container,
      maxWidth: baseStyles.maxWidth,
      alignSelf: 'center' as const,
      width: '100%',
    },
    sidebar: {
      width: baseStyles.sidebarWidth,
      backgroundColor: '#f5f5f5',
      borderRightWidth: baseStyles.sidebarWidth > 0 ? 1 : 0,
      borderRightColor: '#e0e0e0',
    },
    mainContent: {
      flex: 1,
      paddingLeft: deviceType === 'desktop' ? 20 : 0,
    },
  });
};
```

#### 1.2 웹 전용 네비게이션 컴포넌트

**components/WebSidebar.tsx**
```typescript
import React from 'react';
import { View, Text, TouchableOpacity, StyleSheet } from 'react-native';
import { useRouter, usePathname } from 'expo-router';
import { useResponsiveLayout } from '../hooks/useResponsiveLayout';

interface NavigationItem {
  key: string;
  title: string;
  path: string;
  icon: string;
}

const navigationItems: NavigationItem[] = [
  { key: 'summary', title: '뉴스 요약', path: '/summary', icon: '📰' },
  { key: 'keyword', title: '키워드 관리', path: '/keyword', icon: '🔍' },
  { key: 'profile', title: '프로필', path: '/profile', icon: '👤' },
];

export const WebSidebar = () => {
  const router = useRouter();
  const pathname = usePathname();
  const { isDesktop } = useResponsiveLayout();

  if (!isDesktop) return null;

  return (
    <View style={styles.sidebar}>
      <View style={styles.header}>
        <Text style={styles.title}>GCP 뉴스 포털</Text>
      </View>
      
      <View style={styles.navigation}>
        {navigationItems.map((item) => (
          <TouchableOpacity
            key={item.key}
            style={[
              styles.navItem,
              pathname === item.path && styles.activeNavItem
            ]}
            onPress={() => router.push(item.path)}
          >
            <Text style={styles.navIcon}>{item.icon}</Text>
            <Text style={[
              styles.navText,
              pathname === item.path && styles.activeNavText
            ]}>
              {item.title}
            </Text>
          </TouchableOpacity>
        ))}
      </View>
    </View>
  );
};

const styles = StyleSheet.create({
  sidebar: {
    width: 250,
    backgroundColor: '#ffffff',
    borderRightWidth: 1,
    borderRightColor: '#e0e0e0',
    paddingTop: 20,
  },
  header: {
    paddingHorizontal: 20,
    paddingBottom: 20,
    borderBottomWidth: 1,
    borderBottomColor: '#e0e0e0',
  },
  title: {
    fontSize: 18,
    fontWeight: 'bold',
    color: '#333',
  },
  navigation: {
    paddingTop: 20,
  },
  navItem: {
    flexDirection: 'row',
    alignItems: 'center',
    paddingHorizontal: 20,
    paddingVertical: 12,
    marginHorizontal: 10,
    borderRadius: 8,
  },
  activeNavItem: {
    backgroundColor: '#e3f2fd',
  },
  navIcon: {
    fontSize: 16,
    marginRight: 12,
  },
  navText: {
    fontSize: 16,
    color: '#666',
  },
  activeNavText: {
    color: '#1976d2',
    fontWeight: '500',
  },
});
```

#### 1.3 반응형 레이아웃 적용

**app/_layout.tsx** 수정
```typescript
import { Stack } from 'expo-router';
import { View } from 'react-native';
import { WebSidebar } from '../components/WebSidebar';
import { useResponsiveLayout } from '../hooks/useResponsiveLayout';
import { createResponsiveStyles } from '../styles/responsive';

export default function Layout() {
  const { deviceType, isDesktop } = useResponsiveLayout();
  const styles = createResponsiveStyles(deviceType);

  if (isDesktop) {
    return (
      <View style={{ flex: 1, flexDirection: 'row' }}>
        <WebSidebar />
        <View style={styles.mainContent}>
          <Stack screenOptions={{ headerShown: false }} />
        </View>
      </View>
    );
  }

  return <Stack />;
}
```

### Phase 2: 고급 웹 기능

#### 2.1 키보드 단축키 지원

**hooks/useKeyboardShortcuts.ts**
```typescript
import { useEffect, useCallback } from 'react';
import { Platform } from 'react-native';
import { useRouter } from 'expo-router';

interface ShortcutConfig {
  key: string;
  ctrlKey?: boolean;
  altKey?: boolean;
  shiftKey?: boolean;
  action: () => void;
  description: string;
}

export const useKeyboardShortcuts = () => {
  const router = useRouter();

  const shortcuts: ShortcutConfig[] = [
    {
      key: 'k',
      ctrlKey: true,
      action: () => router.push('/keyword'),
      description: '키워드 관리로 이동',
    },
    {
      key: 'h',
      ctrlKey: true,
      action: () => router.push('/summary'),
      description: '홈(요약)으로 이동',
    },
    {
      key: 'r',
      ctrlKey: true,
      action: () => window.location.reload(),
      description: '페이지 새로고침',
    },
  ];

  const handleKeyPress = useCallback((event: KeyboardEvent) => {
    const matchingShortcut = shortcuts.find(shortcut => 
      shortcut.key === event.key.toLowerCase() &&
      !!shortcut.ctrlKey === event.ctrlKey &&
      !!shortcut.altKey === event.altKey &&
      !!shortcut.shiftKey === event.shiftKey
    );

    if (matchingShortcut) {
      event.preventDefault();
      matchingShortcut.action();
    }
  }, [shortcuts]);

  useEffect(() => {
    if (Platform.OS !== 'web') return;

    window.addEventListener('keydown', handleKeyPress);
    return () => window.removeEventListener('keydown', handleKeyPress);
  }, [handleKeyPress]);

  return { shortcuts };
};
```

#### 2.2 웹 전용 헤더 컴포넌트

**components/WebHeader.tsx**
```typescript
import React from 'react';
import { View, Text, TouchableOpacity, StyleSheet } from 'react-native';
import { useResponsiveLayout } from '../hooks/useResponsiveLayout';
import { useUserProfile } from '../hooks/useUserProfile';

export const WebHeader = () => {
  const { isDesktop } = useResponsiveLayout();
  const { profile } = useUserProfile();

  if (!isDesktop) return null;

  return (
    <View style={styles.header}>
      <View style={styles.searchContainer}>
        <Text style={styles.searchPlaceholder}>
          뉴스 검색... (Ctrl+K)
        </Text>
      </View>
      
      <View style={styles.userSection}>
        {profile?.photoURL && (
          <View style={styles.avatar}>
            <Text style={styles.avatarText}>
              {profile.displayName?.[0] || profile.email[0].toUpperCase()}
            </Text>
          </View>
        )}
        <Text style={styles.userName}>
          {profile?.displayName || profile?.email}
        </Text>
      </View>
    </View>
  );
};

const styles = StyleSheet.create({
  header: {
    height: 60,
    flexDirection: 'row',
    alignItems: 'center',
    justifyContent: 'space-between',
    paddingHorizontal: 20,
    backgroundColor: '#ffffff',
    borderBottomWidth: 1,
    borderBottomColor: '#e0e0e0',
  },
  searchContainer: {
    flex: 1,
    maxWidth: 400,
    height: 36,
    backgroundColor: '#f5f5f5',
    borderRadius: 18,
    justifyContent: 'center',
    paddingHorizontal: 16,
  },
  searchPlaceholder: {
    color: '#999',
    fontSize: 14,
  },
  userSection: {
    flexDirection: 'row',
    alignItems: 'center',
  },
  avatar: {
    width: 32,
    height: 32,
    borderRadius: 16,
    backgroundColor: '#1976d2',
    justifyContent: 'center',
    alignItems: 'center',
    marginRight: 8,
  },
  avatarText: {
    color: '#ffffff',
    fontSize: 14,
    fontWeight: 'bold',
  },
  userName: {
    fontSize: 14,
    color: '#333',
  },
});
```

### Phase 3: SEO 및 PWA 최적화

#### 3.1 SEO 메타 태그 확장

**app/+html.tsx** 수정
```typescript
import { type PropsWithChildren } from 'react';

export default function Root({ children }: PropsWithChildren) {
  return (
    <html lang="ko">
      <head>
        <meta charSet="utf-8" />
        <meta httpEquiv="X-UA-Compatible" content="IE=edge" />
        <meta name="viewport" content="width=device-width, initial-scale=1, shrink-to-fit=no" />
        
        {/* SEO 기본 메타 태그 */}
        <title>GCP 뉴스 포털 - AI 기반 뉴스 요약 서비스</title>
        <meta name="description" content="키워드 기반 개인화된 뉴스 요약 서비스. AI가 선별한 중요 뉴스를 간편하게 확인하세요." />
        <meta name="keywords" content="뉴스, 요약, AI, 인공지능, 키워드, 뉴스 포털, 개인화" />
        <meta name="author" content="GCP News Portal Team" />
        
        {/* Open Graph 메타 태그 */}
        <meta property="og:title" content="GCP 뉴스 포털" />
        <meta property="og:description" content="키워드 기반 개인화된 뉴스 요약 서비스" />
        <meta property="og:type" content="website" />
        <meta property="og:image" content="/og-image.png" />
        <meta property="og:url" content="https://your-domain.com" />
        <meta property="og:site_name" content="GCP 뉴스 포털" />
        
        {/* Twitter Card 메타 태그 */}
        <meta name="twitter:card" content="summary_large_image" />
        <meta name="twitter:title" content="GCP 뉴스 포털" />
        <meta name="twitter:description" content="키워드 기반 개인화된 뉴스 요약 서비스" />
        <meta name="twitter:image" content="/twitter-image.png" />
        
        {/* PWA 설정 */}
        <link rel="manifest" href="/manifest.json" />
        <meta name="theme-color" content="#1976d2" />
        <meta name="apple-mobile-web-app-capable" content="yes" />
        <meta name="apple-mobile-web-app-status-bar-style" content="default" />
        <meta name="apple-mobile-web-app-title" content="GCP 뉴스" />
        
        {/* 파비콘 */}
        <link rel="icon" href="/favicon.ico" />
        <link rel="apple-touch-icon" href="/apple-touch-icon.png" />
        
        {/* 폰트 프리로드 */}
        <link rel="preconnect" href="https://fonts.googleapis.com" />
        <link rel="preconnect" href="https://fonts.gstatic.com" crossOrigin="anonymous" />
        
        {/* 구조화 데이터 (JSON-LD) */}
        <script
          type="application/ld+json"
          dangerouslySetInnerHTML={{
            __html: JSON.stringify({
              '@context': 'https://schema.org',
              '@type': 'WebApplication',
              'name': 'GCP 뉴스 포털',
              'description': '키워드 기반 개인화된 뉴스 요약 서비스',
              'url': 'https://your-domain.com',
              'applicationCategory': 'NewsApplication',
              'operatingSystem': 'Any',
              'offers': {
                '@type': 'Offer',
                'price': '0',
                'priceCurrency': 'KRW'
              }
            })
          }}
        />
      </head>
      <body>
        {children}
      </body>
    </html>
  );
}
```

#### 3.2 PWA Manifest 설정

**public/manifest.json** (신규)
```json
{
  "name": "GCP 뉴스 포털",
  "short_name": "GCP News",
  "description": "키워드 기반 개인화된 뉴스 요약 서비스",
  "start_url": "/",
  "display": "standalone",
  "background_color": "#ffffff",
  "theme_color": "#1976d2",
  "orientation": "portrait-primary",
  "icons": [
    {
      "src": "/icon-192x192.png",
      "sizes": "192x192",
      "type": "image/png",
      "purpose": "maskable any"
    },
    {
      "src": "/icon-512x512.png",
      "sizes": "512x512",
      "type": "image/png",
      "purpose": "maskable any"
    }
  ],
  "categories": ["news", "productivity"],
  "shortcuts": [
    {
      "name": "뉴스 요약",
      "short_name": "요약",
      "description": "최신 뉴스 요약 보기",
      "url": "/summary",
      "icons": [{ "src": "/shortcut-summary.png", "sizes": "96x96" }]
    },
    {
      "name": "키워드 관리",
      "short_name": "키워드",
      "description": "관심 키워드 설정",
      "url": "/keyword",
      "icons": [{ "src": "/shortcut-keyword.png", "sizes": "96x96" }]
    }
  ]
}
```

### Phase 4: 성능 최적화

#### 4.1 코드 스플리팅 및 lazy loading

**utils/lazy.ts**
```typescript
import { lazy } from 'react';
import { Platform } from 'react-native';

// 웹에서만 lazy loading 적용
export const createLazyComponent = <T extends React.ComponentType<any>>(
  importFn: () => Promise<{ default: T }>
) => {
  if (Platform.OS === 'web') {
    return lazy(importFn);
  }
  // 모바일에서는 일반 import 사용
  return React.forwardRef((props, ref) => {
    const [Component, setComponent] = React.useState<T | null>(null);
    
    React.useEffect(() => {
      importFn().then(module => setComponent(() => module.default));
    }, []);
    
    if (!Component) return null;
    return <Component {...props} ref={ref} />;
  });
};
```

#### 4.2 이미지 최적화

**components/OptimizedImage.tsx**
```typescript
import React from 'react';
import { Image, Platform } from 'react-native';

interface OptimizedImageProps {
  source: { uri: string } | number;
  width?: number;
  height?: number;
  alt?: string;
  loading?: 'lazy' | 'eager';
}

export const OptimizedImage: React.FC<OptimizedImageProps> = ({
  source,
  width,
  height,
  alt,
  loading = 'lazy',
  ...props
}) => {
  if (Platform.OS === 'web') {
    // 웹에서는 native img 태그 사용하여 최적화
    const imgProps = {
      src: typeof source === 'object' ? source.uri : source,
      alt: alt || '',
      loading,
      width,
      height,
      style: {
        maxWidth: '100%',
        height: 'auto',
      },
    };
    
    return React.createElement('img', imgProps);
  }
  
  // 모바일에서는 React Native Image 사용
  return (
    <Image
      source={source}
      style={[{ width, height }, props.style]}
      {...props}
    />
  );
};
```

## 🔧 빌드 및 배포

### 웹 빌드 최적화

**metro.config.js** 수정
```javascript
const { getDefaultConfig } = require('expo/metro-config');

const config = getDefaultConfig(__dirname);

// 웹 빌드 최적화
if (process.env.EXPO_PLATFORM === 'web') {
  config.resolver.platforms = ['web', 'native', 'ios', 'android'];
  
  // 청크 분할 설정
  config.transformer.minifierConfig = {
    keep_fnames: true,
    mangle: {
      keep_fnames: true,
    },
  };
}

module.exports = config;
```

### 배포 스크립트

**scripts/deploy-web.sh** (신규)
```bash
#!/bin/bash

echo "🚀 웹 배포 시작..."

# 프로덕션 빌드
echo "📦 빌드 중..."
npx expo export --platform web

# 빌드 결과 확인
if [ -d "dist" ]; then
  echo "✅ 빌드 완료"
  
  # 정적 파일 최적화
  echo "🎨 이미지 최적화 중..."
  # ImageOptim 또는 다른 이미지 최적화 도구 실행
  
  # Vercel 배포 (예시)
  echo "🌍 Vercel 배포 중..."
  npx vercel dist --prod
  
  echo "🎉 배포 완료!"
else
  echo "❌ 빌드 실패"
  exit 1
fi
```

## 📊 성능 모니터링

### Web Vitals 측정

**utils/webVitals.ts**
```typescript
import { Platform } from 'react-native';

export const measureWebVitals = () => {
  if (Platform.OS !== 'web') return;

  // Web Vitals 라이브러리 동적 로드
  import('web-vitals').then(({ getCLS, getFID, getFCP, getLCP, getTTFB }) => {
    getCLS(console.log);
    getFID(console.log);
    getFCP(console.log);
    getLCP(console.log);
    getTTFB(console.log);
  });
};
```

## 🧪 테스트 전략

### 크로스 플랫폼 테스트

**__tests__/responsive.test.ts**
```typescript
import { renderHook } from '@testing-library/react-native';
import { useResponsiveLayout } from '../hooks/useResponsiveLayout';

// Mock useWindowDimensions
jest.mock('react-native', () => ({
  useWindowDimensions: jest.fn(),
}));

describe('useResponsiveLayout', () => {
  it('should return mobile for small screens', () => {
    require('react-native').useWindowDimensions.mockReturnValue({ width: 500 });
    
    const { result } = renderHook(() => useResponsiveLayout());
    
    expect(result.current.deviceType).toBe('mobile');
    expect(result.current.isMobile).toBe(true);
  });

  it('should return desktop for large screens', () => {
    require('react-native').useWindowDimensions.mockReturnValue({ width: 1200 });
    
    const { result } = renderHook(() => useResponsiveLayout());
    
    expect(result.current.deviceType).toBe('desktop');
    expect(result.current.isDesktop).toBe(true);
  });
});
```

이 가이드를 따라 단계적으로 구현하면 모바일과 웹에서 모두 최적화된 뉴스 포털 서비스를 제공할 수 있습니다.