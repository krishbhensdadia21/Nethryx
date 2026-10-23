# Nethryx — AI-Powered Network Intelligence & Packet Forensics

NETHRYX is a network traffic analysis dashboard designed to help users inspect packet-capture files, understand traffic patterns, review protocol and endpoint statistics, identify selected suspicious patterns, and explore AI-assisted explanations of network activity.

## Project links

- **Public GitHub repository:** https://github.com/krishbhensdadia21/nethryx
- **Published project explanation video:** https://www.youtube.com/watch?v=-LadDH62X08
- **Project report:** `assets/5_CN_REPORT.pdf` 
- **Presentation:** `assets/5_CN_PPT.pptx`

## Problem statement

Network traffic contains useful information about communication between a device and remote services, but raw packet captures can be difficult for students and beginners to interpret. NETHRYX presents packet-level information through a dashboard, summarizes traffic patterns, and provides readable explanations to support learning and preliminary investigation.

## Objectives

- Analyze supported PCAP and PCAPNG files.
- Summarize packet counts, traffic volume, protocols, IP addresses, ports, and traffic over time.
- Display DNS information and associate IP addresses with DNS names when that information is present in the capture.
- Provide packet-level filtering by protocol, source/destination IP, port, time range, and text query.
- Flag selected patterns, including possible port scans and unusually long DNS queries.
- Provide AI-assisted explanations of extracted capture statistics through the Groq API.
- Make network-analysis results easier to understand through visual summaries.

## Features

- **PCAP/PCAPNG upload:** Upload a supported capture file for analysis. The backend currently limits uploads to 200 MB.
- **Traffic statistics:** Total packets, total bytes, protocol distribution, endpoint data, and traffic timeline.
- **Packet filtering:** Filter captured packet records using protocol, source/destination IP, port, time range, or a text query.
- **DNS visibility:** Inspect DNS query-related information and DNS-derived names where present in the capture.
- **Suspicious-pattern alerts:** Built-in rules flag possible port scans based on TCP SYN packets sent to many distinct destination ports, and unusually long DNS names.
- **AI-assisted insights:** Ask questions about extracted capture statistics using the configured Groq API model.
- **Website automation dashboard:** A separate automation page provides the project's browser-automation workflow, subject to local setup and permissions.
- **Visual dashboard:** Charts for protocol statistics and traffic over time.

## Technology stack

| Component | Technology |
|---|---|
| Frontend | React, Vite |
| Styling | Tailwind CSS, custom CSS |
| Charts | Recharts |
| Icons | Lucide React |
| Backend API | Python, FastAPI, Uvicorn |
| Packet processing | Scapy |
| AI integration | Groq API via HTTPX |
| Browser automation | Playwright |
| Capture inspection and testing | Wireshark |

## Project structure

```text
packet-analyzer/
├── README.md
├── frontend/
│   ├── package.json
│   ├── index.html
│   ├── vite.config.js
│   ├── tailwind.config.js
│   └── src/
│       ├── App.jsx
│       ├── Home.jsx
│       ├── PcapPage.jsx
│       ├── Automation.jsx
│       ├── Glass.jsx
│       ├── main.jsx
│       └── index.css
└── backend/
    ├── main.py
    ├── automation.py
    ├── capture.py
    ├── requirements.txt
    └── .env.example
```

## Architecture overview

1. The user opens the React frontend.
2. The user uploads a supported PCAP/PCAPNG file or uses the available automation workflow.
3. The FastAPI backend parses the uploaded capture using Scapy and derives packet, protocol, endpoint, timeline, and DNS-related information.
4. The frontend displays the available statistics, charts, packet records, and rule-based alerts.
5. When a Groq API key is configured, the user can ask questions about the extracted capture statistics and receive AI-assisted explanations.

## Prerequisites

- Python 3.10 or a compatible version supported by the installed dependencies.
- Node.js 18 or later and npm.
- Git.
- A Groq API key for the AI chat feature.
- Playwright browser dependencies if using browser automation.

## Setup and run locally

Run backend and frontend in **separate terminals**. The commands below assume you have extracted the archive and are in the directory that contains the `packet-analyzer` folder.

### 1. Open the project folder

```bash
cd packet-analyzer
```

### 2. Configure and start the backend

From the `packet-analyzer` directory:

```bash
cd backend
python -m venv .venv
```

Activate the virtual environment.

**Windows PowerShell:**

```powershell
.venv\Scripts\Activate.ps1
```

**Windows Command Prompt:**

```bat
.venv\Scripts\activate.bat
```

**macOS/Linux:**

```bash
source .venv/bin/activate
```

Install dependencies:

```bash
pip install -r requirements.txt
```

Create a local `.env` file by copying `.env.example`.

**Windows Command Prompt:**

```bat
copy .env.example .env
```

**PowerShell or macOS/Linux:**

```bash
cp .env.example .env
```

Edit `backend/.env` and set your own Groq API key. The model setting should match the model configured in the project and available to your Groq account:

```env
GROQ_API_KEY=your_groq_api_key_here
GROQ_MODEL=openai/gpt-oss-120b
```

Start the backend from the `backend` directory:

```bash
uvicorn main:app --reload --port 8000
```

The backend should be available at `http://localhost:8000`. FastAPI's interactive API documentation is normally available at `http://localhost:8000/docs`.

### 3. Configure and start the frontend

Open a **second terminal**. From the `packet-analyzer` directory:

```bash
cd frontend
npm install
npm run dev
```

Open the local URL printed by Vite, normally `http://localhost:5173`.

The frontend's default backend URL is `http://localhost:8000`. If you deploy the backend elsewhere, configure `VITE_API_URL` for the frontend build and make sure the backend CORS settings allow the frontend origin.

### 4. Test the project

- Confirm the frontend and backend are both running.
- Upload a small PCAP/PCAPNG file that you are authorized to analyze.
- Check packet totals, protocol summaries, endpoint statistics, and timeline charts.
- Try packet filters and review any built-in suspicious-pattern alerts.
- Configure the Groq API key and test the AI chat feature.
- Test browser automation separately and only visit websites you are authorized to access.
- Compare relevant packet details with Wireshark when validating results.

## Configuration and security

- Keep the real Groq API key in `backend/.env` on your own machine.
- Commit only the sanitized `backend/.env.example` with placeholder credentials.
- Never publish API keys, tokens, cookies, personal browsing captures, or other sensitive traffic.
- Do not commit virtual environments, `node_modules`, temporary captures, or Python cache files.
- If a real secret was previously published, revoke or rotate it with the provider. Removing it in a later commit does not remove it from Git history.
- Analyze only network traffic you own or have explicit permission to inspect.

## Limitations

- The backend currently accepts `.pcap` and `.pcapng` files up to 200 MB.
- The application keeps uploaded capture results in server memory for the running process; they are not a permanent database and may be lost when the backend restarts.
- Packet detail rows are capped at 300,000 rows for dashboard analysis. Large captures may show a truncated set of rows even though the total packet count is reported.
- HTTPS/TLS encryption generally prevents the application from reading encrypted application content. Metadata such as endpoints, ports, packet sizes, timing, and some DNS information may still be visible.
- A capture can only show traffic that was actually recorded. Missing packets or DNS lookups limit what can be inferred.
- IP addresses and DNS names do not always prove which website or application generated every packet. Shared hosting, CDNs, encrypted DNS, VPNs, and background services can complicate attribution.
- The suspicious-pattern rules are basic heuristics and may produce false positives or miss malicious activity.
- AI-generated explanations may be incomplete or incorrect and should be checked against packet evidence.
- Browser automation depends on local Playwright/browser setup and operating-system permissions.

## Team members

| No. | Student name | PRN Number |
|---:|---|---|
| 1 | Krish Bhensdadia | 1262250028 |
| 2 | Pooja Shah | 1262251181 |
| 3 | Aayush Potdar | 1262250835 |
| 4 | Adarsh Bhosale | 1262252198 |
| 5 | Aditya Dharankar | 1262250024 |

## References

- Wireshark User Guide — https://www.wireshark.org/docs/
- Scapy Documentation — https://scapy.readthedocs.io/
- FastAPI Documentation — https://fastapi.tiangolo.com/
- React Documentation — https://react.dev/
- Vite Documentation — https://vite.dev/
- Groq Documentation — https://console.groq.com/docs
- Playwright Documentation — https://playwright.dev/python/

## Academic use

NETHRYX is intended for educational network analysis and authorized troubleshooting. Analyze only traffic you own or have permission to inspect. Do not use the application to intercept or inspect other people's traffic without authorization.
