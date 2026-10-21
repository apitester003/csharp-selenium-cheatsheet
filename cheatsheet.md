# C# Selenium Cheatsheet - Interview Preparation Guide

## Table of Contents
1. [Setup & Configuration](#setup--configuration)
2. [WebDriver Initialization](#webdriver-initialization)
3. [Locating Elements](#locating-elements)
4. [Element Interactions](#element-interactions)
5. [Waits & Synchronization](#waits--synchronization)
6. [Navigation](#navigation)
7. [Window & Frame Handling](#window--frame-handling)
8. [Advanced Interactions](#advanced-interactions)
9. [Best Practices](#best-practices)

---

## Setup & Configuration

### NuGet Package Installation
```csharp
// Install via Package Manager Console
Install-Package Selenium.WebDriver
Install-Package Selenium.WebDriver.ChromeDriver
Install-Package Selenium.Support
```

### Basic Driver Setup
```csharp
using OpenQA.Selenium;
using OpenQA.Selenium.Chrome;
using OpenQA.Selenium.Support.UI;
using System;

public class SeleniumSetup
{
    private IWebDriver driver;

    public void Setup()
    {
        // Chrome Driver
        var options = new ChromeOptions();
        driver = new ChromeDriver(options);
        driver.Manage().Timeouts().ImplicitWait = TimeSpan.FromSeconds(10);
    }

    public void Teardown()
    {
        driver?.Quit();
    }
}
```

---

## WebDriver Initialization

### Different Browser Options

#### Chrome
```csharp
var chromeOptions = new ChromeOptions();
chromeOptions.AddArgument("--headless");
chromeOptions.AddArgument("--disable-gpu");
chromeOptions.AddArgument("--window-size=1920,1080");
var driver = new ChromeDriver(chromeOptions);
```

#### Firefox
```csharp
var firefoxOptions = new FirefoxOptions();
firefoxOptions.AddArgument("--headless");
var driver = new FirefoxDriver(firefoxOptions);
```

#### Edge
```csharp
var edgeOptions = new EdgeOptions();
edgeOptions.AddArgument("--headless");
var driver = new EdgeDriver(edgeOptions);
```

#### Safari
```csharp
var driver = new SafariDriver();
```

### Remote WebDriver
```csharp
var options = new ChromeOptions();
var driver = new RemoteWebDriver(
    new Uri("http://localhost:4444"), 
    options.ToCapabilities()
);
```

---

## Locating Elements

### By ID
```csharp
IWebElement element = driver.FindElement(By.Id("elementId"));
```

### By Name
```csharp
IWebElement element = driver.FindElement(By.Name("elementName"));
```

### By Class Name
```csharp
IWebElement element = driver.FindElement(By.ClassName("className"));
```

### By CSS Selector
```csharp
IWebElement element = driver.FindElement(By.CssSelector("div.classname > p"));
IWebElement element = driver.FindElement(By.CssSelector("[type='submit']"));
```

### By XPath
```csharp
// Absolute XPath
IWebElement element = driver.FindElement(By.XPath("/html/body/div[1]"));

// Relative XPath
IWebElement element = driver.FindElement(By.XPath("//input[@type='text']"));

// XPath with text
IWebElement element = driver.FindElement(By.XPath("//button[text()='Click Me']"));

// XPath with contains
IWebElement element = driver.FindElement(By.XPath("//a[contains(@href, 'login')]"));

// XPath with multiple conditions
IWebElement element = driver.FindElement(By.XPath("//input[@type='text' and @name='username']"));
```

### By Link Text
```csharp
IWebElement element = driver.FindElement(By.LinkText("Click Here"));
IWebElement element = driver.FindElement(By.PartialLinkText("Click"));
```

### By Tag Name
```csharp
IList<IWebElement> elements = driver.FindElements(By.TagName("button"));
```

### Finding Multiple Elements
```csharp
IList<IWebElement> elements = driver.FindElements(By.ClassName("item"));
foreach(var element in elements)
{
    Console.WriteLine(element.Text);
}
```

---

## Element Interactions

### Click
```csharp
IWebElement button = driver.FindElement(By.Id("submitBtn"));
button.Click();
```

### Send Keys / Typing
```csharp
IWebElement textbox = driver.FindElement(By.Name("username"));
textbox.SendKeys("testuser");
textbox.SendKeys(Keys.Tab);
textbox.SendKeys(Keys.Enter);
```

### Clear Input
```csharp
IWebElement textbox = driver.FindElement(By.Id("inputField"));
textbox.Clear();
```

### Get Text
```csharp
string text = driver.FindElement(By.Id("message")).Text;
Console.WriteLine(text);
```

### Get Attribute
```csharp
string href = driver.FindElement(By.XPath("//a")).GetAttribute("href");
string placeholder = driver.FindElement(By.Id("input")).GetAttribute("placeholder");
```

### Submit Form
```csharp
IWebElement form = driver.FindElement(By.Id("myForm"));
form.Submit();
```

### Check if Element is Displayed
```csharp
bool isDisplayed = driver.FindElement(By.Id("element")).Displayed;
```

### Check if Element is Enabled
```csharp
bool isEnabled = driver.FindElement(By.Id("button")).Enabled;
```

### Check if Element is Selected
```csharp
bool isSelected = driver.FindElement(By.Id("checkbox")).Selected;
```

---

## Waits & Synchronization

### Implicit Wait
```csharp
driver.Manage().Timeouts().ImplicitWait = TimeSpan.FromSeconds(10);
```

### Explicit Wait with WebDriverWait
```csharp
WebDriverWait wait = new WebDriverWait(driver, TimeSpan.FromSeconds(10));

// Wait for element to be visible
IWebElement element = wait.Until(
    ExpectedConditions.ElementIsVisible(By.Id("element"))
);

// Wait for element to be clickable
IWebElement clickable = wait.Until(
    ExpectedConditions.ElementToBeClickable(By.Id("button"))
);

// Wait for element to be present
IWebElement present = wait.Until(
    ExpectedConditions.PresenceOfAllElementsLocatedBy(By.ClassName("item"))
);
```

### Common ExpectedConditions
```csharp
// Title contains
wait.Until(ExpectedConditions.TitleContains("Expected Title"));

// URL contains
wait.Until(ExpectedConditions.UrlContains("/dashboard"));

// Element is invisible
wait.Until(ExpectedConditions.InvisibilityOfElementLocated(By.Id("loader")));

// Number of elements
wait.Until(ExpectedConditions.PresenceOfAllElementsLocatedBy(By.ClassName("row")));

// Text to be present
wait.Until(ExpectedConditions.TextToBePresentInElement(
    driver.FindElement(By.Id("status")), 
    "Success"
));
```

### Fluent Wait
```csharp
var fluentWait = new DefaultWait<IWebDriver>(driver)
{
    Timeout = TimeSpan.FromSeconds(30),
    PollingInterval = TimeSpan.FromMilliseconds(500)
};

fluentWait.IgnoreExceptionTypes(typeof(NoSuchElementException));

IWebElement element = fluentWait.Until(d => 
    d.FindElement(By.Id("element"))
);
```

### Thread Sleep (Not Recommended)
```csharp
System.Threading.Thread.Sleep(2000); // 2 seconds
```

---

## Navigation

### Navigate to URL
```csharp
driver.Navigate().GoToUrl("https://www.example.com");
```

### Back Button
```csharp
driver.Navigate().Back();
```

### Forward Button
```csharp
driver.Navigate().Forward();
```

### Refresh Page
```csharp
driver.Navigate().Refresh();
```

### Get Current URL
```csharp
string currentUrl = driver.Url;
```

### Get Page Title
```csharp
string title = driver.Title;
```

### Get Page Source
```csharp
string pageSource = driver.PageSource;
```

---

## Window & Frame Handling

### Window Management

#### Get Window Handles
```csharp
string mainWindow = driver.CurrentWindowHandle;
var allWindows = driver.WindowHandles;
```

#### Switch Between Windows
```csharp
var windows = driver.WindowHandles;
driver.SwitchTo().Window(windows[1]); // Switch to second window
```

#### Open New Window
```csharp
driver.ExecuteScript("window.open('https://example.com', '_blank');");
var windows = driver.WindowHandles;
driver.SwitchTo().Window(windows[windows.Count - 1]);
```

#### Close Window
```csharp
driver.Close(); // Close current window
```

#### Maximize Window
```csharp
driver.Manage().Window.Maximize();
```

#### Set Window Size
```csharp
driver.Manage().Window.Size = new System.Drawing.Size(1920, 1080);
```

#### Get Window Position
```csharp
var position = driver.Manage().Window.Position;
```

### Frame Handling

#### Switch to Frame by Index
```csharp
driver.SwitchTo().Frame(0); // First frame
```

#### Switch to Frame by Name or ID
```csharp
driver.SwitchTo().Frame("frameName");
```

#### Switch to Frame by WebElement
```csharp
IWebElement frameElement = driver.FindElement(By.TagName("iframe"));
driver.SwitchTo().Frame(frameElement);
```

#### Switch Back to Main Content
```csharp
driver.SwitchTo().DefaultContent();
```

#### Switch to Parent Frame
```csharp
driver.SwitchTo().ParentFrame();
```

---

## Advanced Interactions

### Action Chains
```csharp
using OpenQA.Selenium.Interactions;

var actions = new Actions(driver);

// Mouse Hover
IWebElement hoverElement = driver.FindElement(By.Id("hover"));
actions.MoveToElement(hoverElement).Perform();

// Double Click
actions.DoubleClick(hoverElement).Perform();

// Right Click
actions.ContextClick(hoverElement).Perform();

// Drag and Drop
IWebElement source = driver.FindElement(By.Id("source"));
IWebElement target = driver.FindElement(By.Id("target"));
actions.DragAndDrop(source, target).Perform();

// Drag by Offset
actions.DragAndDropToOffset(source, 100, 50).Perform();

// Click and Hold
actions.ClickAndHold(source).Perform();

// Release
actions.Release(source).Perform();

// Chain Multiple Actions
actions.MoveToElement(hoverElement)
    .Click()
    .SendKeys("text")
    .Perform();
```

### JavaScript Execution
```csharp
// Execute Script
var result = driver.ExecuteScript("return 1 + 1;");

// Scroll to Element
var element = driver.FindElement(By.Id("element"));
driver.ExecuteScript("arguments[0].scrollIntoView(true);", element);

// Scroll to Bottom
driver.ExecuteScript("window.scrollTo(0, document.body.scrollHeight);");

// Get Element Text with JavaScript
var text = driver.ExecuteScript("return arguments[0].innerText;", element);

// Click Element with JavaScript
driver.ExecuteScript("arguments[0].click();", element);

// Set Value with JavaScript
driver.ExecuteScript("arguments[0].value='newValue';", textBox);

// Remove Element
driver.ExecuteScript("arguments[0].remove();", element);

// Change Element CSS
driver.ExecuteScript("arguments[0].style.display='none';", element);
```

### Alert Handling
```csharp
// Simple Alert
IAlert alert = driver.SwitchTo().Alert();
alert.Accept(); // Click OK
alert.Dismiss(); // Click Cancel
string alertText = alert.Text; // Get alert message
alert.SendKeys("text"); // Type in alert (prompt)
```

### Select Dropdown
```csharp
using OpenQA.Selenium.Support.UI;

IWebElement selectElement = driver.FindElement(By.Id("dropdown"));
var select = new SelectElement(selectElement);

// Select by Visible Text
select.SelectByText("Option 1");

// Select by Value
select.SelectByValue("option1");

// Select by Index
select.SelectByIndex(0);

// Get Selected Option
var selectedOption = select.SelectedOption;
Console.WriteLine(selectedOption.Text);

// Get All Options
var allOptions = select.Options;
foreach(var option in allOptions)
{
    Console.WriteLine(option.Text);
}

// Deselect (for multi-select)
select.DeselectByText("Option 1");
select.DeselectAll();
```

### Cookie Handling
```csharp
// Add Cookie
driver.Manage().Cookies.AddCookie(
    new Cookie("cookieName", "cookieValue")
);

// Get Cookie
var cookie = driver.Manage().Cookies.GetCookieNamed("cookieName");

// Get All Cookies
var allCookies = driver.Manage().Cookies.AllCookies;

// Delete Cookie
driver.Manage().Cookies.DeleteCookie("cookieName");

// Delete All Cookies
driver.Manage().Cookies.DeleteAllCookies();
```

---

## Best Practices

### 1. Use Page Object Model
```csharp
public class LoginPage
{
    private IWebDriver driver;
    private By usernameLocator = By.Id("username");
    private By passwordLocator = By.Id("password");
    private By loginButtonLocator = By.Id("loginBtn");

    public LoginPage(IWebDriver driver)
    {
        this.driver = driver;
    }

    public void Login(string username, string password)
    {
        driver.FindElement(usernameLocator).SendKeys(username);
        driver.FindElement(passwordLocator).SendKeys(password);
        driver.FindElement(loginButtonLocator).Click();
    }
}
```

### 2. Error Handling & Exceptions
```csharp
try
{
    IWebElement element = driver.FindElement(By.Id("element"));
    element.Click();
}
catch (NoSuchElementException)
{
    Console.WriteLine("Element not found");
}
catch (StaleElementReferenceException)
{
    Console.WriteLine("Element is stale");
}
catch (ElementNotInteractableException)
{
    Console.WriteLine("Element not interactable");
}
catch (TimeoutException)
{
    Console.WriteLine("Timeout occurred");
}
```

### 3. Logging
```csharp
public static class Logger
{
    public static void LogInfo(string message)
    {
        Console.WriteLine($"[INFO] {DateTime.Now}: {message}");
    }

    public static void LogError(string message, Exception ex)
    {
        Console.WriteLine($"[ERROR] {DateTime.Now}: {message} - {ex.Message}");
    }
}
```

### 4. Taking Screenshots
```csharp
public static void TakeScreenshot(IWebDriver driver, string filename)
{
    var screenshot = ((ITakesScreenshot)driver).GetScreenshot();
    screenshot.SaveAsFile(filename, ScreenshotImageFormat.Png);
}
```

### 5. Headless Mode
```csharp
var options = new ChromeOptions();
options.AddArgument("--headless=new"); // Chrome 109+
options.AddArgument("--disable-gpu");
var driver = new ChromeDriver(options);
```

### 6. Handling Dynamic Elements
```csharp
public IWebElement WaitForElement(By locator, int timeoutSeconds = 10)
{
    var wait = new WebDriverWait(driver, TimeSpan.FromSeconds(timeoutSeconds));
    return wait.Until(ExpectedConditions.PresenceOfElementLocated(locator));
}
```

### 7. Using NUnit/XUnit Test Framework
```csharp
[TestFixture]
public class SeleniumTests
{
    private IWebDriver driver;

    [SetUp]
    public void Setup()
    {
        driver = new ChromeDriver();
        driver.Manage().Timeouts().ImplicitWait = TimeSpan.FromSeconds(10);
    }

    [TearDown]
    public void Teardown()
    {
        driver?.Quit();
    }

    [Test]
    public void TestLogin()
    {
        driver.Navigate().GoToUrl("https://example.com");
        Assert.That(driver.Title, Contains.Substring("Login"));
    }
}
```

### 8. Configuration Management
```csharp
public class Config
{
    public static readonly string BaseUrl = "https://example.com";
    public static readonly int DefaultTimeout = 10;
    public static readonly string BrowserType = "chrome";
    
    public static IWebDriver InitializeDriver()
    {
        return BrowserType.ToLower() switch
        {
            "chrome" => new ChromeDriver(),
            "firefox" => new FirefoxDriver(),
            "edge" => new EdgeDriver(),
            _ => throw new ArgumentException("Invalid browser type")
        };
    }
}
```

### 9. Common Wait Scenarios
```csharp
// Wait for AJAX to complete (jQuery)
public void WaitForAjax()
{
    WebDriverWait wait = new WebDriverWait(driver, TimeSpan.FromSeconds(10));
    wait.Until(d => (bool)((IJavaScriptExecutor)d)
        .ExecuteScript("return (typeof jQuery != 'undefined') ? (jQuery.active == 0) : true"));
}

// Wait for Angular
public void WaitForAngular()
{
    WebDriverWait wait = new WebDriverWait(driver, TimeSpan.FromSeconds(10));
    wait.Until(d => (bool)((IJavaScriptExecutor)d)
        .ExecuteScript("return window.getAllAngularTestabilities().findIndex(x => !x.isStable()) === -1"));
}
```

### 10. Synchronization Best Practices
```csharp
// DON'T use Thread.Sleep
// DON'T rely only on ImplicitWait
// DO use ExplicitWait for critical elements
// DO use WebDriverWait with ExpectedConditions
// DO combine waits appropriately
```

---

## Quick Reference

| Task | Code |
|------|------|
| Find element by ID | `driver.FindElement(By.Id("id"))` |
| Find elements | `driver.FindElements(By.ClassName("class"))` |
| Click element | `element.Click()` |
| Send text | `element.SendKeys("text")` |
| Get element text | `element.Text` |
| Wait for element | `wait.Until(ExpectedConditions.PresenceOfElementLocated(By.Id("id")))` |
| Switch to iframe | `driver.SwitchTo().Frame(0)` |
| Switch to alert | `driver.SwitchTo().Alert()` |
| Execute JavaScript | `driver.ExecuteScript("code")` |
| Take screenshot | `((ITakesScreenshot)driver).GetScreenshot()` |

---

## Interview Tips

✓ **Locator Strategy**: Use ID > Name > CSS > XPath (order of preference)  
✓ **Waits**: Always use explicit waits over implicit  
✓ **Page Object Model**: Maintain separate page classes for better maintainability  
✓ **Exception Handling**: Handle specific exceptions properly  
✓ **Headless Testing**: Use headless mode for CI/CD pipelines  
✓ **Logging**: Implement comprehensive logging for debugging  
✓ **Screenshots**: Take screenshots on test failures  
✓ **Cross-browser**: Test on multiple browsers  
✓ **Performance**: Optimize test execution time  
✓ **Cleanup**: Always quit the driver in teardown  

---

## Useful Links

- [Selenium Official Documentation](https://www.selenium.dev/documentation/)
- [Selenium WebDriver API Docs](https://www.selenium.dev/selenium/docs/api/dotnet/index.html)
- [XPath Cheatsheet](https://devhints.io/xpath)
- [CSS Selectors Reference](https://www.w3schools.com/cssref/css_selectors.asp)

---

**Last Updated**: 2026  
**Version**: 1.0  
