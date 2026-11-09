# Asif GBSS Kalabord website

A static GitHub Pages school website with a Firebase-powered admin dashboard.

## Files
- `index.html` — public school website
- `admin.html` — secure sign-in and content dashboard
- `firebase-config.js` — Firebase web app configuration (fill this in)

## One-time setup required
GitHub Pages serves static files. To publish notices and photos for every visitor, this site uses Firebase (free Spark plan to start) for login, database and image storage.

1. Create a Firebase project at https://console.firebase.google.com/.
2. Add a Web app and copy its Firebase configuration into `firebase-config.js`.
3. In Firebase Authentication, enable Email/Password sign-in and create your admin user. Do not enable public sign-up.
4. Create a Firestore database and a Storage bucket.
5. Firestore Rules (replace the admin email):
```
rules_version = '2';
service cloud.firestore {
  match /databases/{database}/documents {
    function isAdmin() {
      return request.auth != null && request.auth.token.email == "YOUR_ADMIN_EMAIL";
    }
    match /site/{document} { allow read: if true; allow write: if isAdmin(); }
    match /notices/{notice} { allow read: if true; allow create, update, delete: if isAdmin(); }
    match /photos/{photo} { allow read: if true; allow create, update, delete: if isAdmin(); }
  }
}
```
6. Storage Rules:
```
rules_version = '2';
service firebase.storage {
  match /b/{bucket}/o {
    function isAdmin() {
      return request.auth != null && request.auth.token.email == "YOUR_ADMIN_EMAIL";
    }
    match /school-gallery/{fileName} {
      allow read: if true;
      allow write: if isAdmin() && request.resource.size < 5 * 1024 * 1024
                   && request.resource.contentType.matches('image/.*');
    }
  }
}
```
7. Fill in `firebase-config.js` and commit it. Then enable GitHub Pages: Settings → Pages → Deploy from a branch → main → /(root) → Save.

Admin dashboard: /admin.html

Firebase free quotas apply. Never put passwords or service-account keys in website files.
