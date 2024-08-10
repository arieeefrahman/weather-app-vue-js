# weather-app

This application, called "Weather", allows you to track the current and future weather of cities of your choice.

## How It Works?

<li>Search for your city by typing its name into the search field.</li>
<li>Choose a city from the list of results to view the current weather in that location.</li>
<li>Click the "+" symbol in the upper right corner to track the city. This will save the city to the home page for later viewing. </li>

## Removing a City

You may easily remove a city from your tracking list by selecting it directly from the homepage. There will be an option to remove the city at the bottom of the page.

## Environment Variables

Create a `.env` file in the root directory of the project and add the following:

```sh
VITE_OPEN_WEATHER_API_KEY=your_open_weather_api_key
VITE_MAPBOX_ACCESS_TOKEN=your_mapbox_access_token
```

## Project Setup

```sh
npm install
```

### Compile and Hot-Reload for Development

```sh
npm run dev
```

### Compile and Minify for Production

```sh
npm run build
```
