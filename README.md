# TRMNL Plugin Tester

A development tool for testing TRMNL plugins. This project allows you to preview and test plugins locally or in a Docker container before deploying them to the [useTRMNL.com](https://usetrmnl.com) platform.

## Features

- **Plugin Preview**: Preview plugins in various layouts (full, half-horizontal, half-vertical, quadrant).
    - Uses standard `settings.yml` format compatible with TRMNL import/export
    - Preview plugin content with live data or sample data
    - Copy layout templates for different screen sizes
    - Export plugins as ZIP files ready for TRMNL import

- **Local & API Support**: Work with local `sample.json` files or call external APIs for live data.
- **Clipboard Integration**: Copy layouts and API URLs directly to the clipboard for easy pasting into the TRMNL plugin dashboard.
- **Image Generation**: Generate BMP images for TRMNL displays using Puppeteer and ImageMagick.
- **Rate Limiting**: Built-in rate limiting to prevent abuse (default: 400 requests per 5 minutes).
- Basic BYOS features such as 
  - add/remove device in SQLite database
  - `/api/setup` (just mocked to accept any MAC address at present)
  - `/api/display` and associated BMP generation
  - docker built and [pushed to docker hub](https://hub.docker.com/r/stuartleeks/trmnl-plugin-tester) for x86 and ARM

## Live demo
I have a live demo of this project running on [fly.io](https://trmnl-plugins.fly.dev/)

## Configuration Options

The following configuration options are available in `config.js`:

### Paths
- **`PLUGINS_PATH`**: Path to the plugins directory (default: `./_plugins`).
- **`CACHE_PATH`**: Path to the cache directory (default: `./cache`). (NOTE: cache may be removed in future and could be broken)

### Feature Flags
- **`ENABLE_IMAGE_GENERATION`**: Enable/disable image generation (default: `false`).
- **`DEBUG_MODE`**: Enable debug mode for detailed logging (default: `false`).
- **`ADMIN_MODE`**: Enable admin mode for additional BYOS features (default: `false`).

### Rate Limiting
- **`MAX_REQUESTS_PER_5_MIN`**: Maximum number of requests allowed in 5 minutes (default: `400`).

### Server Config
- **`PORT`**: Port for the server to listen on (default: `3000`).

### Browser Config
- **`BROWSER_LAUNCH_CONFIG`**: Configuration for Puppeteer (e.g., sandbox, GPU, and memory settings).


### Image Generation
- **`IMAGE_MAGICK_SWICTHES`**: ImageMagick options for converting images to BMP (default: `-dither FloydSteinberg -monochrome -depth 1 -strip -compress RLE -define bmp:format=bmp3`).
- **`IMAGE_MAGICK_BIN`**: Path to the ImageMagick binary (default: `convert`).

### Derived Paths
- **`FONTS_PATH`**: Path to fonts directory.
- **`CSS_PATH`**: Path to CSS files.
- **`JS_PATH`**: Path to JavaScript files.

## Getting Started

### Prerequisites
- [Node.js](https://nodejs.org/) installed.
- Git installed on your machine.

### Installation
1. Clone the repository:
```bash
git clone https://github.com/gitstua/trmnl-plugin-dev.git
cd trmnl-plugin-dev
```

2. Start the development server:
```bash
./scripts/run.sh
```

This will start the server and provide the URL to open the preview in your browser.

## Docker Support

You can run the TRMNL Plugin Tester using Docker:

### Using Docker in Development
To build and run the image, use the following command:
```bash
./scripts/run-docker.sh
```

### Using Docker Hub Image
The Docker image is built and pushed to Docker Hub for x86 and ARM architectures. You can pull it directly from [stuartleeks/trmnl-plugin-tester](https://hub.docker.com/r/stuartleeks/trmnl-plugin-tester).

To run the image, use the following command:
```bash
docker run -d -p 3000:3000 stuartleeks/trmnl-plugin-tester
```

## Plugin Configuration

Each plugin requires a `settings.yml` file that defines how it works. This format is compatible with TRMNL's import/export functionality. Here's an example:

```yaml
---
strategy: polling                # polling, static, or webhook
no_screen_padding: 'no'         # yes/no - removes padding around the screen
dark_mode: 'no'                 # yes/no - inverts colors for dark mode
polling_verb: get               # HTTP verb for polling strategy
polling_url: https://api.example.com/data  # URL to fetch data from
polling_headers: 'content-type: application/json'  # HTTP headers
name: Example Plugin            # Display name of the plugin
description: Plugin description # Shown in plugin gallery
refresh_interval: 3600          # Seconds between updates

# Optional custom fields that users can configure
custom_fields:
- keyname: apikey              # Internal field name
  field_type: string           # string, number, select
  name: API Key                # Display name
  description: Your API key    # Help text
  placeholder: your-api-key    # Example value
```

The plugin should also include:
- `views/` directory with Liquid templates for different layouts
- `sample.json` for testing without live data
- Optional `.env` file for secrets (add to .gitignore)

## Example Plugins

### Code Clock
- Displays the time of image generation embedded in random code snippets.
- **DATA**: Static snippets with dynamic time insertion.
- **SOURCE**: [_plugins/code-clock](_plugins/code-clock).
![Code Clock Preview](_plugins/code-clock/Preview/full.png)

### Home Assistant TRMNL Plugin
- Displays Home Assistant sensor data in TRMNL.
- **DATA**: Home Assistant API.
- **SOURCE**: [_plugins/home-assistant-trmnl](_plugins/home-assistant-trmnl).
![Home Assistant Preview](_plugins/home-assistant-trmnl/Preview/full.png)

### NTFY Plugin
- Displays periodic alerts from the [ntfy.sh](https://ntfy.sh/) notification service.
- **DATA**: ntfy.sh API.
- **SOURCE**: [_plugins/ntfy](_plugins/ntfy).
![NTFY Plugin Preview](_plugins/NTFY/Preview/full.png)

### My Agenda
- Shows upcoming events in an agenda view.
- **DATA**: Custom API for converting ICAL to JSON.
- **SOURCE**: [_plugins/my-agenda](_plugins/my-agenda).
![My Agenda Preview](_plugins/my-agenda/Preview/full.png)

### Currency Exchange
- Shows the current exchange rate for a currency pair.
- **DATA**: [currencyapi.com](https://currencyapi.com/).
- **SOURCE**: [_plugins/currency-exchange](_plugins/currency-exchange).
![Currency Exchange Preview](_plugins/currency-exchange/Preview/full.png)

### EPL Fixtures
- Shows upcoming English Premier League fixtures.
- **DATA**: [GitHub repo](https://github.com/openfootball).
- **SOURCE**: [_plugins/epl-fixtures](_plugins/epl-fixtures).
![EPL Fixtures Preview](_plugins/epl-fixtures/Preview/full.png)

### Random Fact
- Displays interesting random facts.
- **DATA**: [uselessfacts.jsph.pl](https://uselessfacts.jsph.pl).
- **SOURCE**: [_plugins/random-fact](_plugins/random-fact).
![Random Fact Preview](_plugins/random-fact/Preview/full.png)

### Random Joke
- Shows setup and punchline of random jokes.
- **DATA**: [official-joke-api.appspot.com](https://official-joke-api.appspot.com/random_joke).
- **SOURCE**: [_plugins/random-joke](_plugins/random-joke).
![Random Joke Preview](_plugins/random-joke/Preview/full.png)

### Wind Speed & Direction
- Displays hourly wind speed, direction, and gusts data.
- **DATA**: Open-Meteo API.
- **SOURCE**: [_plugins/wind-speed-direction](_plugins/wind-speed-direction).
![Wind Speed & Direction Preview](_plugins/wind-speed-direction/Preview/full.png)

### TRMNL Broadcast
- Displays custom messages and announcements.
- **DATA**: Static JSON or custom webhook endpoint.
- **SOURCE**: [_plugins/trmnl-broadcast](_plugins/trmnl-broadcast).
![TRMNL Broadcast Preview](_plugins/trmnl-broadcast/Preview/full.png)

## Credits / Acknowledgements

This project would not have been possible without:
- The [TRMNL](https://usetrmnl.com/) project.
- [@schrockwell](https://github.com/schrockwell) for the [TRMNL Preview](https://github.com/schrockwell/trmnl_preview).
- [LiquidJS](https://liquidjs.com) for the templating engine.
- [ImageMagick](https://imagemagick.org/) for image conversion.
