# Gatherly

A black-and-white community discovery app built with React, TypeScript, Firebase Authentication, Cloud Firestore, Cloud Storage, and Firebase Cloud Functions.

## Start locally

1. Install Node.js 22 or newer and Firebase CLI.
2. Create a Firebase project. Enable **Email/Password** under Authentication, create a Firestore database, and create a Storage bucket.
3. Copy `.env.example` to `.env.local` and fill in the Firebase Web app configuration from Firebase Console → Project settings → Your apps.
4. Install frontend dependencies with `npm install`.
5. Start the UI using `npm run dev`.

The development server runs at the URL printed by Vite. Without the Firebase web configuration, the app displays a setup screen rather than fake data.

## Deploy Firebase backend

1. Install function dependencies with `npm install --prefix functions`.
2. Sign in to Firebase CLI using `firebase login`, then select your project with `firebase use --add`.
3. Deploy Firestore rules, Storage rules, indexes, and Cloud Functions with `firebase deploy --only firestore,storage,functions`.
4. Deploy the website with `npm run build` followed by `firebase deploy --only hosting`.

Functions run on Node.js 22. Firebase CLI may request billing-enabled project resources for Cloud Functions and Storage event processing.

## Provision the first administrator

There is no public admin registration. First create an account through the website. Then, from a trusted local terminal:

1. Create/download a Firebase service-account credential with permission to manage Firebase Authentication.
2. Set `GOOGLE_APPLICATION_CREDENTIALS` to that credential file and `ADMIN_EMAIL` to the existing account email.
3. Run `npm run admin:provision`.
4. Sign out and back in, then use `/admin/login` or `/admin/dashboard`.

Never commit the service-account file. The admin UI checks the Firebase ID token's server-issued `admin` custom claim. Privileged callables re-check that claim on every request.

## Implemented data and security

- Firebase email/password sign-in; user profiles are created in Firestore with a UID-suffixed username reservation.
- User follows are stored as paired follower/following documents and protected by reciprocal-write rules; self-follow is rejected. Counts are queried from Firestore, not hardcoded.
- Public communities are discoverable. Private communities expose only a limited preview; join attempts create pending memberships and do not grant access. Membership counts are updated by a trusted Cloud Function.
- Public posts support image uploads, live appreciation counts, comments, sharing, and following creators. Following feed queries content authored by followed users only.
- Firebase rules protect users, follows, community membership, private posts, comments, likes, settings, and report records. Storage rules limit uploads to image MIME types and size; a storage-triggered function checks image file signatures and removes mismatches.
- Administrator portal at `/admin/login` and `/admin/dashboard` uses Firebase custom claims. Admin callables support account suspension/restoration, permanent account/community deletion, report resolution, sensitive user search, and audited branding changes.
- Branding settings are read from Firestore so the saved site name and logo update across the app.

## Firebase configuration

Frontend variables are listed in `.env.example` and must be supplied in `.env.local`. No Firebase credentials are bundled. The admin provisioning script uses Application Default Credentials through `GOOGLE_APPLICATION_CREDENTIALS` and the `ADMIN_EMAIL` environment variable.

## Current scope

This is a working Firebase-backed starting website, not a claim that every item in the original feature brief is complete. In particular, edit-community/member moderation screens, private-profile follow requests, and a full visitor-facing profile directory still need implementation before this should be considered feature-complete. Review and deploy the Firestore/Storage rules to the Firebase project before using real accounts.
