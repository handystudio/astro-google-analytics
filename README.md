# @astro-kits/google-analytics

A performance-optimized Google Analytics integration for Astro that leverages Partytown to run analytics scripts in a web worker.

[![npm version](https://img.shields.io/npm/v/@astro-kits/google-analytics.svg)](https://www.npmjs.com/package/@astro-kits/google-analytics)
[![license](https://img.shields.io/npm/l/@astro-kits/google-analytics.svg)](https://github.com/yourusername/google-analytics/blob/main/LICENSE)

## Features

- 🚀 **Web Worker Powered**: Offloads Google Analytics to a web worker using Partytown
- 🔄 **Simple Integration**: Drop-in component for any Astro project
- 🔧 **Configurable**: Options for development/production behavior
- 📦 **Lightweight**: Minimal implementation with no unnecessary features
- ⚡ **Performance First**: Improves main thread performance
- 🔐 **Environment Variables**: Automatically use GA ID from environment variables

## Installation

```bash
# npm
npm install @astro-kits/google-analytics @astrojs/partytown

# yarn
yarn add @astro-kits/google-analytics @astrojs/partytown

# pnpm
pnpm add @astro-kits/google-analytics @astrojs/partytown
```

## Setup

### 1. Configure Partytown in your Astro config

```js
// astro.config.mjs
import { defineConfig } from "astro/config";
import partytown from "@astrojs/partytown";

export default defineConfig({
  integrations: [
    partytown({
      config: {
        forward: ["dataLayer.push", "gtag"],
      },
    }),
  ],
});
```

### 2. Set up environment variables (optional)

Create a `.env` file in your project root:

```
GA_ID=G-XXXXXXXXXX
```

### 3. Import and use the component

```astro
---
// src/layouts/Layout.astro
import GoogleAnalytics from '@astro-kits/google-analytics';
---

<html lang="en">
  <head>
    <meta charset="utf-8" />
    <meta name="viewport" content="width=device-width, initial-scale=1" />
    <title>My Astro Site</title>

    <!-- Add Google Analytics -->
    <GoogleAnalytics />  <!-- Will use ID from environment variables -->
  </head>
  <body>
    <slot />
  </body>
</html>
```

## Configuration Options

| Option       | Type      | Default     | Description                                                                                                       |
| ------------ | --------- | ----------- | ----------------------------------------------------------------------------------------------------------------- |
| `id`         | `string`  | `undefined` | Your Google Analytics measurement ID (G-XXXXXXXXXX). If not provided, will use `GA_ID` from environment variables |
| `partytown`  | `boolean` | `true`      | Whether to use Partytown to run GA in a web worker                                                                |
| `production` | `boolean` | `true`      | Only load analytics in production environment                                                                     |

## Examples

### Basic Usage (with ID from environment variables)

```astro
<GoogleAnalytics />
```

### Explicitly Providing Measurement ID

```astro
<GoogleAnalytics id="G-XXXXXXXXXX" />
```

### Without Partytown (run on main thread)

```astro
<GoogleAnalytics partytown={false} />
```

### Load in Development Environment

```astro
<GoogleAnalytics production={false} />
```

## How It Works

This component:

1. Loads the Google Analytics gtag.js script
2. Gets the GA ID from props or falls back to the `GA_ID` environment variable
3. Initializes Google Analytics with the provided ID
4. Uses Partytown to run the scripts in a web worker (by default)
5. Conditionally loads only in production environments (by default)

## Component Implementation

The component automatically checks for the Google Analytics ID in this order:

1. From the `id` prop if provided
2. From the `GA_ID` environment variable
3. Logs a warning if no ID is found

```astro
---
// Example of component implementation
export interface Props {
  id?: string; // Google Analytics measurement ID (G-XXXXXXXXXX)
  partytown?: boolean; // Whether to use Partytown
  production?: boolean; // Only load in production environment
}

// Get the Google Analytics ID from the .env file
const GA_ID = import.meta.env.GA_ID;

const {
  id = GA_ID,
  partytown = true,
  production = true
} = Astro.props;

// Component logic...
---
```

## Performance Benefits

By running Google Analytics in a web worker:

- Reduces main thread execution time
- Improves page load performance
- Reduces impact on Core Web Vitals
- Better user experience with less main thread blocking

## Browser Compatibility

This component is compatible with all modern browsers. Partytown provides fallbacks for browsers that don't support web workers.

## License

MIT

## Contributing

Contributions are welcome! Please feel free to submit a Pull Request.

1. Fork the repository
2. Create your feature branch (`git checkout -b feature/amazing-feature`)
3. Commit your changes (`git commit -m 'Add some amazing feature'`)
4. Push to the branch (`git push origin feature/amazing-feature`)
5. Open a Pull Request

## Credits

- [Astro](https://astro.build)
- [Partytown](https://partytown.builder.io)
