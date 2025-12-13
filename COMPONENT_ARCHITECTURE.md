# Component Architecture & Relationships

## Component Dependency Graph

```mermaid
graph TD
    A[App.tsx] --> B[Index.tsx]
    A --> C[Dashboard.tsx]
    A --> D[Chat.tsx]
    A --> E[Appointments.tsx]
    A --> F[Setup.tsx]
    
    B --> G[Hero.tsx]
    B --> H[Features.tsx]
    
    C --> I[NextAppointmentCard.tsx]
    C --> J[TrackerWorkspace.tsx]
    
    D --> K[ChatInterface.tsx]
    K --> L[GenieChatWidget.tsx]
    K --> M[TypingIndicator.tsx]
    K --> N[ExploreToolsSidebar.tsx]
    K --> O[PremiumToolsSidebar.tsx]
    K --> P[TrackerWorkspace.tsx]
    K --> Q[NotificationSettings.tsx]
    
    P --> R[FeedingTracker.tsx]
    P --> S[NappyTracker.tsx]
    P --> T[SleepTracker.tsx]
    P --> U[PumpTracker.tsx]
    P --> V[GrowthTracker.tsx]
    P --> W[MoodTracker.tsx]
    P --> X[TemperatureTracker.tsx]
    P --> Y[MedicineVaccineTracker.tsx]
    P --> Z[ViewModeToggle.tsx]
    P --> AA[ResizableDivider.tsx]
    
    S --> AB[StoolTypeDialog.tsx]
    
    E --> AC[AddAppointmentDialog.tsx]
    E --> AD[AppointmentDetailDialog.tsx]
    
    F --> AE[ProfileSetup.tsx]
    
    K --> AF[useVoiceChat Hook]
    K --> AG[useTimeGreeting Hook]
    K --> AH[useDevUnlock Hook]
    
    N --> AH
    
    style A fill:#F13FBE,color:#fff
    style K fill:#4ECDC4,color:#fff
    style P fill:#FF6B6B,color:#fff
```

## Data Flow Diagram

```mermaid
graph LR
    subgraph "User Interface"
        UI[React Components]
    end
    
    subgraph "State Management"
        LS[LocalStorage]
        RQ[React Query]
        ST[React State]
    end
    
    subgraph "API Layer"
        SC[Supabase Client]
        EF[Edge Functions]
    end
    
    subgraph "External Services"
        AI[Lovable AI]
        VO[ElevenLabs]
        DB[(PostgreSQL)]
    end
    
    UI --> LS
    UI --> ST
    UI --> RQ
    RQ --> SC
    SC --> DB
    SC --> EF
    EF --> AI
    EF --> VO
    EF --> DB
    
    style UI fill:#F13FBE,color:#fff
    style DB fill:#3ECF8E,color:#fff
    style AI fill:#FF6B6B,color:#fff
```

## Tracker System Architecture

```mermaid
graph TB
    subgraph "Tracker Workspace"
        TW[TrackerWorkspace.tsx]
        VM[ViewModeToggle]
        RD[ResizableDivider]
    end
    
    subgraph "Tracker Components"
        FT[FeedingTracker]
        NT[NappyTracker]
        ST[SleepTracker]
        PT[PumpTracker]
        GT[GrowthTracker]
        MT[MoodTracker]
        TT[TemperatureTracker]
        MVT[MedicineVaccineTracker]
    end
    
    subgraph "Data Layer"
        BL[(baby_logs Table)]
        PR[(profiles Table)]
        CH[(children Table)]
    end
    
    subgraph "Advice System"
        GA[genie-advice Function]
        INS[AI Insights]
    end
    
    TW --> FT
    TW --> NT
    TW --> ST
    TW --> PT
    TW --> GT
    TW --> MT
    TW --> TT
    TW --> MVT
    
    FT --> BL
    NT --> BL
    ST --> BL
    PT --> BL
    GT --> BL
    MT --> BL
    TT --> BL
    MVT --> BL
    
    FT --> PR
    NT --> PR
    ST --> PR
    
    FT --> CH
    NT --> CH
    ST --> CH
    
    BL --> GA
    GA --> INS
    
    style TW fill:#F13FBE,color:#fff
    style BL fill:#3ECF8E,color:#fff
    style GA fill:#FF6B6B,color:#fff
```

## Chat System Flow

```mermaid
sequenceDiagram
    participant U as User
    participant CI as ChatInterface
    participant LS as LocalStorage
    participant SC as Supabase Client
    participant EF as Edge Function
    participant AI as Lovable AI
    
    U->>CI: Type Message
    CI->>LS: Save to Conversation
    CI->>SC: Get User Profile
    SC-->>CI: Profile Data
    CI->>EF: Send Message + Profile
    EF->>AI: Stream Request
    AI-->>EF: Stream Response
    EF-->>CI: Stream Chunks
    CI->>LS: Update Conversation
    CI-->>U: Display Message
    
    Note over CI,AI: Real-time Streaming
    Note over LS: Conversation Persistence
```

## Authentication & User Flow

```mermaid
stateDiagram-v2
    [*] --> Landing: Visit App
    Landing --> Setup: New User
    Landing --> Dashboard: Returning User
    Setup --> Profile: Step 1
    Profile --> Children: Step 2
    Children --> Preferences: Step 3
    Preferences --> Complete: Step 4
    Complete --> Dashboard: Finish Setup
    Dashboard --> Chat: Open Chat
    Dashboard --> Trackers: Open Tracker
    Dashboard --> Appointments: View Appointments
    Chat --> Trackers: Quick Action
    Trackers --> Chat: Return to Chat
    [*] --> Chat: Direct Chat Access
```

## Database Relationships

```mermaid
erDiagram
    profiles ||--o{ children : "has"
    profiles ||--o{ baby_logs : "creates"
    profiles ||--o{ appointments : "schedules"
    profiles ||--o{ genie_messages : "receives"
    profiles ||--o{ med_plans : "has"
    profiles ||--o{ vaccines : "schedules"
    
    children ||--o{ baby_logs : "logs for"
    children ||--o{ milestones : "achieves"
    children ||--o{ med_plans : "medication for"
    children ||--o{ vaccines : "vaccines for"
    
    profiles {
        string user_id PK
        string first_name
        json notification_prefs
        string timezone
    }
    
    children {
        string id PK
        string user_id FK
        string name
        date dob
        string status
    }
    
    baby_logs {
        string id PK
        string user_id FK
        string type
        timestamp at
        json data
    }
    
    appointments {
        string id PK
        string user_id FK
        timestamp date_time
        string type
    }
```

## Key Integration Points

```mermaid
graph TB
    subgraph "Frontend"
        REACT[React App]
    end
    
    subgraph "Supabase Services"
        AUTH[Authentication]
        DB[Database]
        STORAGE[File Storage]
        REALTIME[Realtime]
        FUNCTIONS[Edge Functions]
    end
    
    subgraph "AI Services"
        LOVABLE[Lovable AI<br/>Gemini 2.5 Flash]
        ELEVEN[ElevenLabs<br/>Voice AI]
    end
    
    REACT --> AUTH
    REACT --> DB
    REACT --> STORAGE
    REACT --> REALTIME
    REACT --> FUNCTIONS
    
    FUNCTIONS --> LOVABLE
    FUNCTIONS --> ELEVEN
    
    DB --> REALTIME
    
    style REACT fill:#F13FBE,color:#fff
    style LOVABLE fill:#FF6B6B,color:#fff
    style ELEVEN fill:#4ECDC4,color:#fff
```

---

## Component Categories

### **Page Components (6)**
- Index, Dashboard, Chat, Appointments, Setup, NotFound

### **Core UI Components (18)**
- ChatInterface, TrackerWorkspace, ProfileSetup, MilestoneTracker, etc.

### **Tracker Components (8)**
- All specialized tracker components

### **Appointment Components (3)**
- Add, Detail, Card components

### **Custom Hooks (3)**
- useVoiceChat, useTimeGreeting, useDevUnlock

### **Utility Libraries (5)**
- bubbles, celebration, floatingHearts, genieAdvice, utils

### **Backend Functions (5)**
- chat, dashboard-insights, elevenlabs-session, genie-advice, medicine-reminders

---

**Total Components: ~46 major components/files**

