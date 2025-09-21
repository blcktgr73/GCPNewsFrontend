# Web Compatibility Issues

## 문제 요약
GCP News Portal React Native Expo 앱이 Android에서는 정상 작동하지만 웹 브라우저에서는 "Welcome to Expo" 메시지만 표시되는 문제

## 증상
- **Android Expo Go**: ✅ 정상 작동 (QR 코드 스캔으로 테스트 가능)
- **웹 브라우저**: ❌ "Welcome to Expo" 기본 화면만 표시
- **APK 빌드**: ✅ 정상 작동 (배포용)

## 기술적 원인 분석
1. **Expo Router 웹 호환성**: expo-router가 웹에서 제대로 라우팅하지 못함
2. **환경변수**: @env import가 웹에서 다르게 동작
3. **Firebase 설정**: React Native Firebase와 Web Firebase 차이
4. **Component 렌더링**: app/_layout.tsx와 app/index.tsx가 웹에서 실행되지 않음

## 시도한 해결책들
1. Firebase 플랫폼별 초기화 (web vs native)
2. SplashScreen 웹 비활성화
3. babel-config 환경변수 설정 수정
4. app.json 웹 번들러 설정 변경 (static → single)
5. @/ alias 경로 문제 수정
6. 디버그 로그 추가 (콘솔에 나타나지 않음)

## 브라우저 콘솔 메시지
```
Download the React DevTools for a better development experience
Running application "main" with appParams: {rootTag: '#root', hydrate: undefined}
Development-level warnings: ON.
Performance optimizations: OFF.
```

## 권장 해결방안
1. **현재 개발**: Android Expo Go 앱 사용
2. **배포**: APK 빌드 계속 사용
3. **웹 지원 필요시**:
   - React Navigation으로 마이그레이션
   - Next.js 별도 웹 앱 구축
   - Create React App + Firebase Web SDK

## 현재 상태
- Android 앱 완전 기능: 로그인, 뉴스 요약, 키워드 관리
- 웹 버전은 보류하고 Android 개발에 집중
- 모든 핵심 기능은 Android에서 정상 작동

## 날짜
2025-09-21