# HMI Explorer

## Description
**HMI Explorer** is an AI-powered tool that uses **Reinforcement Learning (RL)** to automatically explore and map human-machine interfaces (HMIs). The system combines **OCR-based state recognition** with **RL-driven decision making** to navigate through UI screens intelligently and efficiently.  
It explores UI screens, discovers navigation paths, and builds a **graph-based representation** of the HMI, allowing for automated test generation and system validation.  

The tool interacts with HMIs **like a real user**, performing actions such as:
- Clicking buttons, selecting menu items, and entering inputs.
- Observing UI changes and understanding screen transitions.
- Mapping navigation paths into a structured **graph representation**.
- Handling popups, alerts, and conditional UI states.
- Recognizing UI elements using **text, images, and structural patterns**.
- Saving exploration data for **replay and comparison**.

## Features

### 1. **HMI Configurations & RL Learning Persistence**
- Users can create and save **multiple configurations** for different HMI systems.
- Each configuration contains:
  - **Exploration routes & discovered screens**.
  - **Navigation graphs & AI decision history**.
  - **AI learning data (Reinforcement Learning models)**.
- RL learning **is saved per configuration and never reset**, allowing AI to improve over time.

### 2. **Graph-Based UI Representation (Live Updating)**
- Builds a **directed graph** where:
  - **Nodes = Screens/views**.
  - **Edges = Actions performed to navigate between screens**.
- **Live Execution Visualization**:
  - Shows **step-by-step interactions** as the AI explores.
  - Highlights **current screen, last action performed, and next action**.
  - Provides a timeline of **previous actions and paths taken**.

### 3. **Manual Mode (User-Guided AI)**
- If the AI **gets stuck**, users can manually **guide it** by:
  - Selecting the correct action to proceed.
  - Marking **non-interactive UI elements** to avoid unnecessary attempts.
- Manual adjustments are **saved to the RL model**, improving future exploration.

### 4. **AI-Driven Decision Making (RL & Heuristic-Based)**
- **Reinforcement Learning (RL)** helps the AI optimize its navigation strategy over time.
- **Heuristic-based exploration** ensures systematic coverage of all UI inputs.
- Two exploration strategies:
  1. **Broad Exploration (Main Menu First)** – Maps high-level sections first.
  2. **Deep Exploration (Full Paths First)** – Explores the longest paths before returning.
- RL learning **adapts dynamically** based on past discoveries.

### 4. **Systematic Input Exploration**
- AI agents **must try all available inputs in each view** before moving forward.
- Ensures no interactive elements are missed (buttons, toggles, sliders, etc.).
- If an input leads to a new screen, it is recorded as a new node in the graph.

### 5. **Action & State Recognition**
- Detects available user interactions (buttons, links, form inputs).
- Recognizes **screen names** using visible text labels.
- Uses **image-based AI** to identify graphical UI elements (e.g., maps, icons).
- Supports **fallback strategies** for UI elements without text (e.g., layout-based recognition).

### 6. **Popup & Alert Handling**
- Identifies **temporary UI elements** like popups and modal dialogs.
- Determines whether popups block further navigation or allow interactions.
- Handles **interactive alerts**, such as confirmation messages.

### 7. **Re-Exploration Mode (Partial vs. Full Scan)**
- **Partial Scan Mode:**  
  - Re-explores only selected sections of the UI based on user-defined entry points.  
  - Useful for verifying specific areas after an HMI update.  
- **Full Scan Mode:**  
  - Performs a complete UI traversal from the main menu.  
  - Detects and logs all changes, including modified navigation paths.  

### 8. **Data Export & Visualization**
- Saves the discovered UI structure in multiple formats (JSON, YAML, GraphML).
- Provides a **real-time visualization tool** to display the navigation graph.
- Allows exporting test scenarios based on the discovered HMI structure.

### 9. **Handling Dynamic UI Elements**
- The AI recognizes **changing elements like maps, loading screens, and animations**.
- Strategies for handling dynamic elements:
  - **Detect and Ignore:** Filters out temporary UI elements.
  - **Adaptive Recognition:** Identifies recurring elements as part of the system.
  - **Delayed Actions:** Waits for loading screens before proceeding.
- If an element is unpredictable, the **Manual Mode** allows user intervention.

## System Architecture

### Core Modules

#### 1. **Reinforcement Learning (RL) Module**
- **Decision Engine**: Makes choices based on current state and learned patterns
- **Exploration Strategies**:
  - **Q-Learning**: Optimal path discovery through value function approximation
  - **Epsilon-Greedy**: Balances exploration and exploitation
- **Internal State-Action Logging**: Tracks decisions and outcomes
- **Dynamic Strategy Switching**: Allows runtime strategy changes

#### 2. **State Recognition (SR) Module**
- Performs OCR on screen content
- Identifies UI elements and their properties
- Processes raw data into structured state representation
- Works synchronously with RL for state updates

#### 3. **Action Executor (AE) Module**
- Executes RL-decided actions on HMI
- Supports keyboard and mouse interactions
- Coordinates with UI Stabilizer for timing
- Handles action verification and retry logic

#### 4. **UI Stabilizer (US) Module**
- Ensures UI state stability between actions
- Prevents premature state captures
- Manages transition timing and validation
- Signals system readiness for next action

#### 5. **Error Handler (EH) Module**
- Manages unrecognized states
- Implements fallback strategies
- Coordinates recovery actions
- Logs error patterns for analysis

### Sequence Diagram in PlantUML
```
@startuml
actor User
entity "ExplorerAI" as AI
entity "StateRecognizer" as SR
entity "ActionExecutor" as AE
entity "ReinforcementLearning" as RL
entity "UIStabilizer" as US
entity "ErrorHandler" as EH
entity "ExplorationLog" as Log

User -> AI : Start exploration
AI -> SR : capture_ui()
SR -> SR : Perform OCR on screen
SR -> SR : Extract text and UI elements
SR -> AI : Return OCR data (JSON)
AI -> SR : recognize_state(ocr_data)
SR -> SR : Identify state and UI elements
SR -> AI : Return parsed state (JSON)

AI -> RL : choose_action(parsed_state)
RL -> RL : Analyze state-action history
RL -> AI : Return chosen action

AI -> AE : execute_action(action)
AE -> AE : Perform action on UI
AI -> Log : Log action execution

AI -> US : Wait for UI to stabilize
US -> AI : UI stabilization complete

AE -> SR : capture_ui() (after action)
SR -> SR : Perform OCR on new screen
SR -> SR : Extract updated text and UI elements
SR -> AI : Return updated OCR data (JSON)
AI -> SR : recognize_state(updated_ocr_data)

loop Check and Handle Unrecognized State
    SR -> EH : Check for unrecognized state
    alt Unrecognized state
        EH -> EH : Handle fallback if needed
        EH -> AI : Return handled state or retry
    else Recognized state
        break
    end
end

SR -> AI : Return new state (JSON)

AI -> RL : update_exploration_log(state, action, reward)
RL -> RL : Update knowledge with reward

AI -> Log : Log action and continue exploration
AI -> User : Continue exploration or stop

User -> AI : Stop exploration
AI -> AI : Finalize exploration log
AI -> User : Provide exploration summary
@enduml

```

## User Requirements

### 1. **What Users Need to Use the Tool**
- No programming skills required – AI handles exploration automatically.
- Requires **a running HMI system** (physical device, emulator, or remote interface).
- The tool should have **input access** (keyboard, touchscreen, or API-based control).

### 2. **What Users Can Do**
- **Start an Exploration Session**  
  - Users can trigger an automated scan of the HMI.
  - Real-time UI visualization updates as the tool navigates the interface.

- **Monitor & Adjust AI Exploration (Live Visualization)**
  - View the current **menu, action, and next step** in real-time.
  - See **step-by-step execution details** directly inside the graph.  
  - Pause, resume, or restart the process as needed.  

- **Analyze Navigation Paths**  
  - View the generated **graph representation** of the HMI.
  - Understand how different screens are connected.

- **Compare HMI Versions (Re-Exploration Mode)**  
  - Users can load a previous UI structure and compare it with a new one.
  - The tool highlights **differences, missing elements, and new screens**.
  - Users can select **partial or full UI scans**.

- **Export & Share Findings**  
  - Users can export discovered UI structures in multiple formats.
  - The output can be used for **test case generation** or **UX analysis**.

# RL-Based Adaptive Learning for HMI Explorer

## Introduction
Reinforcement Learning (RL) is a machine learning technique where an **AI agent learns through trial and error**, making decisions based on rewards and penalties.  
For the **HMI Explorer**, RL will improve how the system **navigates and discovers menus**, leading to **more efficient exploration**.

---
# 📂 HMI-Explorer Project Directory Structure

## 📁 HMI-Explorer/  
Root directory containing all source code, configurations, data, and documentation.

### 📁 src/  
Contains the source code for the project.

- 📁 ai/  
  - `rl_model.py` - Reinforcement learning model, handles decision-making.  
  - `exploration_strategy.py` - Supports different RL strategies like Q-Learning, Epsilon-Greedy.  
  - `state_recognition.py` - Uses OCR and UI element detection to determine the current state.  
  - `action_executor.py` - Executes AI-decided actions (keyboard/mouse interactions).  

- 📁 ui/  
  - `graph_visualizer.py` - Visualizes exploration states and actions.  
  - `menu_mapper.py` - Generates UI navigation maps.  
  - `interaction_logger.py` - Logs actions, states, and RL decisions for debugging and analysis.  

- 📁 core/  
  - `explorer.py` - Main exploration engine that orchestrates the AI agent’s workflow.  
  - `ui_stabilizer.py` - Ensures UI stability before proceeding to next action.  
  - `error_handler.py` - Handles unexpected states and errors.  
  - `config_loader.py` - Loads configurations for the exploration system.  

- `main.py` - Entry point for the application.  

### 📁 configs/  
Contains configuration files that define behavior and settings.

- `default_config.json` - Default exploration settings.  
- `user_config.json` - User-defined HMI configurations.  
- `input_mappings.json` - Defines input types for AI.  
- `rl_persistence.json` - Stores reinforcement learning data (Q-tables, policy models, etc.).  

### 📁 data/  
Stores collected exploration data.

- 📁 logs/ - Execution logs.  
- 📁 saved_graphs/ - Saved navigation graphs.  
- 📁 screenshots/ - Captured UI screenshots.  
- 📁 reports/ - Test and exploration reports.  

### 📁 tests/  
Contains unit tests and test scripts.

- `test_explorer.py` - Tests for AI exploration logic.  
- `test_visualizer.py` - Tests for UI visualization.  
- `test_config_loader.py` - Tests for configuration management.  
- `test_error_handler.py` - Tests for error handling mechanisms.  

### 📁 docs/  
Documentation files for the project.

- `README.md` - Project overview and setup guide.  
- `PROJECT_MANAGEMENT.md` - Task tracking and planning.  
- `API_REFERENCE.md` - API documentation for key modules.  
- `ARCHITECTURE.md` - Detailed system architecture and UML diagrams.  

### Other Files  
- `.gitignore` - Ignore unnecessary files in Git.  
- `requirements.txt` - Dependencies and libraries.  
- `setup.py` - Setup script for installation.  
- `LICENSE` - License for the project.  

---

## How RL Will Be Used

### 1. **Action Selection Optimization**
- The AI **learns which inputs** are more likely to lead to new discoveries.
- Instead of random exploration, the agent **prioritizes actions** that have historically led to new screens.
- Rewards:
  - Discovering a **new screen** → High reward.
  - Clicking an already explored button → Low reward.
  - Entering an invalid input (e.g., disabled button) → Negative reward.

### 2. **State-Based Decision Making**
- The agent maintains a **memory of visited states**.
- If the AI **recognizes a repeating pattern**, it avoids redundant actions.
- It dynamically adjusts **between broad-first and deep-first exploration** based on the probability of discovering new paths.

### 3. **Exploration vs. Exploitation Balance**
- **Exploration:** Tries new paths to learn more about the HMI.
- **Exploitation:** Follows the most successful paths based on past experience.
- The AI will balance these two approaches **dynamically**.

---

## Reinforcement Learning Workflow

1. **Initialize Agent & Environment**
   - The AI starts with minimal knowledge about the HMI.
   - It takes exploratory actions **without predefined rules**.

2. **Perform Actions & Observe Results**
   - AI interacts with UI elements **one by one**.
   - Captures **screen transitions, popups, and user feedback**.

3. **Assign Rewards/Penalties**
   - If an action **leads to new UI content**, the agent gets a reward.
   - If an action **leads to no change**, the agent gets a lower reward.
   - If an action **causes an error**, the agent gets a penalty.

4. **Update Learning Model**
   - The agent refines its decision-making strategy.
   - Adjusts future exploration based on success rates.

5. **Repeat Until Convergence**
   - The AI continues learning until it **maps the entire HMI efficiently**.
   - Over time, it develops an **optimal strategy** for discovering menus.

## Expected Benefits of RL
✅ **Faster Exploration:** The AI avoids redundant actions and focuses on promising paths.  
✅ **Smarter Decision Making:** Prioritizes menus with a high likelihood of new discoveries.  
✅ **Adaptive Learning:** Improves with each session, becoming more efficient over time.  
✅ **Less Manual Configuration:** Learns navigation strategies automatically instead of relying on predefined rules.

