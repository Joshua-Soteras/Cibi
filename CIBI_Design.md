# CIBI Design

## SDLC Practice
1. Requirements and Discovery
2. System Design and Architecture
3. Development Implementation
4. Testing and Quality Assurance (QA)
5. Deployment and CI/CD
6. Monitoring and Maintenance

> Agile is a philosophy and Scrum is a practice of that philosophy.

---

## Requirements and Discovery

### Problem Statement and Goals

#### Who is the User
Fitness-conscious individuals who want to build consistent nutrition and workout habits. They range from beginners just starting their health journey to experienced athletes looking to optimize their performance through better data tracking.

#### The Pain Point

**Food Logging:**
- Manually logging meals requires opening an app, searching for each food item, and entering nutritional values by hand — a process that is slow and repetitive.
- Users often give up on tracking because the effort of logging outweighs the perceived benefit, especially when eating foods without easily searchable entries.
- Without consistent logs, users lose visibility into how their diet is affecting their energy, weight, and fitness goals.
- What is lost: time, consistency, and meaningful nutritional insight.

**Gym Logging** *(out of scope for MVP — planned for future phase):*
- Users must manually type out each machine, muscle group, weight, reps, and sets — which is tedious mid-workout.
- Adjusting a routine on the fly (e.g., when a machine is occupied) adds friction that discourages new users.
- What is lost: time, efficiency, and motivation.

#### What Success Looks Like
- Users can log a meal in under 30 seconds by pointing their camera at a nutrition label.
- Users maintain consistent food logs over time due to reduced friction.
- Users gain clear, actionable insight into their nutrition through charts and LLM-powered queries.
- The app requires no manual data entry for labeled food items.

---

### Scope

**MVP Focus:** Food logging only. Gym logging is defined but deferred to a future development phase.

---

### User Requirements

#### Food Logging

1. The user shall be able to scan nutrition labels using the camera to log food.
2. The user shall be able to save and view previously logged food items.
3. The user shall be able to search for food using the camera to extract nutritional values.
4. The user shall be able to categorize meals as breakfast, lunch, dinner, or snack.
5. The user shall be able to define diet windows and time frames (e.g., intermittent fasting).
6. The user shall be able to view their nutritional intake daily, weekly, monthly, and yearly.
7. The user shall be able to view nutritional data through charts and visual summaries.
8. The user shall be able to query an LLM to retrieve food logs, insights, and trends using RAG or agentic tools.

---

### Functional Requirements

#### Food Logging

- The system shall request and manage camera permissions on the device.
- The system shall use the Apple Vision framework to scan and extract text from nutrition labels.
- The system shall parse extracted text into structured nutritional fields (calories, protein, carbohydrates, fat, serving size).
- The system shall pre-populate a food log entry with parsed values and allow the user to confirm or edit before saving.
- The system shall store food log entries locally on-device.
- The system shall retrieve and display previously saved food log entries.
- The system shall allow users to tag each log entry with a meal category (breakfast, lunch, dinner, snack).
- The system shall allow users to set and manage diet windows and time frame configurations.
- The system shall aggregate nutritional data and display it across daily, weekly, monthly, and yearly views.
- The system shall render nutritional data as charts (e.g., bar, line, or pie charts).
- The system shall provide a query interface allowing users to ask the on-device LLM questions about their food logs.
- The system shall use RAG or agentic tooling to ground LLM responses in the user's actual log data.
- The system shall query a food database API when a user logs food without a physical nutrition label.

---

### Non-Functional Requirements

- **Performance:** Nutrition label scanning and parsing shall complete in under 3 seconds under normal conditions.
- **Privacy:** All user data shall be stored on-device. No personal data shall leave the device without explicit user consent.
- **Availability:** The core logging features (scan, save, view) shall function fully offline.
- **Compatibility:** The application shall support iPhone 15 and later models with AFM / Apple Intelligence support.
- **Usability:** A user shall be able to complete a full food log entry via camera scan in under 30 seconds.

---

## System Design and Architecture

### Tech Stack
- **Language:** Swift + SwiftUI
- **Platform:** Native iOS application
- **Target Devices:** iPhone 15–17 (any iPhone model with AFM / Apple Intelligence support)

### Local AI Model
The on-device model will be either AFM (Apple Foundation Model) or Gemma 4. Performance testing is required to determine which model best suits the application's needs.

| Model | Pros | Cons |
|---|---|---|
| **AFM (Apple Foundation Model)** | Native Apple integration, optimized for Apple Silicon, no setup overhead | Limited to Apple ecosystem, less flexible |
| **Gemma 4** | Open model, potentially more capable, cross-platform | Requires manual integration, larger binary size |

### Local Persistence
*Decision pending.*

| Option | Pros | Cons |
|---|---|---|
| **SwiftData** | Modern Swift-native, built for SwiftUI, less boilerplate, easy to migrate to CloudKit later | Newer (iOS 17+), less community resources |
| **CoreData** | Mature, well-documented, CloudKit sync support | Verbose, older API style, less natural with SwiftUI |
| **SQLite (via GRDB)** | Full control, fast, lightweight | Most manual work, no built-in Apple ecosystem sync |

### Backend
Fully on-device. No backend for MVP. Cloud sync (e.g., CloudKit) is a planned future feature.

### Camera / OCR
Apple Vision framework will be used for scanning nutrition labels and extracting text.

### Food Database (for label-less logging)
When a user logs food without a physical label, a food database API will be used as the data source. *Decision pending.*

| Option | Pros | Cons |
|---|---|---|
| **USDA FoodData Central** | Free, no API key required, large dataset | Less consumer-friendly food names |
| **Open Food Facts** | Free, open-source, large barcode database | Community-maintained, inconsistent data quality |
| **Nutritionix API** | Clean API, restaurant + branded foods, natural language search | Paid after free tier |
| **Edamam Food Database** | Good nutrition detail, recipe support | Paid after free tier |

### Open Design Questions
- [ ] High-level architecture diagram
- [ ] Data models (e.g., `FoodLog`, `Meal`, `NutritionEntry`)
- [ ] API design (key endpoints or service interfaces)
- [ ] RAG / agentic tool design for LLM food queries

---

## Development Implementation

*To be defined.*

---

## Testing and Quality Assurance (QA)

*To be defined.*

---

## Deployment and CI/CD

*To be defined.*

---

## Monitoring and Maintenance

*To be defined.*
