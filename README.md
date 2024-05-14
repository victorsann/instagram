This is a new [**React Native**](https://reactnative.dev) project, bootstrapped using [`@react-native-community/cli`](https://github.com/react-native-community/cli).

# Getting Started

>**Note**: Make sure you have completed the [React Native - Environment Setup](https://reactnative.dev/docs/environment-setup) instructions till "Creating a new application" step, before proceeding.

## Step 1: Start the Metro Server

First, you will need to start **Metro**, the JavaScript _bundler_ that ships _with_ React Native.

To start Metro, add a launch.json file to your .vscode folder, the add the fallowing configurations to it:

    {
        "name": "[Dev] Android",
        "request": "launch",
        "type": "reactnativedirect",
        "cwd": "${workspaceFolder}",
        "platform": "android"
    },
    {
        "name": "[Dev] IOS",
        "request": "launch",
        "type": "reactnativedirect",
        "cwd": "${workspaceFolder}",
        "platform": "ios"
    },

This will automatically start a metro server and will launch the application in debug mode.

## Step 2: Set Up Native Apps Configurations

As a hybrid applications, React Native apps require native platform configuration. Each, Android and IOS, will have its own specificities covered in details below:

## Android

[Reference](https://developer.android.com/studio/publish/app-signing) 

## 1. Generating an upload key

The Android platform recognizes applications by their signing process. This process identifies ```aabs``` or ```apks``` for different purposes, including their generation. The following steps guide you on how to sign the application, allowing you to generate the necessary files to release it on the android platform.

This process is made by generating a private signing key using keytool. The keytool command is a key and certificate management utility. 

On Windows keytool must be run from C:\Program Files\Java\jdkx.x.x_x\bin, as administrator (Don't forget to add an alias).

    keytool -genkeypair -v -storetype PKCS12 -keystore my-upload-key.keystore -alias <alias> -keyalg RSA -keysize 2048 -validity 10000

This command prompts you the following data:
 
>Enter keystore password:
>Re-enter new password:

After defining the password, the following questions are going to asked:

>What is your first and last name?
>[Unknown]:
>What is the name of your organizational unit?
>[Unknown]:  
>What is the name of your organization?
>[Unknown]:  
>What is the name of your City or Locality?
>[Unknown]:  
>What is the name of your State or Province?
>[Unknown]:  
>What is the two-letter country code for this unit?
>[Unknown]: 
>Is CN=**, OU=**, O=**, L=**, ST=**, C=** correct?
>[no]: 

At the end answer 'yes' and it will be finished. It then generates the keystore as a file called my-upload-key.keystore at 

>C:\Program Files\Java\jdkx.x.x_x\bin

## 2. Setting up Gradle variables

Place the my-upload-key.keystore file under the android/app directory in your project folder. Edit the file android/gradle.properties, and add the following (replace ***** with the correct keystore password, alias and key password):

    MYAPP_UPLOAD_STORE_FILE=my-upload-key.keystore (not the path)
    MYAPP_UPLOAD_KEY_ALIAS=my-key-alias
    MYAPP_UPLOAD_STORE_PASSWORD=*****
    MYAPP_UPLOAD_KEY_PASSWORD=*****

These are going to be global Gradle variables, which we can later use in our Gradle config to sign our app.

## 3. Adding signing config to your app's Gradle config

The last configuration step that needs to be done is to setup release builds to be signed using
upload key. Edit the file android/app/build.gradle in your project folder, and add the signing
config,

    ...
    android {
        ...
        defaultConfig { ... }
        signingConfigs {
            release {
                if (project.hasProperty('MYAPP_UPLOAD_STORE_FILE')) {
                    storeFile file(MYAPP_UPLOAD_STORE_FILE)
                    storePassword MYAPP_UPLOAD_STORE_PASSWORD
                    keyAlias MYAPP_UPLOAD_KEY_ALIAS
                    keyPassword MYAPP_UPLOAD_KEY_PASSWORD
                }
            }
        }
        buildTypes {
            release {
                ...
                signingConfig signingConfigs.release
            }
        }
    }
    ...

## 4. Generating the release AAB

Run the following command in a terminal:

    npx react-native build-android --mode=release

The generated AAB can be found under android/app/build/outputs/bundle/release/app-release.aab, and is ready to be uploaded to Google Play.

## 5. Testing the release build of your app

Before uploading the release build to the Play Store, make sure you test it thoroughly. First uninstall any previous version of the app you already have installed. Install it on the device using the following command in the project root:

    npm run android -- --mode="release"

Note that ```--mode release``` is only available if you've set up signing as described above. You can terminate any running bundler instances, since all your framework and JavaScript code is bundled in the APK's assets.

## IOS


