---
title: "Making E2E Tests Harder, Better, Faster, and Stronger"
datePublished: Sat Dec 09 2023 06:04:13 GMT+0000 (Coordinated Universal Time)
cuid: clpxnhvut000108jq1wne215i
slug: making-e2e-tests-harder-better-faster-and-stronger
cover: https://cdn.hashnode.com/res/hashnode/image/upload/v1702094754120/123b1617-c310-4f7e-ba6a-9720a23ba137.jpeg
tags: testing, e2e, github-actions-1, playwright

---

<div data-node-type="callout">
<div data-node-type="callout-emoji">💡</div>
<div data-node-type="callout-text">In short: use Playwright for end-to-end testing of your web app; don’t sleep on its built-in code generation and reporting features; aim to target stable, deployed URLs when possible.</div>
</div>

## What is Playwright?

[Playwright](https://playwright.dev/) is an exceptional package that facilitates automated end-to-end (E2E) testing for your web application. Its standout feature lies in its ability to utilize real browser engines, navigating and interacting with your web app programmatically to explore every corner of your user interface. This approach helps uncover bugs and regressions before they reach your users. If you've worked with Selenium or Puppeteer before, Playwright is similar but excels in allowing usage across multiple browser engines, such as Chromium, WebKit, and more.

Like any good testing tool, it can (and should) be available locally as you code, as well as on CI (continuous integration) to ensure code quality from the entire team, automatically.

## What was wrong with the current setup?

When I started as a software engineer on the [Health Equity Tracker](http://healthequitytracker.org), there were no frontend-specific E2E tests in place. All QA was done manually by the developers (and helpful department members on big releases), and as you can imagine on a complex dashboard-style app, edge-case bugs often snuck through the review process. I took it as a challenge to a) identify the up-and-coming industry standard for frontend e2e testing and b) implement a testing suite basically on my own.

Overall I am happy to say I succeeded, and I was able to initially add coverage to the most basic parts of the application. However, with the improvement of both my development skills and Playwright as a tool, we have been able to make significant improvements to our E2E testing setup, making the tests harder (less fragile and flaky), better (improved developer experience with increased visibility and maintainability), faster (the tests were taking quite a long time on CI to run and we were able to reduce that time significantly), and stronger (more comprehensive and representative of the actual user experience).

## `HARDER`

> Making the tests less fragile and more resilient

My initial setup used Playwright to spin up a Vite development server (`npm run dev`) regardless of the environment, and then ran the suite of tests against that dev server. This could happen locally or on CI using GitHub Actions. However, running tests against a quick development build (versus a real production build) can be unreliable for a testing situation.

To make our testing setup less fragile, we targeted our deployed, production builds instead of the temporary dev servers. To accomplish this, we needed to dynamically swap the `baseUrl` that Playwright relies upon, and ensure that all of our tests were using relative URLs. We set these `baseUrl` strings using either environmental variables or command line arguments, depending on the environment under test. This allows us to run things in the following ways:

| INITIATING ENVIRONMENT | SITE UNDER TEST |
| --- | --- |
| The developer manually runs the tests locally | Spin up `npm run dev` and then run tests against `http://localhost:3000` |
| CI runs the tests every time a PR is made or updated | Netlify-generated URL specific to each GitHub pull request, like [https://deploy-preview-2648--health-equity-tracker.netlify.app/](https://deploy-preview-2648--health-equity-tracker.netlify.app/) where 2648 is the number of the PR |
| CI runs the tests every time a PR is merged into `main` and a new version of the frontend is deployed to staging | `https://dev.healthequitytracker.org` which is our "dev" site, a staging pre-production preview that should perfectly mirror what will be released to production |
| CI chron job runs nightly against the real production site | `https://healthequitytracker.org` |

The logic for this dynamic `baseUrl` was straightforward for prod, staging, and local, as each has a consistent URL to test against and its own `.env` file to store the URL string. However, our CI also uses a unique test URL for every PR opened against `main`. This enables fully deployed feature testing (e.g., on a real mobile device, or to run ideas by non-technical stakeholders).

Passing this unique deploy preview URL into our test via GitHub Actions required some research and manual trial and error, but we got it working fairly quickly. Some hiccups still need fine-tuning, but overall, I'm pleased to implement this idea that's been in the back of my head for over a year. To set the `baseUrl` per environment, I added the following to my config:

```typescript
use: {
    baseURL: process.env.PLAYWRIGHT_BASE_URL ?? 'http://localhost:3000',
    /* rest of the config stuff... */
}
```

And set up my `.env` files like this:

```plaintext
# .env.production
PLAYWRIGHT_BASE_URL=https://healthequitytracker.org
```

```plaintext
# .env.staging
PLAYWRIGHT_BASE_URL=https://dev.healthequitytracker.org
```

This allows the specified URLs as needed, falling back to localhost when run locally or if `PLAYWRIGHT_BASE_URL` isn't set. For the deploy\_preview, I used the following GitHub Actions file, with a few tricky bits borrowed from [a post by Joran Quinten](https://www.joranquinten.nl/tutorials/running-e2e-test-suite-on-a-netlify-preview-url/):

```yaml
# frontend.yaml
# For more information see: https://help.github.com/actions/language-and-framework-guides/using-nodejs-with-github-actions

name: Frontend CI

env:
  NETLIFY_SITE_NAME: 'health-equity-tracker'
  GITHUB_PR_NUMBER: ${{github.event.pull_request.number}}

# run on any pushes to main (including merging PRs),
# and any PR creations or updates against main,
# as long as they include changes to files in /frontend
on:
  push:
    branches: [main]
  pull_request:
    branches: [main]
    paths:
      - 'frontend/**' # Run when changes occur in the frontend subfolder
defaults:
  run:
    working-directory: frontend

jobs:
  tests_e2e_netlify_prepare:
    # third-party action that waits until Netlify deploy URL is ready
    name: Wait for Netlify deploy
    runs-on: ubuntu-latest
    if: github.event_name == 'pull_request'
    steps:
      - name: Waiting for Netlify Preview
        uses: josephduffy/wait-for-netlify-action@v1
        id: wait-for-netflify-preview
        with:
          site_name: ${{env.NETLIFY_SITE_NAME}}
          max_timeout: 60

  tests_e2e_netlify:
    # make sure the above job completes first
    needs: tests_e2e_netlify_prepare
    name: Run E2E tests on deploy preview
    runs-on: ubuntu-latest
    if: github.event_name == 'pull_request'
    timeout-minutes: 5
    steps:
      # install node, modules, and playwright
      - uses: actions/checkout@v3
      - uses: actions/setup-node@v3
        with:
          node-version: 19
          cache: 'npm'
          cache-dependency-path: frontend/package-lock.json
      - name: Install dependencies
        run: npm ci
      - name: Install playwright deps
        run: npx playwright install --with-deps chromium
      # run the tests!
      - name: run E2E_DEPLOY_PREVIEW
        run: npx playwright test --project=E2E_DEPLOY_PREVIEW
        # base url based on the GITHUB_PR_NUMBER + NETLIFY_SITE_NAME
        env:
          PLAYWRIGHT_BASE_URL: 'https://deploy-preview-${{env.GITHUB_PR_NUMBER}}--${{env.NETLIFY_SITE_NAME}}.netlify.app'
      # store test run reports if they fail
      - uses: actions/upload-artifact@v3
        if: ${{ failure() }}
        with:
          name: playwright-report
          path: frontend/playwright-report/
          retention-days: 30
```

## `BETTER`

> Increasing reporting visibility to improve developer experience and velocity

Initial configuration attempts inadvertently failed to pass the required environmental variables. As a result, the React site would load, but the fetches from our data API would fail. This caused endless issues; running the tests locally would pass fine, but running on CI would fail. Lack of visibility into the Playwright reporting on CI made it impossible to debug these failures.

Finally, I properly implemented the reporting feature on CI, and then, importantly, set it to upload the generated reports as artifacts (final step of the GitHub Actions file above). Playwright includes great video replay, screenshot, and stack trace visualizations that allow you to rewatch what was happening when your tests fail. This insight into the CI failures made it immediately where I had been calling the wrong npm script. Going forward, this increased visibility and improved testing structure should allow our team to write tests more easily, have more confidence in their results, and quickly update them when things go wrong.

![actual video of playwright's video capture showing site working but failing to load data](https://cdn.hashnode.com/res/hashnode/image/upload/v1702099118926/becf4765-32f7-41ac-842e-d8b2aae1e4df.gif align="center")

## `FASTER`

> Faster checks result in a happier, more productive team

Several aspects of this effort result in significantly faster test runs:

1. Running against an already deployed site eliminates the need to start the dev server on CI
    
2. With the CI server no longer serving the dev build and running tests, we can increase the number of workers Playwright uses, enabling parallelization for simultaneous execution of unrelated tests.
    
3. Switched the report artifact upload step to run only on test failure, reducing unnecessary executions.
    

As an example, comparing the before and after run results, we see an improvement of over 30%! Not bad considering the new, faster test suite is both more comprehensive AND more reliable.

![screenshots of before and after github action reports, showing over 30% speed improvement of E2E runs on deploy preview](https://cdn.hashnode.com/res/hashnode/image/upload/v1702085968481/49ca2552-ea33-4b14-a9cc-f08c64616239.png align="center")

## `STRONGER`

> Increasing coverage of our tests, more realistically replicating user journeys

A major goal was to increase coverage across all public health topics and demographic/geographic disaggregations provided by the tracker. This wasn't possible before due to the flaky, slow configuration. By running tests against real, deployed production builds, we can now navigate our tests with the same confidence we have using the actual app. We have even had our first open-source community contributions from friends and fellow Denver developers [Ali](https://github.com/alinix1) and [James](https://jamesdemlow.com/), whom I would like to personally thank for their efforts!

To easily generate new coverage, we added a new shortcut to package.json, `npm run e2e-new`, which quickly runs Playwright's fantastic codegen feature. Regrettably, I had overlooked this feature earlier, likely because of my negative experiences decades ago with code generation in early site builders like Frontpage and Dreamweaver. However, Playwright's codegen is not only easy; it writes more reliable steps than I could write myself! It launches a special browser instance and smaller console and then records your steps as you navigate throughout the app, using the most robust locators available. Initially, we don't even need to include `expect` statements, since a `click()`action will fail if its target is not available.

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1702097874680/a7b36654-f8a1-4c15-974b-098d6bcdc9b1.gif align="center")

# What's next?

Going forward, we hope to:

* Ensure coverage for every health topic available on the tracker.
    
* Include tests with extensive filtering/UI settings across those topics.
    
* Divide the tests into 'essential' (run every time) and 'non-essential' (only run on production releases), minimizing wait times.
    
* Implement a matrix of test settings on nightly production runs, allowing a combination of browsers (Edge, Firefox, Safari) and device settings (mobile, tablet, and desktop widths).
    
* Ensure the development team culture emphasizes adding test coverage whenever a bug is fixed; minimizing regressions.
    

Thanks for reading! Feel free to follow along or contribute to the open-source [HET GitHub repo](https://github.com/SatcherInstitute/health-equity-tracker), and let me know if you have any questions on optimizing your Playwright configuration!

---

*Photo by* [*Rock'n Roll Monkey*](https://unsplash.com/@rocknrollmonkey?utm_content=creditCopyText&utm_medium=referral&utm_source=unsplash) *on* [*Unsplash*](https://unsplash.com/photos/blue-plastic-robot-toy-R4WCbazrD1g?utm_content=creditCopyText&utm_medium=referral&utm_source=unsplash)