# BDF Lab 01 - Node.js & Express Basics

A foundational backend development lab demonstrating how to build web servers using both Node.js built-in `http` module and the Express.js framework.

---

## 🌐 Live Deployment

🔗 **Live URL:** [https://labreport1-pi.vercel.app/](https://labreport1-pi.vercel.app/)

- 🏠 **Home:** [https://labreport1-pi.vercel.app/](https://labreport1-pi.vercel.app/)
- 👤 **About:** [https://labreport1-pi.vercel.app/about](https://labreport1-pi.vercel.app/about)
- ⏰ **Time:** [https://labreport1-pi.vercel.app/time](https://labreport1-pi.vercel.app/time)

---

## 👤 Author Information

- **Name:** Giddaluru Revathi
- **Section:** CSE AIML-2B

---

## 📁 Project Structure

```text
BDF-Lab-01/
├── api/                  # Vercel serverless entry point
├── node_modules/         # Installed dependencies (git ignored)
├── screenshots/          # Output and verification screenshots
├── .gitignore            # Git ignore file
├── package.json          # Project metadata & dependencies
├── package-lock.json     # Dependency lockfile
├── plain-server.js       # Basic server using Node.js 'http' module
├── server-http.js        # Express.js server with routes and 404 handler
├── vercel.json           # Vercel deployment & routing config
└── README.md             # Project documentation
```

---

## 🚀 Getting Started

### Prerequisites

Ensure you have [Node.js](https://nodejs.org/) (v18 or higher recommended) installed.

Check versions:
```bash
node -v
npm -v
```

### Installation

Clone the repository and install dependencies:

```bash
git clone https://github.com/giddalururevathi1-tech/Lab_Report_1.git
cd Lab_Report_1
npm install
```

---

## 💻 Running the Servers

### 1. Plain HTTP Server
Runs a lightweight server built with the native Node.js `http` module:

```bash
node plain-server.js
```
- **URL:** [http://localhost:3000](http://localhost:3000)
- **Response:** Plain text `"Hello"`

---

### 2. Express Server
Runs an Express.js server with route handling:

```bash
node server-http.js
```
- **URL:** [http://localhost:3000](http://localhost:3000)

---

## 📡 API Endpoints (Express Server)

| Method | Endpoint | Live Link | Description | Sample Response |
| :--- | :--- | :--- | :--- | :--- |
| `GET` | `/` | [Open /](https://labreport1-pi.vercel.app/) | Welcome page | `Welcome to my Node.js and Express Lab!` |
| `GET` | `/about` | [Open /about](https://labreport1-pi.vercel.app/about) | Student / author details | `{"name": "Giddaluru Revathi", "section": "CSE AIML-2B "}` |
| `GET` | `/time` | [Open /time](https://labreport1-pi.vercel.app/time) | Current date and timestamp | `{"currentDateTime": "2026-10-31T14:36:21.166Z"}` |
| `*` | Any other route | [Open /random](https://labreport1-pi.vercel.app/random) | 404 error handler | `404 - Page Not Found` |

---

## 📸 Screenshots

| Lab Execution & Output |
| :---: |
| ![Welcome Route](screenshots/welcome.png) |
| ![About Route](screenshots/about.png) |
| ![Time Route](screenshots/time.png) |
| ![404 Not Found Route](screenshots/404-not-found.png) |

---

## 🛠️ Built With

- [Node.js](https://nodejs.org/) - JavaScript Runtime
- [Express.js](https://expressjs.com/) - Web framework for Node.js