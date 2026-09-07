You are a Principal Backend Architect and Senior Node.js Engineer.

Build a production-ready Node.js Express backend that acts as a DROP-IN REPLACEMENT for AppDynamics Analytics APIs during development.

The objective is NOT to create a dashboard, KPI engine, risk engine, AI engine, or frontend.

The objective is ONLY to create a backend that behaves exactly like AppDynamics Analytics so that later I can simply replace the mock server hostname with the real AppDynamics controller hostname and everything continues working.

========================================================
PRIMARY GOAL
========================================================

The frontend/service layer should make exactly the same request it would make to AppDynamics.

Current:

POST http://localhost:3000/controller/restui/analytics/adql/query

Future:

POST https://company.appdynamics.com/controller/restui/analytics/adql/query

The frontend should not require any code changes.

Only the hostname/base URL should change.

========================================================
TECH STACK
========================================================

Node.js
Express.js
JavaScript
Axios
dotenv
cors
dayjs
uuid

========================================================
PROJECT STRUCTURE
========================================================

src/

├── app.js
├── server.js

├── config/
│   └── env.js

├── routes/
│   └── analytics.routes.js

├── controllers/
│   └── analytics.controller.js

├── services/
│   └── analytics.service.js

├── providers/
│   ├── provider.factory.js
│   ├── mock.provider.js
│   └── appdynamics.provider.js

├── generators/
│   ├── session.generator.js
│   ├── crash.generator.js
│   └── seed.generator.js

├── storage/
│   ├── sessions.store.js
│   └── crashes.store.js

├── middleware/
│   └── error.middleware.js

└── utils/
    └── date.utils.js

.env
.env.example
.gitignore

========================================================
ENVIRONMENT VARIABLES
========================================================

Create .env support.

Store ALL sensitive values inside .env.

Example:

PORT=3000

DATA_SOURCE=mock

APPD_BASE_URL=https://company.appdynamics.com

APPD_CSRF_TOKEN=

APPD_COOKIE=

APPD_USERNAME=

APPD_PASSWORD=

APP_KEY=

Do NOT hardcode any secrets.

========================================================
GITIGNORE
========================================================

Ensure the following are ignored:

node_modules
.env
dist
coverage

========================================================
DATA SOURCE SWITCHING
========================================================

Support:

DATA_SOURCE=mock

and

DATA_SOURCE=appdynamics

ProviderFactory should automatically choose:

MockProvider

or

AppDynamicsProvider

No frontend code changes should be required.

========================================================
MOCK DATA GENERATION
========================================================

Generate realistic mobile analytics data on startup.

Do NOT use static JSON files.

Generate:

500,000 mobile session records

and

5,000 crash records

covering the previous 30 days.

========================================================
SESSION RECORD MODEL
========================================================

{
  sessionId,
  timestamp,
  platform,
  appVersion,
  deviceModel,
  osVersion,
  region,
  city,
  connectionType,
  battery,
  memory,
  userId,
  crashCount
}

========================================================
CRASH RECORD MODEL
========================================================

{
  eventTimestamp,
  platform,
  deviceModel,
  osVersion,
  mobileAppVersion,
  crashException,
  crashFunction,
  crashFile,
  crashLineNumber,
  geoCity,
  geoRegion,
  connectionType,
  battery,
  memory,
  userdata: {
      userid
  },
  breadcrumb
}

========================================================
SUPPORTED PLATFORMS
========================================================

Android
iOS

Distribution:

60% Android
40% iOS

========================================================
SUPPORTED DEVICES
========================================================

Samsung A54
Samsung S24
Pixel 9
OnePlus 13
iPhone 15
iPhone 16

========================================================
SUPPORTED REGIONS
========================================================

Mumbai
Delhi
Bangalore
Hyderabad
Chennai

========================================================
SUPPORTED APP VERSIONS
========================================================

8.3.0
8.3.1
8.3.2

========================================================
HISTORICAL DATA REQUIREMENT
========================================================

Generate data across the previous 30 days.

Every record must contain realistic timestamps.

Distribute records naturally across all days.

Create realistic daily fluctuations.

========================================================
MOCK APPDYNAMICS API
========================================================

Implement EXACTLY this endpoint:

POST /controller/restui/analytics/adql/query

This path MUST match AppDynamics.

========================================================
REQUEST FORMAT
========================================================

Accept requests like:

{
  "requests": [
    {
      "query": "select count(*) from mobile_session_records",
      "label": "total"
    },
    {
      "query": "select count(*) from mobile_session_records where metrics.crashcount > 0",
      "label": "crashed"
    },
    {
      "query": "select count(*) from mobile_session_records where platform='android' and metrics.crashcount > 0",
      "label": "android_crashed"
    },
    {
      "query": "select count(*) from mobile_session_records where platform='ios' and metrics.crashcount > 0",
      "label": "ios_crashed"
    }
  ],
  "start": "1788252075609",
  "end": "1788338475609"
}

========================================================
SUPPORTED LABELS
========================================================

total

crashed

android_crashed

ios_crashed

========================================================
RESPONSE FORMAT
========================================================

Return EXACTLY:

[
  {
    "label":"total",
    "results":[[152265]]
  },
  {
    "label":"crashed",
    "results":[[77]]
  },
  {
    "label":"android_crashed",
    "results":[[47]]
  },
  {
    "label":"ios_crashed",
    "results":[[30]]
  }
]

The structure must match AppDynamics.

========================================================
TIME FILTERING
========================================================

This is the most important requirement.

Every request contains:

start
end

Only data within that time range should be considered.

Example:

const filtered = records.filter(
  record =>
    record.timestamp >= start &&
    record.timestamp <= end
);

All counts must be calculated from filtered data.

Do NOT return hardcoded counts.

========================================================
QUERY HANDLING
========================================================

For now, do not build a full SQL parser.

Use the label field to determine what result to calculate.

Supported labels:

total

crashed

android_crashed

ios_crashed

Implement the architecture in a way that more labels can easily be added later.

========================================================
AUTHENTICATION HEADERS
========================================================

Accept AppDynamics-style headers:

Content-Type

X-CSRF-TOKEN

Cookie

Authorization

For mock mode:

Accept and ignore them.

Do not validate.

This ensures frontend compatibility.

========================================================
APPDYNAMICS PROVIDER
========================================================

Implement AppDynamicsProvider.

Do NOT activate it.

Use axios.

Read:

APPD_BASE_URL

APPD_CSRF_TOKEN

APPD_COOKIE

from .env

Provider should be capable of calling:

POST /controller/restui/analytics/adql/query

against a real AppDynamics controller.

========================================================
ERROR HANDLING
========================================================

Add:

404 handler

500 handler

validation for missing request body

validation for invalid timestamps

========================================================
README
========================================================

Generate a complete README containing:

Setup

Installation

Environment Variables

Running the Project

API Examples

Curl Examples

Provider Switching

Folder Structure

========================================================
PACKAGE.JSON
========================================================

Include scripts:

npm run dev

npm start

Use nodemon for development.

========================================================
FINAL REQUIREMENT
========================================================

Generate complete runnable source code.

The backend must:

1. Start successfully.
2. Generate 30 days of realistic data.
3. Expose POST /controller/restui/analytics/adql/query.
4. Return AppDynamics-compatible responses.
5. Support time-range filtering.
6. Support future switching to a real AppDynamics controller.
7. Keep all secrets in .env.
8. Ensure .env is ignored by git.
9. Require no frontend code changes when switching from mock to AppDynamics.
