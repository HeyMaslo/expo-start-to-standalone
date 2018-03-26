# Expo Start To Standalone App
Expo starter app & full instructions to build & deploy an iOS app. Bootstrapped from [create-react-native-app](https://github.com/react-community/create-react-native-app) (aka `CRNA`).

<sub>Note: the [user guide](./guides/USER_GUIDE.md) generated with this app is available in the `guides` directory. It has some useful information about [sharing/deployment](./guides/USER_GUIDE.md#sharing-and-deployment), [available scripts](./guides/USER_GUIDE.md#sharing-and-deployment#available-scripts), [troubleshooting guide](./guides/USER_GUIDE.md#troubleshooting), and more.</sub>

## Table of Contents
- [Getting Started](#getting-started)
  - <sub>[Requirements](#requirements)</sub>
- [Development](#development)
  - <sub>[Install Dependencies](#install-dependencies)</sub>
  - <sub>[Network Configuration](#network-configuration)</sub>
  - <sub>[Running the Project](#running-the-project)</sub>
  - <sub>[Debugging](#debugging)</sub>
- [Troubleshooting](#troubleshooting)
- [Publishing](#publishing)
- [Deployment](#deployment)
  - <sub>[Installation](#installation)</sub>
  - <sub>[Distribution](#distribution)</sub>
- [Contributing](#contributing)


## Getting Started
```bash
git clone git@github.com:HeyMaslo/expo-start-to-standalone.git
cd expo-start-to-standalone
```

### Requirements
Instructions for setting up your development workstation and phone.

##### Workstation
Mac `OSX`:
- Install [node](https://nodejs.org/en/) `v6`+
  - npm `v3`+

- Install [yarn](https://yarnpkg.com/lang/en/docs/migrating-from-npm/) for package management
```bash
npm install -g yarn
```

- [Xcode](https://itunes.apple.com/us/app/xcode/id497799835?mt=12)
  - `Xcode` command line tools
```bash
xcode-select --install
```

#### Phone
[`CRNA`](https://github.com/react-community/create-react-native-app)'s development environment uses a client app called `Expo Client` for live-reloading in development. Its pretty magical.

Go to the App Store on your iOS or Andriod device, and install the Expo Client app ([ios](https://itunes.apple.com/us/app/expo-client/id982107779), [andriod](https://play.google.com/store/apps/details?id=host.exp.exponent)).

## Development
Run the project on your workstation and preview app on your phone or in simulator.

### Pitfalls
There's an [open issue](https://github.com/react-community/create-react-native-app/issues/234) affecting OSX & Linux developers - to prevent the startup script from failing, users must increase their system-level [file/process limits](https://www.freebsd.org/doc/handbook/configtuning-kernel-limits.html). You may want to write these commands to your shell config file, as the following commands are temporary until reboot.

```bash
sudo sysctl -w kern.maxfiles=5242880
sudo sysctl -w kern.maxfilesperproc=524288
```

### Install dependencies
This will create a `node_modules` folder in your application's root directory and a [lock file](https://yarnpkg.com/lang/en/docs/yarn-lock/).
```bash
yarn install
```

### Network configuration
If you are running the project on your iOS device, **both the device and your workstation must be on the same network**! See the Troubleshooting section below if you are experiencing issues.

### Running the project
```bash
exp start
```
To view the app on your phone:
```
1. Open Expo Client on phone
2. Scan QR code presented in terminal
3. Code
```

Alternatively, you can run the app in a simulator on your workstation, e.g:
```bash
yarn run ios
```

### Debugging
Anything written to the console should print in the terminal. 

#### Remote Debugging
Shake your phone while the Expo Client is running your application, and click `Debug Remote JS` at the bottom of the menu.
More info on [debugging Javascript remotely](https://docs.expo.io/versions/latest/guides/debugging.html#debugging-javascript).

Note: there are some unresolved issues running Expo's remote debugger when using some modules such as [GLView](https://docs.expo.io/versions/latest/sdk/gl-view.html) ([e.g.](https://forums.expo.io/t/debugging-glview-on-ios/630/2)).

**TL;DR** - If you're using a module that is incompatible with Expo client's remote debugger, disable remote debugging.

## Troubleshooting
If you're having trouble connecting the Expo client app with the `node` server running on your workstation, first diagnose the issue with [these instructions](https://github.com/react-community/create-react-native-app/blob/master/react-native-scripts/template/README.md#networking). Also, ensure you are not running a VPN or have a mobile hotspot activated.

If your phone and computer are on the same network and you're still experiencing timeouts, you can override `react-native`'s packager hostname using an environment variable on app boot:

```bash
REACT_NATIVE_PACKAGER_HOSTNAME='<your-ip>' yarn start
```

## Publishing
Below are full instructions for building and distributing your Expo project as a ["standalone app"](https://docs.expo.io/versions/latest/guides/building-standalone-apps.html).

**Note**: the following instructions are targeted for iOS. PR's welcome for Andriod instructions!

### Requirements
- [Expo Account](https://expo.io/signup)
- Developer Account for your target platform

### Expo Host
The first step to distributing your app is to publish your project to Expo's CDN. Before publishing you'll need to signup for an account & login via CLI. 

```bash
exp login # follow prompts
exp publish
```

If publishing was successful you'll receive a URL link to the app hosted by Expo.

### Standalone Builds
```bash
exp build:ios
```

Next, you'll login with your AppleID. Expo will ask if you'd like an auto-generated certificate, or upload a [`p12` cert](https://calvium.com/how-to-make-a-p12-file/). The CLI will walk you through, and here are the [full instructions](https://docs.expo.io/versions/latest/guides/building-standalone-apps.html#if-you-choose-to-build-for-ios).

Builds take up to 10 minutes. Expo's CLI now notifies you when the build is complete and provides a link to download the `.ipa` artifact (`curl -O <.ipa download URL>`)

## Deployment
Fastlane's [Pilot](https://github.com/fastlane/fastlane/tree/master/pilot#pilot) is used to deploy builds to iTunes Connect.

### Installation
`brew` is recommended to install `fastlane`:
```bash
brew cask install fastlane
```

Alternatively, you can install with Ruby:
```bash
gem install fastlane
```

### Distribution
1. To deploy, first obtain a build, download, and put the `.ipa` in the root of this project. Refer to [Build section](#standalone-builds) above.

2. The magic incantation:
```bash
# Note: env var prefix recommended via closed issue: https://github.com/fastlane/fastlane/issues/4544
DELIVER_ITMSTRANSPORTER_ADDITIONAL_UPLOAD_PARAMETERS="-t DAV" fastlane pilot upload
```

3. Next, you'll be asked a few questions and will need to enter your AppleID credentials.
4. Wait. This whole process will take approximately **`35` minutes**, and you will see what looks like an error (`[Transporter Error Output]: description length:9592544`), as well as 20 minutes of `Build doesn't show up in the build list anymore, waiting for it to appear again`. Be patient though!

## Contributing
PR's welcome!
