# vue speech streaming

> A Vue2 Performing Streaming Speech Recognition with Google Cloud Speech on Progressive Web App

<p align="center">
  <img src ="https://i.imgur.com/HD07Rj9.gif" />
</p>

## Authentication
1. Visit the [Google Developers Console](https://console.developers.google.com/project)
2. Create a new project or click on an existing project.
3. Navigate to  **APIs & auth** > **APIs section** and turn on the following APIs (you may need to enable billing in order to use these services): Google Cloud Speech API
4. Navigate to **APIs & auth** >  **Credentials** and then:
  * If you want to use a new service account key, click on **Create credentials** and select **Service account key**. After the account key is created, you will be prompted to download the JSON key file that the library uses to authenticate your requests.
  * If you want to generate a new service account key for an existing service account, click on **Generate new JSON key** and download the JSON key file.

## Config
After at Authentication done getting the json file and insert At ``app.js``
``` bash
const speech = Speech({
   keyFilename: '' // insert this file json here.
});
```

## Setup Project

``` bash
# Git Clone Project
git clone git@github.com:aofdev/vue-speech-streaming.git

# Cd project
cd vue-speech-streaming

# install dependencies project
npm install || yarn install

```

## Setup websocket

``` bash
# Cd folder speech-websocket 
cd speech-websocket

# install dependencies websocket
npm install

```

## Run Project

``` bash
# run websocket 
node app

# run project app
npm run dev || yarn dev

# build for production with minification and to build Progressive Web Apps
npm run build || yarn build

```

