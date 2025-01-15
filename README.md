# How to solve CAPTCHA Using pyppeteer and CaptchaSonic

* Sponsored by CaptchaSonic
<a href="https://captchasonic.com/" target="_blank">
    <img src="https://github.com/user-attachments/assets/5556b8a3-4691-405c-9284-63be309ad325" 
         alt="Presskit Card" 
         style="width: 100%; max-width: 600px; border: none;">
</a>


This repository provides a Python-based solution for solving CAPTCHAs using the CaptchaSonic Chrome extension and pyppeteer.

## Features
- Automates CAPTCHA solving for testing or research purposes.
- Integrates the CaptchaSonic Chrome extension to handle CAPTCHA solving.
- Uses pyppeteer for browser automation.

---

## Prerequisites

### Tools and Libraries

1. **Python**: Ensure Python 3.7 or later is installed on your system.
2. **pyppeteer**: Install pyppeteer for Python.
3. **CaptchaSonic API Key**: Get an API key from [CaptchaSonic](https://github.com/Captcha-Sonic).
4. **Dependencies**: Install the required Python libraries listed in `requirements.txt`.

### Installation

1. Install pyppeteer:
   ```bash
   pip install pyppeteer
   
   ```

2. Install additional dependencies:
   ```bash
   pip install python-dotenv pyppeteer
   ```

3. Create a `.env` file in the project directory:
   ```env
   APIKEY=your_api_key
   ```

---

## How to Use

### Step 1: Automation code
The script downloads the CaptchaSonic extension and configures it with your API key.

```python
import asyncio
import os
import zipfile
import urllib.request
import json
from dotenv import load_dotenv
from pyppeteer import launch

load_dotenv()

async def download_and_extract_extension(url, extract_to):
    zip_path = os.path.join(extract_to, 'extension.zip')
    print(f"Downloading extension from {url} to {zip_path}")  # Debug statement
    urllib.request.urlretrieve(url, zip_path)
    with zipfile.ZipFile(zip_path, 'r') as zip_ref:
        zip_ref.extractall(extract_to)
    os.remove(zip_path)
    print(f"Extracted extension to {extract_to}")  # Debug statement
    return extract_to

def update_config_with_api_key(config_path, api_key):
    print(f"Updating config at {config_path} with API key")  # Debug statement
    with open(config_path, 'r') as file:
        config = json.load(file)
    config['APIKEY'] = api_key
    with open(config_path, 'w') as file:
        json.dump(config, file, indent=2)

async def main():
    current_dir = os.getcwd()
    extension_url = 'https://github.com/Captcha-Sonic/CaptchaSonic-Extension/releases/download/v0.1.3/CaptchaSonic_Chrome_v0.1.3.zip'
    extension_dir = await download_and_extract_extension(extension_url, current_dir)
    config_path = os.path.join(extension_dir, 'assets', 'defaultConfig.json')
    api_key = os.getenv('APIKEY')  # Load API key from environment variable
    # Create a .env file and add APIKEY=your_api_key
    update_config_with_api_key(config_path, api_key)

    # Check if manifest file exists
    manifest_path = os.path.join(extension_dir, 'manifest.json')
    if not os.path.exists(manifest_path):
        print(f"Manifest file not found at {manifest_path}")  # Debug statement
    else:
        print(f"Manifest file found at {manifest_path}")  # Debug statement

    browser = await launch(
        headless=False,
        userDataDir=current_dir,
        args=[
            f'--disable-extensions-except={extension_dir}',
            f'--load-extension={extension_dir}'
        ]
    )
    page = await browser.newPage()
    await page.goto('https://accounts.hcaptcha.com/demo')
    # Add any additional actions you want to perform on the page
    await asyncio.sleep(20)
    await page.screenshot({'path': 'example.png'})
    await browser.close()

asyncio.run(main())
```

- **Download the extension**: The script fetches the extension ZIP file.
- **Configure the extension**: The API key is inserted into the extension's `defaultConfig.json` file.

### Step 2: Automate CAPTCHA Solving

The script launches a browser with the configured CaptchaSonic extension and navigates to a test CAPTCHA page.

```python
browser = await launch(
    headless=False,
    userDataDir=current_dir,
    args=[
        f'--disable-extensions-except={extension_dir}',
        f'--load-extension={extension_dir}'
    ]
)
page = await browser.newPage()
await page.goto('https://accounts.hcaptcha.com/demo')
await page.screenshot({'path': 'example.png'})
await browser.close()
```

- **Browser automation**: Playwright launches a non-headless browser and interacts with the page.
- **CAPTCHA solving**: The CaptchaSonic extension handles CAPTCHA solving.

### Step 3: Run the Script
Run the script:

```bash
python script_name.py
```

---

## Example Output

1. The browser launches and navigates to the CAPTCHA demo page.
2. The CAPTCHA is solved by the CaptchaSonic extension.
3. A screenshot is saved as `example.png`.

---

## Notes and Debugging
- Ensure the Chrome version matches the CaptchaSonic extension requirements.
- The `.env` file must include a valid API key.
- If the extension does not load, check for the `manifest.json` file in the extracted directory.

---


