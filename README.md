# Pustakam AI Book Generation Engine - Technical Documentation

## Table of Contents

1. [Executive Summary](https://claude.ai/chat/94fbb564-9726-4dfc-83d3-440aedca56e4#executive-summary)
2. [System Architecture](https://claude.ai/chat/94fbb564-9726-4dfc-83d3-440aedca56e4#system-architecture)
3. [Core Features](https://claude.ai/chat/94fbb564-9726-4dfc-83d3-440aedca56e4#core-features)
4. [Technical Stack](https://claude.ai/chat/94fbb564-9726-4dfc-83d3-440aedca56e4#technical-stack)
5. [Application Flow](https://claude.ai/chat/94fbb564-9726-4dfc-83d3-440aedca56e4#application-flow)
6. [Component Architecture](https://claude.ai/chat/94fbb564-9726-4dfc-83d3-440aedca56e4#component-architecture)
7. [AI Integration & Generation Pipeline](https://claude.ai/chat/94fbb564-9726-4dfc-83d3-440aedca56e4#ai-integration--generation-pipeline)
8. [Data Management & Storage](https://claude.ai/chat/94fbb564-9726-4dfc-83d3-440aedca56e4#data-management--storage)
9. [User Interface & Experience](https://claude.ai/chat/94fbb564-9726-4dfc-83d3-440aedca56e4#user-interface--experience)
10. [Advanced Features](https://claude.ai/chat/94fbb564-9726-4dfc-83d3-440aedca56e4#advanced-features)
11. [Error Handling & Recovery](https://claude.ai/chat/94fbb564-9726-4dfc-83d3-440aedca56e4#error-handling--recovery)
12. [Performance Optimizations](https://claude.ai/chat/94fbb564-9726-4dfc-83d3-440aedca56e4#performance-optimizations)
13. [Security & Privacy](https://claude.ai/chat/94fbb564-9726-4dfc-83d3-440aedca56e4#security--privacy)
14. [Deployment & PWA Capabilities](https://claude.ai/chat/94fbb564-9726-4dfc-83d3-440aedca56e4#deployment--pwa-capabilities)

---

## Executive Summary

**Pustakam** is a sophisticated Progressive Web Application (PWA) that leverages multiple AI providers (Google Gemini, Mistral AI, ZhipuAI) to transform user ideas into comprehensive, structured digital books. The application operates entirely client-side with local-first data storage, ensuring privacy while providing enterprise-grade book generation capabilities.

### Key Capabilities

- **Multi-AI Provider Support**: Seamless integration with 3 major AI providers and 11+ models
- **Intelligent Generation Pipeline**: Structured roadmap creation → module generation → final assembly
- **Advanced Recovery System**: Checkpoint-based recovery with smart retry mechanisms
- **Professional Output**: Markdown and PDF export with publication-ready formatting
- **Offline-First Architecture**: Full PWA support with service worker caching
- **Privacy-Centric**: All data stored locally, API keys never leave the browser

---

## System Architecture

### High-Level Architecture

```
┌─────────────────────────────────────────────────────────────┐
│                     User Interface Layer                     │
│  ┌──────────────┐  ┌──────────────┐  ┌──────────────┐      │
│  │   Sidebar    │  │  Book View   │  │   Settings   │      │
│  │  Component   │  │  Component   │  │    Modal     │      │
│  └──────────────┘  └──────────────┘  └──────────────┘      │
└─────────────────────────────────────────────────────────────┘
                            │
                            ▼
┌─────────────────────────────────────────────────────────────┐
│                   Application State Layer                    │
│  ┌──────────────────────────────────────────────────────┐   │
│  │  React State Management (useState, useEffect, etc.)  │   │
│  │  - Book Projects State                               │   │
│  │  - Generation Status Tracking                        │   │
│  │  - Settings Management                               │   │
│  └──────────────────────────────────────────────────────┘   │
└─────────────────────────────────────────────────────────────┘
                            │
                            ▼
┌─────────────────────────────────────────────────────────────┐
│                     Service Layer                            │
│  ┌──────────────┐  ┌──────────────┐  ┌──────────────┐      │
│  │    Book      │  │     PDF      │  │  Enhancement │      │
│  │   Service    │  │   Service    │  │   Service    │      │
│  └──────────────┘  └──────────────┘  └──────────────┘      │
└─────────────────────────────────────────────────────────────┘
                            │
                            ▼
┌─────────────────────────────────────────────────────────────┐
│                  AI Provider Integration                     │
│  ┌──────────────┐  ┌──────────────┐  ┌──────────────┐      │
│  │   Google     │  │   Mistral    │  │   ZhipuAI    │      │
│  │   Gemini     │  │     AI       │  │     GLM      │      │
│  └──────────────┘  └──────────────┘  └──────────────┘      │
└─────────────────────────────────────────────────────────────┘
                            │
                            ▼
┌─────────────────────────────────────────────────────────────┐
│                   Storage & Persistence                      │
│  ┌──────────────────────────────────────────────────────┐   │
│  │         Browser LocalStorage (storageUtils)          │   │
│  │  - Book Projects                                     │   │
│  │  - API Settings                                      │   │
│  │  - Generation Checkpoints                            │   │
│  │  - User Preferences                                  │   │
│  └──────────────────────────────────────────────────────┘   │
└─────────────────────────────────────────────────────────────┘
```

### Data Flow Architecture

```
User Input → Session Creation → Roadmap Generation → Module Generation → Assembly → Export
     │              │                    │                    │              │         │
     ▼              ▼                    ▼                    ▼              ▼         ▼
  Form Data    Book Project         AI Request         AI Streaming     Final Book  PDF/MD
                 Creation           (with retry)        with Progress    Assembly    Files
```

---

## Core Features

### 1. **Multi-Provider AI Integration**

Pustakam supports three major AI providers with seamless switching:

#### Supported Models

**Google Gemini (Recommended)**

- `gemini-2.5-flash` - Latest flash model, optimal balance
- `gemini-2.5-pro` - Most capable, for complex content
- `gemini-2.0-flash` - Fast generation
- `gemini-1.5-flash` - Previous generation
- `gemini-2.0-flash-lite` - Lightweight option
- `gemini-2.5-flash-lite` - Enhanced lightweight

**Mistral AI**

- `mistral-large-latest` - Most powerful Mistral model
- `mistral-small-latest` - Cost-effective option
- `open-mixtral-8x7b` - Mixture of experts model
- `open-mistral-7b` - Open source base model

**ZhipuAI (Chinese Provider)**

- `glm-4.5-flash` - Fast Chinese language model

#### Model Selection System

```typescript
// From Sidebar.tsx
const modelConfig = {
  google: {
    name: "Google AI",
    icon: GoogleIcon,
    models: [
      { id: 'gemini-2.5-flash', name: 'Gemini 2.5 Flash', description: 'Latest flash model' },
      // ... more models
    ]
  },
  mistral: { /* ... */ },
  zhipu: { /* ... */ }
}
```

Users can switch models mid-generation if failures occur, with the system automatically resuming from the last checkpoint.

### 2. **Intelligent Book Generation Pipeline**

#### Phase 1: Roadmap Generation

```typescript
// User provides:
- Learning Goal (e.g., "Learn Python for Data Science")
- Target Audience (e.g., "Beginners")
- Complexity Level (beginner/intermediate/advanced)
- Preferences (examples, exercises, quizzes)

// AI generates:
- 8-12 structured modules
- Learning objectives for each module
- Estimated time per module
- Overall reading time
- Difficulty assessment
```

#### Phase 2: Module Generation

Each module is generated with:

- **2,000-4,000 words** of comprehensive content
- **Structured sections**: Introduction, Core Concepts, Practical Application, Exercises
- **Context-aware**: References previous modules for continuity
- **Streaming output**: Real-time text generation display
- **Quality validation**: Minimum word count checks

#### Phase 3: Final Assembly

- Introduction generation
- Table of contents creation
- Module integration
- Summary generation
- Glossary extraction
- Metadata addition (word count, generation date, AI model used)

### 3. **Advanced Recovery & Checkpoint System**

The application implements a sophisticated checkpoint system that saves progress after each completed module:

```typescript
interface GenerationCheckpoint {
  bookId: string;
  completedModuleIds: string[];
  failedModuleIds: string[];
  moduleRetryCount: Record<string, number>;
  lastSuccessfulIndex: number;
  timestamp: string;
  totalWordsGenerated: number;
}
```

**Key Recovery Features:**

- **Auto-save after each module**: Progress never lost
- **Pause/Resume functionality**: Close browser, come back later
- **Smart retry logic**: Exponential backoff with jitter
- **User-controlled retry decisions**: Retry with same model, switch models, or skip
- **Rate limit handling**: Automatic detection and intelligent waiting

### 4. **Export & Output Capabilities**

#### Markdown Export

- Clean, formatted markdown with proper headers
- Code blocks with syntax highlighting markers
- Tables, lists, and blockquotes preserved
- Downloadable `.md` file with sanitized filename

#### PDF Export

Premium PDF generation with:

- **Professional cover page** with metadata
- **Interactive table of contents** with page numbers
- **Custom typography** (Roboto font family)
- **Code syntax highlighting** using Courier font
- **Responsive tables** with proper cell padding
- **Header/footer** with book title and page numbers
- **A4 page size** with optimal margins (70pt sides, 85pt top/bottom)

```typescript
// PDF Generation Features
- Cover page with title, word count, module count, date
- Table of contents with hierarchical structure
- Content with proper spacing and typography
- Tables with borders and cell formatting
- Code blocks with monospace font
- Blockquotes with left border
- Page breaks between modules
```

---

## Technical Stack

### Frontend Framework

- **React 18.3.1** - UI rendering with hooks
- **TypeScript 5.5.3** - Type safety and developer experience
- **Vite 5.4.2** - Build tool and development server

### Styling & UI

- **Tailwind CSS 3.4.1** - Utility-first CSS framework
- **Tailwind Typography** - Prose styling for markdown
- **Tailwind Animate** - Animation utilities
- **Lucide React 0.344.0** - Icon library (900+ icons)

### Markdown & Content Processing

- **React Markdown 10.1.0** - Markdown rendering
- **Remark GFM 4.0.1** - GitHub Flavored Markdown support
- **React Syntax Highlighter 15.6.6** - Code syntax highlighting
- **Marked 13.0.1** - Additional markdown parsing

### PDF Generation

- **pdfmake 0.2.10** - Client-side PDF generation
- Custom font integration (Roboto family)
- Virtual file system (VFS) for fonts

### AI Integration

- **@google/generative-ai 0.24.1** - Google Gemini SDK
- Native Fetch API for Mistral and ZhipuAI
- Custom streaming implementation

### Analytics & Monitoring

- **@vercel/analytics 1.1.1** - Usage tracking

### PWA & Service Worker

- Custom service worker (`sw.js`)
- Manifest.json for installability
- Offline-first caching strategy

---

## Application Flow

### 1. Application Initialization

```typescript
// App.tsx - Initialization sequence
useEffect(() => {
  // Load books from localStorage
  const savedBooks = storageUtils.getBooks();
  setBooks(savedBooks);
  
  // Load settings and validate
  const savedSettings = storageUtils.getSettings();
  setSettings(savedSettings);
  
  // Initialize book service with settings
  bookService.updateSettings(savedSettings);
  
  // Set up callbacks
  bookService.setProgressCallback(handleBookProgressUpdate);
  bookService.setGenerationStatusCallback(handleGenerationStatusUpdate);
}, []);
```

### 2. Book Creation Flow

```
User clicks "Create New Book"
        ↓
BookView renders creation form
        ↓
User fills in:
  - Learning Goal
  - Target Audience
  - Complexity Level
  - Preferences
        ↓
handleCreateBookRoadmap() triggered
        ↓
New BookProject created with status: 'planning'
        ↓
bookService.generateRoadmap() called
        ↓
AI generates structured roadmap (8-12 modules)
        ↓
BookProject status → 'roadmap_completed'
        ↓
User views roadmap and clicks "Generate All Modules"
```

### 3. Module Generation Flow

```
handleGenerateAllModules() triggered
        ↓
Check for existing checkpoint
  If found: Ask user to resume or start fresh
        ↓
BookProject status → 'generating_content'
        ↓
For each module in roadmap:
  ┌─────────────────────────────────────┐
  │ generateModuleContentWithRetry()    │
  │   ↓                                 │
  │ Build context-aware prompt          │
  │   ↓                                 │
  │ Call AI with streaming              │
  │   ↓                                 │
  │ Display live text generation        │
  │   ↓                                 │
  │ Validate word count (min 300)       │
  │   ↓                                 │
  │ Save module with status 'completed' │
  │   ↓                                 │
  │ Save checkpoint                     │
  └─────────────────────────────────────┘
        ↓
  If error occurs:
    - Check retry count (max 5)
    - Calculate retry delay
    - Show retry decision UI
    - User chooses: retry/switch model/skip
        ↓
All modules completed
        ↓
BookProject status → 'roadmap_completed'
        ↓
User clicks "Assemble Final Book"
```

### 4. Final Assembly Flow

```
handleAssembleBook() triggered
        ↓
BookProject status → 'assembling'
        ↓
Generate in parallel:
  - Introduction (800-1200 words)
  - Summary (600-900 words)
  - Glossary (20-30 terms)
        ↓
Assemble final book:
  - Cover page with metadata
  - Table of contents
  - Introduction
  - All modules with separators
  - Summary
  - Glossary
        ↓
BookProject status → 'completed'
finalBook field populated
        ↓
User can now:
  - Read in fullscreen mode
  - Download as Markdown
  - Download as PDF
  - View analytics
```

---

## Component Architecture

### 1. **App.tsx** (Root Component)

**Responsibilities:**

- Global state management for books and settings
- Coordinate communication between components
- Handle AI provider switching
- Manage generation lifecycle

**Key State Variables:**

```typescript
const [books, setBooks] = useState<BookProject[]>([]);
const [settings, setSettings] = useState<APISettings>();
const [currentBookId, setCurrentBookId] = useState<string | null>(null);
const [generationStatus, setGenerationStatus] = useState<GenerationStatus>();
const [sidebarOpen, setSidebarOpen] = useState(false);
const [view, setView] = useState<AppView>('list');
```

**Critical Functions:**

- `handleCreateBookRoadmap()` - Initiates book creation
- `handleGenerateAllModules()` - Starts module generation
- `handlePauseGeneration()` - Pauses ongoing generation
- `handleResumeGeneration()` - Resumes from checkpoint
- `handleRetryDecision()` - Handles user retry choices
- `handleModelSwitch()` - Switches AI model mid-generation

### 2. **BookView.tsx** (Main Content Area)

**View States:**

- `list` - Home screen or book list grid
- `create` - Book creation form
- `detail` - Individual book details with tabs

**Detail Tabs:**

- `overview` - Roadmap and generation controls
- `analytics` - Book statistics and insights
- `read` - Fullscreen reading mode

**Key Sub-components:**

- `HomeView` - Welcome screen with call-to-action
- `BookListGrid` - Grid display of all books
- `EmbeddedProgressPanel` - Live generation progress with streaming text
- `RetryDecisionPanel` - User interface for retry decisions
- `ReadingMode` - Fullscreen reader with customization

### 3. **Sidebar.tsx** (Navigation & Model Selection)

**Features:**

- **Book List** with search functionality
- **Model Selector Dropdown** showing all available models grouped by provider
- **Collapsible Design** for desktop (foldable to icon-only view)
- **Settings Access**
- **Creator Attribution**

**Model Switching Logic:**

```typescript
const handleModelChange = (modelId: string, provider: ModelProvider) => {
  // Validate API key exists
  if (!hasApiKeyForProvider(provider)) {
    alert('Please configure API key first');
    onOpenSettings();
    return;
  }
  
  // Update settings
  onModelChange(modelId, provider);
  setModelDropdownOpen(false);
};
```

**Responsive Behavior:**

- **Mobile**: Full-screen overlay with backdrop
- **Tablet**: Slide-in panel
- **Desktop**: Persistent sidebar with fold/unfold

### 4. **SettingsModal.tsx** (Configuration)

**Three Tabs:**

1. **API Keys Tab**
    
    - Input fields for all three providers
    - Show/hide password toggle
    - Connection status indicators
    - Direct links to get API keys
2. **Data Tab**
    
    - Export all data as JSON (books + settings)
    - Import data with conflict resolution:
        - **Merge mode**: Add new books, preserve existing
        - **Replace mode**: Complete data replacement
    - Storage statistics
    - Danger zone: Clear all data
3. **About Tab**
    
    - App version and features
    - Creator information
    - Technology stack overview

### 5. **BookAnalytics.tsx** (Insights)

**Analytics Displayed:**

- **Total Words** with localized number formatting
- **Reading Time** (calculated at 250 words/minute)
- **Complexity Level** (beginner/intermediate/advanced)
    - Determined by keyword analysis
- **Key Topics** extracted from module titles
- **Module Count**

**Downloadable Materials:**

- **Progress Tracker** - Markdown checklist of all modules
- **Study Summary** - Key objectives and next steps

### 6. **GenerationProgressPanel.tsx** (Progress Tracking)

**Real-time Displays:**

- Overall progress bar
- Current module with live streaming text
- Statistics grid:
    - Completed modules
    - Failed modules
    - Total words generated
    - Words per minute
    - Average time per module
    - Estimated time remaining

**Event Log System:**

- Timestamped log entries
- Log types: info, success, warn, error
- Downloadable full log for debugging
- Show/hide toggle

**Actions:**

- Pause generation
- Resume generation
- Cancel generation

---

## AI Integration & Generation Pipeline

### AI Provider Abstraction

The `bookService` provides a unified interface for all AI providers:

```typescript
class BookGenerationService {
  private async generateWithAI(
    prompt: string, 
    bookId?: string, 
    onChunk?: (chunk: string) => void
  ): Promise<string> {
    // Route to appropriate provider
    switch (this.settings.selectedProvider) {
      case 'google': 
        return this.generateWithGoogle(prompt, signal, onChunk);
      case 'mistral': 
        return this.generateWithMistral(prompt, signal, onChunk);
      case 'zhipu': 
        return this.generateWithZhipu(prompt, signal, onChunk);
    }
  }
}
```

### Google Gemini Implementation

```typescript
private async generateWithGoogle(
  prompt: string, 
  signal?: AbortSignal, 
  onChunk?: (chunk: string) => void
): Promise<string> {
  const streamEndpoint = 
    `https://generativelanguage.googleapis.com/v1beta/models/${model}:streamGenerateContent`;
  
  const response = await fetch(streamEndpoint, {
    method: 'POST',
    headers: { 'Content-Type': 'application/json' },
    body: JSON.stringify({
      contents: [{ parts: [{ text: prompt }] }],
      generationConfig: { 
        temperature: 0.7, 
        topK: 40, 
        topP: 0.95, 
        maxOutputTokens: 8192 
      }
    }),
    signal
  });
  
  // Stream parsing with Server-Sent Events
  const reader = response.body.getReader();
  const decoder = new TextDecoder();
  let fullContent = '';
  
  while (true) {
    const { done, value } = await reader.read();
    if (done) break;
    
    // Parse SSE format: "data: {...}\n"
    const lines = decoder.decode(value).split('\n');
    for (const line of lines) {
      if (line.startsWith('data: ')) {
        const data = JSON.parse(line.substring(6));
        const text = data?.candidates?.[0]?.content?.parts?.[0]?.text;
        if (text) {
          fullContent += text;
          if (onChunk) onChunk(text); // Real-time streaming callback
        }
      }
    }
  }
  
  return fullContent;
}
```

### Mistral AI Implementation

```typescript
private async generateWithMistral(/* ... */): Promise<string> {
  const response = await fetch('https://api.mistral.ai/v1/chat/completions', {
    method: 'POST',
    headers: { 
      'Content-Type': 'application/json', 
      'Authorization': `Bearer ${apiKey}` 
    },
    body: JSON.stringify({
      model: model,
      messages: [{ role: 'user', content: prompt }],
      temperature: 0.7,
      max_tokens: 8192,
      stream: true
    }),
    signal
  });
  
  // Similar SSE parsing as Google, different JSON structure
  // data.choices[0].delta.content
}
```

### ZhipuAI Implementation

```typescript
private async generateWithZhipu(/* ... */): Promise<string> {
  const response = await fetch('https://open.bigmodel.cn/api/paas/v4/chat/completions', {
    method: 'POST',
    headers: { 
      'Content-Type': 'application/json', 
      'Authorization': `Bearer ${apiKey}` 
    },
    body: JSON.stringify({
      model: model,
      messages: [{ role: 'user', content: prompt }],
      temperature: 0.7,
      max_tokens: 8192,
      stream: true
    }),
    signal
  });
  
  // Similar structure to Mistral
}
```

### Prompt Engineering

#### Roadmap Generation Prompt

```typescript
private buildRoadmapPrompt(session: BookSession): string {
  return `Create a comprehensive learning roadmap for: "${session.goal}"

Requirements:
- Generate 8-12 modules in a logical learning sequence
- Each module should have a clear title and 3-5 specific learning objectives
- Estimate realistic reading/study time for each module
- Target audience: ${session.targetAudience || 'general learners'}
- Complexity: ${session.complexityLevel || 'intermediate'}

Return ONLY valid JSON:
{
  "modules": [
    {
      "title": "Module Title",
      "objectives": ["Objective 1", "Objective 2"],
      "estimatedTime": "2-3 hours"
    }
  ],
  "estimatedReadingTime": "20-25 hours",
  "difficultyLevel": "intermediate"
}`;
}
```

#### Module Content Prompt

```typescript
private buildModulePrompt(
  session: BookSession,
  roadmapModule: RoadmapModule,
  previousModules: BookModule[],
  isFirstModule: boolean,
  moduleIndex: number,
  totalModules: number
): string {
  const contextSummary = !isFirstModule && previousModules.length > 0 ?
    `\n\nPREVIOUS MODULES CONTEXT:\n${previousModules.slice(-2).map(m =>
      `${m.title}: ${m.content.substring(0, 300)}...`
    ).join('\n\n')}` : '';

  return `Generate a comprehensive chapter for: "${roadmapModule.title}"

CONTEXT:
- Learning Goal: ${session.goal}
- Module ${moduleIndex} of ${totalModules}
- Objectives: ${roadmapModule.objectives.join(', ')}
- Target Audience: ${session.targetAudience || 'general learners'}
- Complexity: ${session.complexityLevel || 'intermediate'}${contextSummary}

REQUIREMENTS:
- Write 2000-4000 words
- ${isFirstModule ? 'Provide introduction' : 'Build upon previous content'}
- Use ## markdown headers
- Include bullet points and lists
${session.preferences?.includeExamples ? '- Include practical examples' : ''}
${session.preferences?.includePracticalExercises ? '- Add exercises at the end' : ''}

STRUCTURE:
## ${roadmapModule.title}
### Introduction
### Core Concepts
### Practical Application
${session.preferences?.includePracticalExercises ? '### Practice Exercises' : ''}
### Key Takeaways`;
}
```

---

## Data Management & Storage

### Storage Architecture

All data is stored in **browser LocalStorage** using the `storageUtils` service:

```typescript
// src/utils/storage.ts
export const storageUtils = {
  getSettings(): APISettings,
  saveSettings(settings: APISettings): void,
  getBooks(): BookProject[],
  saveBooks(books: BookProject[]): void,
  clearAllData(): void
};
```

### Data Structures

#### BookProject

```typescript
interface BookProject {
  id: string;                    // Unique identifier
  title: string;                 // Book title (from goal)
  goal: string;                  // Original learning goal
  language: 'en';                // Language (English only currently)
  status: BookStatus;            // Current generation status
  progress: number;              // 0-100 percentage
  createdAt: Date;
  updatedAt: Date;
  roadmap?: BookRoadmap;         // Generated learning roadmap
  modules: BookModule[];         // Generated content modules
  finalBook?: string;            // Complete markdown content
  error?: string;                // Error message if failed
  category: BookCategory;        // programming/science/art/business/general
}

type BookStatus = 
  | 'planning' 
  | 'generating_roadmap' 
  | 'roadmap_completed' 
  | 'generating_content' 
  | 'assembling' 
  | 'completed' 
  | 'error';
```

#### BookRoadmap

```typescript
interface BookRoadmap {
  modules: RoadmapModule[];
  totalModules: number;
  estimatedReadingTime: string;  // e.g., "20-25 hours"
  difficultyLevel: 'beginner' | 'intermediate' | 'advanced';
}

interface RoadmapModule {
  id: string;
  title: string;
  objectives: string[];          // 3-5 learning objectives
  estimatedTime: string;         // e.g., "2-3 hours"
  order: number;                 // 1-based sequence
}
```

#### BookModule

```typescript
interface BookModule {
  id: string;
  roadmapModuleId: string;       // Links to RoadmapModule
  title: string;
  content: string;               // Full markdown content
  wordCount: number;
  status: 'pending' | 'generating' | 'completed' | 'error';
  generatedAt?: Date;
  error?: string;
}
```

#### APISettings

```typescript
interface APISettings {
  googleApiKey: string;
  zhipuApiKey: string;
  mistralApiKey: string;
  selectedModel: ModelID;
  selectedProvider: ModelProvider;
}
```

### Checkpoint System

Checkpoints are stored separately with the key pattern `checkpoint_{bookId}`:

```typescript
interface GenerationCheckpoint {
  bookId: string;
  completedModuleIds: string[];
  failedModuleIds: string[];
  moduleRetryCount: Record<string, number>;  // Track retry attempts per module
  lastSuccessfulIndex: number;
  timestamp: string;
  totalWordsGenerated: number;
}
```

**Checkpoint Lifecycle:**

1. **Save** after each completed module
2. **Load** when resuming generation
3. **Clear** when book is completed or generation cancelled
4. **Update** on retry attempts

### Data Persistence Flow

```typescript
// Every state change triggers save
useEffect(() => { 
  storageUtils.saveBooks(books); 
}, [books]);

// Settings are saved immediately on change
const handleSaveSettings = (newSettings: APISettings) => {
  setSettings(newSettings);
  storageUtils.saveSettings(newSettings);
};

// Books are updated through a callback system
const handleBookProgressUpdate = (bookId: string, updates: Partial<BookProject>) => {
  setBooks(prev => prev.map(book => 
    book.id === bookId 
      ? { ...book, ...updates, updatedAt: new Date() } 
      : book
  ));
};
```

### Data Import/Export

#### Export Format

```json
{
  "books": [...],
  "settings": {...},
  "exportDate": "2025-10-28T12:00:00.000Z",
  "version": "1.0.0"
}
```

#### Import with Conflict Resolution

**Merge Mode:**

- Adds new books that don't exist
- Preserves existing API keys
- Merges settings intelligently

**Replace Mode:**

- Completely replaces all data
- Useful for restoring backups

```typescript
// From SettingsModal.tsx
const executeImport = (mode: 'merge' | 'replace') => {
  if (mode === 'replace') {
    storageUtils.saveBooks(importPreview.books);
    storageUtils.saveSettings(importPreview.settings);
  } else {
    // Merge logic: skip duplicates, add new ones
    const mergedBooks = [...existingBooks];
    importPreview.books.forEach(importBook => {
      const exists = mergedBooks.some(existing => existing.id === importBook.id);
      if (!exists) mergedBooks.push(importBook);
    });
    storageUtils.saveBooks(mergedBooks);
  }
  
  window.location.reload();
};
```

---

## User Interface & Experience

### Responsive Design Strategy

The application uses a mobile-first approach with three breakpoints:

```css
/* Mobile: < 768px */
- Full-screen sidebar overlay
- Simplified navigation
- Touch-optimized buttons (larger hit areas)
- Single-column layouts

/* Tablet: 768px - 1023px */
- Slide-in sidebar panel
- Two-column layouts where appropriate
- Hybrid touch/mouse interactions

/* Desktop: ≥ 1024px */
- Persistent sidebar
- Multi-column layouts
- Hover interactions
- Collapsible sidebar (fold to icons)
```

### Theme System

The application uses CSS custom properties for theming:

```css
:root {
  /* Core Colors */
  --color-bg: #0A0A0A;               /* Main background */
  --color-sidebar: #141414;          /* Sidebar background */
  --color-card: #1F1F1F;             /* Card background */
  --color-border: #2A2A2A;           /* Border color */
  --color-text-primary: #FFFFFF;     /* Primary text */
  --color-text-secondary: #A0A0A0;   /* Secondary text */
  
  /* Accents */
  --color-accent-primary: #3B82F6;   /* Blue-500 */
  --color-accent-danger: #EF4444;    /* Red-500 */
  --color-accent-warning: #F59E0B;   /* Amber-500 */
}
```

### Reading Mode

The reading mode provides an immersive, distraction-free reading experience:

**Features:**

**Fullscreen reading overlay** with custom controls

- **Customizable typography**:
    - Font size: 12px - 28px
    - Line height: 1.2 - 2.2
    - Font family: Serif, Sans, Mono
- **Theme options**:
    - Dark theme (default)
    - Sepia theme (warm, paper-like)
- **Column width**: Narrow (65ch), Medium (75ch), Wide (85ch)
- **Text alignment**: Left or Justified
- **Progress tracking**: Visual progress bar at top
- **Scroll-to-top button** appears after scrolling
- **Settings panel** with auto-hide after 5 seconds

```typescript
interface ReadingSettings {
  fontSize: number;           // 12-28
  lineHeight: number;         // 1.2-2.2
  fontFamily: 'serif' | 'sans' | 'mono';
  theme: 'dark' | 'sepia';
  maxWidth: 'narrow' | 'medium' | 'wide';
  textAlign: 'left' | 'justify';
}
```

**Reading Mode Keyboard Shortcuts:**

- `Esc` - Exit fullscreen
- `Ctrl/Cmd + =` - Increase font size
- `Ctrl/Cmd + -` - Decrease font size
- `Ctrl/Cmd + 0` - Reset font size

**Accessibility Features:**

- Settings persist in localStorage
- High contrast support
- Reduced motion respect
- Keyboard navigation
- Screen reader optimizations

### Animation System

The application uses Tailwind CSS animations with custom keyframes:

```css
/* Fade Animations */
@keyframes fadeIn {
  from { opacity: 0; }
  to { opacity: 1; }
}

@keyframes fadeInUp {
  from { opacity: 0; transform: translateY(20px); }
  to { opacity: 1; transform: translateY(0); }
}

/* Shimmer Effect (for loading states) */
@keyframes shimmer {
  0% { background-position: -200px 0; }
  100% { background-position: calc(200px + 100%) 0; }
}

/* Glow Effects */
@keyframes subtle-glow {
  0% { box-shadow: 0 0 20px 0px rgba(59, 130, 246, 0.1); }
  50% { box-shadow: 0 0 35px 5px rgba(59, 130, 246, 0.15); }
  100% { box-shadow: 0 0 20px 0px rgba(59, 130, 246, 0.1); }
}

@keyframes assembling-glow {
  0%, 100% { 
    border-color: theme('colors.green.500 / 30%'); 
    box-shadow: 0 0 10px theme('colors.green.500 / 20%'); 
  }
  50% { 
    border-color: theme('colors.green.500 / 70%'); 
    box-shadow: 0 0 20px theme('colors.green.500 / 40%'); 
  }
}
```

**Applied Animations:**

- Page transitions: `animate-fade-in-up`
- Modal overlays: `animate-fade-in`
- Logo on home: `animate-subtle-glow`
- Assembly status: `animate-assembling-glow`
- Loading states: `animate-shimmer`
- Generation progress: `animate-slide-in-out`

### Component States

**Button States:**

```css
.btn {
  /* Base */
  @apply inline-flex items-center justify-center gap-2 px-4 py-2 
         rounded-lg font-semibold transition-all duration-200;
  
  /* Focus */
  @apply focus:outline-none focus:ring-2 focus:ring-offset-2;
  
  /* Disabled */
  &:disabled {
    @apply opacity-50 cursor-not-allowed;
  }
}

.btn-primary {
  @apply bg-white text-black hover:bg-gray-200 focus:ring-white;
}

.btn-secondary {
  @apply bg-white/5 border border-white/10 text-gray-300 
         hover:bg-white/10 hover:text-white focus:ring-white;
}
```

**Input States:**

```css
.input-style {
  @apply w-full bg-gray-900/50 border border-white/10 rounded-lg 
         px-3 py-2 text-white placeholder-gray-500 
         focus:outline-none focus:ring-2 focus:ring-gray-600 
         focus:border-transparent transition-colors;
}
```

### Progress Indicators

**Overall Progress Bar:**

```typescript
<GradientProgressBar 
  progress={overallProgress} 
  active={isGenerating} 
/>

// Implementation
const GradientProgressBar = ({ progress = 0, active = true }) => (
  <div className="relative w-full h-2.5 bg-zinc-800/50 rounded-full overflow-hidden">
    <div
      className="absolute inset-0 bg-gradient-to-r from-orange-500 via-yellow-400 to-orange-500"
      style={{
        width: `${progress}%`,
        backgroundSize: '200% 100%',
        animation: active ? 'gradient-flow 3s ease infinite' : 'none',
      }}
    />
  </div>
);
```

**Module Progress:**

- Checkmark (✓) for completed
- Loader spinner for in progress
- Number for pending
- X for failed
- Color coding: green (completed), blue (active), red (error), gray (pending)

**Pixel Animation** (during generation):

```typescript
const PixelAnimation = () => {
  const [pixels, setPixels] = useState([]);

  useEffect(() => {
    const generate = () => {
      const newPixels = Array(70).fill(0).map((_, i) => ({
        id: i,
        color: colors[Math.floor(Math.random() * colors.length)],
        opacity: Math.random() > 0.6 ? 'opacity-100' : 'opacity-30',
      }));
      setPixels(newPixels);
    };

    generate();
    const interval = setInterval(generate, 200);
    return () => clearInterval(interval);
  }, []);

  return (
    <div className="flex flex-wrap gap-1.5 h-14">
      {pixels.map(p => (
        <div key={p.id} className={`w-1.5 h-1.5 rounded-sm ${p.color} ${p.opacity}`} />
      ))}
    </div>
  );
};
```

### Notification System

**Offline Notification:**

```typescript
{showOfflineMessage && (
  <div className="fixed z-50 top-4 right-4 content-card p-3 animate-fade-in-up">
    <div className="flex items-center gap-2 text-yellow-400">
      <WifiOff className="w-4 h-4" />
      <span className="font-medium text-sm">You're offline</span>
    </div>
  </div>
)}
```

**Toast Notifications:**

- Auto-dismiss after 5-7 seconds
- Different styles for success/error/warning
- Mobile-optimized positioning

---

## Advanced Features

### 1. **Live Text Streaming**

During module generation, the application displays the AI's output in real-time:

```typescript
// In bookService.ts - generateWithAI()
const onChunk = (chunk: string) => {
  const currentText = (this.currentGeneratedTexts.get(bookId) || '') + chunk;
  this.currentGeneratedTexts.set(bookId, currentText);
  
  const currentWordCount = currentText.split(/\s+/).filter(w => w.length > 0).length;
  const estimatedWordTarget = 3000;
  const progress = Math.min(95, (currentWordCount / estimatedWordTarget) * 100);
  
  // Determine AI stage based on progress
  let aiStage: 'analyzing' | 'writing' | 'examples' | 'polishing';
  if (currentWordCount >= estimatedWordTarget * 0.9) aiStage = 'polishing';
  else if (currentWordCount >= estimatedWordTarget * 0.6) aiStage = 'examples';
  else if (currentWordCount >= estimatedWordTarget * 0.15) aiStage = 'writing';
  else aiStage = 'analyzing';
  
  this.updateGenerationStatus(bookId, {
    currentModule: {
      id: roadmapModule.id,
      title: roadmapModule.title,
      attempt: attemptNumber,
      progress,
      generatedText: currentText.slice(-800) // Last 800 chars
    },
    totalProgress: 0,
    status: 'generating',
    totalWordsGenerated: totalWordsBefore + currentWordCount,
    aiStage
  });
};
```

**UI Display:**

```typescript
{generationStatus.currentModule?.generatedText && (
  <div ref={streamBoxRef} className="streaming-text-box">
    <div className="whitespace-pre-wrap">
      {typedText}
      <span className="inline-block w-1.5 h-3 bg-blue-400 animate-pulse ml-1" />
    </div>
  </div>
)}
```

**Typewriter Effect:**

```typescript
useEffect(() => {
  const fullText = generationStatus.currentModule?.generatedText || '';
  if (fullText === typedText) return;

  let i = typedText.length;
  const timer = setInterval(() => {
    if (i < fullText.length) {
      setTypedText(prev => prev + fullText[i]);
      i++;
      
      // Auto-scroll to bottom
      if (liveFeedRef.current) {
        liveFeedRef.current.scrollTop = liveFeedRef.current.scrollHeight;
      }
    } else {
      clearInterval(timer);
    }
  }, 10); // 10ms per character

  return () => clearInterval(timer);
}, [generationStatus.currentModule?.generatedText]);
```

### 2. **Generation Statistics**

Real-time statistics are calculated and displayed:

```typescript
export function useGenerationStats(
  totalModules: number,
  completedModules: number,
  failedModules: number,
  startTime: Date,
  totalWordsGenerated: number
): GenerationStats {
  const [stats, setStats] = useState<GenerationStats>({/* ... */});

  useEffect(() => {
    const elapsedSeconds = (Date.now() - startTime.getTime()) / 1000;
    const avgTime = completedModules > 0 ? elapsedSeconds / completedModules : 0;
    const remaining = totalModules - completedModules;
    const estimatedRemaining = avgTime * remaining;
    const wpm = elapsedSeconds > 0 ? (totalWordsGenerated / elapsedSeconds) * 60 : 0;

    setStats({
      startTime,
      totalModules,
      completedModules,
      failedModules,
      averageTimePerModule: avgTime,
      estimatedTimeRemaining: estimatedRemaining,
      totalWordsGenerated,
      wordsPerMinute: wpm
    });
  }, [totalModules, completedModules, failedModules, startTime, totalWordsGenerated]);

  return stats;
}
```

**Displayed Metrics:**

- **Completed Modules**: Green badge with checkmark
- **Failed Modules**: Red badge with X
- **Total Words**: Formatted number (e.g., "12,543")
- **Words Per Minute**: Average generation speed
- **Average Time/Module**: In minutes and seconds
- **Estimated Remaining**: Calculated based on average

### 3. **Event Logging System**

All generation events are logged for debugging and transparency:

```typescript
interface LogEntry {
  id: number;
  timestamp: string;  // HH:MM:SS format
  message: string;
  type: 'info' | 'success' | 'warn' | 'error';
  data?: any;         // Optional structured data
}

// Usage
const newEntry: LogEntry = {
  id: Date.now(),
  timestamp: new Date().toLocaleTimeString([], { 
    hour: '2-digit', 
    minute: '2-digit', 
    second: '2-digit' 
  }),
  message: generationStatus.logMessage,
  type: generationStatus.logMessage.includes('✓') ? 'success' : 
        generationStatus.logMessage.includes('⚠️') ? 'warn' : 
        generationStatus.logMessage.includes('✗') ? 'error' : 'info',
  data: generationStatus.currentModule ? {
    moduleId: generationStatus.currentModule.id,
    moduleTitle: generationStatus.currentModule.title,
    attempt: generationStatus.currentModule.attempt,
    totalWords: generationStatus.totalWordsGenerated
  } : undefined
};
```

**Log Download Feature:**

```typescript
const downloadLogs = () => {
  const logsContent = [
    '=' .repeat(80),
    'PUSTAKAM AI BOOK GENERATION - DETAILED LOG',
    '='.repeat(80),
    `Generated: ${new Date().toLocaleString()}`,
    `Session Start: ${stats.startTime.toLocaleString()}`,
    `Total Modules: ${stats.totalModules}`,
    `Completed: ${stats.completedModules} | Failed: ${stats.failedModules}`,
    '='.repeat(80),
    '',
    ...eventLog.map(log => 
      `[${log.timestamp}] [${log.type.toUpperCase()}] ${log.message}${
        log.data ? `\n   Data: ${JSON.stringify(log.data, null, 2)}` : ''
      }`
    ).reverse()
  ].join('\n');

  const blob = new Blob([logsContent], { type: 'text/plain;charset=utf-8' });
  const url = URL.createObjectURL(blob);
  const a = document.createElement('a');
  a.href = url;
  a.download = `pustakam-generation-log-${new Date().toISOString()}.txt`;
  a.click();
};
```

### 4. **Book Analytics**

Comprehensive analytics are calculated post-generation:

```typescript
analyzeBook(book: BookProject): BookAnalytics {
  const totalWords = book.modules.reduce((sum, m) => sum + m.wordCount, 0);
  const readingTime = Math.ceil(totalWords / 250); // 250 wpm average
  
  // Complexity analysis based on keyword frequency
  const content = book.finalBook.toLowerCase();
  const complexityIndicators = {
    beginner: ['basic', 'simple', 'introduction', 'getting started', 'fundamentals'],
    intermediate: ['advanced', 'complex', 'implementation', 'optimization', 'architecture'],
    advanced: ['sophisticated', 'enterprise', 'scalable', 'theoretical', 'research']
  };

  let complexity: 'beginner' | 'intermediate' | 'advanced' = 'beginner';
  let maxScore = 0;

  Object.entries(complexityIndicators).forEach(([level, keywords]) => {
    const score = keywords.reduce((count, keyword) => 
      count + (content.match(new RegExp(keyword, 'g')) || []).length, 0
    );
    if (score > maxScore) {
      maxScore = score;
      complexity = level as 'beginner' | 'intermediate' | 'advanced';
    }
  });

  const topics = this.extractTopics(book);

  return {
    totalWords,
    readingTime: readingTime > 60 
      ? `${Math.floor(readingTime / 60)} hours ${readingTime % 60} minutes`
      : `${readingTime} minutes`,
    complexity,
    topics,
  };
}
```

### 5. **Study Materials Generation**

**Progress Tracker:**

```markdown
# Python for Data Science - Reading Progress

## Progress Tracker
- [ ] **Module 1**: Introduction to Python _(2-3 hours)_
- [ ] **Module 2**: Data Structures _(3-4 hours)_
- [ ] **Module 3**: NumPy Fundamentals _(4-5 hours)_
...

## Study Schedule Suggestion
- **Daily Reading**: 30-45 minutes
- **Weekly Goal**: 2-3 modules
- **Practice Time**: 15-30 minutes after each module

## Notes Section
_Use this space for your personal notes and insights_
```

**Study Summary:**

```markdown
# Python for Data Science - Study Summary

## Key Learning Objectives
- Understand Python syntax and data structures
- Master NumPy for numerical computing
- Learn Pandas for data manipulation
...

## Important Topics
- Python Basics
- Data Structures
- NumPy Arrays
- Pandas DataFrames
...
```

---

## Error Handling & Recovery

### Retry Strategy

The application implements a sophisticated multi-layered retry system:

#### 1. **Automatic Retries** (Network/Rate Limit Errors)

```typescript
private async generateWithGoogle(/* ... */): Promise<string> {
  const maxRetries = 3;
  let attempt = 0;

  while (attempt < maxRetries) {
    try {
      const response = await fetch(streamEndpoint, {/* ... */});

      if (response.status === 429 || response.status === 503) {
        // Rate limit or service unavailable
        const delay = Math.pow(2, attempt) * 1000 + Math.random() * 1000;
        await sleep(delay);
        attempt++;
        continue;
      }

      if (!response.ok) {
        throw new Error(`HTTP ${response.status}`);
      }

      // Success - return content
      return fullContent;

    } catch (error) {
      if (error.name === 'AbortError') throw error;
      attempt++;
      if (attempt >= maxRetries) throw error;
      await sleep(Math.pow(2, attempt) * 1000);
    }
  }
}
```

#### 2. **User-Controlled Retries** (Module Generation Failures)

When a module fails after automatic retries, the system presents three options:

```typescript
const RetryDecisionPanel = ({
  retryInfo,
  onRetry,
  onSwitchModel,
  onSkip,
  availableModels
}) => {
  return (
    <div className="retry-panel">
      <h3>Generation Failed</h3>
      <p>Module: {retryInfo.moduleTitle}</p>
      <p>Error: {retryInfo.error}</p>
      <p>Attempt {retryInfo.retryCount} of {retryInfo.maxRetries}</p>
      
      {/* Recommended actions based on error type */}
      {isRateLimit && (
        <p>✓ Wait {countdown}s and retry with same model</p>
      )}
      
      <button onClick={onRetry} disabled={countdown > 0}>
        Retry Same Model
      </button>
      
      {availableModels.length > 0 && (
        <button onClick={onSwitchModel}>
          Switch AI Model ({availableModels.length} available)
        </button>
      )}
      
      <button onClick={onSkip}>
        Skip This Module
      </button>
    </div>
  );
};
```

**Decision Flow:**

```typescript
const handleRetryDecision = async (decision: 'retry' | 'switch' | 'skip') => {
  if (decision === 'retry') {
    // User wants to retry with same model
    bookService.setRetryDecision(currentBook.id, 'retry');
    
  } else if (decision === 'switch') {
    // User wants to switch to different AI model
    const alternatives = getAlternativeModels();
    
    if (alternatives.length === 0) {
      alert('No alternative AI models available. Configure API keys in Settings.');
      return;
    }
    
    showModelSwitchModal(alternatives);
    
  } else if (decision === 'skip') {
    // User wants to skip this module
    const confirmed = window.confirm('Skip this module? It will be marked as failed.');
    
    if (confirmed) {
      bookService.setRetryDecision(currentBook.id, 'skip');
    }
  }
};
```

#### 3. **Exponential Backoff**

```typescript
private calculateRetryDelay(attempt: number, isRateLimit: boolean): number {
  if (isRateLimit) {
    // For rate limits, use longer delays with moderate growth
    return this.RATE_LIMIT_DELAY * Math.pow(1.5, attempt);
  }
  
  // For other errors, use exponential backoff with jitter
  const exponentialDelay = this.RETRY_DELAY_BASE * Math.pow(2, attempt - 1);
  const jitter = Math.random() * 1000;
  return Math.min(exponentialDelay + jitter, this.MAX_RETRY_DELAY);
}

// Constants
private readonly MAX_MODULE_RETRIES = 5;
private readonly RETRY_DELAY_BASE = 3000;      // 3 seconds
private readonly MAX_RETRY_DELAY = 30000;      // 30 seconds
private readonly RATE_LIMIT_DELAY = 5000;      // 5 seconds
```

**Retry Delay Progression:**

- Attempt 1: 3s + jitter
- Attempt 2: 6s + jitter
- Attempt 3: 12s + jitter
- Attempt 4: 24s + jitter
- Attempt 5: 30s (max)

#### 4. **Error Classification**

```typescript
private isRateLimitError(error: any): boolean {
  const errorMessage = error?.message?.toLowerCase() || '';
  const statusCode = error?.status || error?.response?.status;
  
  return (
    statusCode === 429 ||
    statusCode === 503 ||
    errorMessage.includes('rate limit') ||
    errorMessage.includes('quota') ||
    errorMessage.includes('too many requests')
  );
}

private isNetworkError(error: any): boolean {
  const errorMessage = error?.message?.toLowerCase() || '';
  return (
    errorMessage.includes('network') ||
    errorMessage.includes('fetch') ||
    errorMessage.includes('connection') ||
    error?.name === 'NetworkError'
  );
}

private shouldRetry(error: any, attempt: number): boolean {
  if (attempt >= this.MAX_MODULE_RETRIES) return false;
  
  // Always retry rate limits and network errors
  if (this.isRateLimitError(error) || this.isNetworkError(error)) {
    return true;
  }
  
  // Retry other transient errors
  const errorMessage = error?.message?.toLowerCase() || '';
  const retryableErrors = ['timeout', 'overloaded', 'unavailable', 'internal error', 'bad gateway'];
  
  return retryableErrors.some(msg => errorMessage.includes(msg));
}
```

### Checkpoint Recovery

**Save Checkpoint:**

```typescript
private saveCheckpoint(
  bookId: string, 
  completedModuleIds: string[], 
  failedModuleIds: string[], 
  lastIndex: number,
  moduleRetryCount: Record<string, number> = {},
  totalWordsGenerated: number = 0
) {
  const checkpoint: GenerationCheckpoint = {
    bookId,
    completedModuleIds,
    failedModuleIds,
    moduleRetryCount,
    lastSuccessfulIndex: lastIndex,
    timestamp: new Date().toISOString(),
    totalWordsGenerated
  };
  
  this.checkpoints.set(bookId, checkpoint);
  localStorage.setItem(`checkpoint_${bookId}`, JSON.stringify(checkpoint));
}
```

**Load and Resume:**

```typescript
const handleGenerateAllModules = async (book: BookProject, session: BookSession) => {
  const hasCheckpoint = bookService.hasCheckpoint(book.id);
  const checkpointInfo = bookService.getCheckpointInfo(book.id);

  if (hasCheckpoint && checkpointInfo && checkpointInfo.completed > 0) {
    const message = `📚 Previous Progress Found\n\n` +
      `Last saved: ${checkpointInfo.lastSaved}\n` +
      `✓ ${checkpointInfo.completed} module(s) completed\n` +
      `${checkpointInfo.failed > 0 ? `✗ ${checkpointInfo.failed} module(s) failed\n` : ''}\n` +
      `Would you like to:\n\n` +
      `• Click OK to RESUME from checkpoint\n` +
      `• Click Cancel to START FRESH (progress will be lost)`;

    const shouldResume = window.confirm(message);
    
    if (!shouldResume) {
      // Clear checkpoint and start fresh
      localStorage.removeItem(`checkpoint_${book.id}`);
      bookService.resumeGeneration(book.id);
      handleBookProgressUpdate(book.id, { 
        modules: [],
        status: 'generating_content',
        progress: 15
      });
    } else {
      // Resume from checkpoint
      bookService.resumeGeneration(book.id);
    }
  }

  // Start generation
  await bookService.generateAllModulesWithRecovery(book, session);
};
```

### Pause/Resume Functionality

**Pause Generation:**

```typescript
pauseGeneration(bookId: string) {
  try {
    localStorage.setItem(`pause_flag_${bookId}`, 'true');
    console.log('⏸ Pause flag set for book:', bookId);
  } catch (error) {
    console.warn('Failed to set pause flag:', error);
  }
  
  this.updateGenerationStatus(bookId, {
    status: 'paused',
    totalProgress: 0,
    logMessage: '⏸ Generation paused by user'
  });
}
```

**Check if Paused:**

```typescript
isPaused(bookId: string): boolean {
  try {
    const pauseFlag = localStorage.getItem(`pause_flag_${bookId}`);
    return pauseFlag === 'true';
  } catch (error) {
    return false;
  }
}
```

**Resume Generation:**

```typescript
resumeGeneration(bookId: string) {
  try {
    localStorage.removeItem(`pause_flag_${bookId}`);
    console.log('▶ Pause flag cleared for book:', bookId);
  } catch (error) {
    console.warn('Failed to clear pause flag:', error);
  }
}
```

**Generation Loop with Pause Checks:**

```typescript
for (let i = 0; i < modulesToGenerate.length; i++) {
  const roadmapModule = modulesToGenerate[i];
  
  // Check pause flag before starting module
  if (this.isPaused(book.id)) {
    console.log('⏸ Generation paused, saving checkpoint...');
    this.saveCheckpoint(book.id, completedModuleIds, failedModuleIds, i - 1);
    return; // Exit generation loop
  }
  
  try {
    const newModule = await this.generateModuleContentWithRetry(/* ... */);
    
    // Check pause flag after completing module
    if (this.isPaused(book.id)) {
      completedModules.push(newModule);
      this.saveCheckpoint(book.id, completedModuleIds, failedModuleIds, i);
      return; // Exit generation loop
    }
    
    // Continue to next module
  } catch (error) {
    // Handle error
  }
}
```

### Request Cancellation

```typescript
private activeRequests = new Map<string, AbortController>();

cancelActiveRequests(bookId?: string): void {
  if (bookId) {
    if (this.activeRequests.has(bookId)) {
      this.activeRequests.get(bookId)?.abort();
      this.activeRequests.delete(bookId);
    }
    this.pauseGeneration(bookId);
  } else {
    // Cancel all active requests
    this.activeRequests.forEach(controller => controller.abort());
    this.activeRequests.clear();
  }
}
```

---

## Performance Optimizations

### 1. **React Optimizations**

**useMemo for Expensive Calculations:**

```typescript
const currentBook = useMemo(() => 
  currentBookId ? books.find(b => b.id === currentBookId) : null,
  [currentBookId, books]
);

const sortedBooks = useMemo(() => {
  const filtered = books.filter(book =>
    book.title.toLowerCase().includes(searchQuery.toLowerCase())
  );
  return filtered.sort((a, b) => 
    new Date(b.updatedAt).getTime() - new Date(a.updatedAt).getTime()
  );
}, [books, searchQuery]);

const isGenerating = useMemo(() => {
  if (!currentBook) return false;
  return (
    currentBook.status === 'generating_content' ||
    generationStatus.status === 'generating'
  );
}, [currentBook?.status, generationStatus.status]);
```

**React.memo for Component Optimization:**

```typescript
const CodeBlock = React.memo(({ children, language, theme }: any) => (
  <SyntaxHighlighter
    style={vscDarkPlus}
    language={language}
    PreTag="div"
    customStyle={{
      padding: '1.5rem',
      fontSize: '0.875rem',
      lineHeight: '1.5',
    }}
  >
    {String(children).replace(/\n$/, '')}
  </SyntaxHighlighter>
));
```

### 2. **Lazy Loading & Code Splitting**

**Dynamic pdfMake Import:**

```typescript
let pdfMake: any = null;
let fontsLoaded = false;

async function loadPdfMake() {
  if (pdfMake && fontsLoaded) {
    console.log('📦 Using cached pdfMake');
    return pdfMake;
  }
  
  const [pdfMakeModule, pdfFontsModule] = await Promise.all([
    import('pdfmake/build/pdfmake'),
    import('pdfmake/build/vfs_fonts')
  ]);
  
  pdfMake = pdfMakeModule.default || pdfMakeModule;
  const fonts = pdfFontsModule.default || pdfFontsModule;
  
  // Configure fonts
  pdfMake.vfs = fonts.pdfMake.vfs;
  pdfMake.fonts = {/* ... */};
  
  fontsLoaded = true;
  return pdfMake;
}
```

### 3. **Debouncing & Throttling**

**Search Debouncing:**

```typescript
const [searchQuery, setSearchQuery] = useState('');

// Debounced search
useEffect(() => {
  const timer = setTimeout(() => {
    // Perform search
  }, 300);
  
  return () => clearTimeout(timer);
}, [searchQuery]);
```

**Auto-Hide Settings Panel:**

```typescript
useEffect(() => {
  if (showSettings) {
    if (settingsTimeoutRef.current) clearTimeout(settingsTimeoutRef.current);
    settingsTimeoutRef.current = setTimeout(() => setShowSettings(false), 5000);
  }
  return () => {
    if (settingsTimeoutRef.current) clearTimeout(settingsTimeoutRef.current);
  };
}, [showSettings]);
```

### 4. **Virtual Scrolling Considerations**

For large book lists, the application uses CSS transforms for smooth scrolling:

```css
.book-list {
  will-change: transform;
  transform: translateZ(0);
}
```

### 5. **Streaming Optimization**

**Chunked Text Updates:**

```typescript
// Update UI every 10-20 characters instead of every character
let buffer = '';
const CHUNK_SIZE = 20;

const onChunk = (chunk: string) => {
  buffer += chunk;
  
  if (buffer.length >= CHUNK_SIZE) {
    const currentText = this.currentGeneratedTexts.get(bookId) || '';
    this.currentGeneratedTexts.set(bookId, currentText + buffer);
    
    // Update UI with batched chunk
    this.updateGenerationStatus(bookId, {
      currentModule: {
        generatedText: currentText + buffer
      }
    });
    
    buffer = '';
  }
};

// Flush remaining buffer at end
if (buffer.length > 0) {
  const currentText = this.currentGeneratedTexts.get(bookId) || '';
  this.currentGeneratedTexts.set(bookId, currentText + buffer);
}
```

### 6. **LocalStorage Optimization**

**Batch Updates:**

```typescript
// Instead of saving on every small change
useEffect(() => {
  const saveTimer = setTimeout(() => {
    storageUtils.saveBooks(books);
  }, 500); // Debounce saves
  
  return () => clearTimeout(saveTimer);
}, [books]);
```

**Storage Size Management:**

```typescript
// Check storage quota
const checkStorageQuota = async () => {
  if ('storage' in navigator && 'estimate' in navigator.storage) {
    const estimate = await navigator.storage.estimate();
    const percentUsed = (estimate.usage! / estimate.quota!) * 100;
    
    if (percentUsed > 80) {
      console.warn('Storage quota nearly full:', percentUsed.toFixed(2) + '%');
    }
  }
};
```

### 7. **Memory Management**

**Cleanup on Unmount:**

```typescript
useEffect(() => {
  // Setup
  const controller = new AbortController();
  
  return () => {
    // Cleanup
    controller.abort();
    bookService.cancelActiveRequests(currentBookId);
  };
}, [currentBookId]);
```

**Clear Cached Data:**

```typescript
private clearCurrentGeneratedText(bookId: string): void {
  this.currentGeneratedTexts.delete(bookId);
}

// Called after module completion
this.clearCurrentGeneratedText(book.id);
```

---

## Security & Privacy

### 1. **Data Privacy Model**

**Local-First Architecture:**

- All data stored in browser's localStorage
- No server-side storage of user data
- No telemetry or tracking (except anonymized Vercel Analytics)
- API keys never transmitted to Pustakam servers

**Data Flow:**

```
User Data → Browser localStorage → AI Provider API (direct)
                                    ↓
                              AI Response → Browser → LocalStorage
```

### 2. **API Key Security**

**Storage:**

```typescript
// API keys stored in localStorage with clear warnings
const SETTINGS_KEY = 'pustakam-settings';

export const storageUtils = {
  saveSettings(settings: APISettings): void {
    // Keys stored as plain text in localStorage
    // User is warned about browser security
    localStorage.setItem(SETTINGS_KEY, JSON.stringify(settings));
  }
};
```

**Security Warnings in Settings:**

```typescript
<div className="bg-blue-500/10 border border-blue-500/20 rounded-lg p-4">
  <p className="text-sm text-blue-300 mb-2">
    🔒 Your API keys are stored locally in your browser and are never sent to our servers.
  </p>
  <p className="text-xs text-gray-400">
    You need at least one API key to generate books. Google AI is recommended for beginners.
  </p>
</div>
```

**Best Practices:**

- Never log API keys to console
- Use environment variables for development
- Show/hide toggle for key visibility
- Validation before saving

### 3. **Input Sanitization**

**Filename Sanitization:**

```typescript
downloadAsMarkdown(project: BookProject): void {
  const safeTitle = project.title
    .replace(/[^a-z0-9\s-]/gi, '')  // Remove special characters
    .replace(/\s+/g, '_')            // Replace spaces with underscores
    .toLowerCase()
    .substring(0, 50);               // Limit length
    
  const filename = `${safeTitle}_${new Date().toISOString().slice(0, 10)}_book.md`;
  
  // Create download
  const blob = new Blob([project.finalBook], { type: 'text/markdown;charset=utf-8' });
  const url = URL.createObjectURL(blob);
  const a = document.createElement('a');
  a.href = url;
  a.download = filename;
  a.click();
  
  // Cleanup
  URL.revokeObjectURL(url);
}
```

**Content Sanitization:**

```typescript
private cleanText(text: string): string {
  return text
    .replace(/\*\*\*(.+?)\*\*\*/g, '$1')   // Remove bold+italic
    .replace(/\*\*(.+?)\*\*/g, '$1')       // Remove bold
    .replace(/\*(.+?)\*/g, '$1')           // Remove italic
    .replace(/__(.+?)__/g, '$1')           // Remove underline
    .replace(/_(.+?)_/g, '$1')             // Remove italic
    .replace(/~~(.+?)~~/g, '$1')           // Remove strikethrough
    .replace(/`(.+?)`/g, '$1')             // Remove inline code
    .replace(/\[(.+?)\]\(.+?\)/g, '$1')    // Remove links
    .replace(/!\[.*?\]\(.+?\)/g, '')       // Remove images
    .replace(/[\u{1F300}-\u{1F9FF}]/gu, '') // Remove emojis
    .trim();
}
```

### 4. **XSS Prevention**

**React's Built-in Protection:** React automatically escapes content rendered via JSX, preventing XSS attacks:

```typescript
// Safe - React escapes content
<div>{userInput}</div>

// Unsafe - dangerouslySetInnerHTML (not used in Pustakam)
<div dangerouslySetInnerHTML={{ __html: userInput }} />
```

**Markdown Rendering:**

```typescript
// react-markdown with remark-gfm handles sanitization
<ReactMarkdown
  remarkPlugins={[remarkGfm]}
  components={{
    code: (props) => <CodeBlock {...props} />,
  }}
>
  {content}
</ReactMarkdown>
```

### 5. **CORS & API Security**

**Direct API Calls:**

```typescript
// Calls made directly from browser to AI providers
// No proxy server that could intercept keys

const response = await fetch(apiEndpoint, {
  method: 'POST',
  headers: { 
    'Authorization': `Bearer ${apiKey}`,  // Sent directly to provider
    'Content-Type': 'application/json' 
  },
  body: JSON.stringify(payload),
  signal: abortController.signal
});
```

**No Backend Server:**

- Static hosting on Vercel
- No server-side code execution
- No database to compromise
- No session management vulnerabilities

### 6. **Content Security Policy**

Recommended CSP headers for deployment:

```
Content-Security-Policy: 
  default-src 'self';
  script-src 'self' 'unsafe-inline' 'unsafe-eval' https://va.vercel-scripts.com;
  style-src 'self' 'unsafe-inline' https://fonts.googleapis.com;
  font-src 'self' https://fonts.gstatic.com data:;
  img-src 'self' data: blob:;
  connect-src 'self' 
    https://generativelanguage.googleapis.com 
    https://api.mistral.ai 
    https://open.bigmodel.cn
    https://vitals.vercel-insights.com;
  worker-src 'self' blob:;
```

### 7. **Service Worker Security**

**Cache Restrictions:**

```javascript
// sw.js - Fetch event handler
self.addEventListener('fetch', event => {
  // Skip external API requests
  const url = new URL(event.request.url);
  if (
    url.origin !== self.location.origin ||
    event.request.url.includes('googleapis.com') || 
    event.request.url.includes('bigmodel.cn') ||
    event.request.url.includes('api.mistral.ai')
  ) {
    return; // Don't cache sensitive API requests
  }
  
  // Cache only same-origin resources
  event.respondWith(caches.match(event.request).then(/* ... */));
});
```

### 8. **Data Export Security**

**Import Validation:**

```typescript
const handleImportPreview = (event: React.ChangeEvent<HTMLInputElement>) => {
  const file = event.target.files?.[0];
  if (!file) return;
  
  const reader = new FileReader();
  reader.onload = (e) => {
    try {
      const importData = JSON.parse(e.target?.result as string);
      
      // Validate structure
      if (!importData.books || !Array.isArray(importData.books)) {
        throw new Error('Invalid data structure');
      }
      
      // Validate each book
      importData.books.forEach((book: any) => {
        if (!book.id || !book.title || !book.goal) {
          throw new Error('Invalid book data');
        }
      });
      
      // Show preview
      setImportPreview(importData);
      
    } catch (error) {
      alert('Failed to read import file. Please check the file format.');
    }
  };
  
  reader.readAsText(file);
  event.target.value = ''; // Clear input
};
```

---

## Deployment & PWA Capabilities

### 1. **Progressive Web App (PWA) Features**

**Manifest Configuration:**

```json
{
  "name": "Pustakam: AI Book Generation Engine",
  "short_name": "Pustakam",
  "description": "Transform ideas into structured digital books with AI",
  "start_url": "/",
  "display": "standalone",
  "background_color": "#ffffff",
  "theme_color": "#374151",
  "orientation": "portrait-primary",
  "scope": "/",
  "lang": "en",
  "categories": ["education", "productivity"],
  "icons": [
    {
      "src": "/pustakam-logo.png",
      "sizes": "192x192",
      "type": "image/png",
      "purpose": "maskable any"
    },
    {
      "src": "/pustakam-logo.png",
      "sizes": "512x512",
      "type": "image/png",
      "purpose": "maskable any"
    }
  ]
}
```

**Service Worker Strategy:**

```javascript
// Cache-first strategy for static assets
const CACHE_NAME = 'ai-tutor-v1';
const urlsToCache = [
  '/',
  '/index.html',
  '/pustakam-logo.png',
  '/manifest.json'
];

// Install - cache core assets
self.addEventListener('install', event => {
  event.waitUntil(
    caches.open(CACHE_NAME)
      .then(cache => cache.addAll(urlsToCache))
  );
  self.skipWaiting();
});

// Activate - clean old caches
self.addEventListener('activate', event => {
  event.waitUntil(
    caches.keys().then(cacheNames => {
      return Promise.all(
        cacheNames.map(cacheName => {
          if (cacheName !== CACHE_NAME) {
            return caches.delete(cacheName);
          }
        })
      );
    })
  );
  self.clients.claim();
});

// Fetch - serve from cache, fallback to network
self.addEventListener('fetch', event => {
  if (event.request.method !== 'GET') return;

  event.respondWith(
    caches.match(event.request)
      .then(response => {
        if (response) return response;
        
        return fetch(event.request).then(fetchResponse => {
          // Cache successful responses
          if (fetchResponse && fetchResponse.status === 200) {
            const responseToCache = fetchResponse.clone();
            caches.open(CACHE_NAME).then(cache => {
              cache.put(event.request, responseToCache);
            });
          }
          return fetchResponse;
        });
      })
      .catch(() => {
        // Offline fallback
        if (event.request.destination === 'document') {
          return caches.match('/index.html');
        }
      })
  );
});
```

**Install Prompt:**

```typescript
export function usePWA() {
  const [deferredPrompt, setDeferredPrompt] = useState<BeforeInstallPromptEvent | null>(null);
  const [isInstallable, setIsInstallable] = useState(false);
  const [isInstalled, setIsInstalled] = useState(false);

  useEffect(() => {
    // Check if already installed
    const checkIfInstalled = () => {
      if (window.matchMedia('(display-mode: standalone)').matches) {
        setIsInstalled(true);
      }
    };

    // Capture install prompt
    const handleBeforeInstallPrompt = (e: BeforeInstallPromptEvent) => {
      e.preventDefault();
      setDeferredPrompt(e);
      setIsInstallable(true);
    };

    checkIfInstalled();
    window.addEventListener('beforeinstallprompt', handleBeforeInstallPrompt);

    return () => {
      window.removeEventListener('beforeinstallprompt', handleBeforeInstallPrompt);
    };
  }, []);

  const installApp = async () => {
    if (!deferredPrompt) return false;

    await deferredPrompt.prompt();
    const choiceResult = await deferredPrompt.userChoice;
    
    if (choiceResult.outcome === 'accepted') {
      setIsInstallable(false);
      setDeferredPrompt(null);
      return true;
    }
    
    return false;
  };

  return { isInstallable, isInstalled, installApp };
}
```

### 2. **Offline Capabilities**

**Online/Offline Detection:**

```typescript
const [isOnline, setIsOnline] = useState(navigator.onLine);

useEffect(() => {
  const handleOnline = () => { 
    setIsOnline(true); 
    setShowOfflineMessage(false); 
  };

  const handleOffline = () => {
    setIsOnline(false);
    setShowOfflineMessage(true);
    setTimeout(() => setShowOfflineMessage(false), 5000);
  };

  window.addEventListener('online', handleOnline);
  window.addEventListener('offline', handleOffline);
  
  return () => {
    window.removeEventListener('online', handleOnline);
    window.removeEventListener('offline', handleOffline);
  };
}, []);
```

**Offline Feature Availability:**

- ✅ View existing books
- ✅ Edit book content
- ✅ Download as Markdown
- ✅ Download as PDF
- ✅ View analytics
- ✅ Change settings
- ❌ Generate new books (requires internet)
- ❌ Generate modules (requires internet)

### 3. **Vercel Deployment**

**Build Configuration:**

```json
// package.json
{
  "scripts": {
    "dev": "vite",
    "build": "vite build",
    "preview": "vite preview"
  }
}
```

**Vite Configuration:**

```typescript
// vite.config.ts
import { defineConfig } from 'vite';
import react from '@vitejs/plugin-react';

export default defineConfig({
  plugins: [react()],
  optimizeDeps: {
    exclude: ['lucide-react'],
  },
  build: {
    outDir: 'dist',
    sourcemap: false,
    minify: 'terser',
    rollupOptions: {
      output: {
        manualChunks: {
          'react-vendor': ['react', 'react-dom'],
          'markdown': ['react-markdown', 'remark-gfm', 'react-syntax-highlighter'],
          'pdf': ['pdfmake']
        }
      }
    }
  }
});
```

**Vercel Configuration:**

```json
// vercel.json
{
  "buildCommand": "npm run build",
  "outputDirectory": "dist",
  "framework": "vite",
  "rewrites": [
    { "source": "/(.*)", "destination": "/" }
  ],
  "headers": [
    {
      "source": "/sw.js",
      "headers": [
        {
          "key": "Service-Worker-Allowed",
          "value": "/"
        },
        {
          "key": "Cache-Control",
          "value": "public, max-age=0, must-revalidate"
        }
      ]
    },
    {
      "source": "/manifest.json",
      "headers": [
        {
          "key": "Content-Type",
          "value": "application/manifest+json"
        }
      ]
    }
  ]
}
```

### 4. **Performance Metrics**

**Lighthouse Scores (Target):**

- Performance: 90+
- Accessibility: 95+
- Best Practices: 95+
- SEO: 100
- PWA: 100

**Bundle Size Optimization:**

```typescript
// Code splitting
const BookView = lazy(() => import('./components/BookView'));
const SettingsModal = lazy(() => import('./components/SettingsModal'));

// Tree shaking
import { BookOpen, Settings } from 'lucide-react'; // Named imports only
```

### 5. **Analytics Integration**

```typescript
// src/main.tsx
import { inject } from '@vercel/analytics';

inject(); // Initialize Vercel Analytics

// Tracks:
// - Page views
// - User interactions (anonymized)
// - Performance metrics
// - Error rates
```

### 6. **Environment Variables**

```bash
# .env.local (for development)
VITE_GOOGLE_API_KEY=your_key_here
VITE_MISTRAL_API_KEY=your_key_here
VITE_ZHIPU_API_KEY=your_key_here
```

**Usage:**

```typescript
// Only for development/testing
const apiKey = import.meta.env.VITE_GOOGLE_API_KEY || settings.googleApiKey;
```

### 7. **Mobile Optimization**

**Viewport Configuration:**

```html
<meta 
  name="viewport" 
  content="width=device-width, initial-scale=1.0, maximum-scale=1.0, user-scalable=no" 
/>
```

**Touch Optimization:**

```css
/* Prevent overscroll bounce */
.app-container {
  height: 100vh;
  height: 100dvh; /* Dynamic viewport height */
  overflow: hidden;
  position: relative;
}

/* Touch targets */
.btn {
  min-height: 44px; /* iOS touch target */
  min-width: 44px;
}

/* Prevent text selection during drag */
.sidebar {
  user-select: none;
  -webkit-user-select: none;
}
```

**Safe Area Insets:**

```css
/* iOS notch support */
.sidebar {
  padding-top: env(safe-area-inset-top);
  padding-bottom: env(safe-area-inset-bottom);
}
```

---

## Technical Challenges & Solutions

### Challenge 1: **Streaming Text Display**

**Problem:** Displaying real-time AI streaming output without performance degradation.

**Solution:**

- Batch updates every 20 characters instead of every character
- Use `slice(-800)` to show only last 800 characters in UI
- Implement auto-scroll with smooth behavior
- Store full text separately from displayed text

```typescript
// Display only last 800 chars
generatedText: currentText.slice(-800)

// Auto-scroll to bottom
useEffect(() => {
  if (liveFeedRef.current) {
    liveFeedRef.current.scrollTop = liveFeedRef.current.scrollHeight;
  }
}, [displayedText]);
```

### Challenge 2: **PDF Generation with Fonts**

**Problem:** pdfmake requires font files but VFS structure varies across versions.

**Solution:**

- Dynamic font detection with fallback logic
- Multiple VFS location checks
- Error handling with user-friendly messages
- Fallback to Markdown if PDF fails

```typescript
// Smart VFS detection
let vfs = null;
if (fonts?.pdfMake?.vfs) vfs = fonts.pdfMake.vfs;
else if (fonts?.vfs) vfs = fonts.vfs;
else {
  // Deep search
  const findVfs = (obj: any, depth = 0): any => {
    if (depth > 3) return null;
    if (obj?.vfs && typeof obj.vfs === 'object') return obj.vfs;
    // ... recursive search
  };
  vfs = findVfs(fonts);
}
```

### Challenge 3: **Rate Limit Handling**

**Problem:** AI providers have rate limits that cause generation failures.

**Solution:**

- Detect rate limit errors (429, 503 status codes)
- Implement exponential backoff with jitter
- Show user-friendly countdown timer
- Offer model switching as alternative

```typescript
if (response.status === 429) {
  const retryAfter = response.headers.get('Retry-After');
  const delay = retryAfter 
    ? parseInt(retryAfter) * 1000 
    : this.calculateRetryDelay(attempt, true);
  
  await sleep(delay);
  attempt++;
  continue;
}
```

### Challenge 4: **Checkpoint Data Corruption**

**Problem:** LocalStorage can become corrupted, causing app crashes.

**Solution:**

- Validate checkpoint structure before loading
- Try-catch all localStorage operations
- Clear corrupted data automatically
- Provide import/export for backup

```typescript
try {
  const stored = localStorage.getItem(`checkpoint_${bookId}`);
  if (stored) {
    const checkpoint = JSON.parse(stored);
    
    // Validate structure
    if (!checkpoint.completedModuleIds || !Array.isArray(checkpoint.completedModuleIds)) {
      console.warn('Invalid checkpoint structure, ignoring');
      return null;
    }
    
    return checkpoint;
  }
} catch (error) {
  console.warn('Failed to load checkpoint:', error);
  localStorage.removeItem(`checkpoint_${bookId}`);
  return null;
}
```

### Challenge 5: **Mobile Safari Viewport Issues**

**Problem:** iOS Safari has inconsistent viewport height due to address bar.

**Solution:**

- Use `100dvh` (dynamic viewport height) CSS unit
- Calculate safe area insets
- Prevent overscroll bounce
- Handle orientation changes

```css
.app-container {
  height: 100vh;
  height: 100dvh; /* Dynamic viewport - better for mobile */
  overflow: hidden;
}

/* Safe areas for notch */
padding-top: max(16px, env(safe-area-inset-top));
```

### Challenge 6: **Context Awareness Between Modules**

**Problem:** Each module needs to build on previous content without duplication.

**Solution:**

- Include last 2 module summaries in prompt
- Pass module index and total count
- Mark first module for special introduction treatment
- Validate continuity in content

```typescript
const contextSummary = !isFirstModule && previousModules.length > 0 ?
  `\n\nPREVIOUS MODULES CONTEXT:\n${previousModules.slice(-2).map(m =>
    `${m.title}: ${m.content.substring(0, 300)}...`
  ).join('\n\n')}` : '';
```

---

## Future Enhancements

### Planned Features

1. **Multi-Language Support**
    
    - Add support for Spanish, French, German, Chinese
    - Localized UI
    - Language-specific AI prompts
2. **Collaborative Features**
    
    - Share books via link
    - Real-time collaboration
    - Comments and annotations
3. **Advanced Templates**
    
    - Pre-built templates for common book types
    - Custom template creation
    - Template marketplace
4. **Enhanced Analytics**
    
    - Reading time tracking
    - Comprehension quizzes
    - Progress charts
    - Learning path recommendations
5. **Cloud Sync (Optional)**
    
    - Optional cloud backup
    - Sync across devices
    - End-to-end encryption
6. **Audio Generation**
    
    - Text-to-speech integration
    - Podcast-style audio books
    - Voice notes
7. **Interactive Elements**
    
    - Embedded code editors
    - Interactive diagrams
    - Quizzes and assessments
8. **Export Formats**
    
    - EPUB export
    - DOCX export
    - HTML website generation

### Technical Debt

1. **Type Safety Improvements**
    
    - Stricter TypeScript configuration
    - More comprehensive type definitions
    - Runtime type validation
2. **Testing**
    
    - Unit tests for services
    - Integration tests for components
    - E2E tests with Playwright
3. **Error Boundary**
    
    - React Error Boundaries
    - Better error reporting
    - Automatic error recovery
4. **Accessibility**
    
    - ARIA labels
    - Keyboard navigation improvements
    - Screen reader optimization

---

## Conclusion

Pustakam represents a sophisticated approach to AI-powered content generation, combining:

- **Multi-provider AI integration** with seamless switching
- **Robust error handling** with intelligent retry mechanisms
- **Local-first architecture** ensuring privacy and offline capability
- **Professional output** with publication-ready formatting
- **Enterprise-grade recovery** with checkpoint system
- **Modern PWA features** for native app-like experience

The application demonstrates best practices in:

- React architecture and state management
- AI API integration with streaming
- Error recovery and user experience
- Performance optimization
- Security and privacy
- Progressive web app implementation

### Key Metrics

- **Lines of Code**: ~8,000+ (TypeScript/React)
- **Components**: 15+ major components
- **Services**: 3 core services
- **AI Providers**: 3 (11+ models)
- **Export Formats**: 2 (Markdown, PDF)
- **Average Book**: 20,000-40,000 words
- **Generation Time**: 30-60 minutes (depending on model and length)

### Technology Stack Summary

**Frontend**: React 18, TypeScript 5, Tailwind CSS **Build**: Vite 5, ESLint, PostCSS **AI**: Google Gemini, Mistral AI, ZhipuAI **PDF**: pdfmake with custom styling **Markdown**: react-markdown, remark-gfm **PWA**: Service Worker, Web App Manifest **Hosting**: Vercel with edge network **Analytics**: Vercel Analytics

---

**Document Version**: 1.0  
**Last Updated**: October 28, 2025  
**Application Version**: 1.0.0  
**Author**: Technical Documentation for Pustakam AI Book Generation Engine
