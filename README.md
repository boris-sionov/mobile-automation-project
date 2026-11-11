# mobile-automation-project

Mobile test automation for the Code2Lead demo Android app (`utilities/Android_Demo_App.apk`).
Stack: Python, Appium (UiAutomator2), pytest, Allure, Page Object Model.

## Prerequisites

- Python 3 and Node.js with Appium 2 and the `uiautomator2` driver
- Android emulator or a real device connected (`adb devices` shows it)
- [Allure commandline](https://allurereport.org/docs/install/) to view reports

## Setup

```bash
git clone https://github.com/boris-sionov/mobiile-automation-project
cd mobiile-automation-project
pip install -r configuration/requirements.txt
```

## Configuration

Edit `configuration/config.ini` to match your device:

```ini
[capabilities]
platform_name = Android
platform_version = 15
automation_name = UiAutomator2
device_name = emulator-5554
app_path = utilities/Android_Demo_App.apk
app_package = com.code2lead.kwad
app_activity = com.code2lead.kwad.MainActivity
appium_server = http://127.0.0.1:4723

[general]
timeout = 5
; web = browsers, Android = Android phone/tablet (iOS is not implemented yet)
platform = Android
```

Test data (form values, texts) is in the `[enter_some_value_page_tests]` and `[contact_us_page_tests]` sections.

## Running tests

Run from the repository root. The Appium server is started and stopped automatically by the test session.

```bash
pytest tests/scroll_view_test.py --alluredir=reports/allure-reports   # one file
python tests/run_order.py                                             # ordered suite
allure serve reports/allure-reports                                   # view the report
```

## Project structure

| Folder | Purpose |
|---|---|
| `tests/` | Test classes (`*_test.py`) and fixtures in `conftest.py` |
| `pages/` | Page objects, one per app screen |
| `base/` | Base page, driver setup, UI actions and verifications |
| `utilities/` | Config reader, logger, locator types, `PageFactory`, the APK |
| `configuration/` | `config.ini` and `requirements.txt` |
| `reports/` | Allure results and logs (git-ignored) |
