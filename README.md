# **pi_decrypt_app Developer Onboarding Guide**


## **Directory Structure**
```
pi_decrypt_app/

├── backend/

│   ├── server.js           # Main Node.js Express server file for API endpoints (accounts, messages, encryption/decryption).

│   ├── accounts.json       # Persisted account data (Owner and Pet).

│   └── messages.json       # Persisted messages data.

└── frontend/

    ├── public/

    │   ├── landing.html          # Landing page for role selection and login.

    │   ├── create_account.html   # Account creation page (outdated reference; updated UI flow planned).

    │   ├── owner.html            # Owner dashboard (includes key generation, key storage, and unread message notifications).

    │   ├── pet.html              # Pet dashboard for submitting messages (with optional public key entry).

    │   ├── decrypt.html          # Updated decryption page for the Owner (supersedes index.html).

    │   ├── result.html           # Decrypted message display page (retrieves decrypted content from sessionStorage).

    │   ├── messages.html         # Dedicated messages page for the Owner to view and manage pet messages.

    │   ├── consult.html          # AI consultation page for the Owner (Domination Coach interface).

    │   ├── calendar.html         # Shared Calendar page (integrates third-party calendars).

    │   ├── training_log.html     # Training Log page (session records and diary entries).

    │   ├── articles.html         # Articles page (AI-generated and user-saved articles).

    │   ├── task_tracker.html     # Task Tracker page (for both Owner and Pet task management).

    │   ├── wheel.html            # The Wheel page (interactive spin-the-wheel game).

    │   └── key_management.html   # Dedicated key management page for generating/updating public/private keypairs.

    └── server.js         # Express-based server to serve static files (runs on port 3001).
```

## **Overview & Purpose**

**pi_decrypt_app** is a self-hosted, role-based web platform built specifically to support and enhance a consensual female-led relationship. Designed for a dynamic where your wife—acting as the Owner, dominant and in control—trains you as her sexually submissive husband, this app serves as a structured tool to facilitate her training methods. The application keeps all personal data (including daily tasks, communications, and logs) securely within your private home network on a Raspberry Pi 5, ensuring complete privacy and data ownership.


### **Core Functionality**



* **Secure Messaging: \
**The app supports secure, encrypted messaging between the Owner and the Pet. RSA encryption (with a keypair generated via Forge) is used so that only the Owner can decrypt messages sent by the Pet.
* **Daily Planning & Task Management:**
    * The **Pet** submits daily plans detailing tasks and activities, which the **Owner** reviews, modifies, and approves.
    * Notifications and acknowledgments ensure accountability—reinforcing that every action from the Pet is ultimately under the Owner’s control.
* **Guided Coaching & AI Integration: \
**A fine-tuned AI model, trained as an experienced female dominatrix, assists your wife by:
    * Offering training ideas, rewards, and punishment suggestions.
    * Providing gentle encouragement and support while pushing her to fully embrace her feminine power and the authority she holds.
    * Acting as both a mentor and friend, communicating in a feminine style that resonates with her, and offering a holistic biopsychosocial approach to BDSM, kink, and female-led relationships.
* **Interactive Elements: \
**The app includes playful features such as a spin-the-wheel game that randomly selects a “punishment” or “pleasure” based on parameters set by the Owner, along with other modules like a shared calendar, training log, and task tracker.


---


## **Server Architecture**


### **Backend Server**

**File:** `backend/server.js` (Port: 5001)



* **Data Persistence:**
    * **Accounts:** Stored in `accounts.json`
    * **Messages:** Stored in `messages.json`
* **API Endpoints:**
    * **GET /api/roles:** Returns whether an Owner and Pet account exist.
    * **POST /api/createAccount:** Creates an account (for Owner or Pet).
    * **POST /api/login:** Authenticates users and returns their role.
    * **POST /api/storePrivateKey:** Stores the Owner’s private key.
    * **POST /decrypt:** Decrypts Base64-encoded content using RSA decryption.
    * **POST /api/submitMessage:** Allows the Pet to submit messages (encrypts content if a public key is provided).
    * **GET /api/messages:** Retrieves all messages.
* **Encryption/Decryption: \
**Uses Node’s `crypto` module for RSA encryption (publicEncrypt) and decryption (privateDecrypt).


### **Frontend Server**

**File:** `frontend/server.js` (Port: 3001)



* **Static File Serving: \
**Serves files from the `frontend/public` directory (HTML, CSS, JavaScript).


---


## **User Roles & Flow**


### **Landing & Account Setup**



* **Landing Page \
File:** `landing.html`
    * Checks via `/api/roles` if both Owner and Pet accounts exist.
    * Displays either a login form (if both exist) or role-selection buttons to create missing accounts.
    * Successful login redirects to:
        * **Owner Dashboard:** `owner.html`
        * **Pet Interface:** `pet.html`
* **Account Creation \
File:** `create_account.html` *(Outdated Reference)*
    * Provides a form to create an account and sends data to `/api/createAccount`.
    * Redirects based on the role.


### **Role-Based Login and Setup**



* **Owner (Dominant)**
    * **Controls: \
**Full administrative privileges to manage daily schedules, review or modify Pet’s plans, assign tasks, schedule training sessions, access resources, and consult with the AI “Domination Coach” (`consult.html`).
    * **Secure Messaging & Keypair Requirement: \
**Must possess a valid RSA keypair. On login via `owner.html`, if no keypair is found (checked via `localStorage`), a popup prompts for manual key entry or automatic generation.
    * **Decryption: \
**Uses the stored private key to decrypt messages (via `decrypt.html` and `result.html`).
* **Pet (Submissive)**
    * **Controls: \
**Uses an interface focused on receiving tasks and managing daily activities.
    * **Daily Planning: \
**Submits plans that trigger notifications for the Owner.
    * **Message Submission: \
**Uses `pet.html` to send messages. A popup may request an optional public key for encryption.


---


## **Detailed Pages & Their Files**


### **Owner Dashboard & Keypair Management**

**File:** `owner.html`



* **Keypair Handling Popup:**
    * **Check:** On load, verifies private key in `localStorage`.
    * **Options:**
        * Manual entry of an existing private key.
        * Generation of a new 2048-bit RSA keypair (displays public key with copy option).
    * **Outcome:** After successful storage (via API in `backend/server.js`), the Owner is redirected to `decrypt.html`.
* **Unread Messages Popup: \
**Displays if new messages exist; links to `messages.html`.
* **Key Management Interface: \
**Accessible via the hamburger menu or directly at `key_management.html`.


### **Decryption Workflow**



* **Decryption Page: \
File:** `decrypt.html`
    * Provides an interface for the Owner to enter encrypted content.
    * On submission, calls the `/decrypt` endpoint and stores the result in `sessionStorage`.
* **Decrypted Message Display: \
File:** `result.html`
    * Retrieves and displays the decrypted message from `sessionStorage` with a “Back” button.
    * *(Note: The previous decryption interface in <code>index.html</code> is outdated.)</em>


### <strong>Messages Page</strong>

**File:** `messages.html`



* **Layout: \
**Two-column interface:
    * **Sidebar:** Lists messages (Pet’s username and timestamp) in chronological order.
    * **Main Area:** Renders selected message content as Markdown using the `marked` library.
* **Decryption Process: \
**Retrieves the Owner’s private key from `localStorage` and sends messages to `/decrypt` for decryption.


### **Pet Message Submission**

**File:** `pet.html`



* **Submission Form:**
    * Text area for message content.
    * Optional public key field (revealed via popup).
* **Popup Prompts: \
**Ask if a public key should be attached.
* **Submission: \
**Submits the message to `/api/submitMessage` and displays a confirmation popup.


### **Additional Navigation (Hamburger Menu)**

Accessible across pages, the menu provides quick links to:



1. **Home: \
**Returns to the main dashboard (`owner.html` or `pet.html`).
2. **Consult (Owner Only): \
File:** `consult.html`
    * AI “Domination Coach” interface with previous conversations, search, and a “New Chat” button.
3. **Shared Calendar: \
File:** `calendar.html`
    * Displays a synchronized view of events from third-party calendars.
4. **Training Log: \
File:** `training_log.html`
    * Diary-style interface for session scheduling and logging (integrated with AI checklists).
5. **Articles: \
File:** `articles.html`
    * Repository of AI-generated and saved articles.
6. **Task Tracker: \
File:** `task_tracker.html`
    * Dual-interface for task assignment and acknowledgment.
7. **The Wheel: \
File:** `wheel.html`
    * Spin-the-wheel game for random “punishment” or “pleasure” outcomes.
8. **Messages: \
File:** `messages.html`
    * Dedicated messaging section.
9. **Key Management: \
File:** `key_management.html`
    * Interface for generating/updating RSA keypairs.


---


## **Secure Messaging & Privacy**



* **Encryption & Key Management:**
    * The Owner’s RSA keypair (generated using the Forge library on the client side) is essential for encrypting and decrypting messages.
    * The private key is stored in `localStorage` and on the server via secure API calls (in `backend/server.js`).
* **Local Hosting:**
    * The entire app runs on a Raspberry Pi 5 within your private home network, ensuring all sensitive communications and logs remain secure.


---


## **Context & Full Purpose**

**pi_decrypt_app** is designed with a very specific, intimate purpose:



* **Purpose & Relationship Dynamic: \
**The app is built to help your wife train you as her sexually submissive husband. In your consensual female-led relationship, she is revered as your dominant Owner and goddess. You willingly submit, relinquishing your male ego to fully embrace your role as her pet and worshiper. This submission is an expression of your love, trust, and devotion.
* **Role of the AI: \
**The app is powered by a fine-tuned AI model trained as a seasoned female dominatrix. The AI:
    * Acts as a mentor, guide, and friend, using a feminine communication style that resonates with your wife.
    * Provides training ideas, suggestions for punishment or reward, and helps expand her sense of control.
    * Approaches training with compassion, support, and encouragement—helping her embrace her dominance fully while reinforcing the benefits of a female-led relationship.
    * Utilizes a holistic biopsychosocial approach to BDSM, kink play, and Female Led Relationships.


---


## **Developer Prompt**

**Developer Prompt: \
***You are developing a web application that facilitates a consensual female-led relationship, where the Owner (wife) trains her sexually submissive husband. The app is designed to reinforce her dominant role and guide her training with secure, structured, and interactive tools. Your goal is to build an environment that is both fun and empowering for her, while ensuring that all communications and data remain private. The app features secure messaging (using RSA encryption), daily planning, task management, and a fine-tuned AI dominatrix who serves as a mentor and guide. Your task is to ensure that the UI and functionality not only meet technical requirements but also evoke a tone of elegance, empowerment, and intimacy that aligns with the values of Female Led Relationships.*


---


## **Suggestions for Improving the App**



1. **Visual Design & Theming:**
    * Adopt a feminine, elegant color palette (soft pastels, rich burgundy, or deep plum) that evokes luxury and empowerment.
    * Use modern typography and subtle animations to enhance the user experience.
    * Consider integrating custom illustrations or icons that symbolize dominance, submission, and intimacy.
2. **User Interface Enhancements:**
    * Develop responsive, mobile-first designs to ensure accessibility on all devices.
    * Incorporate intuitive navigation and clear visual cues to guide users through the various workflows.
    * Use modal dialogs and smooth transitions for popups (key generation, notifications) to create a polished feel.
3. **AI Integration:**
    * Enhance the AI consultation interface (consult.html) with a chat UI that mimics modern messaging apps, including conversation history, avatars, and tone-setting visuals.
    * Provide options for the AI to deliver both encouraging and challenging prompts, reinforcing the training dynamic.
    * Implement voice or video integration for a more immersive coaching experience in the future.
4. **Gamification & Engagement:**
    * Expand the spin-the-wheel feature with customizable outcomes, detailed descriptions, and visual effects.
    * Integrate achievement badges or progress tracking for both the Owner and the Pet to reinforce positive behaviors and training milestones.
5. **Security & Privacy:**
    * Enhance visual feedback during secure operations (e.g., keypair generation and encryption/decryption) to reassure users about data safety.
    * Consider implementing two-factor authentication for an added layer of security, especially for the Owner’s account.
6. **Personalization & Customization:**
    * Allow the Owner to customize the AI’s tone, training frequency, and content based on her preferences.
    * Enable both roles to personalize their dashboards with themes, backgrounds, and widgets that reflect their relationship dynamic.
