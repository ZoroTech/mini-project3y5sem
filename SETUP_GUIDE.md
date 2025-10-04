# Mini Project Approval System - Complete Setup Guide

## Prerequisites

Before you begin, make sure you have the following installed:

1. **Flutter SDK** (3.0.0 or higher)
   - Download from: https://flutter.dev/docs/get-started/install
   - Add Flutter to your PATH

2. **Android Studio** (Latest version)
   - Download from: https://developer.android.com/studio
   - Install Android SDK and command-line tools
   - Set up Android emulator or connect physical device

3. **Firebase Account**
   - Sign up at: https://firebase.google.com/

## Step-by-Step Setup

### 1. Install Flutter Dependencies

Open terminal in the project root directory and run:

```bash
flutter pub get
```

### 2. Firebase Project Setup

#### A. Create Firebase Project

1. Go to [Firebase Console](https://console.firebase.google.com/)
2. Click "Add project"
3. Enter project name: `mini-project-approval` (or your preferred name)
4. Disable Google Analytics (optional)
5. Click "Create project"

#### B. Enable Email/Password Authentication

1. In Firebase Console, go to **Build > Authentication**
2. Click "Get started"
3. Go to "Sign-in method" tab
4. Click on "Email/Password"
5. Enable the first toggle (Email/Password)
6. Click "Save"

#### C. Create Firestore Database

1. In Firebase Console, go to **Build > Firestore Database**
2. Click "Create database"
3. Select "Start in production mode" (we'll add rules next)
4. Choose a Cloud Firestore location (closest to your users)
5. Click "Enable"

#### D. Configure Firestore Rules

In Firestore Database, go to "Rules" tab and replace with:

```javascript
rules_version = '2';
service cloud.firestore {
  match /databases/{database}/documents {
    // Allow authenticated users to read/write their own data
    match /students/{studentId} {
      allow read, write: if request.auth != null && request.auth.uid == studentId;
    }

    // Allow all authenticated users to read teachers
    match /teachers/{teacherId} {
      allow read: if request.auth != null;
      allow write: if false; // Only admin can write through admin portal
    }

    // Projects can be read/written by student owner or assigned teacher
    match /projects/{projectId} {
      allow read, write: if request.auth != null &&
        (resource.data.studentUid == request.auth.uid ||
         resource.data.teacherUid == request.auth.uid);
      allow create: if request.auth != null;
    }
  }
}
```

Click "Publish"

### 3. Add Android App to Firebase

#### A. Register Android App

1. In Firebase Console, click the Android icon to add an Android app
2. Enter package name: `com.example.mini_project_approval`
3. App nickname: `Mini Project Approval` (optional)
4. Debug signing certificate: Leave blank for now
5. Click "Register app"

#### B. Download google-services.json

1. Click "Download google-services.json"
2. Place the file in: `android/app/google-services.json`

**IMPORTANT:** This file contains your Firebase credentials. Keep it secure!

#### C. Get Firebase Configuration

1. In Firebase Console, go to **Project Settings** (gear icon)
2. Scroll down to "Your apps" section
3. Click on your Android app
4. You'll see the Firebase SDK snippet

Copy the following values:
- `apiKey`
- `appId`
- `messagingSenderId`
- `projectId`
- `storageBucket`

### 4. Update Firebase Configuration in Code

Open `lib/firebase_options.dart` and update the Android configuration:

```dart
static const FirebaseOptions android = FirebaseOptions(
  apiKey: 'YOUR_ANDROID_API_KEY',              // Replace this
  appId: '1:YOUR_APP_ID:android:YOUR_APP_ID',  // Replace this
  messagingSenderId: 'YOUR_SENDER_ID',         // Replace this
  projectId: 'YOUR_PROJECT_ID',                // Replace this
  storageBucket: 'YOUR_PROJECT_ID.appspot.com', // Replace this
);
```

### 5. Android SDK Setup

#### A. Create local.properties

1. Copy the example file:
   ```bash
   cp android/local.properties.example android/local.properties
   ```

2. Edit `android/local.properties` and update paths:
   ```properties
   flutter.sdk=/Users/yourusername/flutter
   sdk.dir=/Users/yourusername/Library/Android/sdk
   ```

   **Windows paths example:**
   ```properties
   flutter.sdk=C:\\Users\\yourusername\\flutter
   sdk.dir=C:\\Users\\yourusername\\AppData\\Local\\Android\\Sdk
   ```

**IMPORTANT:** Never commit `local.properties` to version control!

### 6. Initialize Teacher Accounts in Firebase

The app will automatically initialize two teacher accounts in Firestore on first launch:

- **Teacher 1:** teacher1@pvppcoe.ac.in
- **Teacher 2:** teacher2@pvppcoe.ac.in

However, you need to manually create their authentication accounts:

#### Option A: Use Firebase Console

1. Go to Firebase Console > Authentication > Users
2. Click "Add user"
3. Email: `teacher1@pvppcoe.ac.in`
4. Password: `12345678`
5. Click "Add user"
6. Repeat for `teacher2@pvppcoe.ac.in`

#### Option B: Let them self-register (recommended)

The teachers will be able to login and it will automatically create their auth accounts through the app.

### 7. Create Admin Account

The admin email is hardcoded as: `admin@pvppcoe.ac.in`

**Create admin in Firebase:**

1. Go to Firebase Console > Authentication > Users
2. Click "Add user"
3. Email: `admin@pvppcoe.ac.in`
4. Password: Your secure password (minimum 8 characters)
5. Click "Add user"

### 8. Run the App

#### A. Check Flutter Setup

```bash
flutter doctor
```

Fix any issues that appear.

#### B. Connect Device or Start Emulator

**For Physical Device:**
- Enable USB debugging on your Android device
- Connect via USB
- Verify with: `flutter devices`

**For Emulator:**
- Open Android Studio > AVD Manager
- Create/Start an Android Virtual Device

#### C. Run the Application

```bash
flutter run
```

Or use Android Studio:
- Open the project in Android Studio
- Wait for Gradle sync to complete
- Click the "Run" button (green play icon)

### 9. Test the Application

#### Test Student Flow:
1. Select "Student" from role selection
2. Click "Register"
3. Email: `student1@pvppcoe.ac.in`
4. Password: `12345678`
5. Fill in team details
6. Select a teacher
7. Complete registration
8. Submit a project

#### Test Teacher Flow:
1. Select "Teacher" from role selection
2. Login with: `teacher1@pvppcoe.ac.in` / `12345678`
3. View submitted projects
4. Click on a project to review
5. Approve or decline with feedback

#### Test Admin Flow:
1. Select "Admin" from role selection
2. Login with: `admin@pvppcoe.ac.in` / (your password)
3. Add a new teacher
4. Verify teacher appears in the list

## Troubleshooting

### Common Issues

#### 1. "google-services.json not found"
- Ensure `google-services.json` is in `android/app/` directory
- Check the file is not corrupted

#### 2. "Firebase not initialized"
- Verify `google-services.json` is correctly placed
- Check Firebase configuration in `lib/firebase_options.dart`
- Ensure Firebase packages are installed: `flutter pub get`

#### 3. "Build failed"
- Clean the build: `flutter clean`
- Get dependencies: `flutter pub get`
- Try again: `flutter run`

#### 4. "Cannot login"
- Verify user exists in Firebase Authentication
- Check Firestore rules are published
- Ensure internet connection is active

#### 5. "Gradle sync failed"
- Check `android/local.properties` paths are correct
- Verify Android SDK is properly installed
- Try in Android Studio: File > Invalidate Caches and Restart

#### 6. Teacher/Student data not saving
- Check Firestore rules are properly configured
- Verify you have internet connection
- Check Firebase Console > Firestore for data

### Debug Mode

To run in debug mode with verbose logging:

```bash
flutter run -v
```

### Check Logs

View real-time logs:

```bash
flutter logs
```

## Production Deployment

Before deploying to production:

1. **Update Firestore Rules** - Make them more restrictive
2. **Enable Email Verification** in Firebase Authentication
3. **Add proper error handling** for network issues
4. **Implement rate limiting** for API calls
5. **Add analytics** to track usage
6. **Generate signed APK** for release:
   ```bash
   flutter build apk --release
   ```

## Security Notes

- Never commit `google-services.json` to public repositories
- Never commit `local.properties` to version control
- Use environment variables for sensitive data in production
- Implement proper Firestore security rules
- Enable App Check in Firebase for production
- Use strong passwords for admin accounts
- Regularly update Firebase SDK and dependencies

## Support

For issues or questions:
- Check the README.md file
- Review Flutter documentation: https://flutter.dev/docs
- Review Firebase documentation: https://firebase.google.com/docs
- Check Firebase Console logs for errors

## Default Login Credentials Summary

| Role | Email | Password |
|------|-------|----------|
| Admin | admin@pvppcoe.ac.in | (Set by you) |
| Teacher 1 | teacher1@pvppcoe.ac.in | 12345678 |
| Teacher 2 | teacher2@pvppcoe.ac.in | 12345678 |
| Students | (student email)@pvppcoe.ac.in | (Set during registration) |

**Remember:** Change default passwords in production!

---

## Quick Start Checklist

- [ ] Install Flutter SDK
- [ ] Install Android Studio
- [ ] Create Firebase project
- [ ] Enable Email/Password authentication
- [ ] Create Firestore database
- [ ] Configure Firestore rules
- [ ] Add Android app to Firebase
- [ ] Download google-services.json
- [ ] Place google-services.json in android/app/
- [ ] Update firebase_options.dart
- [ ] Create android/local.properties
- [ ] Run flutter pub get
- [ ] Create admin account in Firebase
- [ ] Create teacher accounts in Firebase
- [ ] Run flutter doctor
- [ ] Connect device or start emulator
- [ ] Run flutter run
- [ ] Test all three user flows

Good luck with your Mini Project Approval System! 🚀
