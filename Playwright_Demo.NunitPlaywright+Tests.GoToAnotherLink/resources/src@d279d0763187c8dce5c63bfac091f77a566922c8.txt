
using Microsoft.Playwright;
using Microsoft.Playwright.NUnit;
using NUnit.Framework;
using System.Text.Json;

namespace Playwright_Demo
{
    public class NunitPlaywright
    {
        [SetUp]
        public async Task Setup()
        {}
        /// <summary>
        /// Test cases with headless = false i.e., Visual Testing
        /// </summary>
        /// <returns></returns>
        [Test]
        public async Task DiffrentDevice()
        {
            using var playwright = await Playwright.CreateAsync();
            await using var browser = await playwright.Chromium.LaunchAsync(new()
            {
                Headless = false
            });
            var iphone13 = playwright.Devices["iPhone 13"];
            await using var context = await browser.NewContextAsync(iphone13);
            var Page = await context.NewPageAsync();
            await Page.GotoAsync("https://openweathermap.org/");
            await Page.GetByRole(AriaRole.Img, new() { Name = "Icon hamburger" }).ClickAsync();
            await Page.GetByRole(AriaRole.Button, new() { Name = "Accept" }).ClickAsync();
            await Page.GetByRole(AriaRole.Link, new() { Name = "Sign in" }).ClickAsync();
            await Page.GetByLabel("Email").ClickAsync();
            await Page.GetByLabel("Email").FillAsync("playwright@yopmail.com");
            await Page.GetByPlaceholder("Password").ClickAsync();
            await Page.GetByPlaceholder("Password").FillAsync("Test@123");
            await Page.GetByPlaceholder("Password").PressAsync("Enter");
            Assert.IsTrue(await Page.GetByText("Signed in successfully.").IsVisibleAsync());
        }
        [Test]
        public async Task ExploreWebsite()
        {
            using var playwright = await Playwright.CreateAsync();
            await using var browser = await playwright.Chromium.LaunchAsync(new BrowserTypeLaunchOptions
            {
                Headless = false,
            });
            var context = await browser.NewContextAsync();

            var page = await context.NewPageAsync();
            await page.GotoAsync("https://openweathermap.org/");
            await page.GetByRole(AriaRole.Link, new() { Name = "current weather (current)" }).ClickAsync();
            System.Threading.Thread.Sleep(TimeSpan.FromSeconds(2));
            await page.GetByRole(AriaRole.Link, new() { Name = "Home" }).ClickAsync();
            await page.GetByRole(AriaRole.Link, new() { Name = "hourly forecast (4 days)" }).ClickAsync();
            System.Threading.Thread.Sleep(TimeSpan.FromSeconds(2));
            var heading = page.GetByRole(AriaRole.Heading, new() { Name = "List of weather condition" });
            Assert.IsTrue(await heading.IsVisibleAsync());
            await page.GetByRole(AriaRole.Link, new() { Name = "Home" }).ClickAsync();
            await page.GetByRole(AriaRole.Link, new() { Name = "daily forecast (16 days)" }).ClickAsync();
            System.Threading.Thread.Sleep(TimeSpan.FromSeconds(2));
            await page.GetByRole(AriaRole.Link, new() { Name = "Home" }).ClickAsync();
            await page.GetByRole(AriaRole.Link, new() { Name = "climatic forecast (30 days)" }).ClickAsync();
            System.Threading.Thread.Sleep(TimeSpan.FromSeconds(2));
            var heading1 = page.GetByRole(AriaRole.Heading, new() { Name = "Units of measurement" });
            bool isVisible = await heading1.IsVisibleAsync();
            Assert.IsTrue(isVisible);
            await page.GetByRole(AriaRole.Link, new() { Name = "Home" }).ClickAsync();
            await page.GetByRole(AriaRole.Link, new() { Name = "historical weather (40+ years" }).ClickAsync();
            System.Threading.Thread.Sleep(TimeSpan.FromSeconds(2));
            var heading3 = page.GetByRole(AriaRole.Heading, new() { Name = "History Bulk" });
            bool isVisible1 = await heading3.IsVisibleAsync();
            Assert.IsTrue(isVisible1);
            await page.GetByRole(AriaRole.Link, new() { Name = "Home" }).ClickAsync();
        }
        [Test]
        public async Task WebsiteWihoutImage()
        {
            using var playwright = await Playwright.CreateAsync();
            await using var browser = await playwright.Chromium.LaunchAsync(new BrowserTypeLaunchOptions
            {
                Headless = false,
            });
            var context = await browser.NewContextAsync();
            var Page = await context.NewPageAsync();
            await Page.GotoAsync("https://openweathermap.org/");
            await Page.RouteAsync(url: "**/*", handler: async route =>
            {
                if (route.Request.ResourceType == "image")
                    await route.AbortAsync();
                else
                    await route.ContinueAsync();
            });
            // Navigate to the website
            await Page.GotoAsync("https://openweathermap.org/");

            // wait for some time to see the effect of image blocking
            await Task.Delay(9000); // Wait for 9 seconds 

            // Close the browser
            await browser.CloseAsync();
        }

        /// <summary>
        /// Test cases with records
        /// if you want to see the records open 'trace.playwright.dev' and drang trace.zip file  OR run 'pwsh bin/Debug/netX/playwright.ps1 show-trace trace.zip' where zip file located.
        /// </summary>

        [Parallelizable(ParallelScope.Self)]
        [TestFixture]
        public class Tests : PageTest
        {
            [SetUp]
            public async Task Setup()
            {
                await Context.Tracing.StartAsync(new()
                {
                    Title = TestContext.CurrentContext.Test.ClassName + "." + TestContext.CurrentContext.Test.Name,
                    Screenshots = true,
                    Snapshots = true,
                    Sources = true
                });
            }

            [TearDown]
            public async Task TearDown()
            {
                // This will produce e.g.:
                // bin/Debug/net8.0/playwright-traces/PlaywrightTests.Tests.Test1.zip
                await Context.Tracing.StopAsync(new()
                {
                    Path = Path.Combine(
                        TestContext.CurrentContext.WorkDirectory,
                        "playwright-traces",
                        $"{TestContext.CurrentContext.Test.ClassName}.{TestContext.CurrentContext.Test.Name}.zip"
                    )
                });
            }
            [Test]
            public async Task Login()
            {
                await Page.GotoAsync("https://openweathermap.org/");
                await Page.GetByRole(AriaRole.Link, new() { Name = "Sign in" }).ClickAsync();
                await Page.GetByLabel("Email").ClickAsync();
                await Page.GetByLabel("Email").FillAsync("playwright@yopmail.com");
                await Page.GetByPlaceholder("Password").ClickAsync();
                await Page.GetByPlaceholder("Password").FillAsync("Test@123");
                await Page.GetByPlaceholder("Password").PressAsync("Enter");
                await Expect(Page.GetByText("Signed in successfully.")).ToBeVisibleAsync();
            }
            [Test]
            public async Task MapsTest()
            {
                await Page.GotoAsync("https://openweathermap.org/");
                await Page.GetByRole(AriaRole.Link, new() { Name = "Maps", Exact = true }).ClickAsync();
                await Page.Locator("label").Filter(new() { HasText = "Pressure" }).ClickAsync();
                await Page.Locator("label").Filter(new() { HasText = "Wind speed" }).ClickAsync();
                await Page.GetByText("Clouds", new() { Exact = true }).ClickAsync();
                await Page.GetByText("Cities").ClickAsync();
                await Page.GetByText("Cities").ClickAsync();
                await Page.GetByText("Global Precipitation").ClickAsync();
                await Page.GetByText("Temperature").ClickAsync();
                await Page.GetByText("17", new() { Exact = true }).First.ClickAsync();
                await Expect(Page.GetByRole(AriaRole.Cell, new() { Name = "country" })).ToBeVisibleAsync();
                await Page.GetByRole(AriaRole.Cell, new() { Name = "temp" }).ClickAsync();
                await Page.Locator("span").Filter(new() { HasText = "Ponta Delgada" }).ClickAsync();
                await Expect(Page.GetByRole(AriaRole.Cell, new() { Name = "pressure" })).ToBeVisibleAsync();
            }
            [Test]
            public async Task WeatherOfCity()
            {
                await Page.GotoAsync("https://openweathermap.org/city/1279233");
                await Page.GetByPlaceholder("Search city").ClickAsync();
                await Page.GetByPlaceholder("Search city").FillAsync("ahmedabad");
                await Page.GetByPlaceholder("Search city").PressAsync("Enter");
                await Page.GetByText("Ahmedabad, IN 27°C23.033,").ClickAsync();
                await Page.GetByRole(AriaRole.Heading, new() { Name = "Hourly forecast", Exact = true }).ClickAsync();
                await Expect(Page.GetByRole(AriaRole.Heading, new() { Name = "Hourly forecast", Exact = true })).ToBeVisibleAsync();
            }
            [Test]
            public async Task GoToAnotherLink()
            {
                await Page.GotoAsync("https://openweathermap.org/price");
                await Expect(Page.GetByRole(AriaRole.Heading, new() { Name = "Pricing" })).ToBeVisibleAsync();
                await Page.GetByRole(AriaRole.Link, new() { Name = "Home" }).ClickAsync();
                await Expect(Page.GetByRole(AriaRole.Heading, new() { Name = "OpenWeather", Exact = true }).Locator("span")).ToBeVisibleAsync();
            }
        }
    }
}