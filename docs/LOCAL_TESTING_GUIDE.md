# Local Testing Guide

## Pre-Testing Checklist

### 1. Environment Setup
- [ ] `.env` file configured with Firebase credentials
- [ ] Google OAuth client IDs set up for all platforms
- [ ] Backend URL configured in environment variables
- [ ] Dependencies installed (`npm install`)

### 2. Firebase Configuration
- [ ] Firebase project created and configured
- [ ] Authentication methods enabled (Google + Email/Password)
- [ ] Google OAuth credentials configured in Firebase
- [ ] Firebase SDK configuration updated in `src/firebaseConfig.ts`

### 3. Development Environment
- [ ] Expo CLI installed (`npm install -g expo-cli`)
- [ ] Android Studio (for Android testing) or iOS Simulator (for iOS testing)
- [ ] Physical device with Expo Go app (optional)

## Testing Steps

### Step 1: Start Development Server
```bash
npm start
```

This will start the Expo development server and display a QR code.

### Step 2: Choose Testing Platform

#### Option A: iOS Simulator
```bash
npm run ios
```
- Requires macOS and Xcode
- iOS Simulator will launch automatically

#### Option B: Android Emulator
```bash
npm run android
```
- Requires Android Studio and emulator setup
- Android emulator will launch automatically

#### Option C: Web Browser
```bash
npm run web
```
- Opens in default web browser
- Good for quick UI testing

#### Option D: Physical Device
- Install Expo Go app from App Store/Play Store
- Scan QR code from development server
- Test on actual device hardware

### Step 3: Authentication Testing

#### Test 1: Google Authentication
1. **Navigate to Login Page**
   - App should load with unified login interface
   - Should see Google login button and email/password form

2. **Google Sign-In Flow**
   - Tap "Google로 로그인" button
   - Should open Google OAuth flow
   - Complete authentication in browser/webview
   - Should return to app and redirect to news feed

3. **Verify Authentication State**
   - Check if user data appears in profile
   - Verify persistence (close/reopen app)
   - Test automatic redirect to news feed

#### Test 2: Email Authentication
1. **Email Registration**
   - Tap "계정이 없으신가요? 회원가입" link
   - Fill out registration form
   - Verify form validation
   - Complete registration process
   - Should redirect to keyword setup

2. **Email Login**
   - Use registered email credentials
   - Test form validation
   - Verify successful login
   - Check redirect to news feed

#### Test 3: Authentication State Management
1. **Auto-Redirect Testing**
   - Authenticated users should go directly to news feed
   - Unauthenticated users should see login page
   - Test app restart behavior

2. **Logout Testing**
   - Access three-dot menu (⋮) in news feed
   - Tap "로그아웃"
   - Should return to login page
   - Verify session cleanup

### Step 4: Core Functionality Testing

#### Test 1: News Feed
1. **Load News Feed** (`/summary`)
   - Should display news articles (if backend connected)
   - Test pagination (scroll to bottom)
   - Verify loading states

2. **Navigation Menu**
   - Tap three-dot menu (⋮) in top-right
   - Verify menu options: Profile, Keywords, Logout, Exit
   - Test each menu item navigation

3. **Link Testing**
   - Tap "Link" on news articles
   - Should open external browser/webview

#### Test 2: Profile Management
1. **Profile Display** (`/profile`)
   - Verify user information display
   - Check profile picture (Google users)
   - Verify account type badges
   - Test account metadata display

2. **Profile Actions**
   - Test "키워드 설정" button
   - Test logout from profile page

#### Test 3: Keyword Management
1. **Keyword Interface** (`/keyword`)
   - Verify keyword input form
   - Test adding new keywords
   - Test deleting keywords
   - Verify empty state message

2. **Backend Integration**
   - Test keyword save/load (if backend connected)
   - Verify error handling for network issues

### Step 5: UI/UX Testing

#### Test 1: Dark/Light Mode
1. **Theme Switching**
   - Change device theme settings
   - Verify app responds to system theme
   - Check all screens for proper theme support

#### Test 2: Responsive Design
1. **Screen Orientation**
   - Test portrait and landscape modes
   - Verify layout adjustments

2. **Different Screen Sizes**
   - Test on various device sizes
   - Check text readability and button sizes

#### Test 3: Loading States
1. **Authentication Loading**
   - Verify loading indicators during sign-in
   - Check loading states for authentication checks

2. **Data Loading**
   - Test news feed loading
   - Verify keyword management loading states

### Step 6: Error Handling Testing

#### Test 1: Network Errors
1. **Offline Testing**
   - Disable internet connection
   - Test app behavior
   - Verify error messages

2. **Backend Errors**
   - Test with invalid backend URL
   - Verify graceful error handling

#### Test 2: Authentication Errors
1. **Invalid Credentials**
   - Test with wrong email/password
   - Verify error messages

2. **Google Auth Errors**
   - Test Google auth cancellation
   - Test network errors during OAuth

### Step 7: Performance Testing

#### Test 1: App Startup
1. **Cold Start**
   - Measure app launch time
   - Check for smooth animations

2. **Authentication Check**
   - Verify quick authentication state resolution
   - Test auto-redirect speed

#### Test 2: Navigation Performance
1. **Screen Transitions**
   - Test navigation speed between screens
   - Verify smooth animations

2. **State Management**
   - Check for unnecessary re-renders
   - Verify efficient state updates

## Common Issues and Solutions

### Authentication Issues

#### Google Sign-In Not Working
- **Symptoms**: Google login button not responding or errors
- **Solutions**:
  - Check Google OAuth client IDs in `.env`
  - Verify Firebase configuration
  - Check bundle ID/package name matches Google Console
  - Verify SHA-1 fingerprints (Android)

#### Email Authentication Errors
- **Symptoms**: Registration/login failures
- **Solutions**:
  - Check Firebase Authentication settings
  - Verify email/password provider is enabled
  - Check network connectivity
  - Verify Firebase configuration

### UI Issues

#### Layout Problems
- **Symptoms**: Overlapping elements, incorrect sizing
- **Solutions**:
  - Check safe area handling
  - Verify responsive design implementation
  - Test on different screen sizes

#### Theme Issues
- **Symptoms**: Incorrect colors in dark/light mode
- **Solutions**:
  - Check theme detection logic
  - Verify color scheme usage in styles
  - Test system theme changes

### Navigation Issues

#### Redirect Loops
- **Symptoms**: App continuously redirecting between screens
- **Solutions**:
  - Check authentication state logic
  - Verify useEffect dependencies
  - Check navigation guard conditions

#### Menu Not Working
- **Symptoms**: Three-dot menu not opening or responding
- **Solutions**:
  - Check modal state management
  - Verify TouchableOpacity implementation
  - Test on different platforms

## Testing Completion Checklist

- [ ] Google authentication works end-to-end
- [ ] Email registration and login work correctly
- [ ] Authentication state persists across app restarts
- [ ] All protected routes properly redirect unauthorized users
- [ ] News feed displays and functions correctly
- [ ] Profile page shows correct user information
- [ ] Keyword management works (add/delete keywords)
- [ ] Three-dot menu functions properly
- [ ] Dark/light mode switching works
- [ ] All error states display appropriate messages
- [ ] App performs well on target devices
- [ ] Navigation flows smoothly between screens

## Reporting Issues

When reporting issues, include:
1. **Device Information**: OS version, device model
2. **Steps to Reproduce**: Detailed reproduction steps
3. **Expected Behavior**: What should happen
4. **Actual Behavior**: What actually happens
5. **Screenshots/Videos**: Visual evidence of issues
6. **Console Logs**: Any error messages from development console
7. **Environment**: Development vs production build

## Next Steps After Testing

1. **Issue Resolution**: Fix any bugs found during testing
2. **Performance Optimization**: Address any performance issues
3. **User Experience Improvements**: Enhance based on testing feedback
4. **Production Build**: Prepare for production deployment
5. **Store Submission**: Submit to app stores if ready