# AGENTS.md

This file provides guidance to coding agents when working with code in this repository.
(Per owner's global rules: `AGENTS.md` is the only instruction file here; do not create a `CLAUDE.md`.)

## What this is

Mobile test automation for the **Code2Lead demo Android app** (`utilities/Android_Demo_App.apk`,
package `com.code2lead.kwad`): Python + Appium (UiAutomator2) + pytest + Allure, Page Object Model.
`platform = web` in config also works (Selenium browsers via `base/driver_class.py`); `ios` is a stub.

## Commands

```bash
pip install -r configuration/requirements.txt
pytest tests/scroll_view_test.py                          # one file
pytest tests/login_test.py::TestLogin::test_correct_info   # one test
python tests/run_order.py                                  # ordered suite (run from repo root)
allure serve reports/allure-reports
```

- Prerequisites: Appium server installed (the session fixture starts/stops it itself), a connected
  device/emulator, and `configuration/config.ini` filled in (`[capabilities]`, `[general] platform`).
- `pytest.ini` has `--alluredir=.././reports/allure-reports` (relative to cwd, one level up). From the repo
  root, results land outside the repo; `run_order.py` passes its own `--alluredir`. Pass `--alluredir=reports/allure-reports`
  explicitly when running pytest from the root.
- Test files are named `*_test.py`. No linter configured.

## Architecture

- `tests/conftest.py` — session fixtures (clear Allure reports, start/stop `AppiumService`) and two driver
  fixtures: `setup_class` (one driver per class) and `setup_function` (one per test). Both call
  `attach_driver_and_pages`, which creates every page object listed in `PageFactory` and sets it on the test class.
- `utilities/page_factory.py` — `PageFactory` is only a class with type annotations; test classes inherit it so
  `self.login_page`, `self.main_page`, etc. resolve. **Adding a page = create `pages/x_page.py` + add an annotation
  here** (the conftest iterates `PageFactory.__annotations__`).
- `pages/*` — page objects extending `base.base_page.BasePage` (waits, locator helpers via `get_by_locator(LocatorType, value)`),
  composed with `base.ui_actions.UIActions` / `base.ui_verifications.UIVerifications` (also `general_actions.py`).
  Locators live in a `self.locators` dict per page.
- `base/driver_class.py` — `Driver.get_driver_method()` builds the driver from `config.ini`; on connection errors it
  calls `sys.exit(1)` (kills the whole run). The APK path is resolved from `sys.path[0]` + `[capabilities] app_path`,
  so run from the repo root.
- `utilities/config.py` (`ConfigReader.read_config(section, key)`), `custom_logger.py` (logger + Allure report clearing),
  `base/files_path.py` (all absolute paths: reports, logs, config, APK).
- Test style: one class per page, `@pytest.mark.usefixtures("setup_class")`, `@allure.title` per test,
  `with allure.step(...)` inside. Tests in a class share one app session and depend on order (e.g. login_test).
- `debug/` is scratch (`test_google.py`), not part of the suite.

## Notes

- `reports/` is git-ignored. `*.png` and `*.log` are git-ignored too.
- `README.md` says to run `pip install` from `configuration/`; the requirements file is at `configuration/requirements.txt`.
- No commits or pushes unless the owner asks.
