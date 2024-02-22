# OpenWeatherMap Automated Testing

## Description

This project contains automated tests using Microsoft Playwright in C# with NUnit. It verifies the functionality of the OpenWeatherMap website.

## Installation

1. Clone the repository.
2. Install required dependencies:
   ```bash
   dotnet add package Microsoft.Playwright
   dotnet add package Microsoft.Playwright.NUnit
   ```

## Usage

1. Open the project in Visual Studio or any IDE.
2. Build the solution.
3. Run the tests using NUnit or any test runner.

## Test Cases

### Visual Testing with iPhone Emulation

- **Test Name:** Visual Testing with iPhone
- **Description:** Tests various interactions on OpenWeatherMap emulating an iPhone 13.
- **Steps:** Clicks on menu, accepts terms, and signs in.
- **Expected:** Successful sign-in with a confirmation message.

### Exploring Website Navigation

- **Test Name:** Explore Website Navigation
- **Description:** Navigates through different sections of OpenWeatherMap.
- **Steps:** Clicks on various links like current weather, hourly forecast, etc.
- **Expected:** Each section loads correctly with visible headings.

### Blocking Images on Website

- **Test Name:** Website without Images
- **Description:** Tests website behavior when images are blocked.
- **Steps:** Blocks image requests and navigates to the website.
- **Expected:** Website functions properly even without images.

### Record and Trace Test Execution

- **Test Name:** Record and Trace Tests
- **Description:** Records test execution and provides traces for analysis.
- **Steps:** Runs tests for login, map exploration, weather check, and link navigation.
- **Expected:** Generates trace files for analysis.

## Note

For detailed trace analysis, open `trace.playwright.dev` and drag the generated `trace.zip` file, or run `pwsh bin/Debug/netX/playwright.ps1 show-trace trace.zip`.
