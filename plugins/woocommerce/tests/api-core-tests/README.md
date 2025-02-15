# WooCommerce Core API Test Suite

This package contains automated API tests for WooCommerce, based on Playwright and `wp-env`. It supersedes the SuperTest based [api-core-tests package](https://www.npmjs.com/package/@woocommerce/api-core-tests) and e2e-environment [setup](../tests/e2e), which we will gradually deprecate.

## Table of contents

- [Pre-requisites](#pre-requisites)
- [Introduction](#introduction)
- [About the Environment](#about-the-environment)
- [Test Variables](#test-variables)
- [Guide for writing tests](#guide-for-writing-tests)
  - [Tools for writing tests](#tools-for-writing-tests)
  - [Creating test structure](#creating-test-structure)
  - [Writing the test](#writing-the-test)
- [Debugging tests](#debugging-tests)

## Pre-requisites

- Node.js ([Installation instructions](https://nodejs.org/en/download/))
- NVM ([Installation instructions](https://github.com/nvm-sh/nvm))
- PNPM ([Installation instructions](https://pnpm.io/installation))
- Docker and Docker Compose ([Installation instructions](https://docs.docker.com/engine/install/))

Note, that if you are on Mac and you install Docker through other methods such as homebrew, for example, your steps to set it up might be different. The commands listed in steps below may also vary.

If you are using Windows, we recommend using [Windows Subsystem for Linux (WSL)](https://docs.microsoft.com/en-us/windows/wsl/) for running tests. Follow the [WSL Setup Instructions](../tests/e2e/WSL_SETUP_INSTRUCTIONS.md) first before proceeding with the steps below.

### Introduction

WooCommerce's `api-core-tests` are powered by Playwright. The test site is spun up using `wp-env` (recommended), but we will continue to support `e2e-environment` in the meantime.

**Running tests for the first time:**

- `nvm use`
- `pnpm install`
- `pnpm run build --filter=woocommerce`
- `cd plugins/woocommerce`
- `pnpm env:test`
- `pnpm test:api-pw`

To run the test again, re-create the environment to start with a fresh state 

- `pnpm env:destroy`
- `pnpm env:test`
- `pnpm test:api-pw`

Other ways of running tests:

- `pnpm test:api-pw --debug` (debug)
- `pnpm test:api-pw ./tests/api-core-tests/tests/hello/hello.test.js` (running a single test)

To see all options, run `cd plugins/woocommerce && pnpm playwright test --help`

## Environment variables

The following environment variables can be configured as shown in `.env.example`:

```
# Your site's base URL, not including a trailing slash
BASE_URL="https://mysite.com"

# The admin user's username or generated consumer key
USER_KEY=""

# The admin user's password or generated consumer secret
USER_SECRET=""
```

For local setup, create a `.env` file in the `woocommerce/plugins/woocommerce` folder with the three required values described above. If any of these variables are configured they will override the values automatically set in the `playwright.config.js`

When using a username and password combination instead of a consumer secret and consumer key, make sure to have the [JSON Basic Authentication plugin](https://github.com/WP-API/Basic-Auth) installed and activated on the test site.

For more information about authentication with the WooCommerce API, please see the [Authentication](https://woocommerce.github.io/woocommerce-rest-api-docs/?javascript#authentication) section in the WooCommerce REST API documentation.

### About the environment

The default values are:

- Latest stable WordPress version
- PHP 7.4
- MariaDB
- URL: `http://localhost:8086/`
- Admin credentials: `admin/password`

If you want to customize these, check the [Test Variables](#test-variables) section.


For more information how to configure the test environment for `wp-env`, please checkout the [documentation](https://github.com/WordPress/gutenberg/tree/trunk/packages/env) documentation.

### Test Variables

The test environment uses the following test variables:

```json
{ 
  "url": "http://localhost:8086/",
  "users": {
    "admin": {
      "username": "admin",
      "password": "password"
    },
    "customer": {
      "username": "customer",
      "password": "password"
    }
  }
}
```

If you need to modify the port for your local test environment (eg. port is already in use) or use, edit [playwright.config.js](https://github.com/woocommerce/woocommerce/blob/trunk/plugins/woocommerce/tests/e2e/playwright.config.js). Depending on what environment tool you are using, you will need to also edit the respective `.json` file.

**Modify the port wp-env**

Edit [.wp-env.json](https://github.com/woocommerce/woocommerce/blob/trunk/plugins/woocommerce/.wp-env.json) and [playwright.config.js](https://github.com/woocommerce/woocommerce/blob/trunk/plugins/woocommerce/tests/e2e/playwright.config.js).

**Modify port for e2e-environment**

Edit [tests/e2e/config/default.json](https://github.com/woocommerce/woocommerce/blob/trunk/plugins/woocommerce/tests/e2e/config/default.json).****

### Starting/stopping the environment

After you run a test, it's best to restart the environment to start from a fresh state. We are currently working to reset the state more efficiently to avoid the restart being needed, but this is a work-in-progress.

- `pnpm env:down` to stop the environment
- `pnpm env:destroy` when you make changes to `.wp-env.json`
- `pnpm env:test` to spin up the test environment

## Guide for writing tests

### Creating test structure

The structure of the test serves as a skeleton for the test itself. You can turn it into a test by using `describe()` and `it()` methods of Playwright:

- [`test.describe()`](https://playwright.dev/docs/api/class-test#test-describe) - creates a block that groups together several related tests;
- [`test()`](https://playwright.dev/docs/api/class-test#test-call) - actual method that runs the test.

Based on our example, the test skeleton would look as follows:

```js
test.describe( 'Merchant can create virtual product', () => {
	test( 'merchant can log in', async () => { } );

	test( 'merchant can create virtual product', async () => { } );

	test( 'merchant can verify that virtual product was created', async () => { } );
} );
```

## Debugging tests

For Playwright debugging, follow [Playwright's documentation](https://playwright.dev/docs/debug).
