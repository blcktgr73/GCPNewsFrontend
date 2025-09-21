# GCP News Portal 구현 로드맵

## 🎯 프로젝트 목표
- **현재**: React Native 모바일 앱 (Firebase Email Auth)
- **목표**: 크로스플랫폼 뉴스 포털 (Mobile + Web + Google OAuth)

## 📊 현재 상태 요약

### ✅ 완료된 기능
- Firebase Email/Password 인증
- 키워드 기반 뉴스 요약 시스템
- 페이지네이션 및 무한 스크롤
- 다크/라이트 모드 지원
- EAS 빌드 설정 (Android/iOS)

### 🚧 진행중인 개발
- `app/summary.tsx` UI 개선 (최근 수정됨)
- 반응형 레이아웃 작업

### ❌ 미완성 기능
- 웹 플랫폼 최적화
- Google OAuth 연동
- 고급 사용자 프로필 관리
- PWA 기능

## 🗓️ 상세 구현 일정

### Phase 1: Web 플랫폼 기초 구축 (3-4주)

#### Week 1: 반응형 기초 설정
**목표**: 웹에서 기본 동작하는 앱 구현

**Day 1-2: 반응형 훅 및 유틸리티**
- [ ] `hooks/useResponsiveLayout.ts` 구현
- [ ] `styles/responsive.ts` 스타일 시스템 구축
- [ ] 기존 컴포넌트에 반응형 적용 테스트

**Day 3-4: 웹 네비게이션**
- [ ] `components/WebSidebar.tsx` 구현
- [ ] `components/WebHeader.tsx` 구현  
- [ ] `app/_layout.tsx` 웹 레이아웃 적용

**Day 5-7: 테스트 및 디버깅**
- [ ] 웹 빌드 테스트 (`npx expo start --web`)
- [ ] 모바일/웹 크로스 플랫폼 테스트
- [ ] UI/UX 개선 및 버그 수정

#### Week 2: 웹 최적화 기능
**목표**: 웹 사용자 경험 향상

**Day 1-3: 키보드 및 상호작용**
- [ ] `hooks/useKeyboardShortcuts.ts` 구현
- [ ] 웹 전용 단축키 추가 (Ctrl+K, Ctrl+H 등)
- [ ] 키보드 네비게이션 지원

**Day 4-5: 브라우저 최적화**
- [ ] `hooks/useBrowserHistory.ts` 구현
- [ ] URL 기반 라우팅 개선
- [ ] 브라우저 뒤로가기/앞으로가기 지원

**Day 6-7: 성능 최적화**
- [ ] `components/OptimizedImage.tsx` 구현
- [ ] Lazy loading 적용
- [ ] 번들 크기 최적화

#### Week 3: SEO 및 PWA
**목표**: 검색 엔진 최적화 및 앱 같은 웹 경험

**Day 1-3: SEO 최적화**
- [ ] `app/+html.tsx` 메타 태그 확장
- [ ] Open Graph, Twitter Card 설정
- [ ] 구조화 데이터 (JSON-LD) 추가
- [ ] 사이트맵 및 robots.txt 생성

**Day 4-5: PWA 구현**
- [ ] `public/manifest.json` 작성
- [ ] Service Worker 설정
- [ ] 오프라인 지원 기본 구현

**Day 6-7: 테스트 및 배포**
- [ ] Lighthouse 성능 테스트
- [ ] 웹 접근성(a11y) 검증
- [ ] Vercel/Netlify 배포 설정

#### Week 4: 폴리싱 및 고급 기능
**목표**: 웹 플랫폼 완성도 향상

**Day 1-3: 고급 웹 기능**
- [ ] 브라우저 알림 지원
- [ ] 파일 업로드/다운로드 (향후 확장용)
- [ ] 인쇄 최적화 CSS

**Day 4-5: 크로스 브라우저 테스트**
- [ ] Chrome, Firefox, Safari, Edge 테스트
- [ ] 모바일 브라우저 테스트
- [ ] IE11 호환성 검토 (필요시)

**Day 6-7: 문서화 및 배포**
- [ ] 웹 배포 가이드 작성
- [ ] 성능 벤치마크 문서화
- [ ] 프로덕션 배포

### Phase 2: Google OAuth 통합 (2-3주)

#### Week 5: Firebase Google Auth 설정
**목표**: Firebase 및 Google OAuth 기본 설정

**Day 1-2: Firebase 프로젝트 설정**
- [ ] Firebase Console에서 Google 인증 활성화
- [ ] Google Cloud Console OAuth 2.0 클라이언트 생성
  - Web Client ID
  - Android Client ID  
  - iOS Client ID
- [ ] 환경변수 설정 및 보안 검토

**Day 3-4: 패키지 설치 및 기본 설정**
- [ ] `@react-native-google-signin/google-signin` 설치
- [ ] 플랫폼별 설정 파일 추가
  - `google-services.json` (Android)
  - `GoogleService-Info.plist` (iOS)
- [ ] `src/firebaseConfig.ts` 확장

**Day 5-7: 기본 구현 및 테스트**
- [ ] Google Auth Provider 설정
- [ ] 웹용 기본 Google 로그인 테스트
- [ ] 모바일용 기본 Google 로그인 테스트

#### Week 6: 플랫폼별 Google Auth 구현
**목표**: 각 플랫폼에 최적화된 Google Auth 구현

**Day 1-3: 웹 플랫폼 구현**
- [ ] `hooks/useGoogleAuth.web.ts` 구현
- [ ] Google OAuth 팝업 처리
- [ ] 에러 처리 및 사용자 피드백

**Day 4-6: 모바일 플랫폼 구현**
- [ ] `hooks/useGoogleAuth.native.ts` 구현
- [ ] Google Play Services 연동
- [ ] iOS Google Sign-In 설정

**Day 7: 통합 인터페이스**
- [ ] `hooks/useGoogleAuth.ts` 통합 훅 구현
- [ ] 플랫폼 감지 및 동적 로드

#### Week 7: UI 컴포넌트 및 사용자 경험
**목표**: Google Auth UI 및 사용자 프로필 시스템

**Day 1-3: 인증 UI 컴포넌트**
- [ ] `components/AuthButtons.tsx` 구현
- [ ] 소셜 로그인 버튼 디자인
- [ ] 로딩 상태 및 에러 처리 UI

**Day 4-6: 사용자 프로필 시스템**
- [ ] `types/user.ts` 타입 정의 확장
- [ ] `hooks/useUserProfile.ts` 확장
- [ ] `components/UserProfileCard.tsx` 구현

**Day 7: 기존 화면 업데이트**
- [ ] `app/index.tsx` Google Auth 통합
- [ ] 프로필 화면 추가 (선택사항)

### Phase 3: 고급 기능 및 최적화 (2-3주)

#### Week 8: 사용자 경험 향상
**목표**: 고급 인증 기능 및 사용자 설정

**Day 1-2: 계정 연동**
- [ ] 기존 이메일 계정과 Google 계정 연동
- [ ] 여러 인증 방법 관리
- [ ] 계정 병합 플로우

**Day 3-4: 사용자 설정**
- [ ] 프로필 편집 기능
- [ ] 테마 설정 저장
- [ ] 알림 설정 관리

**Day 5-7: 보안 강화**
- [ ] 토큰 갱신 로직
- [ ] 세션 관리 개선
- [ ] 보안 이벤트 로깅

#### Week 9: 성능 최적화 및 테스트
**목표**: 앱 성능 최적화 및 안정성 향상

**Day 1-3: 성능 최적화**
- [ ] 인증 상태 캐싱 최적화
- [ ] 불필요한 리렌더링 방지
- [ ] 메모리 사용량 최적화

**Day 4-5: 에러 처리 강화**
- [ ] 네트워크 오류 대응
- [ ] 인증 실패 복구 메커니즘
- [ ] 사용자 친화적 에러 메시지

**Day 6-7: 자동화된 테스트**
- [ ] 인증 플로우 단위 테스트
- [ ] E2E 테스트 시나리오 작성
- [ ] CI/CD 파이프라인 설정

#### Week 10: 배포 및 모니터링 (선택사항)
**목표**: 프로덕션 배포 및 모니터링 체계 구축

**Day 1-3: 배포 준비**
- [ ] 프로덕션 환경 설정 점검
- [ ] 보안 설정 최종 검토
- [ ] 배포 스크립트 작성

**Day 4-5: 모니터링 설정**
- [ ] Firebase Analytics 설정
- [ ] 인증 이벤트 추적
- [ ] 에러 모니터링 (Sentry 등)

**Day 6-7: 문서화 및 인수인계**
- [ ] API 문서 업데이트
- [ ] 운영 가이드 작성
- [ ] 개발팀 교육 자료 준비

## 🎯 주요 마일스톤

### Milestone 1: 웹 MVP (Week 3 종료)
**성공 기준**:
- ✅ 웹 브라우저에서 기본 기능 동작
- ✅ 반응형 디자인 적용
- ✅ SEO 기본 설정 완료
- ✅ 프로덕션 배포 가능

### Milestone 2: Google Auth 통합 (Week 7 종료)  
**성공 기준**:
- ✅ 웹/모바일 Google 로그인 동작
- ✅ 기존 이메일 인증과 공존
- ✅ 사용자 프로필 시스템 완성
- ✅ 에러 처리 및 UX 완성도

### Milestone 3: 완전한 크로스플랫폼 앱 (Week 10 종료)
**성공 기준**:
- ✅ 모든 플랫폼에서 안정적 동작
- ✅ 성능 최적화 완료
- ✅ 자동화된 테스트 커버리지 80% 이상
- ✅ 프로덕션 모니터링 체계 구축

## 🚨 리스크 및 대응 방안

### 기술적 리스크

#### 1. 크로스 플랫폼 호환성 문제
**위험도**: 🟡 중간
**대응방안**:
- 플랫폼별 조건부 렌더링 활용
- 충분한 테스트 기간 확보
- 단계적 배포 (웹 먼저, 모바일 나중)

#### 2. Google Auth 설정 복잡성
**위험도**: 🔴 높음  
**대응방안**:
- 상세한 설정 문서 준비
- 개발 환경에서 충분한 테스트
- Firebase 지원팀 연락처 확보

#### 3. 성능 저하
**위험도**: 🟡 중간
**대응방안**:
- 각 단계마다 성능 벤치마크
- 프로파일링 도구 활용
- 조기 최적화보다 기능 완성 우선

### 일정 리스크

#### 1. 예상보다 복잡한 웹 최적화
**위험도**: 🟡 중간
**대응방안**:
- MVP 기능 우선 구현
- 고급 기능은 Phase 4로 연기 가능
- 주간 단위 검토 및 일정 조정

#### 2. Google OAuth 연동 이슈
**위험도**: 🔴 높음
**대응방안**:
- 2-3일 버퍼 확보
- 대안 인증 방법 검토 (Apple, Facebook)
- 최악 시나리오에서는 이메일 인증만 유지

## 📊 성공 지표 (KPI)

### 기술적 지표
- **크로스 플랫폼 호환성**: 모든 주요 브라우저에서 동작
- **성능 지표**: 웹 Lighthouse 스코어 90+ 
- **테스트 커버리지**: 단위 테스트 80% 이상
- **빌드 시간**: 5분 이내 (웹+모바일)

### 사용자 경험 지표  
- **인증 성공률**: 95% 이상
- **페이지 로딩 시간**: 웹 3초 이내, 모바일 2초 이내
- **사용자 유지율**: 기존 대비 유지 또는 개선
- **에러율**: 1% 이하

### 비즈니스 지표
- **플랫폼 다양성**: 모바일 + 웹 동시 지원
- **사용자 접근성**: Google 계정 연동으로 가입 장벽 완화
- **유지보수성**: 코드베이스 통합으로 개발 효율성 향상

## 🔧 개발 환경 및 도구

### 필수 개발 도구
```bash
# 기본 환경
Node.js 18+ 
npm 8+
Expo CLI

# 플랫폼별 개발 도구
Android Studio (Android 개발)
Xcode (iOS 개발) 
Chrome DevTools (웹 디버깅)

# 테스트 도구
Jest (단위 테스트)
Playwright (E2E 테스트)
Lighthouse (성능 측정)
```

### 권장 VS Code 확장
```json
{
  "recommendations": [
    "ms-vscode.vscode-typescript-next",
    "bradlc.vscode-tailwindcss",
    "esbenp.prettier-vscode",
    "ms-vscode.vscode-jest",
    "ms-playwright.playwright"
  ]
}
```

## 📚 참고 문서

### 공식 문서
- [Expo Router 문서](https://docs.expo.dev/router/introduction/)
- [Firebase Auth 문서](https://firebase.google.com/docs/auth)
- [Google Sign-In for React Native](https://github.com/react-native-google-signin/google-signin)

### 내부 문서
- [`PROJECT_DOCUMENTATION.md`](./PROJECT_DOCUMENTATION.md) - 프로젝트 전체 개요
- [`WEB_MIGRATION_GUIDE.md`](./WEB_MIGRATION_GUIDE.md) - 웹 플랫폼 확장 가이드  
- [`GOOGLE_AUTH_INTEGRATION.md`](./GOOGLE_AUTH_INTEGRATION.md) - Google Auth 연동 가이드

## 👥 팀 역할 및 책임

### 개발 담당 영역 (1인 개발 시)
**Week 1-3**: 웹 플랫폼 기초
**Week 4-6**: Google Auth 통합  
**Week 7-9**: 고급 기능 및 최적화
**Week 10**: 배포 및 문서화

### 팀 개발 시 역할 분담
- **Frontend Lead**: 웹 UI/UX, 반응형 디자인
- **Mobile Lead**: React Native 최적화, 플랫폼 통합
- **Backend/Auth**: Firebase 설정, Google OAuth 연동
- **QA/DevOps**: 테스트 자동화, 배포 파이프라인

이 로드맵을 통해 체계적이고 안정적으로 크로스플랫폼 뉴스 포털을 구축할 수 있습니다. 각 주차별 목표를 달성하며 지속적으로 품질을 검증하는 것이 성공의 핵심입니다.