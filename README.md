# `@bgotink/playwright-coverage` [![Latest published version on NPM](https://img.shields.io/npm/v/@bgotink/playwright-coverage)](https://npm.im/@bgotink/playwright-coverage)

Report coverage on playwright tests

## Usage

Install this package

```bash
yarn add -D @bgotink/playwright-coverage
```

Then add the reporter to your playwright configuration:

```ts
const config = {
  // ...

  reporter: [
    ['list'],
    [
      '@bgotink/playwright-coverage',
      {
        // Path to the root files should be resolved from, most likely your repository root
        sourceRoot: __dirname,
        // Files to ignore in coverage, useful
        // - if you're testing the demo app of a component library and want to exclude the demo sources
        // - or part of the code is generated
        // - or if you're running into any of the other many reasons people have for excluding files
        exclude: ['path/to/ignored/code/**'],
        // Base directory where output will be written to
        resultDir: path.join(__dirname, 'results/e2e-coverage'),
        // Configure the reports to generate.
        // The value is an array of istanbul reports, with optional configuration attached.
        reports: [
          // Create an HTML view at <resultDir>/index.html
          ['html'],
          // Create <resultDir>/coverage.lcov for consumption by tooling
          [
            'lcovonly',
            {
              file: 'coverage.lcov',
            },
          ],
          // Log a coverage summary at the end of the test run
          [
            'text-summary',
            {
              file: null,
            },
          ],
        ],
        // Configure watermarks, see https://github.com/istanbuljs/nyc#high-and-low-watermarks
        // watermarks: {},
      },
    ],
  ],
};
```

Finally mix in coverage to your `test` function:

```ts
import {test as base} from '@playwright/test';
import {mixinFixtures as mixinCoverage} from '@bgotink/playwright-coverage';

export * from '@playwright/test';
export const test = mixinCoverage(base);
```

Now replace imports to `@playwright/test` in your tests with imports from this file, and coverage will be tracked.

## How does it work?

This uses V8's builtin coverage tracking. The fixtures registered via `mixinFixtures` hook into the created `Page` object and tracks javascript coverage. After the test is complete, this coverage is stored as attachment to the test execution.

Upon completion of all tests, the reporter combines the generated coverage files into one and then converts the v8 coverage format into the format used by istanbul. This is then passed into the reports of `istanbul-reports`.

## Status

This project is very experimental. It has been proven to work on one angular application, i.e. with webpack with the unmodified configuration angular applies to it.

## License

Licensed under the MIT license, see `LICENSE.md`.
