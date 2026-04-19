# 🗳️ Online Voting Application

An Android-based online voting system with **face recognition authentication**, built as a System Development Project. The app enables admins to create and manage polls while ensuring that only verified, registered voters can cast ballots — authenticated through real-time facial recognition.

## 📋 Table of Contents

- [Features](#-features)
- [Tech Stack](#-tech-stack)
- [Architecture Overview](#-architecture-overview)
- [Project Structure](#-project-structure)
- [Prerequisites](#-prerequisites)
- [Getting Started](#-getting-started)
- [How It Works](#-how-it-works)
- [Screens & Navigation](#-screens--navigation)
- [APK Download](#-apk-download)
- [Contributors](#-contributors)

## ✨ Features

### 🔐 Admin
- Sign up and sign in via Firebase Authentication
- Create new polls with unique auto-generated IDs
- Add and manage candidates for each poll
- Register voters with name, email, and a reference face photo
- Edit or delete existing polls, candidates, and voter records
- View poll results at any time

### 🧑‍💼 Voter
- Sign in using email and poll-specific password
- **Face recognition verification** before casting a vote — live camera capture is compared against the registered reference image
- Browse the candidate list for an active poll
- Cast exactly **one vote per poll** (duplicate voting is prevented)
- View live poll results

## ⚙️ How It Works

### Poll ID System
Each poll is assigned a unique 8-character ID derived from a UUID at creation time. This ID doubles as the voter's poll-specific password, ensuring voters can only access polls they are explicitly registered for.

### Face Recognition Flow
1. The voter signs in with their email and poll password.
2. The app loads the voter's **registered reference image** from Firebase Storage.
3. The voter captures a **live selfie** via the device camera.
4. Both images are base64-encoded and passed to the Python `scripts.py` module via Chaquopy.
5. Facial recognition runs a comparison with a tolerance of `0.05`.
6. If faces match, the voter is granted access to the voting screen; otherwise, access is denied.

### One-Vote Enforcement
A voter's status for each poll is stored in Firebase Realtime Database. Once a vote is cast, the value is flipped to prevent re-voting — the UI shows a "You have already voted" message on any subsequent attempt.

## 🛠️ Tech Stack

| Layer | Technology |
|---|---|
| Platform | Android (Java) |
| Face Recognition | Python — `face_recognition`, `OpenCV`, `NumPy` |
| Python–Android Bridge | [Chaquopy](https://chaquo.com/chaquopy/) |
| Backend / Database | Firebase Realtime Database |
| Authentication | Firebase Authentication |
| File Storage | Firebase Storage |
| Image Loading | Picasso |
| Build System | Gradle |


## 🏗️ Architecture Overview

```
┌─────────────────────────────────────┐
│            Android App (Java)        │
│                                     │
│   ┌──────────┐     ┌─────────────┐  │
│   │  Admin   │     │    Voter    │  │
│   │  Module  │     │   Module   │  │
│   └────┬─────┘     └──────┬──────┘  │
│        │                  │         │
│        ▼                  ▼         │
│   ┌──────────────────────────────┐  │
│   │     Chaquopy Python Bridge   │  │
│   │  (face_recognition + OpenCV) │  │
│   └──────────────────────────────┘  │
│        │                  │         │
└────────┼──────────────────┼─────────┘
         ▼                  ▼
   ┌─────────────────────────────┐
   │          Firebase           │
   │  Auth · Database · Storage  │
   └─────────────────────────────┘
```


## 📁 Project Structure

```
Online-Voting-Application/
├── app/
│   ├── build.gradle                   # Dependencies, Chaquopy & Firebase config
│   ├── google-services.json           # Firebase project config
│   └── src/main/
│       ├── AndroidManifest.xml
│       ├── java/com/project/VotingFinal2/
│       │   ├── MainActivity.java          # Entry point — Admin / Voter choice
│       │   ├── AdminSignUp.java
│       │   ├── AdminSignIn.java
│       │   ├── AdminFirstPage.java        # Admin dashboard
│       │   ├── CreatePoll.java            # Create new poll with UUID
│       │   ├── PollList.java              # List of all polls
│       │   ├── PollListCustomAdapter.java
│       │   ├── EditPoll.java
│       │   ├── CreatePollCandidate.java   # Add candidate to a poll
│       │   ├── EditCandidateList.java
│       │   ├── CandidateListCustomAdapter.java
│       │   ├── CreateNewVoter.java        # Register voter with face photo
│       │   ├── EditVoterList.java
│       │   ├── VoterListCustomAdapter.java
│       │   ├── VoterSignIn.java           # Voter login
│       │   ├── VoterRecognitionInput.java # Face recognition screen
│       │   ├── VoterFirstPage.java        # Vote or see results
│       │   ├── CandidateListForVoter.java # Casting vote
│       │   ├── VoterSeePollResult.java    # Result display
│       │   ├── VoterSeeCandidateListCustomAdapter.java
│       │   ├── PollMetaData.java          # Poll data model
│       │   ├── NewVoterData.java          # Voter data model
│       │   ├── NewCandidateData.java      # Candidate data model
│       │   └── SecondActivity.java
│       ├── python/
│       │   └── scripts.py                # Face recognition logic (Python)
│       └── res/                          # Layouts, drawables, menus, themes
├── build.gradle
├── settings.gradle
└── gradle.properties
```


## ✅ Prerequisites

- **Android Studio** (Arctic Fox or later recommended)
- **Android SDK** — compile SDK 29, min SDK 21
- **Python 3.8 (32-bit)** — required by Chaquopy for local builds
- A **Firebase** project with Authentication, Realtime Database, and Storage enabled
- **NDK** configured for `armeabi-v7a` and `x86` ABI filters (Chaquopy requirement)


## 🚀 Getting Started

### 1. Clone the repository

```bash
git clone https://github.com/FaiyajAmin/Online-Voting-Application.git
cd Online-Voting-Application
```

### 2. Configure Firebase

- Create a Firebase project at [console.firebase.google.com](https://console.firebase.google.com)
- Enable **Email/Password** authentication
- Enable **Realtime Database** and **Firebase Storage**
- Download `google-services.json` and replace the existing one at `app/google-services.json`

### 3. Configure Chaquopy (Python path)

In `app/build.gradle`, update the Python executable path to match your local Python 3.8 (32-bit) installation:

```groovy
python {
    buildPython "C:/path/to/your/python38-32/python.exe"
}
```

### 4. Open in Android Studio

Open the cloned directory in Android Studio and let Gradle sync. Chaquopy will automatically install the required Python packages (`opencv-contrib-python-headless`, `numpy`, `face_recognition`).

### 5. Build & Run

Connect an Android device or start an emulator, then run the app via Android Studio.

> **Note:** Face recognition requires a physical device with a working rear camera. The feature will not function correctly on emulators.


## 📱 Screens & Navigation

```
MainActivity (Welcome)
├── Admin Sign In → Admin Sign Up
│   └── Admin Dashboard
│       ├── Create Poll → Poll Created → Poll List
│       │   ├── Edit Poll
│       │   ├── Add / Edit Candidates
│       │   └── Add / Edit Voters (with face photo upload)
│       └── View Poll Results
│
└── Voter Sign In
    └── Face Recognition Screen
        └── Voter Dashboard
            ├── Cast Vote (Candidate List)
            └── View Poll Result
```


## 📦 APK Download

A pre-built debug APK is available for direct installation:

📥 [Download APK from Google Drive](https://drive.google.com/drive/folders/16d7Fjzd-IMdiwN6BDfn7nfHov2fIy0Vj?usp=sharing)

> Requires Android 5.0 (Lollipop / API 21) or higher. Allow installation from unknown sources if prompted.


## 👥 Contributors

| Name | GitHub |
|---|---|
| Faiyaj Bin Amin | [@FaiyajAmin](https://github.com/FaiyajAmin) |
| Md. Tahmid Hasan Fuad | [@ffuad](https://github.com/ffuad) |

---
