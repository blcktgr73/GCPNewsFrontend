# Android Expo 앱으로 테스트하기

## 준비사항

### 1. Android 기기에 Expo Go 앱 설치
- Google Play Store에서 "Expo Go" 검색 후 설치
- 또는 이 링크 사용: https://play.google.com/store/apps/details?id=host.exp.exponent

### 2. 네트워크 연결 확인
- 개발 PC와 Android 기기가 같은 Wi-Fi 네트워크에 연결되어 있어야 합니다
- 방화벽이 포트 8081을 차단하지 않는지 확인

## 연결 방법

### 방법 1: QR 코드 스캔 (추천)

1. **QR 코드 표시**
   - 터미널에서 QR 코드가 표시되지 않는 경우
   - 브라우저에서 http://localhost:8081 접속
   - 페이지에 QR 코드가 표시됩니다

2. **Expo Go 앱에서 스캔**
   - Android 기기에서 Expo Go 앱 실행
   - "Scan QR Code" 버튼 탭
   - 개발 PC 화면의 QR 코드를 스캔

### 방법 2: 수동 URL 입력

1. **개발 PC의 IP 주소 확인**
   ```bash
   ipconfig
   ```
   - Wi-Fi 어댑터의 IPv4 주소를 찾습니다 (예: 192.168.1.100)

2. **Expo Go 앱에서 수동 연결**
   - Expo Go 앱에서 "Enter URL manually" 선택
   - 다음 형식으로 입력: `exp://[PC_IP_ADDRESS]:8081`
   - 예: `exp://192.168.1.100:8081`

### 방법 3: Expo 터널 사용

1. **터널 모드로 서버 재시작**
   ```bash
   npx expo start --tunnel
   ```
   - 이 방법은 인터넷을 통해 연결되므로 같은 네트워크가 아니어도 됩니다

## 현재 서버 상태

- **개발 서버**: http://localhost:8081 (실행 중)
- **Metro Bundler**: 실행 중
- **환경 변수**: 로드 완료

## 연결 후 테스트할 항목

### 1. 앱 로딩
- [ ] 앱이 정상적으로 로딩되는지 확인
- [ ] 로그인 화면이 표시되는지 확인

### 2. 인증 테스트
- [ ] Google 로그인 버튼 작동 확인
- [ ] 이메일 로그인 폼 작동 확인
- [ ] 회원가입 링크 작동 확인

### 3. UI 테스트
- [ ] 다크/라이트 모드 전환 확인
- [ ] 터치 인터랙션 반응 확인
- [ ] 화면 회전 대응 확인

## 문제 해결

### 연결이 안 되는 경우

1. **네트워크 확인**
   - PC와 Android 기기가 같은 Wi-Fi에 연결되어 있는지 확인
   - 모바일 데이터가 아닌 Wi-Fi 사용 확인

2. **방화벽 확인**
   - Windows Defender 방화벽에서 포트 8081 허용
   - 안티바이러스 소프트웨어 방화벽 설정 확인

3. **IP 주소 확인**
   ```bash
   ipconfig | findstr IPv4
   ```

4. **서버 재시작**
   ```bash
   # 현재 서버 종료 후
   npx expo start --clear
   ```

### 앱 로딩 오류가 발생하는 경우

1. **캐시 클리어**
   ```bash
   npx expo start --clear
   ```

2. **의존성 재설치**
   ```bash
   npm install
   ```

3. **환경 변수 확인**
   - `.env` 파일이 올바른지 확인
   - Firebase 설정이 정확한지 확인

## 개발 서버 명령어

```bash
# 일반 시작
npm start

# 캐시 클리어 후 시작
npx expo start --clear

# 터널 모드로 시작 (인터넷 연결)
npx expo start --tunnel

# 특정 포트로 시작
npx expo start --port 8081
```

## 실시간 로그 확인

연결 후 Android 기기에서 앱을 사용하면 PC 터미널에서 실시간 로그를 확인할 수 있습니다:
- 네트워크 요청
- 콘솔 로그 출력
- 오류 메시지
- 성능 정보

이 정보를 통해 앱의 동작을 실시간으로 모니터링할 수 있습니다.