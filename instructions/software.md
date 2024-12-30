## Installing the Koala firmware

### Flashing DFU software to Respeaker Lite
Your Respeaker Lite board comes pre-flashed with some software. However, most probably it's software, built to use the board as external microphone for computer over USB. There's also chance that the flashed firmware is intended for usage over I2S (which we need), but is using 16kHz sample rate (which we don't, as Koala is using 48kHz sample rate for better sound playback).

To flash Respeaker board with correct software, you can use [included sowtware](/respeaker_lite_i2s_dfu_firmware_48k_v1.0.9.bin) (or take same one from [official repo](https://github.com/respeaker/ReSpeaker_Lite/tree/master/xmos_firmwares)) and [instructions from Seeed site](https://wiki.seeedstudio.com/xiao_respeaker/#flash-the-i2s-firmware).

#### _Please re-insert the board after flash and make sure that displayed version is correct one._

### Flashing ESPHome configuration to ESP32-S3 board
There's two configurations available: [dashboard](/config/koala-dashboard.yaml) and [factory](/config/koala-factory.yaml). Also you will need to copy [base config YAML](/config/common/koala-base.yaml) into `common` directory in your ESPHome root folder. Alternatively, you can copy everything from `koala-base.yaml` to the chosen config and merge everything inside.

1. Pick your config:
    - **Dashboard YAML** will build regular ESPhome device, with your WiFi credentials and without BLE Improv functionality. It will connect to your WiFi automatically and will be available for adding in Home Assistant right away.
    - **Factory YAML** will build the firmware, similar to the one Voice PE has. It will be able to connect to any WiFi network via BLE Improv, and in case of reconnecting to other WiFi you will have to just reset the device to factory settings. Also this version will receive OTA from this repository. You will see Home Assistant system prompt to update your device once the update is available. Basically set-and-forget option, and the option to choose if you're making the device for a friend/family.
2. Build the firmware with ESPHome and flash it with your method of choice.

### Connecting to Home Assistant
- I'm sure you already know how to connect ESPHome devices to Home Assistant. If it's your first time, please check the [official documentation](https://www.home-assistant.io/integrations/esphome/).
- In case of Factory firmware you will need to connect the device to the WiFi first. Check [Improv docs](https://www.home-assistant.io/integrations/improv_ble/) for details.
_**(When asked to authorize by pressing button on device, use small round button!)**_
- The voice satellite onboarding wizard will help you setup the pipeline and wake word.

### _Happy tinkering!_
