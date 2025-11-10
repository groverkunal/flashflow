# Product Requirements Document: FlashFlow

## Executive Summary

**Product Name**: FlashFlow
**Version**: 1.0
**Last Updated**: November 10, 2025
**Document Owner**: Product Team

### Product Vision
FlashFlow is an AI-powered learning platform that transforms any text content into intelligent, multi-perspective flashcards. By leveraging specialized AI agents with distinct expertise areas, FlashFlow helps users extract maximum learning value from educational materials, articles, documents, and research papers.

### Target Audience
- **Students** (High school, undergraduate, graduate) studying diverse subjects
- **Professionals** learning new skills or preparing for certifications
- **Educators** creating study materials for students
- **Lifelong learners** consuming educational content
- **Researchers** synthesizing complex academic papers

---

## 1. Product Overview

### 1.1 Problem Statement
Traditional learning from text is passive and time-consuming. Learners struggle to:
- Extract key information from lengthy documents
- Identify different types of knowledge (facts, concepts, applications)
- Create effective study materials
- Maintain engagement during review sessions
- Connect information across multiple dimensions (historical, practical, theoretical)

### 1.2 Solution
FlashFlow uses a multi-agent AI system where each agent represents a different cognitive lens or expertise area. Users can select which perspectives matter most for their learning goals, and the AI generates comprehensive flashcards that capture:
- Facts and figures
- Historical context
- Definitions and terminology
- Practical applications
- Analogies and examples
- Theoretical frameworks
- Current relevance
- Future implications

### 1.3 Key Differentiators
- **Multi-Agent Intelligence**: 10 specialized AI agents vs. single generic AI
- **Customizable Perspectives**: Users control which cognitive lenses to apply
- **Agent Attribution**: Each flashcard is tagged with its generating agent
- **Dynamic Agent Instructions**: Users can customize agent behaviors
- **Auto-Scroll Review**: Hands-free flashcard review mode
- **Minimalist Design**: Focus-first interface for distraction-free learning

---

## 2. Core Features

### 2.1 Content Input System

#### 2.1.1 File Upload
**Priority**: P0 (MVP)

**Description**: Users can upload text-based files for processing.

**Acceptance Criteria**:
- Support for .txt file format (currently implemented)
- File size validation (implicit via browser)
- Clear feedback on successful upload with filename display
- Error handling for unsupported file types
- File content automatically loads into processing pipeline

**Technical Implementation**:
- FileReader API for client-side file processing
- Component: `InputArea.tsx:22-44`
- Supported MIME type: `text/plain`

**Future Enhancements** (P2):
- PDF parsing support (.pdf)
- Microsoft Word document support (.docx)
- Rich text format (.rtf)
- Maximum file size: 10MB
- Progress indicator for large files

#### 2.1.2 Text Paste Interface
**Priority**: P0 (MVP)

**Description**: Users can directly paste text content into a textarea.

**Acceptance Criteria**:
- Multi-line textarea supporting up to 50,000 characters
- Real-time text length validation
- Paste detection to clear file upload state
- Disabled state during processing
- Textarea maintains content until user initiates reset

**Technical Implementation**:
- Controlled React component with state management
- Component: `InputArea.tsx:46-55`
- 8-row textarea with resize disabled for consistent UX

### 2.2 AI Agent System

#### 2.2.1 Agent Profiles
**Priority**: P0 (MVP)

**Description**: 10 specialized AI agents, each with unique expertise and cognitive focus.

**Agent Roster**:

| Agent ID | Name | Icon | Primary Function |
|----------|------|------|------------------|
| fact-finder | FactFinder | Calculator | Extracts numerical data, statistics, key facts |
| historian | Historian | Landmark | Identifies historical events, timelines, contexts |
| news-hound | NewsHound | Newspaper | Connects to current events and recent developments |
| lexicographer | Lexicographer | BookOpenText | Defines terminology, jargon, key vocabulary |
| illustrator | Illustrator | Lightbulb | Provides concrete examples and illustrations |
| analogist | Analogist | Brain | Creates analogies and metaphors for complex concepts |
| pragmatist | Pragmatist | Wrench | Highlights practical applications and real-world uses |
| futurist | Futurist | TrendingUp | Discusses implications, trends, future scenarios |
| theorist | Theorist | FlaskConical | Explains underlying theories, principles, frameworks |
| summarizer | Summarizer | ScrollText | Creates concise summaries of main ideas |

**Acceptance Criteria**:
- Each agent has unique ID, name, description, and icon
- Agents are consistently defined across frontend and AI flows
- Default selection: First 3 agents (FactFinder, Historian, NewsHound)
- Visual representation through Lucide icons

**Technical Implementation**:
- Configuration: `src/config/agent-profiles.ts`
- Type-safe agent profile interface
- Lucide React icons for visual identity

#### 2.2.2 Agent Selection Interface
**Priority**: P0 (MVP)

**Description**: Visual interface for selecting which AI agents to apply to the text.

**Acceptance Criteria**:
- Display all 10 agents as selectable cards
- Show agent name, icon, and description
- Toggle selection on/off with visual feedback
- Minimum 1 agent must be selected to generate flashcards
- Default: First 3 agents pre-selected
- Disabled state during flashcard generation

**Technical Implementation**:
- Component: `AgentSelector.tsx`
- Individual agent cards: `AgentCard.tsx`
- State management via parent component callbacks

**User Interaction Flow**:
1. User sees 10 agent cards in a grid layout
2. Click to toggle selection (adds/removes from selectedAgents array)
3. Selected agents show visual distinction (border/background change)
4. Selection persists until user resets the session

#### 2.2.3 Customizable Agent Descriptions
**Priority**: P1 (Nice-to-have)

**Description**: Users can edit agent descriptions to fine-tune their behavior.

**Acceptance Criteria**:
- Inline editing of agent descriptions
- Changes apply only to current session
- Reset to defaults when starting new session
- Visual indicator for modified descriptions
- Character limit: 500 characters per description

**Technical Implementation**:
- State: `agentDescriptions` record in `page.tsx:27`
- Callback: `handleAgentDescriptionChange` in `page.tsx:43-48`
- Editable text field within each AgentCard

**Use Cases**:
- Focus FactFinder on "only financial metrics"
- Instruct Historian to "emphasize 20th century events"
- Tell Pragmatist to "focus on software engineering applications"

### 2.3 Flashcard Generation Engine

#### 2.3.1 AI Flow Architecture
**Priority**: P0 (MVP)

**Description**: Server-side AI flow that orchestrates multi-agent flashcard generation.

**Technical Stack**:
- **AI Framework**: Google Genkit 1.8.0
- **Model**: Google AI Gemini 2.0 Flash
- **Schema Validation**: Zod
- **Execution**: Server-side action via Next.js App Router

**Input Schema**:
```typescript
{
  text: string,
  agents: Array<{
    id: string,
    name: string,
    description: string
  }>
}
```

**Output Schema**:
```typescript
{
  flashcards: Array<{
    term: string,
    definition: string,
    example?: string,
    relatedConcepts?: string[],
    agentTag?: string  // e.g., "#fact-finder"
  }>
}
```

**Technical Implementation**:
- Flow definition: `src/ai/flows/generate-flashcards.ts:70-86`
- Prompt template: `src/ai/flows/generate-flashcards.ts:47-68`
- Genkit configuration: `src/ai/genkit.ts`

**Acceptance Criteria**:
- Successfully generates flashcards from provided text
- Each flashcard includes term and definition (required)
- Examples and related concepts are optional but encouraged
- Each flashcard is tagged with primary agent ID
- Handles empty agent arrays gracefully with warning
- Error handling for AI API failures
- Response time: < 30 seconds for 1000-word input

#### 2.3.2 Prompt Engineering
**Priority**: P0 (MVP)

**Description**: AI prompt that enables multi-agent perspective generation.

**Prompt Strategy**:
- **Role Assignment**: AI acts as "team of expert assistants"
- **Agent Context**: Each agent's role explicitly described in prompt
- **Collaboration Directive**: Agents "collaborate and leverage unique perspectives"
- **Attribution Requirement**: Primary agent must be identified for each flashcard
- **Quality Guidelines**: "Clear, concise, easy to understand"
- **Focus Directive**: "Extract meaningful information based on agent roles"

**Prompt Template** (Handlebars):
```handlebars
You are a team of expert AI assistants...

Available AI Agents and their roles:
{{#each agents}}
- {{this.name}} (ID: {{this.id}}): {{this.description}}
{{/each}}

[Instructions for flashcard generation and agentTag attribution]

Use the following text to generate the flashcards:
{{{text}}}
```

**Acceptance Criteria**:
- Generates diverse flashcards representing different agent perspectives
- Agent tags accurately reflect primary expertise applied
- Output quality is consistent across different text types
- Flashcards avoid duplication of similar concepts
- Maintains factual accuracy from source text

### 2.4 Flashcard Display System

#### 2.4.1 Flashcard Viewer Component
**Priority**: P0 (MVP)

**Description**: Primary interface for viewing generated flashcards.

**Acceptance Criteria**:
- Displays total flashcard count in header
- Each flashcard shows term, definition, example (if available), related concepts
- Agent tag displayed as badge/chip
- Scrollable container for multiple flashcards
- Vertical card layout with spacing
- Empty state when no flashcards generated

**Technical Implementation**:
- Component: `FlashcardViewer.tsx`
- Individual card: `FlashcardItem.tsx`
- Scroll container: Radix UI ScrollArea

**Layout**:
- Header: Title + flashcard count + auto-scroll controls
- Scroll speed controls
- Scrollable content area with 4px spacing between cards
- Sticky position on larger screens (>768px)

#### 2.4.2 Individual Flashcard Design
**Priority**: P0 (MVP)

**Description**: Single flashcard visual presentation.

**Acceptance Criteria**:
- Card-based design with subtle shadow
- Clear typography hierarchy (term > definition > example)
- Agent tag displayed prominently with color/icon
- Related concepts displayed as comma-separated list or chips
- Readable on mobile and desktop
- Sufficient padding for comfortable reading

**Visual Hierarchy**:
1. **Term** (h3, semibold, larger font)
2. **Agent Tag** (small badge with # prefix and agent icon)
3. **Definition** (body text, primary description)
4. **Example** (italic or distinct styling, optional)
5. **Related Concepts** (small tags or list, optional)

#### 2.4.3 Auto-Scroll Feature
**Priority**: P1 (Enhancement)

**Description**: Automated scrolling for hands-free flashcard review.

**Acceptance Criteria**:
- Play/Pause button to control auto-scroll
- Adjustable scroll speed (1-10 scale)
- Automatically stops when user manually scrolls
- Loops to top when reaching bottom
- Disabled when only 1 flashcard exists
- Speed slider persists during session

**Technical Implementation**:
- Component: `FlashcardViewer.tsx:19-170`
- Interval-based scrolling at 50ms ticks
- Scroll speed: 1-10 pixels per tick
- Manual scroll detection via event listeners
- Programmatic scroll flagging to prevent false detection

**User Interaction Flow**:
1. User clicks Play button
2. Cards begin auto-scrolling at set speed
3. User can adjust speed with slider (doesn't stop scroll)
4. If user manually scrolls/touches, auto-scroll stops
5. When reaching bottom, seamlessly loops to top
6. User clicks Pause to stop

### 2.5 Session Management

#### 2.5.1 Reset Functionality
**Priority**: P0 (MVP)

**Description**: Ability to start a new flashcard generation session.

**Acceptance Criteria**:
- "Create New Set" button visible after flashcards generated
- Clears all flashcards
- Resets input area (text and file)
- Resets agent selection to default (first 3)
- Resets agent descriptions to defaults
- Forces re-mount of InputArea component

**Technical Implementation**:
- Function: `resetProcess` in `page.tsx:88-94`
- Key-based component reset: `inputAreaKey` state
- Resets 5 state variables simultaneously

**User Interaction Flow**:
1. After flashcards displayed, left column shows "Flashcards Ready!" message
2. User clicks "Create New Set" button
3. UI returns to input state
4. Previous flashcards cleared from view
5. User can start fresh generation

#### 2.5.2 Loading States
**Priority**: P0 (MVP)

**Description**: Visual feedback during AI processing.

**Acceptance Criteria**:
- Loading indicator on "Create Flashcards" button
- Disabled state on all inputs during processing
- Loading text: "Generating..."
- Spinner icon animation
- Loading state persists until flashcards returned or error occurs

**Technical Implementation**:
- React useTransition hook: `isPending` state
- Loading indicator: Loader2 icon from Lucide
- Conditional rendering based on `isPending` boolean

### 2.6 Feedback & Notifications

#### 2.6.1 Toast Notifications
**Priority**: P0 (MVP)

**Description**: Contextual feedback messages for user actions and system events.

**Toast Scenarios**:

| Scenario | Type | Message |
|----------|------|---------|
| Flashcards successfully generated | Success | "Success! Generated {N} flashcards." |
| No text provided | Error | "Input Required - Please provide text to generate flashcards." |
| No agents selected | Error | "Agent Required - Please select at least one AI agent." |
| AI generation error | Error | "Error - Failed to generate flashcards. Please try again." |
| No flashcards generated | Warning | "No Flashcards Generated - The AI couldn't generate flashcards. Try different agents or refine your text." |
| Unsupported file type | Error | "File Type Not Supported - Currently, only .txt files can be automatically processed." |

**Technical Implementation**:
- Hook: `useToast` from `@/hooks/use-toast`
- Component: `Toaster.tsx`
- Properties: title, description, variant (default/destructive)

**Acceptance Criteria**:
- Toast appears at consistent location (typically top-right or bottom)
- Auto-dismisses after 5 seconds
- User can manually dismiss
- Multiple toasts stack properly
- Destructive variant shows red/error styling

---

## 3. User Experience

### 3.1 User Flow Diagram

```
[Landing Page]
     |
     v
[Input Text] --> Upload .txt file
     |        └─> Paste text
     v
[Select Agents] --> Toggle agents (min 1)
     |           └─> Optionally edit descriptions
     v
[Click "Create Flashcards"]
     |
     v
[Processing...] (Loading state)
     |
     v
[Display Flashcards] --> Scroll/Review
     |                 └─> Enable auto-scroll
     |                 └─> Adjust scroll speed
     v
[Start Over?] --> Yes: Reset and return to input
              └─> No: Continue reviewing
```

### 3.2 Layout Structure

#### Desktop (≥768px)
```
┌─────────────────────────────────────────────────┐
│                    Header                        │
│              FlashFlow + Tagline                 │
└─────────────────────────────────────────────────┘
┌──────────────────┬──────────────────────────────┐
│                  │                              │
│  Input Column    │    Flashcard Column          │
│  (7/12 width)    │    (5/12 width, sticky)      │
│                  │                              │
│  - Input Area    │    [Flashcard Viewer]        │
│  - Agent Select  │    - Header + Controls       │
│  - Generate Btn  │    - Scroll Speed            │
│                  │    - Flashcards (scrollable) │
│  OR              │                              │
│                  │                              │
│  - "Ready!" Card │                              │
│  - Reset Button  │                              │
│                  │                              │
└──────────────────┴──────────────────────────────┘
│                    Footer                        │
└─────────────────────────────────────────────────┘
```

#### Mobile (<768px)
```
┌─────────────────┐
│     Header      │
└─────────────────┘
│  Input Area     │
│  Agent Selector │
│  Generate Btn   │
│                 │
│  OR             │
│                 │
│  Ready! + Reset │
├─────────────────┤
│  Flashcard      │
│  Viewer         │
│  (if generated) │
└─────────────────┘
│     Footer      │
└─────────────────┘
```

### 3.3 Design System

#### 3.3.1 Color Palette
**Brand Colors** (from blueprint.md):
- **Primary**: Muted Violet `#9D69A3` - Main interactive elements, focus states
- **Background**: Light Grey-Violet `#F4F0F5` - Page background
- **Accent**: Dusty Rose `#C1858E` - Complementary accents, highlights

**Semantic Colors** (Tailwind/Shadcn):
- **Foreground**: High contrast text
- **Muted**: Secondary text, subtle backgrounds
- **Card**: Component backgrounds
- **Border**: Dividers, outlines
- **Destructive**: Error states

#### 3.3.2 Typography
**Fonts**:
- **Headlines**: 'Belleza' sans-serif (per blueprint)
  - Currently using Next.js default (Inter/System)
  - Recommendation: Integrate Belleza via Google Fonts
- **Body**: 'Alegreya' serif (per blueprint)
  - Currently using Next.js default
  - Recommendation: Integrate Alegreya for better reading experience

**Type Scale**:
- **h1** (Page Title): 4xl/5xl (36px/48px) - "FlashFlow"
- **h2** (Section Headers): 2xl (24px) - "Flashcards Ready!"
- **h3** (Card Titles): xl (20px) - Input Area titles
- **Body**: base (16px) - Main content
- **Small**: sm (14px) - Helper text, descriptions
- **Tiny**: xs (12px) - Labels, metadata

#### 3.3.3 Component Library
**UI Framework**: Radix UI + Shadcn/ui

**Core Components Used**:
- Button (variants: default, outline, destructive)
- Card (CardHeader, CardTitle, CardDescription, CardContent)
- Input (file, text)
- Textarea
- Label
- ScrollArea
- Slider
- Toast/Toaster
- Progress (referenced in blueprint, not yet implemented)

#### 3.3.4 Icons
**Library**: Lucide React

**Icon Usage**:
- UploadCloud - File upload
- Sparkles - Generate/Magic action
- RotateCcw - Reset/Start over
- Loader2 - Loading state
- Play/Pause - Auto-scroll controls
- Agent-specific icons (Calculator, Landmark, Newspaper, etc.)

### 3.4 Responsive Behavior

#### Breakpoints
- **Mobile**: < 768px
- **Desktop**: ≥ 768px

#### Mobile Adaptations
- Single column layout (stacked)
- Reduced padding and spacing
- Smaller text sizes (4xl → text-4xl on mobile)
- Full-width buttons
- Flashcard viewer height: calc(100vh - 220px)
- Hide text labels on small auto-scroll buttons (show icon only)

#### Desktop Enhancements
- Two-column grid layout
- Sticky flashcard viewer (top: 6rem)
- Max height for flashcard viewer: 75vh
- Increased max-width: screen-xl (1280px)
- Show text labels on buttons

---

## 4. Technical Architecture

### 4.1 Technology Stack

#### Frontend
- **Framework**: Next.js 15.3.3 (App Router, React Server Components)
- **Language**: TypeScript 5
- **UI Library**: React 18.3.1
- **Styling**: Tailwind CSS 3.4.1
- **Component Library**: Radix UI + Shadcn/ui
- **Icons**: Lucide React
- **Form Handling**: React Hook Form + Zod validation
- **Build Tool**: Turbopack (Next.js 15 default)

#### Backend / AI
- **AI Framework**: Google Genkit 1.8.0
- **AI Model**: Google AI Gemini 2.0 Flash (`googleai/gemini-2.0-flash`)
- **Schema Validation**: Zod 3.24.2
- **Runtime**: Node.js 20+

#### Deployment
- **Platform**: Firebase App Hosting (indicated by `apphosting.yaml`)
- **Environment**: Firebase 11.8.1 SDK

#### Development Tools
- **Dev Server**: Next.js dev with Turbopack on port 9002
- **Genkit Dev UI**: `genkit start` with tsx watch mode
- **Type Checking**: tsc --noEmit
- **Linting**: Next.js ESLint

### 4.2 Project Structure

```
flashflow/
├── src/
│   ├── app/                    # Next.js App Router
│   │   ├── layout.tsx         # Root layout
│   │   ├── page.tsx           # Main FlashFlow page
│   │   ├── globals.css        # Global styles
│   │   └── favicon.ico
│   ├── ai/                     # AI/Genkit flows
│   │   ├── genkit.ts          # Genkit configuration
│   │   ├── dev.ts             # Genkit dev entry
│   │   └── flows/
│   │       ├── generate-flashcards.ts  # Main flashcard flow
│   │       └── summarize-document.ts   # Future feature
│   ├── components/
│   │   ├── flashflow/         # App-specific components
│   │   │   ├── AgentCard.tsx
│   │   │   ├── AgentSelector.tsx
│   │   │   ├── AppProgressBar.tsx
│   │   │   ├── FlashcardItem.tsx
│   │   │   ├── FlashcardViewer.tsx
│   │   │   └── InputArea.tsx
│   │   └── ui/                # Shadcn/ui components (30+ components)
│   ├── config/
│   │   └── agent-profiles.ts  # Agent definitions
│   ├── hooks/
│   │   ├── use-mobile.tsx
│   │   └── use-toast.ts
│   └── lib/
│       └── utils.ts           # Utility functions (cn, etc.)
├── docs/
│   └── blueprint.md           # Design specifications
├── .idx/                       # IDX configuration
├── .vscode/                    # VS Code settings
├── public/                     # Static assets
├── apphosting.yaml            # Firebase hosting config
├── components.json            # Shadcn/ui config
├── next.config.ts
├── tailwind.config.ts
├── tsconfig.json
└── package.json
```

### 4.3 Data Flow

#### Flashcard Generation Flow

```
┌──────────────┐
│  User Input  │
│  (text +     │
│   agents)    │
└──────┬───────┘
       │
       v
┌─────────────────────────────────────────┐
│  Frontend (page.tsx)                    │
│  - handleGenerateFlashcards()           │
│  - Validates input                      │
│  - Transforms agent IDs → full profiles │
└──────┬──────────────────────────────────┘
       │ (Server Action call)
       v
┌─────────────────────────────────────────┐
│  Server (generate-flashcards.ts)        │
│  - Receives: { text, agents }           │
│  - Validates with Zod schemas           │
└──────┬──────────────────────────────────┘
       │
       v
┌─────────────────────────────────────────┐
│  Genkit Flow (generateFlashcardsFlow)   │
│  - Constructs prompt with agent context │
│  - Calls flashcardAgentPrompt           │
└──────┬──────────────────────────────────┘
       │
       v
┌─────────────────────────────────────────┐
│  Google AI API (Gemini 2.0 Flash)       │
│  - Processes prompt                     │
│  - Generates structured response        │
│  - Returns JSON matching Zod schema     │
└──────┬──────────────────────────────────┘
       │
       v
┌─────────────────────────────────────────┐
│  Server Response                        │
│  - Returns: { flashcards: [...] }      │
│  - Each flashcard includes agentTag     │
└──────┬──────────────────────────────────┘
       │
       v
┌─────────────────────────────────────────┐
│  Frontend State Update                  │
│  - setFlashcards(result.flashcards)    │
│  - Triggers FlashcardViewer render      │
│  - Shows success toast                  │
└─────────────────────────────────────────┘
```

### 4.4 State Management

**Strategy**: React local state (useState, useTransition)

**Key State Variables** (page.tsx):
- `textToProcess`: Current input text
- `selectedAgentIds`: Array of selected agent IDs
- `agentDescriptions`: Record<string, string> for custom descriptions
- `flashcards`: Array of generated flashcard objects
- `isPending`: Loading state from useTransition
- `inputAreaKey`: Unique key for InputArea re-mounting

**State Flow**:
- Parent component (page.tsx) manages all core state
- Child components receive state + callbacks via props
- Callbacks bubble up state changes (handleTextReady, handleToggleAgent)
- useTransition wraps async flashcard generation for React 18 concurrent features

### 4.5 API Integration

#### Google AI (Gemini)
**Configuration**:
- Plugin: `@genkit-ai/googleai`
- Model: `googleai/gemini-2.0-flash`
- Authentication: API key via environment variables (assumed)

**API Call Pattern**:
```typescript
const {output} = await flashcardAgentPrompt(input);
return output!;
```

**Error Handling**:
- Try-catch wrapper in handleGenerateFlashcards
- Console error logging
- User-facing error toast
- Empty flashcard array on failure

### 4.6 Environment Configuration

**Required Environment Variables**:
```
GOOGLE_AI_API_KEY=<your-gemini-api-key>
```

**Development Ports**:
- Next.js dev server: 9002
- Genkit dev UI: Default port (4000)

---

## 5. Non-Functional Requirements

### 5.1 Performance
- **Initial Page Load**: < 2 seconds (First Contentful Paint)
- **Flashcard Generation**: < 30 seconds for ~1000 words
- **Auto-scroll**: Smooth 60fps scrolling
- **UI Responsiveness**: All interactions < 100ms feedback

### 5.2 Scalability
- **Input Text Limit**: 50,000 characters (recommendation)
- **Max Flashcards**: 100 per generation (recommendation)
- **Concurrent Users**: Support via serverless architecture (Firebase/Vercel)
- **API Rate Limits**: Respect Google AI API quotas

### 5.3 Accessibility (WCAG 2.1 AA)
- **Keyboard Navigation**: All interactive elements accessible via keyboard
- **Screen Readers**: Proper ARIA labels (currently: aria-label on auto-scroll buttons)
- **Color Contrast**: Minimum 4.5:1 for body text, 3:1 for large text
- **Focus Indicators**: Visible focus states on all interactive elements
- **Semantic HTML**: Proper heading hierarchy (h1 → h2 → h3)

**Current Accessibility Features**:
- Labeled form inputs
- Disabled state communication
- aria-label on auto-scroll controls
- Lucide icons with descriptive meanings

**Recommendations for Improvement**:
- Add skip-to-content link
- Implement focus trap in modals (if added)
- Add loading announcements for screen readers
- Provide alternative text for all icons
- Test with screen readers (NVDA, JAWS, VoiceOver)

### 5.4 Browser Support
- **Modern Browsers**: Chrome 90+, Firefox 88+, Safari 14+, Edge 90+
- **Mobile Browsers**: iOS Safari 14+, Chrome Mobile 90+
- **No support**: IE11

### 5.5 Security
- **Input Sanitization**: User text should be sanitized before AI processing
- **API Key Protection**: Never expose Google AI API keys to client
- **Content Security Policy**: Implement CSP headers
- **HTTPS Only**: Enforce secure connections in production
- **Rate Limiting**: Implement per-user generation limits to prevent abuse

### 5.6 Privacy
- **Data Storage**: Currently no persistent storage (session-only)
- **Analytics**: No analytics currently implemented
- **Third-party Services**: Google AI API (text sent to Google)
- **User Data**: Input text is NOT stored server-side
- **Cookies**: Session cookies only (Next.js default)

**Privacy Recommendations**:
- Add privacy policy page
- Inform users that text is sent to Google AI
- Provide option to delete generated flashcards
- Consider local processing for sensitive content

---

## 6. Future Enhancements

### 6.1 Planned Features (P1)

#### 6.1.1 Document Summarization
**File**: `src/ai/flows/summarize-document.ts` (exists but not implemented)

**Description**: Generate overall document summary before flashcard creation.

**Benefits**:
- Helps users understand document structure
- Provides context for flashcards
- Enables better agent selection

#### 6.1.2 PDF & DOCX Support
**Description**: Parse and extract text from PDF and Word documents.

**Technical Approach**:
- PDF: pdf-parse or pdfjs
- DOCX: mammoth.js
- Server-side processing to handle large files

#### 6.1.3 Flashcard Export
**Description**: Export flashcards to various formats.

**Formats**:
- Anki deck (.apkg)
- Quizlet compatible (CSV)
- JSON download
- Printable PDF

#### 6.1.4 Progress Bar
**File**: `AppProgressBar.tsx` (exists but not used)

**Description**: Visual progress indicator during flashcard generation.

**Implementation**: Show percentage complete, estimated time remaining

### 6.2 Advanced Features (P2)

#### 6.2.1 User Accounts & Persistence
- Save flashcard sets to user profile
- History of generated sets
- Custom agent profiles
- Personal learning analytics

#### 6.2.2 Collaborative Features
- Share flashcard sets via link
- Public flashcard library
- Community-contributed agents
- Rating and feedback system

#### 6.2.3 Advanced Learning Features
- Spaced repetition algorithm
- Quiz mode with multiple choice
- Flashcard difficulty rating
- Learning streaks and gamification
- AI-powered practice recommendations

#### 6.2.4 Enhanced Agent System
- User-created custom agents
- Agent chaining (agents build on each other's output)
- Agent priority weighting
- Multi-language agent support
- Domain-specific agent packs (e.g., "Medical School Pack")

#### 6.2.5 Mobile Applications
- Native iOS app (React Native / Swift)
- Native Android app (React Native / Kotlin)
- Offline mode with local AI models
- Push notifications for spaced repetition

#### 6.2.6 Integrations
- Browser extension for one-click flashcard generation
- Chrome extension to capture web articles
- Integration with note-taking apps (Notion, Obsidian, Roam)
- LMS integrations (Canvas, Blackboard, Moodle)
- API for third-party developers

### 6.3 Technical Improvements (P2)

#### 6.3.1 Performance Optimization
- Implement streaming responses for faster perceived loading
- Cache common queries
- Optimize Genkit flow for parallel agent processing
- Edge function deployment for lower latency

#### 6.3.2 Advanced AI Features
- Support for multiple AI models (Claude, GPT-4, Local LLMs)
- Model selection based on use case
- Ensemble approach (multiple models vote)
- Fine-tuned models for specific domains

#### 6.3.3 Analytics & Monitoring
- User behavior analytics
- AI generation quality metrics
- Error tracking (Sentry)
- Performance monitoring (Vercel Analytics)
- A/B testing framework

---

## 7. Success Metrics

### 7.1 Key Performance Indicators (KPIs)

#### User Engagement
- **Daily Active Users (DAU)**: Target 1,000 within 6 months
- **Avg. Flashcards Generated per User**: Target 3+ per session
- **Session Duration**: Target 5-10 minutes
- **Return Rate**: 30% of users return within 7 days

#### Product Quality
- **Flashcard Generation Success Rate**: > 95%
- **Avg. Flashcards per Generation**: 10-20
- **User Satisfaction (CSAT)**: > 4.0/5.0
- **AI Generation Time**: < 20 seconds average

#### Technical Performance
- **API Error Rate**: < 1%
- **Page Load Time (P95)**: < 3 seconds
- **Uptime**: 99.5%

#### Business Metrics (if applicable)
- **User Acquisition Cost (CAC)**
- **Conversion Rate** (free to paid, if freemium model)
- **Monthly Recurring Revenue (MRR)**
- **Churn Rate**: < 5% monthly

### 7.2 Success Criteria for MVP

**Definition of Success**:
1. Users can successfully generate flashcards from text input
2. At least 3 different agents produce distinct, valuable flashcards
3. UI is intuitive enough that 80% of users complete their first generation without help
4. 50% of users who generate flashcards once, return to generate more
5. Positive qualitative feedback from initial user testing (10+ users)

---

## 8. Risks & Mitigations

### 8.1 Technical Risks

| Risk | Impact | Likelihood | Mitigation |
|------|--------|------------|------------|
| **Google AI API downtime** | High | Low | Implement fallback to cached responses; show graceful error messages |
| **API rate limits exceeded** | High | Medium | Implement per-user rate limiting; queue system for high load |
| **Slow AI response times** | Medium | Medium | Set timeout limits; show progress indicators; optimize prompts |
| **Poor flashcard quality** | High | Medium | Extensive prompt engineering; user feedback loop; manual review |
| **Browser compatibility issues** | Low | Low | Test on major browsers; use progressive enhancement |

### 8.2 Product Risks

| Risk | Impact | Likelihood | Mitigation |
|------|--------|------------|------------|
| **Users don't understand agent concept** | High | Medium | Improve onboarding; add tooltips; provide examples |
| **Generated flashcards are too generic** | High | Medium | Refine prompts; add more specific agents; allow customization |
| **Users expect more formats than .txt** | Medium | High | Prioritize PDF/DOCX support; communicate limitations clearly |
| **Privacy concerns with AI** | Medium | Medium | Add privacy policy; transparent about data handling; offer local options |
| **Competition from established players** | High | High | Focus on unique multi-agent approach; superior UX; niche markets |

### 8.3 Business Risks

| Risk | Impact | Likelihood | Mitigation |
|------|--------|------------|------------|
| **High AI API costs** | High | Medium | Optimize token usage; consider tiered pricing; explore cheaper models |
| **Limited market demand** | High | Low | Conduct user research; validate with beta users; pivot if needed |
| **Difficulty monetizing** | Medium | Medium | Explore freemium, subscription, or B2B models; institutional licensing |

---

## 9. Open Questions & Decisions Needed

### 9.1 Product Questions
1. **Should flashcards be editable by users after generation?**
   - Pros: Empowers users to refine; handles AI errors
   - Cons: Added complexity; potential for user frustration

2. **What is the optimal default number of agents selected?**
   - Current: 3 (FactFinder, Historian, NewsHound)
   - Alternative: Allow user to choose first time, remember preference

3. **Should there be a maximum number of flashcards generated?**
   - Recommended: 100 to prevent overwhelming UI and long load times

4. **How should duplicate or similar flashcards be handled?**
   - Option A: AI deduplication in prompt
   - Option B: Post-processing to merge similar cards
   - Option C: Show all, let user filter

### 9.2 Technical Questions
1. **Should we implement server-side caching of flashcard generations?**
   - Could save API costs for common inputs
   - Privacy implications

2. **What's the authentication strategy for future user accounts?**
   - Firebase Auth (already using Firebase)
   - NextAuth.js
   - Auth0 or similar

3. **Should we stream AI responses for perceived performance?**
   - Genkit supports streaming
   - Would require UI updates to handle partial responses

### 9.3 Design Questions
1. **Should we implement the custom fonts from blueprint.md?**
   - Belleza for headlines
   - Alegreya for body
   - Trade-off: Brand identity vs. bundle size

2. **Is the current two-column layout optimal?**
   - Alternative: Single column with tabs
   - Alternative: Wizard-style stepper

3. **Should flashcards support rich media?**
   - Images, diagrams, embedded videos
   - Requires more complex flashcard schema

---

## 10. Appendix

### 10.1 Glossary

- **Agent**: A specialized AI persona with expertise in a specific area (e.g., FactFinder, Historian)
- **Flashcard**: A learning unit with a term, definition, and optional metadata
- **Agent Tag**: Hashtag identifier showing which agent primarily created a flashcard
- **Genkit**: Google's AI framework for building AI-powered applications
- **Flow**: A Genkit concept representing an AI workflow with inputs, processing, and outputs
- **Server Action**: Next.js feature allowing direct server function calls from client components

### 10.2 References

**External Documentation**:
- [Next.js 15 Documentation](https://nextjs.org/docs)
- [Google Genkit Docs](https://firebase.google.com/docs/genkit)
- [Gemini API Reference](https://ai.google.dev/docs)
- [Radix UI Documentation](https://www.radix-ui.com/primitives/docs/overview/introduction)
- [Tailwind CSS Docs](https://tailwindcss.com/docs)
- [Shadcn/ui Components](https://ui.shadcn.com/)

**Internal Documentation**:
- Design Blueprint: `docs/blueprint.md`
- Agent Configuration: `src/config/agent-profiles.ts`
- Main Flow: `src/ai/flows/generate-flashcards.ts`

### 10.3 Revision History

| Version | Date | Author | Changes |
|---------|------|--------|---------|
| 1.0 | 2025-11-10 | Claude | Initial comprehensive PRD based on codebase analysis |

### 10.4 Contact & Feedback

**Product Owner**: [To be assigned]
**Technical Lead**: [To be assigned]
**Design Lead**: [To be assigned]

**Feedback**: [Create issue in repository or contact product team]

---

## Document Status: **DRAFT v1.0**

This PRD is based on analysis of the FlashFlow codebase as of November 10, 2025. It serves as both documentation of the current implementation and a roadmap for future development.

**Next Steps**:
1. Review and validation by product team
2. User testing to validate assumptions
3. Prioritization of P1 and P2 features
4. Assignment of owners for open questions
5. Creation of implementation roadmap with timelines
