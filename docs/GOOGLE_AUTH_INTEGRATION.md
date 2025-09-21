# Google Authentication Integration Guide

This guide explains how to set up Google authentication with Firebase in your React Native Expo app with the new unified authentication system.

## What's Been Implemented

### 1. Firebase Configuration Updates (`src/firebaseConfig.ts`)
- Added Google authentication imports from Firebase Auth
- Implemented `useGoogleAuth` hook using Expo's Google authentication provider
- Added `onAuthStateChange` listener for authentication state management
- Created `signOutUser` function for logging out

### 2. Global Authentication State Management (`src/stores/authStore.ts`)
- Zustand-based global state management for authentication
- Centralized user state and loading management
- Automatic Firebase auth state synchronization
- Consistent authentication state across all app components

### 3. Unified Authentication UI (`app/index.tsx`)
- **NEW**: Combined Google and Email authentication in single interface
- Modern UI design with dark/light mode support
- Loading states and error handling
- Automatic navigation after successful authentication
- Link to registration page for new users

### 4. Email Registration System (`app/signup.tsx`)
- **NEW**: Complete email-based registration flow
- Form validation and user-friendly error messages
- Profile setup with display name
- Automatic redirect to keyword setup after registration

### 5. Route Protection System
- **NEW**: Automatic authentication state checking in `app/_layout.tsx`
- Protected routes redirect unauthorized users to login
- Seamless user experience with persistent sessions

### 6. Enhanced User Interface
- **NEW**: Top navigation menu with three-dot button (⋮) in news feed
- **NEW**: Profile page (`app/profile.tsx`) with account details
- **NEW**: Updated keyword management with auth integration
- Modern modal-based menu system

## Setup Instructions

### Step 1: Firebase Console Configuration

1. Go to [Firebase Console](https://console.firebase.google.com/)
2. Select your project
3. Navigate to **Authentication** > **Sign-in method**
4. Enable **Google** as a sign-in provider
5. Note down the Web SDK configuration (you should already have this)

### Step 2: Google Cloud Console Setup

1. Go to [Google Cloud Console](https://console.cloud.google.com/)
2. Select your Firebase project
3. Navigate to **APIs & Services** > **Credentials**
4. Create OAuth 2.0 Client IDs for each platform:

#### For Android:
- Application type: **Android**
- Package name: Get from `app.json` or `app.config.js`
- SHA-1 certificate fingerprint: 
  ```bash
  # For development (use Expo's debug keystore)
  keytool -keystore ~/.android/debug.keystore -list -v -alias androiddebugkey
  # Password: android
  
  # For production (use your release keystore)
  keytool -keystore path/to/release.keystore -list -v -alias your_alias
  ```

#### For iOS:
- Application type: **iOS**
- Bundle ID: Get from `app.json` or `app.config.js`

#### For Web:
- Application type: **Web application**
- Add your authorized redirect URIs

### Step 3: Environment Variables

1. Copy `.env.example` to `.env`
2. Fill in your Google OAuth Client IDs:
   ```env
   EXPO_PUBLIC_GOOGLE_IOS_CLIENT_ID=your_ios_client_id.apps.googleusercontent.com
   EXPO_PUBLIC_GOOGLE_ANDROID_CLIENT_ID=your_android_client_id.apps.googleusercontent.com
   EXPO_PUBLIC_GOOGLE_WEB_CLIENT_ID=your_web_client_id.apps.googleusercontent.com
   ```

### Step 4: App Configuration

Update your `app.config.ts` or `app.json` with Google configuration:

```typescript
export default {
  expo: {
    // ... other config
    plugins: [
      // ... other plugins
      [
        "expo-auth-session",
        {
          scheme: "your-app-scheme"
        }
      ]
    ],
    scheme: "your-app-scheme",
  }
};
```

### Step 5: Platform-Specific Setup

#### Android Setup
1. Add Google Services plugin to `android/app/build.gradle`
2. Place `google-services.json` in `android/app/`

#### iOS Setup  
1. Place `GoogleService-Info.plist` in `ios/YourApp/`
2. Add URL schemes to `ios/YourApp/Info.plist`

## Usage

### New Unified Authentication Flow

The authentication system now uses a centralized Zustand store for state management:

```typescript
import { useAuthStore } from '../src/stores/authStore';
import { useGoogleAuth } from '../src/firebaseConfig';

export default function MyComponent() {
  const { user, signOut } = useAuthStore();
  const { signInWithGoogle } = useGoogleAuth();

  const handleGoogleSignIn = async () => {
    try {
      const user = await signInWithGoogle();
      console.log('Signed in:', user.displayName);
    } catch (error) {
      console.error('Sign-in failed:', error);
    }
  };

  const handleSignOut = async () => {
    try {
      await signOut();
      console.log('Signed out successfully');
    } catch (error) {
      console.error('Sign-out failed:', error);
    }
  };

  return (
    <View>
      {user ? (
        <Text>Welcome, {user.displayName || user.email}!</Text>
      ) : (
        <TouchableOpacity onPress={handleGoogleSignIn}>
          <Text>Sign in with Google</Text>
        </TouchableOpacity>
      )}
    </View>
  );
}
```

### Global Authentication State Management

```typescript
import { useAuthStore } from '../src/stores/authStore';

export default function ProtectedComponent() {
  const { user, isLoading, initialize } = useAuthStore();

  useEffect(() => {
    // Initialize auth state on app start
    initialize();
  }, []);

  useEffect(() => {
    // Redirect if not authenticated
    if (user === null && !isLoading) {
      router.replace('/');
    }
  }, [user, isLoading]);

  if (isLoading) {
    return <ActivityIndicator />;
  }

  if (!user) {
    return null; // Will redirect
  }

  return (
    <View>
      <Text>Protected content for {user.email}</Text>
    </View>
  );
}
```

### Authentication Flow Examples

#### 1. Login Page (`app/index.tsx`)
```typescript
export default function LoginPage() {
  const { user } = useAuthStore();
  const { signInWithGoogle } = useGoogleAuth();

  // Auto-redirect authenticated users
  useEffect(() => {
    if (user) {
      router.replace('/summary');
    }
  }, [user]);

  // Combined Google and Email login interface
  return (
    <View>
      <TouchableOpacity onPress={handleGoogleLogin}>
        <Text>Google로 로그인</Text>
      </TouchableOpacity>

      <TextInput placeholder="이메일" />
      <TextInput placeholder="비밀번호" secureTextEntry />
      <TouchableOpacity onPress={handleEmailLogin}>
        <Text>이메일로 로그인</Text>
      </TouchableOpacity>

      <TouchableOpacity onPress={() => router.push('/signup')}>
        <Text>계정이 없으신가요? 회원가입</Text>
      </TouchableOpacity>
    </View>
  );
}
```

#### 2. Profile Management (`app/profile.tsx`)
```typescript
export default function ProfilePage() {
  const { user, signOut } = useAuthStore();

  const isGoogleUser = user?.providerData?.some(
    provider => provider.providerId === 'google.com'
  );

  return (
    <View>
      <Image source={{ uri: user?.photoURL }} />
      <Text>{user?.displayName || '사용자'}</Text>
      <Text>{user?.email}</Text>

      {isGoogleUser && (
        <View>
          <Text>🔍 Google 계정</Text>
        </View>
      )}

      <TouchableOpacity onPress={signOut}>
        <Text>로그아웃</Text>
      </TouchableOpacity>
    </View>
  );
}
```

## Security Best Practices

1. **Environment Variables**: Never commit actual OAuth client IDs to version control
2. **Firebase Rules**: Configure Firestore security rules based on authentication state
3. **Token Validation**: Always validate tokens on your backend
4. **HTTPS Only**: Use HTTPS in production for redirect URIs

## Troubleshooting

### Common Issues

1. **"Google Sign-In is not ready"**
   - Check that all OAuth client IDs are correctly configured
   - Verify environment variables are loaded

2. **"Invalid package name/Bundle ID"**
   - Ensure the package name/bundle ID matches exactly in Google Cloud Console
   - Check `app.json` or `app.config.js` for correct identifiers

3. **"SHA-1 fingerprint mismatch"**
   - Use the correct keystore for development vs production
   - Register both debug and release SHA-1 fingerprints

4. **"Redirect URI mismatch"**
   - Add all possible redirect URIs to your OAuth client configuration
   - Include both custom schemes and deep link URIs

### Testing

1. Test on both Android and iOS devices/simulators
2. Test with different Google accounts
3. Verify sign-out functionality works properly
4. Test authentication state persistence across app restarts

## Next Steps

1. **Backend Integration**: Set up your backend to verify Firebase ID tokens
2. **User Management**: Implement user profile management features  
3. **Security Rules**: Configure Firebase Security Rules based on authentication
4. **Analytics**: Track authentication events in Firebase Analytics
5. **Error Handling**: Implement more sophisticated error handling and retry logic

## Resources

- [Firebase Auth Documentation](https://firebase.google.com/docs/auth)
- [Expo Auth Session](https://docs.expo.dev/guides/authentication/#google)
- [Google Identity Platform](https://developers.google.com/identity/protocols/oauth2)
- [Firebase Console](https://console.firebase.google.com/)
- [Google Cloud Console](https://console.cloud.google.com/)