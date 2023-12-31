# Vedro Screenshot Matcher

The screenshot_matcher (a.k.a. Machu Picchu) is a powerful tool designed for developers and testers to visually compare two versions of an application. By contrasting a production version (known as "golden") with a development version (referred to as "test"), spotting visual inconsistencies becomes more straightforward.

Integrated seamlessly with the vedro testing framework, it aids in performing visual regression tests with ease.

![rn_image_picker_lib_temp_96855992-842a-40ec-8d1e-bc81f029e72a](https://github.com/MashaJing/vedro-screenshot-matcher/assets/53327043/aed74384-9489-4e23-a6fe-2e6ec338edee)

# Installation

1. Install the Plugin
```shell
vedro plugin install vedro-screenshot-matcher
# or
pip install machu-picchu
```

3. Configure the Plugin
In your `vedro.cfg.py`:
```python
import vedro
import screenshot_matcher

class Config(vedro.Config):
    class Plugins(vedro.Config.Plugins):
        class ScreenshotMatcher(screenshot_matcher.ScreenshotMatcher):
            test_app_url = "http://localhost"
            golden_app_url = "http://golden-app.com"
```
Make sure to provide the correct `test_app_url` and `golden_app_url` in the configuration.

# Quick Start

Suppose you have a basic test as below:
```python
import vedro
from contexts import opened_index_page

class Scenario(vedro.Scenario):
subject = "share page"

def given_opened_page(self):
    self.page = opened_index_page()

def when_user_click_on_share(self):
    self.page.get_by_text("Share").click()

def then_it_should_show_share_popup(self):
    share_popup = self.page.locator(".share-popup .title")
    assert share_popup.text_content() == "Share Page"
```

The context that opens the index page:
```python
import vedro
import playwright
from os import environ

@vedro.context
async def opened_index_page():
browser = await playwright.chromium.launch()
page = await browser.new_page()

await page.goto(environ["APP_URL"])

return page
```

To implement a screenshot assertion:

1. Add the `@screenshot_asserts` decorator at the beginning of the scenario
2. Include the `match_screenshot` assertion where needed
```python
import vedro
from screenshot_matcher import screenshot_asserts, match_screenshot

@screenshot_asserts()  # Step 1: Add decorator
class Scenario(vedro.Scenario):
subject = "share page"

def given_opened_page(self):
    self.page = opened_index_page()

def when_user_click_on_share(self):
    self.page.get_by_text("Share").click()

def then_it_should_show_share_popup(self):
    share_popup = self.page.locator(".share-popup .title")
    assert share_popup.text_content() == "Share Page"
    assert match_screenshot(share_popup)  # Step 2: Add screenshot assertion
```

By following these steps, the plugin will:

1. Execute the test using `APP_URL` set to your `golden_app_url`
2. Capture a screenshot, storing it as the expected outcome
3. Run the test again with `APP_URL` set to your `test_app_url`
4. Finally, it will compare the new screenshot with the previously saved one
