# ECI Affidavit Downloader — Tamil Nadu

Python + Playwright automation for downloading candidate affidavit PDFs from the Election Commission of India's affidavit portal: https://affidavit.eci.gov.in/

The workflow is configured for the Tamil Nadu Assembly General/Bye Election data described below. Review the portal, current configuration, and applicable terms before running it; website structure and available records can change.

## ✨ What It Does

1. Opens the ECI affidavit portal.
2. Applies the configured election, period, type, and state filters.
3. Selects the Accepted results tab.
4. Iterates through candidate records with pagination support.
5. Opens each candidate's details and downloads the affidavit PDF.
6. Organizes downloaded files by constituency and candidate.

**Default output structure**

```text
<output-directory>/
└── <Constituency Name>/
    └── <Candidate Name>.pdf
```

The configured output path is documented in `config.yaml`. Review it before the first run.

## 🚀 Highlights

- **Resumable runs:** stores progress in `progress.json` so completed candidates can be skipped.
- **Retry handling:** retries transient UI and network failures with exponential backoff.
- **Dry-run mode:** allows workflow inspection without downloading PDFs.
- **Structured logs:** writes informational and error logs under `logs/`.
- **Download validation:** checks that a downloaded file exists and is non-empty.
- **Graceful stop:** saves progress when interrupted with Ctrl+C.
- **Configurable selectors:** keeps portal selectors and runtime settings in `config.yaml`.
- **Human verification:** pauses for manual intervention when a verification challenge is detected; it does not attempt to bypass security controls.

## 🧰 Project Structure

| File | Purpose |
| --- | --- |
| `main.py` | Automation workflow |
| `config.yaml` | Selectors, paths, retry and browser settings |
| `requirements.txt` | Python dependencies |
| `.env.example` | Optional environment configuration |

## ⚙️ Requirements

- Python 3.11 or later
- pip
- Chromium browser installed through Playwright
- Network access to the ECI portal

## 🛠️ Installation

### 1. Clone the repository

```bash
git clone https://github.com/mohanrajgunasekaran/election-affidavit-downloader-2026.git
cd election-affidavit-downloader-2026
```

### 2. Create and activate a virtual environment

**Windows (PowerShell)**

```powershell
py -3 -m venv .venv
.\.venv\Scripts\Activate.ps1
```

**Linux / macOS**

```bash
python3 -m venv .venv
source .venv/bin/activate
```

### 3. Install dependencies and browser

```bash
python -m pip install --upgrade pip
pip install -r requirements.txt
python -m playwright install chromium
```

### 4. Review configuration

Open `config.yaml` and verify:
- election filters and state;
- output directory;
- selectors match the current portal;
- timeout, retry, and throttle settings are appropriate.

If the project uses optional environment variables, copy `.env.example` to `.env` and configure only the values you need. Never commit credentials or private environment files.

## ▶️ Run

Check available options with:

```bash
python main.py --help
```

Example dry run:

```bash
python main.py --dry-run true --headless false --max-candidates 10
```

Example run with downloads enabled:

```bash
python main.py --headless false --max-candidates 50 --resume true --dry-run false
```

The CLI options documented by this project include:

- `--headless true/false`
- `--max-candidates N`
- `--resume true/false`
- `--dry-run true/false`
- `--config path/to/config.yaml`

CLI values override runtime values in `config.yaml`, where supported by the current implementation.

## 📋 Progress and Logs

- `progress.json` records candidate processing status and supports resume behaviour.
- `logs/info.log` records the normal workflow.
- `logs/error.log` records failures and exceptions.

Review the run summary and sample a few downloaded PDFs before relying on a large batch. Treat logs and progress files as operational data; inspect them before sharing publicly.

## 🔧 Troubleshooting

**Selectors stop working**
- Run in headful mode and inspect the failing step.
- Check the selectors in `config.yaml` against the current page.
- Test with a small dry run before downloading a larger batch.

**Navigation or element timeouts**
- Check network stability and the portal's response.
- Adjust timeout values carefully and reduce the batch size while diagnosing.

**Downloads are missing or empty**
- Verify the download selector and the candidate detail flow.
- Check the logs and confirm the output directory is writable.
- Validate the resulting PDFs before using them.

**Human verification appears**
- Complete any challenge manually if permitted and supported by the workflow.
- Do not automate or bypass CAPTCHA, authentication, rate limits, or other access controls.

## ⚖️ Responsible Use

This project automates access to publicly available records, but public availability does not remove usage restrictions. Follow the portal's terms, applicable law, and reasonable request-rate limits. Use throttling, avoid excessive traffic, and do not bypass access controls. The project is provided as-is; changes to the portal may require maintenance.

## 🗺️ Future Improvements

- Add automated tests for parsing, progress tracking, and retry behaviour.
- Add a sanitized example log and sample configuration.
- Add CI checks for formatting and basic tests.
- Produce a machine-readable end-of-run summary for auditing.

---

Built with Python and Playwright.
