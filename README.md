# Mini Project Approval System

A Flutter mobile app for managing mini project approvals in an educational institution.

## Features

### Student Portal
- Register with @pvppcoe.ac.in email (group leader only)
- Submit project proposals with topic and description
- Track project status (Pending/Approved/Declined)
- Resubmit projects if declined with teacher feedback
- View team information and assigned teacher

### Teacher Portal
- Review all project submissions from assigned students
- View project details including domain and similarity score
- Approve or decline projects with feedback
- Real-time updates on pending reviews
- Track all project submissions with status

### Admin Portal
- Add new teachers to the system
- Manage teacher accounts
- View all registered teachers
- Teachers automatically appear in student registration dropdown

## Setup Instructions

### Prerequisites
- Flutter SDK (3.0.0 or higher)
- Android Studio or VS Code with Flutter extensions
- Firebase account

### Firebase Setup

1. Create a new Firebase project at [Firebase Console](https://console.firebase.google.com/)

2. Enable Authentication:
   - Go to Authentication > Sign-in method
   - Enable Email/Password authentication

3. Create Firestore Database:
   - Go to Firestore Database
   - Create database in production mode
   - Start in test mode for development

4. Add Android App:
   - Click "Add app" and select Android
   - Package name: `com.example.mini_project_approval`
   - Download `google-services.json`
   - Place it in `android/app/` directory

5. Get Firebase Configuration:
   - Go to Project Settings > General
   - Scroll to "Your apps" section
   - Copy the configuration values
   - Update `lib/firebase_options.dart` with your values:
     - apiKey
     - appId
     - messagingSenderId
     - projectId
     - storageBucket

### Installation

1. Clone or extract the project

2. Install dependencies:
```bash
flutter pub get
```

3. Place your `google-services.json` in the `android/app/` directory

4. Update Firebase configuration in `lib/firebase_options.dart`

5. Run the app:
```bash
flutter run
```

## Default Credentials

### Admin
- Email: `admin@pvppcoe.ac.in`
- Password: (Set during first login or use your custom password)

### Pre-configured Teachers
- Teacher 1: `teacher1@pvppcoe.ac.in` / Password: `12345678`
- Teacher 2: `teacher2@pvppcoe.ac.in` / Password: `12345678`

Note: Teachers need to be created by logging in with these credentials for the first time, or the admin can add them through the admin portal.

## Usage

### For Students
1. Select "Student" role on the home screen
2. Click "Register" if you're a new user (group leader)
3. Fill in team details and select a teacher
4. After registration, submit your project proposal
5. Track status and resubmit if declined

### For Teachers
1. Select "Teacher" role on the home screen
2. Login with provided credentials
3. View all project submissions
4. Click on a project to review details
5. Approve or decline with feedback

### For Admin
1. Select "Admin" role on the home screen
2. Login with admin credentials
3. Add new teachers with name, email, and password
4. View all registered teachers

## Project Structure

```
lib/
├── main.dart                          # App entry point
├── firebase_options.dart              # Firebase configuration
├── models/                            # Data models
│   ├── student_model.dart
│   ├── teacher_model.dart
│   └── project_model.dart
├── services/                          # Business logic
│   ├── auth_service.dart              # Firebase Auth
│   └── database_service.dart          # Firestore operations
└── screens/                           # UI screens
    ├── role_selection_screen.dart
    ├── login_screen.dart
    ├── student_registration_screen.dart
    ├── student_dashboard_screen.dart
    ├── teacher_dashboard_screen.dart
    └── admin_dashboard_screen.dart
```

## Database Collections

### students
- uid, email, teamLeaderName, teamMembers[], year, semester, teacherUid, teacherName

### teachers
- uid, email, name

### projects
- id, studentUid, studentName, teacherUid, teacherName, topic, description
- status (pending/approved/declined), feedback
- submittedAt, reviewedAt
- year, semester, teamMembers[]
- domain, similarityScore (dummy data for now)

## Notes

- Email validation enforces @pvppcoe.ac.in domain for students and teachers
- Only group leaders register for students
- Project similarity scores and domains are currently dummy data
- Teacher accounts must be created before students can select them
- Two teachers are pre-initialized on app startup

## Future Enhancements

- Real similarity score calculation using ML
- Push notifications for status updates
- File upload support for project documents
- Project category filtering
- Analytics dashboard
- Email verification
