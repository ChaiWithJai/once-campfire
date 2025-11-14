# COMPREHENSIVE CODEBASE CARTOGRAPHY ANALYSIS
## once-campfire Repository

---

## 1. PROJECT OVERVIEW

**Project Name:** Campfire (once-campfire)

**Type:** Full-stack web-based real-time chat application (single-tenant)

**Primary Language:** Ruby (Rails backend) with JavaScript (Stimulus controllers)

**Purpose:**
Campfire is a modern web-based chat application providing:
- Multiple rooms with granular access controls
- Direct messaging between users
- File attachments with previews
- Real-time search functionality
- Web Push notifications
- @mention support
- Bot integration via webhooks
- PWA capabilities

**Tech Stack:**
- **Backend Framework:** Rails 8.1 (main branch)
- **Runtime:** Ruby 3.4.5
- **Database:** SQLite3
- **Web Server:** Puma (6.6+)
- **Cache/Jobs:** Redis (5.4+)
- **Background Jobs:** Resque (2.7.0)
- **Real-time:** ActionCable with WebSocket support
- **Frontend Framework:** Hotwire (Turbo + Stimulus)
- **Asset Pipeline:** Propshaft + Importmap-rails

---

## 2. ARCHITECTURE

**Architectural Pattern:** MVC with Real-time Broadcasting

**Key Architectural Principles:**
- Single-tenant deployment model
- Event-driven real-time messaging via ActionCable
- Background job processing for async tasks
- RESTful API design with bot authentication support
- Modular concerns-based composition
- Streaming updates via Turbo Streams

**Core Layers:**

```
┌─────────────────────────────────────────────────────────┐
│          Frontend (Hotwire + Stimulus)                  │
│  - Turbo Streams for real-time updates                  │
│  - Stimulus controllers for interactivity               │
│  - PWA with Service Worker support                      │
└──────────────────┬──────────────────────────────────────┘
                   │
┌──────────────────▼──────────────────────────────────────┐
│     Puma (Web Server + Rails App)                       │
│  - Controllers (REST endpoints)                         │
│  - Views (ERB templates)                                │
│  - ActionCable (WebSocket server)                       │
└──────────────────┬──────────────────────────────────────┘
                   │
┌──────────────────▼──────────────────────────────────────┐
│     Application Layer (Models & Business Logic)         │
│  - ActiveRecord models with concerns                    │
│  - Message publishing system                            │
│  - Webhook delivery system                              │
│  - Web Push notification system                         │
│  - Search indexing (FTS5)                               │
└──────────────────┬──────────────────────────────────────┘
                   │
┌──────────────────▼──────────────────────────────────────┐
│     Data Layer & External Services                      │
│  - SQLite3 database with FTS5 search                    │
│  - Active Storage (file attachments)                    │
│  - Redis (caching, pub/sub)                             │
│  - Web Push API (third-party)                           │
│  - Webhook endpoints (external bots)                    │
└─────────────────────────────────────────────────────────┘
```

**Asynchronous Processing:**
- Resque pool for background jobs (1-5 workers based on CPU count)
- Jobs: Bot webhook delivery, push message distribution
- Redis pub/sub for ActionCable broadcasts

---

## 3. DIRECTORY STRUCTURE

```
once-campfire/
├── app/
│   ├── assets/               # Images, sounds, stylesheets
│   ├── channels/             # ActionCable channels (WebSocket)
│   │   ├── application_cable/
│   │   ├── room_channel.rb
│   │   ├── presence_channel.rb
│   │   ├── typing_notifications_channel.rb
│   │   ├── read_rooms_channel.rb
│   │   ├── unread_rooms_channel.rb
│   │   └── heartbeat_channel.rb
│   ├── controllers/          # HTTP request handlers
│   │   ├── accounts/         # Account management, bots, users
│   │   ├── messages/         # Message creation, editing, boosts
│   │   ├── rooms/            # Room CRUD and involvement
│   │   ├── users/            # User profiles, avatars, push subscriptions
│   │   ├── sessions/         # Authentication
│   │   ├── searches/         # Message search
│   │   └── [other controllers]
│   ├── helpers/              # View helpers (~26 helper modules)
│   ├── javascript/           # Frontend JS (Stimulus + utilities)
│   │   ├── controllers/      # 30+ Stimulus controllers
│   │   ├── lib/              # Autocomplete, rich text, unfurl systems
│   │   ├── models/           # JS models for frontend state
│   │   └── initializers/     # JS setup
│   ├── jobs/                 # Background jobs
│   │   ├── bot/webhook_job.rb
│   │   └── room/push_message_job.rb
│   ├── models/               # ActiveRecord models (38 models)
│   │   ├── user/             # User concerns (Role, Bot, Avatar, Transferable, Mentionable)
│   │   ├── message/          # Message concerns (Attachment, Broadcasts, Mentionee, Pagination, Searchable)
│   │   ├── room/             # Room logic (MessagePusher)
│   │   ├── rooms/            # Room subtypes (Open, Closed, Direct)
│   │   ├── membership/        # Membership concerns (Connectable)
│   │   ├── push/             # Push subscription model
│   │   ├── opengraph/        # Link preview/unfurl system
│   │   ├── account/          # Account concerns (Joinable)
│   │   └── [core models: Room, User, Message, Account, etc.]
│   └── views/                # ERB templates organized by domain
│       ├── layouts/
│       ├── rooms/            # Room display templates
│       ├── messages/         # Message templates
│       ├── users/            # User-related views
│       ├── accounts/         # Account settings
│       └── [other views]
├── config/
│   ├── initializers/         # Rails initializers (14 files)
│   │   ├── web_push.rb       # Web Push setup with custom pool
│   │   ├── sentry.rb         # Error tracking
│   │   ├── extensions.rb     # Load custom Rails extensions
│   │   ├── vapid.rb          # Web Push VAPID keys
│   │   ├── content_security_policy.rb
│   │   └── [other initializers]
│   ├── environments/         # Environment-specific configs
│   ├── application.rb        # Rails app configuration
│   ├── routes.rb             # Route definitions
│   ├── database.yml          # Database configuration (SQLite3)
│   ├── puma.rb               # Puma server configuration
│   ├── cable.yml             # ActionCable adapter config
│   └── [other config files]
├── db/
│   ├── migrate/              # Database migrations (11 migrations)
│   │   ├── 20231215043540_create_initial_schema.rb
│   │   ├── 20231220143106_add_bio_to_users.rb
│   │   ├── 20240110071740_create_sessions.rb
│   │   └── [other migrations]
│   └── schema.sql            # SQL schema (FTS5 search index)
├── lib/
│   ├── rails_ext/            # Custom Rails extensions
│   │   ├── action_text_attachables.rb
│   │   ├── filter.rb
│   │   └── [utility modules]
│   ├── restricted_http/      # Private network guard
│   ├── web_push/             # Custom Web Push pool
│   └── [custom libraries]
├── test/                     # Test suite
│   ├── controllers/          # Controller tests
│   ├── models/               # Model tests
│   ├── channels/             # Channel tests
│   ├── system/               # System/integration tests
│   ├── fixtures/             # Test data
│   └── test_helpers/         # Test utilities
├── script/                   # Administrative scripts
├── public/                   # Static assets
├── Dockerfile                # Multi-stage production build
├── Gemfile                   # Gem dependencies (45 main gems + dev/test)
├── Procfile                  # Process definitions for Foreman
├── bin/
│   ├── boot                  # Container boot script
│   ├── setup                 # Development setup
│   ├── start-app             # App startup wrapper
│   └── [other executables]
└── README.md                 # Documentation
```

---

## 4. CORE COMPONENTS

### 4.1 Data Models (16 core models)

**User Model** (`app/models/user.rb`)
- Roles: member, administrator, bot
- Concerns: Avatar, Bot, Mentionable, Role, Transferable
- Relationships:
  - Many memberships → rooms
  - Many messages (as creator)
  - Many push subscriptions
  - Many boosts (reactions)
  - Many searches
  - Many sessions
- Key Features:
  - Secure password with BCrypt
  - Auto-grant membership to open rooms
  - Deactivation with email obfuscation
  - Remote connection management

**Room Model** (`app/models/room.rb`)
- Base class with STI: Rooms::Open, Rooms::Closed, Rooms::Direct
- Relationships:
  - Many memberships → users
  - Many messages
  - Belongs to creator (User)
- Key Features:
  - Automatic membership management
  - Involvement levels (invisible, nothing, mentions, everything)
  - Message receipt handling
  - Push notification delivery

**Room Subtypes** (Single Table Inheritance)
- **Rooms::Open**: Public to all users; new users auto-join
- **Rooms::Closed**: Private; users explicitly invited
- **Rooms::Direct**: 1-to-1 messaging; singleton per user pair

**Message Model** (`app/models/message.rb`)
- Concerns: Attachment, Broadcasts, Mentionee, Pagination, Searchable
- Relationships:
  - Belongs to room & creator (User)
  - Many boosts (reactions/emojis)
  - Rich text body (ActionText)
- Key Features:
  - Client-side message ID tracking
  - Automatic broadcasting on creation
  - Audio playback support (/play command)
  - FTS5 full-text search indexing

**Membership Model** (`app/models/membership.rb`)
- Links users to rooms
- Concerns: Connectable
- Involvement enum: invisible, nothing, mentions, everything
- Tracking:
  - Connection state (connected_at, connections)
  - Unread status per room
  - Read/unread states

**Account Model** (`app/models/account.rb`)
- Single account per Campfire instance
- Joinable concern (join codes)
- One attached logo (ActiveStorage)

**Webhook Model** (`app/models/webhook.rb`)
- Bot integration endpoints
- Delivers messages to external endpoints
- Handles timeouts (7s limit)
- Receives text or attachment responses

**Session Model** (`app/models/session.rb`)
- Session tracking
- Secure tokens
- Activity timestamps
- User agent & IP logging

**Push::Subscription Model** (`app/models/push/subscription.rb`)
- Web Push API subscriptions
- Stores endpoint, keys (p256dh, auth)
- User agent tracking

**Other Core Models**
- **Boost**: Reaction/emoji reactions on messages
- **Search**: Recent search history (keeps last 10)
- **FirstRun**: One-time setup wizard
- **Sound**: Built-in sound effects (48 sounds with emojis/images)
- **ApplicationPlatform**: Browser/OS detection (iOS, Android, Chrome, Safari, etc.)
- **Current**: CurrentAttributes for request scope (user, request)
- **Push**: Namespace module for push subscriptions
- **Opengraph**: Link preview/unfurl system (Document, Metadata, Fetch, Location)

### 4.2 Controllers (12 main controllers + subcategories)

**RoomsController**
- Show room with paginated messages
- Destroy (with authorization)
- Redirect to last visited room

**MessagesController**
- Create messages with attachments
- Edit/update messages
- Destroy messages
- Deliver webhooks to mentioned bots
- Pagination (before/after cursors, 40 per page)

**SessionsController**
- New/create (with rate limiting: 10/3min)
- Destroy (logout)
- Push subscription cleanup

**AccountsController**
- Edit account settings
- Update account name/logo
- Pagination for users

**UsersController**
- New (signup/join)
- Create (with join code)
- Show profile

**Sub-controllers** (nested under resources)
- Accounts::UsersController
- Accounts::BotsController
- Accounts::Bots::KeyController (bot key rotation)
- Accounts::JoinCodesController
- Rooms::InvolvementController
- Rooms::SettingsController
- Rooms::RefreshController
- Users::AvatarController
- Users::ProfileController
- Users::Push_SubscriptionsController
- Messages::BoostsController

**Other Controllers**
- SearchesController: Full-text message search
- FirstRunsController: Initial setup
- SessionsController: Authentication
- PWAController: Service worker, manifest
- QRCodeController: Account join QR code
- UnfurlLinksController: Link preview generation
- WelcomeController: Landing page

### 4.3 Background Jobs (2 main jobs)

**Bot::WebhookJob** (`app/jobs/bot/webhook_job.rb`)
- Delivers messages to bot webhook endpoints
- Enqueued when messages mention bots or in direct rooms

**Room::PushMessageJob** (`app/jobs/room/push_message_job.rb`)
- Distributes push notifications to users
- Routes through MessagePusher for selective delivery
- Based on involvement level (everything vs mentions)

### 4.4 WebSocket Channels (6 channels)

**RoomChannel** (`app/channels/room_channel.rb`)
- Base channel; streams room-specific messages
- Authorization check before subscription

**PresenceChannel** (`app/channels/presence_channel.rb`)
- Extends RoomChannel
- Tracks user presence (online/offline)
- Broadcasts when users join/leave
- Manages connection count & state

**TypingNotificationsChannel** (`app/channels/typing_notifications_channel.rb`)
- Real-time typing indicators
- Broadcast when user starts typing

**ReadRoomsChannel** (`app/channels/read_rooms_channel.rb`)
- Broadcasts when room is marked as read
- Syncs read state across devices

**UnreadRoomsChannel** (`app/channels/unread_rooms_channel.rb`)
- Broadcasts unread room updates
- Used for sidebar badge updates

**HeartbeatChannel** (`app/channels/heartbeat_channel.rb`)
- Keep-alive channel for long-lived connections
- Periodic ping/pong

---

## 5. ENTRY POINTS

### 5.1 Application Boot
**File:** `/home/user/once-campfire/bin/boot`
- Loads Bundler environment
- Requires Rails app
- Executes configured processes via Thrust

**Startup Flow:**
```
1. bin/boot (entry point)
2. bin/start-app (app startup)
3. Puma server initialization (Puma.rb)
4. Rails app initialization (config/application.rb)
5. Database migrations auto-run
6. ActionCable subscription setup
7. Resque worker pool starts
```

### 5.2 Web Request Flow
```
Request → Puma → Thrust (HTTP/SSL) → Rails Router
→ Controller (with concerns) → Model layer → Database/Cache
→ View (Turbo Streams or ERB)
```

### 5.3 Real-time Initialization
- ActionCable connects via WebSocket
- Subscribes to RoomChannel, PresenceChannel, etc.
- Establishes presence tracking
- Starts typing notification listener

### 5.4 FirstRun Setup
- Checks if users exist
- Redirects to FirstRunsController if none
- Creates Account, Administrator user, and "All Talk" room
- Auto-populates room membership

---

## 6. DEPENDENCIES

### 6.1 Core Frameworks
| Gem | Version | Purpose |
|-----|---------|---------|
| rails | main branch | Full-stack framework |
| puma | ~6.6 | Web server |
| sqlite3 | ~2.7 | Database |
| redis | ~5.4 | Caching & pub/sub |
| resque | ~2.7.0 | Job queue |
| resque-pool | ~0.7.1 | Job worker pool |

### 6.2 Frontend & Assets
| Gem | Purpose |
|-----|---------|
| propshaft | Asset pipeline |
| importmap-rails | JavaScript bundling |
| turbo-rails | Real-time streaming |
| stimulus-rails | JavaScript framework |

### 6.3 Media & Content
| Gem | Purpose |
|-----|---------|
| image_processing | Image processing (with libvips) |
| web-push | Web Push notifications |
| rqrcode | QR code generation |
| rails_autolink | Auto-linkify text |
| jbuilder | JSON responses |

### 6.4 Infrastructure & Utilities
| Gem | Purpose |
|-----|---------|
| bcrypt | Password hashing |
| kredis | Redis data structures |
| geared_pagination | Cursor-based pagination |
| platform_agent | User agent parsing |
| thruster | HTTP/HTTPS proxy |
| net-http-persistent | Connection pooling |
| sentry-ruby, sentry-rails | Error tracking |

### 6.5 Development & Testing
| Gem | Purpose |
|-----|---------|
| rubocop-rails-omakase | Style linting |
| faker | Fake data generation |
| brakeman | Security scanning |
| capybara | Acceptance testing |
| mocha | Mocking/stubbing |
| selenium-webdriver | Browser automation |
| webmock | HTTP mocking |

**Total Dependencies:** ~45 main gems + dev/test gems

---

## 7. DATA MODELS & SCHEMAS

### 7.1 Database Schema Overview

**Tables:**
- `accounts` - Single instance configuration
- `users` - User profiles & authentication
- `rooms` - Chat rooms (polymorphic: Open/Closed/Direct)
- `messages` - Room messages with rich text
- `memberships` - Room membership & involvement levels
- `boosts` - Message reactions/emojis
- `push_subscriptions` - Web Push endpoints
- `sessions` - User session tokens
- `searches` - Recent search history
- `webhooks` - Bot webhook endpoints
- `action_text_rich_texts` - Rich text bodies
- `active_storage_blobs` - File metadata
- `active_storage_attachments` - File associations
- `active_storage_variant_records` - Image variant cache
- `message_search_index` - FTS5 full-text search

### 7.2 Key Data Relationships

```
Account (1)
  ├─ has_many :users
  └─ has_one_attached :logo

User (many)
  ├─ has_many :memberships → Room
  ├─ has_many :rooms (through memberships)
  ├─ has_many :messages (as creator)
  ├─ has_many :push_subscriptions
  ├─ has_many :boosts (reactions)
  ├─ has_many :searches
  ├─ has_many :sessions
  ├─ has_secure_password
  └─ enum :role (member, administrator, bot)

Room (polymorphic STI)
  ├─ Rooms::Open (auto-join all users)
  ├─ Rooms::Closed (explicit invite)
  └─ Rooms::Direct (1:1 messaging)

  ├─ has_many :memberships → User
  ├─ has_many :users (through memberships)
  ├─ has_many :messages
  ├─ belongs_to :creator (User)
  └─ has_one_attached :logo (via Account)

Message
  ├─ belongs_to :room
  ├─ belongs_to :creator (User)
  ├─ has_many :boosts
  ├─ has_rich_text :body (ActionText)
  └─ has_one_attached :attachment (optional)

Membership
  ├─ belongs_to :room
  ├─ belongs_to :user
  ├─ enum :involvement (invisible, nothing, mentions, everything)
  ├─ tracks :unread_at
  └─ tracks :connections, :connected_at

Boost (Reaction)
  ├─ belongs_to :message
  ├─ belongs_to :booster (User)
  └─ content: emoji/reaction string

Push::Subscription
  ├─ belongs_to :user
  ├─ stores endpoint, keys (p256dh, auth)
  └─ tracks user_agent

Session
  ├─ belongs_to :user
  ├─ has_secure_token
  └─ tracks last_active_at, user_agent, ip_address

Webhook
  ├─ belongs_to :user (bot)
  └─ url: endpoint for webhooks

Search
  ├─ belongs_to :user
  └─ tracks query (last 10 per user)
```

### 7.3 Enums

| Model | Enum | Values |
|-------|------|--------|
| User | role | member, administrator, bot |
| Membership | involvement | invisible, nothing, mentions, everything |

---

## 8. API/INTERFACES

### 8.1 RESTful Endpoints

```
Sessions
  POST   /session                    → create (login)
  DELETE /session                    → destroy (logout)

Account Management
  GET    /account/edit               → edit account settings
  PATCH  /account                    → update account
  POST   /account/join_code          → create join code
  GET    /account/logo               → show logo
  DELETE /account/logo               → remove logo
  GET|PATCH /account/custom_styles   → manage branding

  GET    /account/users              → list users
  POST   /account/users              → create user
  DELETE /account/users/:id          → remove user

  GET|POST /account/bots             → list/create bots
  PATCH    /account/bots/:id         → update bot
  DELETE   /account/bots/:id         → delete bot
  PATCH    /account/bots/:id/key     → rotate bot key

Rooms
  GET    /rooms                      → list rooms (redirects to last)
  GET    /rooms/:id                  → show room with messages
  DELETE /rooms/:id                  → destroy room

  GET    /rooms/:id/refresh          → refresh room
  GET    /rooms/:id/settings         → room settings
  PATCH  /rooms/:id/involvement      → set involvement level

  GET    /rooms/opens                → list public rooms
  GET    /rooms/closeds              → list private rooms
  GET    /rooms/directs              → list direct messages

Messages
  POST   /rooms/:room_id/messages           → create message
  GET    /rooms/:room_id/messages           → paginated list (with before/after cursors)
  GET    /rooms/:room_id/messages/:id       → show single message
  PATCH  /rooms/:room_id/messages/:id       → edit message
  DELETE /rooms/:room_id/messages/:id       → delete message

  POST   /rooms/:room_id/:bot_key/messages  → bot message creation (webhook)
  POST   /rooms/:room_id/messages/:id/boosts → add reaction
  DELETE /messages/:id/boosts/:id           → remove reaction

Users
  GET    /users/:id                  → show user profile
  PATCH  /users/me/profile           → update own profile (name, bio)

  GET    /users/:id/avatar           → get avatar image
  DELETE /users/:id/avatar           → remove avatar

  POST   /users/me/push_subscriptions    → register push subscription
  DELETE /users/me/push_subscriptions/:id → unregister push
  POST   /users/me/push_subscriptions/:id/test_notifications → test push

Search
  GET    /searches                   → autocomplete search
  POST   /searches                   → perform search
  DELETE /searches/clear             → clear search history

Autocomplete
  GET    /autocompletable/users      → autocomplete users for mentions

PWA
  GET    /webmanifest                → PWA manifest
  GET    /service-worker             → service worker script

Utilities
  POST   /unfurl_link                → generate link preview
  GET    /qr_code                    → account join QR code
  GET    /up                         → health check

Special Routes
  POST   /join/:join_code            → join account with code
  GET    @:message_id                → jump to specific message
```

### 8.2 WebSocket Channels (Real-time)

```
RoomChannel (room-specific messages)
  subscribe    → stream_for room

PresenceChannel (user presence)
  subscribe    → present; broadcast presence
  unsubscribe  → absent; broadcast absence
  refresh      → refresh connection

TypingNotificationsChannel
  type_start   → broadcast user is typing
  type_end     → broadcast user stopped typing

ReadRoomsChannel
  (broadcasts when membership.unread_at = nil)

UnreadRoomsChannel
  (broadcasts unread room updates)

HeartbeatChannel
  ping         → keep connection alive
```

### 8.3 Bot/Webhook API

**Bot Authentication:**
- Format: `bot_key = "#{user_id}-#{bot_token}"`
- Pass as URL parameter: `?bot_key=123-abc123xyz`
- Or in HTTP Authorization header

**Webhook Delivery (Bot → Campfire):**
```json
POST {webhook_url}
{
  "user": {
    "id": 123,
    "name": "Bot Name"
  },
  "room": {
    "id": 456,
    "name": "Room Name",
    "path": "/rooms/456/bot-key/messages"
  },
  "message": {
    "id": 789,
    "body": {
      "html": "<p>Message with <strong>formatting</strong></p>",
      "plain": "Message with formatting"
    },
    "path": "/rooms/456#message_789"
  }
}
```

**Bot Response Handling:**
- HTTP 200 with `text/html` or `text/plain` → reply as text message
- HTTP 200 with file content → reply as attachment
- Timeout (>7s) → error message posted to room

---

## 9. CONFIGURATION

### 9.1 Environment Variables

| Variable | Purpose | Example |
|----------|---------|---------|
| RAILS_ENV | Environment | development, test, production |
| RAILS_MASTER_KEY | Encryption key | (generated) |
| SECRET_KEY_BASE | Session encryption | (required in production) |
| DATABASE_URL | Database path | sqlite3:storage/db/production.sqlite3 |
| REDIS_URL | Redis connection | redis://localhost:6379/0 |
| PORT | Web server port | 3000 |
| WEB_CONCURRENCY | Puma workers | (auto-calculated) |
| JOB_CONCURRENCY | Resque workers | (auto-calculated) |
| SSL_DOMAIN | Auto-SSL domain | chat.example.com |
| DISABLE_SSL | Skip SSL | true/false |
| VAPID_PUBLIC_KEY | Web Push public key | (generated) |
| VAPID_PRIVATE_KEY | Web Push private key | (generated) |
| SENTRY_DSN | Error tracking | https://key@sentry.io/proj-id |
| SKIP_TELEMETRY | Skip Sentry in prod | true/false |

### 9.2 Configuration Files

**Puma Configuration** (`config/puma.rb`)
- Multi-worker setup (0.666 × CPU count)
- Thread pool (min/max from RAILS_MAX_THREADS)
- Plugin-based restart
- Membership disconnect on reload

**Database Configuration** (`config/database.yml`)
- SQLite3 with connection pooling
- Separate databases: development, test, production, performance
- Immediate transaction mode
- Storage path: `/rails/storage/db/`

**ActionCable** (`config/cable.yml`)
- Redis adapter (production)
- In-memory for development

**Initializers** (14 custom initializers)
- **web_push.rb**: Web Push pool with custom request handling
- **sentry.rb**: Error tracking setup
- **vapid.rb**: Web Push key initialization
- **extensions.rb**: Load custom Rails extensions
- **content_security_policy.rb**: CSP headers
- **permissions_policy.rb**: Permissions-Policy headers
- **filter_parameter_logging.rb**: Hide passwords in logs
- **session_store.rb**: Session storage config
- **active_storage.rb**: File storage setup
- **assets.rb**: Asset precompilation
- **time_formats.rb**: Custom time formats
- **storage_paths.rb**: Consistent storage paths
- **inflections.rb**: Pluralization rules
- **version.rb**: App version tracking

### 9.3 Security Configuration

**CSRF Protection** (`app/controllers/application_controller.rb`)
- Enabled except for bot key authentication

**Content Security Policy** (`config/initializers/content_security_policy.rb`)
- Restricts inline scripts
- Allows form submissions to current host
- Media from HTTP/HTTPS sources

**Passwords & Sessions**
- BCrypt hashing with `has_secure_password`
- Secure session tokens
- HttpOnly, SameSite=Lax cookies
- Session activity tracking (1-hour refresh)

---

## 10. TESTING

### 10.1 Test Structure

```
test/
├── controllers/          # Controller unit tests
│   ├── accounts/
│   ├── messages/
│   ├── rooms/
│   ├── users/
│   └── [other controllers]
├── models/               # Model unit tests
│   ├── user/
│   ├── message/
│   ├── room/
│   └── [other models]
├── channels/             # ActionCable channel tests
├── system/               # End-to-end tests (Capybara + Selenium)
├── fixtures/             # Test data (YAML format)
├── helpers/              # Helper method tests
├── performance/          # Performance benchmarks
├── test_helpers/         # Custom test utilities
│   ├── session_test_helper.rb
│   ├── mention_test_helper.rb
│   └── turbo_test_helper.rb
└── test_helper.rb        # Main test configuration
```

### 10.2 Testing Framework

**Framework:** Minitest (Rails default)

**Gems:**
- **mocha**: Mocking and stubbing
- **capybara**: Browser automation framework
- **selenium-webdriver**: Chrome/Firefox automation
- **webmock**: HTTP request mocking
- **faker**: Fake data generation

### 10.3 Test Configuration

**File:** `test/test_helper.rb`
```ruby
- Parallelizes tests (by processor count)
- Includes fixtures (all YAML files)
- Includes custom test helpers:
  - SessionTestHelper
  - MentionTestHelper
  - TurboTestHelper
- Clears ActionCable pub/sub between tests
- Resets Web Push pool
- Enables WebMock for HTTP stubbing
```

### 10.4 Testing Patterns

- Unit tests for models and concerns
- Controller tests for request/response
- Channel tests for WebSocket behavior
- System tests for critical user flows
- Integration tests with mocked external services

---

## 11. BUILD & DEPLOYMENT

### 11.1 Dockerfile (Multi-stage)

**Stage 1: Base**
- Ruby 3.4.5 slim image
- System packages: libsqlite3, libvips, ffmpeg, redis
- jemalloc for memory optimization

**Stage 2: Build**
- Installs build tools
- Bundles gems
- Precompiles assets with dummy SECRET_KEY_BASE

**Stage 3: Final**
- Minimal production image
- Copies built artifacts from build stage
- Runs as non-root user (rails:1000)
- Exposes ports 80, 443

**Build Settings:**
- Environment: production
- No dev/test gems
- jemalloc memory allocation
- Thruster HTTP/HTTPS proxy

### 11.2 Process Management

**Procfile:**
```
web: bundle exec thrust bin/start-app
redis: redis-server config/redis.conf
workers: FORK_PER_JOB=false INTERVAL=0.1 bundle exec resque-pool
```

**Thrust:**
- HTTP/HTTPS reverse proxy
- SSL via Let's Encrypt
- Automatic cert renewal
- Configuration via environment variables

### 11.3 Deployment Configuration

**Docker Run Example:**
```bash
docker build -t campfire .
docker run \
  --publish 80:80 --publish 443:443 \
  --restart unless-stopped \
  --volume campfire:/rails/storage \
  --env SECRET_KEY_BASE=$KEY_BASE \
  --env VAPID_PUBLIC_KEY=$PUB_KEY \
  --env VAPID_PRIVATE_KEY=$PRIV_KEY \
  --env SSL_DOMAIN=chat.example.com \
  campfire
```

**Volume Mounting:**
- `-v campfire:/rails/storage` → Persists database, attachments, logs

### 11.4 Build Artifacts

**Entry Point:** `bin/boot`
- Loads environment
- Starts Puma, Redis, Resque workers
- All managed by Thrust

**Asset Pipeline:**
- Propshaft (Asset compilation)
- Precompiled in Docker build phase
- Served via Thrust with far-future expires

---

## 12. KEY PATTERNS & ARCHITECTURAL DECISIONS

### 12.1 Design Patterns

**Model Concerns (Composition over Inheritance)**
- User: Avatar, Bot, Mentionable, Role, Transferable
- Message: Attachment, Broadcasts, Mentionee, Pagination, Searchable
- Membership: Connectable
- Room: Base with STI for Open/Closed/Direct
- Benefits: Modular, reusable, testable

**Single Table Inheritance (Rooms)**
```ruby
class Room < ApplicationRecord
end

class Rooms::Open < Room        # public, auto-join
class Rooms::Closed < Room      # private, explicit invite
class Rooms::Direct < Room      # 1:1 messaging
```

**Current Request Tracking**
```ruby
class Current < ActiveSupport::CurrentAttributes
  attribute :user, :request
end
# Access via Current.user, Current.account throughout app
```

**Streaming Patterns**
- Turbo Streams for real-time updates
- ActionCable pub/sub for broadcasts
- Room-scoped streams for privacy

**Background Jobs Pattern**
- Resque for job queuing
- Rate limiting (sessions: 10/3min)
- Async webhook delivery
- Push notification batching

### 12.2 Architectural Conventions

**Authorization Pattern** (Concerns: Authentication, Authorization)
```ruby
before_action :require_authentication    # in ApplicationController
skip_before_action :require_authentication, only: [:new, :create]
before_action :ensure_can_administer      # for sensitive actions
```

**Pagination Pattern** (Cursor-based)
```ruby
# Instead of offset/limit, uses message.created_at boundaries
@messages = @room.messages.page_before(@ref_message)  # 40 per page
```

**Search Implementation** (FTS5)
```ruby
# Full-text search index on SQLite3
Message.search(query)  # Automatically joins message_search_index
```

**Involvement Levels** (Membership tracking)
```ruby
invisible  # Don't show room at all
nothing    # Show room, no notifications
mentions   # Notify on @mentions only
everything # Notify on all messages
```

**Connection State Tracking** (Membership::Connectable)
```ruby
# Tracks active connections per user per room
# TTL: 60 seconds
# Used for intelligent push notification delivery
```

### 12.3 Hosting & Multi-tenancy

**Single-Tenant Model:**
- One Account per Campfire instance
- All users share same instance
- Public rooms visible to all
- Closed rooms selectively visible

**Scalability Patterns:**
- Horizontal scaling via Puma workers
- Resque worker pool scales with CPU
- Redis for distributed caching
- SQLite suitable for single-machine deployments

**Storage Strategy:**
- Active Storage for file attachments
- Local filesystem default (Docker volume)
- Supports cloud storage backends (S3, GCS, etc.)

### 12.4 Real-time Architecture

**Event Flow:**
```
User creates message
→ Message.create!
→ after_create_commit → broadcast_create
→ Turbo Stream broadcast to room
→ Also queues push notifications
→ Room::PushMessageJob processes
→ Distributes to relevant subscriptions
```

**Presence Tracking:**
```
User connects via PresenceChannel
→ present() increments connections
→ broadcast presence update
→ On disconnect: disconnected() decrements
→ When connections < 1, marks disconnected_at = nil
```

**Notification Routing:**
- MessagePusher.push selects recipients based on involvement
- Everything involvement: always notified
- Mentions involvement: only if mentioned
- Respects connected_at (only offline users)

### 12.5 Notable Implementation Details

**Message Client ID Tracking:**
```ruby
# Prevents duplicate display if message arrives twice
before_create { self.client_message_id ||= Random.uuid }
```

**Sound System:**
```ruby
# Sound effects via /play command
Sound.find_by_name("bell")  # Returns structured data with emoji/image
```

**Bot Token Format:**
```ruby
bot_key = "#{user_id}-#{bot_token}"  # e.g., "123-abc123xyz"
# Enables unique authentication per bot instance
```

**Opengraph Fetching:**
- Unfurls link previews server-side
- Extracts og:title, og:image, og:description
- Caches in message body
- Sanitizes encoding issues

**Rich Text Attachments:**
- ActionText stores formatted content
- User mentions stored as attachable objects
- Hashtags for future tagging
- Link unfurling integrated

---

## SUMMARY

The **once-campfire** repository is a well-architected, modern Rails application demonstrating:

1. **Clean Architecture**: Clear separation of concerns via models, controllers, helpers, and jobs
2. **Real-time Capabilities**: Production-ready ActionCable WebSocket implementation
3. **Scalable Design**: Multi-worker process management, background job processing, connection pooling
4. **Security-First**: CSRF protection, password hashing, rate limiting, secure sessions
5. **Developer Experience**: Comprehensive routing, helper utilities, test suite with fixtures
6. **Deployment-Ready**: Multi-stage Docker build, environment configuration, health checks
7. **Feature-Rich**: Chat, presence, notifications, file attachments, search, bots, PWA support

**Codebase Statistics:**
- ~116 Ruby files (app code)
- ~38 models with concerns-based composition
- 12+ main controllers with sub-resources
- 30+ Stimulus JavaScript controllers
- 14 initializers
- 11 database migrations
- Comprehensive test suite with fixtures
- ~45 gems with production focus

The application follows Rails conventions closely while adding custom extensions for specific needs (Web Push pooling, link unfurling, sound effects). It's production-ready and single-container deployable via Docker.

---

**Analysis Date:** 2025-11-14
**Generated by:** Claude Code Codebase Cartography Analysis
