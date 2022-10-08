# Getting Started with Appium Gauge and Python

pip install -r requirements.txt (Python 2)

pip3 install -r requirements.txt (Python 3)

pip install Appium-Python-Client

npm install -g @getgauge/cli

# Setting Up Your Authentication

Make sure you have your LambdaTest credentials with you to run test automation scripts on LambdaTest. To obtain your access credentials, purchase a plan or access the Automation Dashboard.

Set LambdaTest Username and Access Key in environment variables.

For Linux/macOS:

export LT_USERNAME=YOUR_LAMBDATEST_USERNAME \
export LT_ACCESS_KEY=YOUR_LAMBDATEST_ACCESS_KEY

For Windows:

set LT_USERNAME=YOUR_LAMBDATEST_USERNAME `
set LT_ACCESS_KEY=YOUR_LAMBDATEST_ACCESS_KEY

Upload Your Application

Step-3: Upload your iOS application (.ipa file) or android application (.apk file) to the LambdaTest servers using our REST API. You need to provide your Username and AccessKey in the format Username:AccessKey in the cURL command for authentication. Make sure to add the path of the appFile in the cURL request. Here is an example cURL request to upload your app using our REST API:

Using App File:

Linux/macOS:

curl -u "YOUR_LAMBDATEST_USERNAME:YOUR_LAMBDATEST_ACCESS_KEY" \
--location --request POST 'https://manual-api.lambdatest.com/app/upload/realDevice' \
--form 'name="Android_App"' \
--form 'appFile=@"/Users/macuser/Downloads/proverbial_android.apk"'

Windows:

curl -u "YOUR_LAMBDATEST_USERNAME:YOUR_LAMBDATEST_ACCESS_KEY" -X POST "https://manual-api.lambdatest.com/app/upload/realDevice" -F "appFile=@"/Users/macuser/Downloads/proverbial_android.apk""

Using App URL:

Linux/macOS:

curl -u "YOUR_LAMBDATEST_USERNAME:YOUR_LAMBDATEST_ACCESS_KEY" \
--location --request POST 'https://manual-api.lambdatest.com/app/upload/realDevice' \
--form 'name="Android_App"' \
--form 'url="https://prod-mobile-artefacts.lambdatest.com/assets/docs/proverbial_android.apk"'

For Windows:

curl -u "YOUR_LAMBDATEST_USERNAME:YOUR_LAMBDATEST_ACCESS_KEY" -X POST "https://manual-api.lambdatest.com/app/upload/realDevice" -d "{"url":"https://prod-mobile-artefacts.lambdatest.com/assets/docs/proverbial_android.apk","name":"sample.apk"}"

Tip:

Response of above cURL will be a JSON object containing the App URL of the format - lt://APP123456789123456789 and will be used in the next step.

## Change driver.py to point to LT Hub

In step_impl -> utils, you will find a driver.py file. In order to connect to Lambdatest, you will need to point to our hub.

This is also where you should enter your LT credentials along with the capabilities for the device/browser configuration you want to test.

You can generate the desired capabilities for your test using our capabilities generator: https://www.lambdatest.com/capabilities-generator/

```
from getgauge.python import before_suite, after_suite
from appium import webdriver

class Driver(object):
    driver = None

    @before_suite
    def init(self):
        self.username = os.getenv("LT_USERNAME") # replace with your LT username
        self.authkey  = os.getenv("LT_ACCESS_KEY") # replace with your LT access key


        caps = {}
        
        # replace with your desired test capabilities
        caps['name'] = 'Gauge Sample Test'
        caps['build'] = 'Python_Gauge_LambdaTest'
        caps['isRealMobile'] = 'true'
        caps['platformVersion'] = '11'
        caps['platform'] = 'Android'
        caps['deviceName'] = 'Galaxy S21 Ultra 5G'
        caps['app'] = 'APP_URL'  


        # start the remote browser on our server
        Driver.driver = webdriver.Remote(
            desired_capabilities=caps,
            command_executor="http://%s:%s@mobile-hub.lambdatest.com/wd/hub"%(self.username,self.authkey)
         )


    @after_suite
    def close():
        Driver.driver.quit()

```

<br>

## Add your Test Spec and Step Implementations

We are adding the following test steps to specs -> example.spec:

```
# Getting Started with Gauge

## Let's Run the Sample
* changes color to pink
* changes the text to Proverbial
* toast will be visible
* notification will be visible
* perform the speed test
* back to home
"
```

So that our test knows how to run those steps, we will also need to add the step implementations for this test under step_impl -> get_started.py

```
from sqlite3 import Time
from appium.webdriver.common.mobileby import MobileBy
from selenium.webdriver.support.ui import WebDriverWait
from selenium.webdriver.support import expected_conditions as EC
from getgauge.python import step
from step_impl.utils.driver import Driver

@step("changes color to pink")
def change_color():
  colorElement = WebDriverWait(Driver.driver, 20).until(EC.element_to_be_clickable(
            (MobileBy.ID, "com.lambdatest.proverbial:id/color")))
  colorElement.click()

@step("changes the text to Proverbial")
def chnage_text():
    textElement = WebDriverWait(Driver.driver, 20).until(
            EC.element_to_be_clickable((MobileBy.ID, "com.lambdatest.proverbial:id/Text")))
    textElement.click()

@step("toast will be visible")
def toast_visible():
    toastElement = WebDriverWait(Driver.driver, 20).until(EC.element_to_be_clickable(
            (MobileBy.ID, "com.lambdatest.proverbial:id/toast")))
    toastElement.click()

@step("notification will be visible")
def notification():
  notification = WebDriverWait(Driver.driver, 20).until(EC.element_to_be_clickable(
            (MobileBy.ID, "com.lambdatest.proverbial:id/notification")))
  notification.click()

@step("perform the speed test")
def speedTest():
  speedTest = WebDriverWait(Driver.driver, 20).until(EC.element_to_be_clickable(
            (MobileBy.ID, "com.lambdatest.proverbial:id/speedTest")))
  speedTest.click()

@step("back to home")
def home():
  home = WebDriverWait(Driver.driver, 20).until(EC.element_to_be_clickable(
            (MobileBy.ID, "com.lambdatest.proverbial:id/buttonPage")))
  home.click()
```

<br>

## Run Your Test

Now you can run your Appium Gauge Python test on LambdaTest:

`gauge run specs`


