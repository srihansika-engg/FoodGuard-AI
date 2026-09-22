# FoodGuard AI 🥬

> **Smart Living Track · iQOO Hackathon 2026**
> **Zero Food Waste. Zero Cloud Leaks. 100% On-Device Intelligence.**

FoodGuard AI is a **privacy-first, on-device Android pantry assistant** designed to help users track food, identify items that should be used first, and generate practical recipes from ingredients already available at home.

The project combines **computer vision, local pantry intelligence, expiry-aware prioritization, and on-device AI** into a single offline-first experience.

---

## 🚨 The Problem

Food waste often begins with something simple: people lose track of what they already have.

```text
Food purchased
      ↓
Stored at home
      ↓
Forgotten / overlooked
      ↓
Expiry approaches
      ↓
Food gets wasted
```

At the same time, many AI-powered applications depend on cloud services for processing household data, which can introduce connectivity requirements, latency, and privacy concerns.

### Our Approach

```text
SEE
 ↓
STORE
 ↓
UNDERSTAND
 ↓
USE FIRST
 ↓
COOK
 ↓
WASTE LESS
```

FoodGuard AI turns the pantry itself into the source of context for its recommendations.

---

# 💡 What FoodGuard AI Does

## 1. 📷 Smart Pantry Capture

Users can add food items through camera-assisted recognition or manual input.

The pantry can maintain information such as:

* Food item
* Quantity
* Category
* Purchase date
* Expiry / best-before date
* Storage notes

---

## 2. ⏳ Expiry-Aware Pantry

FoodGuard identifies items that should be consumed first.

Example:

```text
🔴 USE FIRST
Spinach       1 day
Tomato        2 days

🟠 USE SOON
Milk          3 days

🟢 FRESH
Carrot        6 days
Rice          30 days
```

Instead of simply showing everything in the pantry, FoodGuard prioritizes what matters most.

---

## 3. 🤖 On-Device Recipe Intelligence

The recipe engine uses the user's locally stored pantry information to generate recipe suggestions based on what is actually available.

Example:

```json
{
  "available": [
    "tomato",
    "onion",
    "rice",
    "egg"
  ],
  "use_first": [
    "spinach"
  ],
  "constraints": [
    "quick",
    "vegetarian"
  ]
}
```

This structured context can be provided to a small, quantized language model running locally on the device.

---

## 4. 🔐 Privacy-First by Design

The core pantry → recipe workflow is designed to work without sending pantry information to a remote server.

```text
Camera
  ↓
Local Recognition
  ↓
Local Pantry Database
  ↓
Local Context Builder
  ↓
On-Device AI
  ↓
Recipe
```

### Core Design Target

> **The user's pantry data should not need to leave the device to generate useful recommendations.**

---

## 5. 💻 Optional Cross-Device Workflow

An optional iQOO Office Kit integration is planned for sharing generated recipes or meal plans with a connected laptop.

This integration is **not required for the core FoodGuard experience**.

---

# 🧠 System Architecture

```text
                       FOODGUARD AI
┌──────────────────────────────────────────────────────────┐
│                    Android Application                   │
│                                                          │
│  ┌──────────────┐                                        │
│  │   CameraX    │                                        │
│  └──────┬───────┘                                        │
│         ↓                                                │
│  ┌──────────────────────────────┐                        │
│  │ Food / Text Recognition     │                        │
│  └──────────────┬───────────────┘                        │
│                 ↓                                        │
│  ┌──────────────────────────────┐                        │
│  │       Local Pantry           │                        │
│  │       Room / SQLite          │                        │
│  └──────────────┬───────────────┘                        │
│                 ↓                                        │
│  ┌──────────────────────────────┐                        │
│  │     Pantry Context Builder   │                        │
│  │ Available • Expiring • Qty   │                        │
│  └──────────────┬───────────────┘                        │
│                 ↓                                        │
│  ┌──────────────────────────────┐                        │
│  │      On-Device AI Runtime    │                        │
│  │   Quantized Language Model   │                        │
│  │    Snapdragon Acceleration  │                        │
│  └──────────────┬───────────────┘                        │
│                 ↓                                        │
│  ┌──────────────────────────────┐                        │
│  │      Recipe Recommendation   │                        │
│  │  Recipe • Use First • Tips   │                        │
│  └──────────────────────────────┘                        │
│                                                          │
│        Optional iQOO Office Kit Bridge                  │
└──────────────────────────────────────────────────────────┘
```

---

# ⚙️ Technology Stack

| Layer                | Technology                        |
| -------------------- | --------------------------------- |
| Platform             | Android                           |
| Language             | Kotlin                            |
| UI                   | Jetpack Compose                   |
| Camera               | CameraX                           |
| Local Database       | Room / SQLite                     |
| Vision               | ML Kit / MobileNet-class model    |
| AI Runtime           | ExecuTorch                        |
| Model                | Small quantized language model    |
| AI Context           | Structured local pantry context   |
| Target Hardware      | Snapdragon-powered Android device |
| Optional Integration | iQOO Office Kit                   |

---

# 🔄 End-to-End Data Flow

```text
                   USER
                    │
                    ▼
             ┌─────────────┐
             │   Camera    │
             └──────┬──────┘
                    ▼
           Food Recognition
                    │
                    ▼
          Expiry / Item Capture
                    │
                    ▼
          ┌──────────────────┐
          │  Local Pantry DB │
          └────────┬─────────┘
                   ▼
          Expiry Prioritization
                   │
                   ▼
          Pantry Context Builder
                   │
                   ▼
          Quantized Local Model
                   │
                   ▼
          Recipe Generation
                   │
                   ▼
             User Output
```

---

# 🔐 Privacy Architecture

FoodGuard AI follows an **offline-first architecture**.

### Core data stays local

| Data              | Intended Processing     |
| ----------------- | ----------------------- |
| Pantry items      | Local                   |
| Quantity          | Local                   |
| Expiry dates      | Local                   |
| Camera processing | On-device               |
| Pantry context    | On-device               |
| Recipe inference  | On-device               |
| Generated recipe  | Local application state |

### Network Design Goal

**Target: 0 bytes of network egress during the core offline workflow.**

This is treated as an **engineering target that will be verified through device/network testing**, rather than a pre-assumed benchmark.

---

# 🤖 AI Pipeline

```text
Pantry Database
       ↓
Identify Expiring Items
       ↓
Build Structured Context
       ↓
Apply User Preferences
       ↓
Prepare Model Input
       ↓
Quantized On-Device Model
       ↓
Generate Recipe Candidates
       ↓
Validate Against Pantry
       ↓
Display Recommendation
```

The AI layer is intentionally separated from the rest of the application through an abstraction layer so that different mobile inference runtimes or models can be evaluated without redesigning the complete application.

---

# 📱 Example User Journey

### Step 1 — Scan Food

```text
Camera
   ↓
Tomato detected
   ↓
Quantity: 4
Expiry: 2 days
   ↓
Save to Pantry
```

### Step 2 — FoodGuard Prioritizes

```text
USE FIRST
• Spinach
• Tomato

AVAILABLE
• Rice
• Egg
• Onion
```

### Step 3 — User Asks

```text
"What can I cook tonight?"
```

### Step 4 — FoodGuard Responds

```text
Spinach Egg Rice Bowl

Use first:
✓ Spinach
✓ Tomato

Also available:
✓ Rice
✓ Egg
✓ Onion
```

---

# ✈️ Offline Demo

One of the core demonstrations is designed around **Airplane Mode**.

```text
1. Enable Airplane Mode
          ↓
2. Open FoodGuard
          ↓
3. Add / Scan Pantry Items
          ↓
4. View Expiry Priorities
          ↓
5. Generate Recipe
          ↓
6. Display On-Device Result
```

### Demo Proof Points

* Offline core workflow
* Local pantry storage
* On-device AI inference
* Expiry-aware recommendations
* No mandatory cloud API
* Snapdragon-oriented edge architecture

---

# 📊 Engineering Validation

Performance claims will be based on measurements from the actual target device.

| Metric                         | Measurement |
| ------------------------------ | ----------- |
| App cold-start time            | ms          |
| Vision inference latency       | ms/frame    |
| Recipe generation latency      | seconds     |
| Model size                     | MB          |
| Peak memory usage              | MB          |
| Battery impact                 | % / hour    |
| Device temperature             | °C          |
| Offline workflow success rate  | %           |
| Network bytes during core flow | bytes       |

This repository intentionally distinguishes between:

**Design Target** → what we aim to achieve

and

**Measured Result** → what we actually observe

---

# 🏗️ Repository Structure

```text
FoodGuard-AI/
│
├── android/
│   ├── app/
│   │   └── src/
│   │       └── main/
│   │           └── java/
│   │               └── com/
│   │                   └── algomind/
│   │                       └── foodguard/
│   │                           ├── ai/
│   │                           ├── data/
│   │                           ├── vision/
│   │                           └── MainActivity.kt
│   └── README.md
│
├── models/
│   ├── README.md
│   └── model-config.example.json
│
├── bridge/
│   └── README.md
│
├── docs/
│   ├── ARCHITECTURE.md
│   ├── PRIVACY.md
│   ├── DEMO_FLOW.md
│   └── ROADMAP.md
│
├── assets/
│   └── README.md
│
├── .github/
│   └── ISSUE_TEMPLATE/
│       └── bug_report.md
│
├── .gitignore
├── CONTRIBUTING.md
├── LICENSE
└── README.md
```

# 🌱 Expected Impact

FoodGuard AI focuses on a simple everyday loop:

```text
Know what you have
        ↓
Know what expires first
        ↓
Know what to cook
        ↓
Use food on time
        ↓
Reduce avoidable waste
```

The project explores how **edge AI, mobile computer vision, and local structured data** can be combined to solve a practical consumer problem without making cloud processing a requirement.

---

# 👩‍💻 Team AlgoMind

| Member                | Role                                      |
| --------------------- | ----------------------------------------- |
| **Thota Sri Hansika** | Team Lead · Product & System Architecture |
| **Donthula Akshaya**  | Android / AI Engineering                  |
| **Patlolla Geethika** | Vision / Data / Integration               |

---

FoodGuard AI is not designed as just another recipe generator.

Its core idea is:

> **Your pantry already contains the context.**

Instead of asking a remote AI what to cook from a generic list of ingredients, FoodGuard connects:

**what you have + what needs attention + what you can cook**

into one privacy-first, on-device workflow.

---

# 📌 Project Status

**Hackathon Prototype — Active Development**

The repository documents the architecture, implementation plan, experiments, and measured results.

Hardware-specific performance claims, accelerator support, and benchmark values will be added only after validation on the target device.
