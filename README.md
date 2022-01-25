# Local polling of ZeverSolar inverter statistics in Home Assistant
## Description
Home Assistant configuration for local polling of ZeverSolar inverter stats. Implemented as a [package](https://www.home-assistant.io/docs/configuration/packages/)

## Configuration
1. Add the following lines to your `configuration.yaml` to make Home Assistant load "packaged" configuation files in the `/packages/` subdirectory. [Read this for more about this.](https://www.home-assistant.io/docs/configuration/packages/#create-a-packages-folder)

    ```yaml
    homeassistant:
      packages: !include_dir_merge_named packages/
    ```

2. Copy the contents of [`zeversolar.yaml`](/zeversolar.yaml) from this repository and save it as `/packages/zeversolar.yaml` in your Home Assistant configuration directory.
3. Replace `<zeversolar inverter ip/hostname>` in `zeversolar.yaml` with your inverter IP or hostname.

## Sensors
This configuration adds a few sensors to Home Assistant containing statistics from a ZeverSolar inverter. This is done by scraping the webpage the inverter exposes locally. 

- `sensor.zeversolar_full_output`: Full output of the webpage exposed by the inverter. Used internally by template sensors to read statistics.
- `binary_sensor.zeversolar_status`: Binary sensor indicating if the webpage exposed by the inverter is accessible and produces valid output. This will be "off" when there is no or not enough sun to provide the inverter with energy.

![image](https://user-images.githubusercontent.com/2001094/150830285-70b936c1-db29-44ce-b1cd-4a4a0caea93e.png)

- `sensor.zeversolar_e_today`: Total energy (kWh) produced today.

![image](https://user-images.githubusercontent.com/2001094/150829925-30ffbbff-a98e-48d3-b2d5-7efe81fb9e03.png)

- `sensor.zeversolar_pac`: Current output (W).

![image](https://user-images.githubusercontent.com/2001094/150830006-de2ed908-b987-4fa6-8024-91834da305e8.png)

## Fixes
There are some issues with other implementations that I tried to fix here.
- The zeversolar output contains a bug that whenever the output for total energy today has a single decimal place, the actual value it represents requires a leading zero in the decimals. For example `0.4` should be `0.04`.
- Sometimes the inverter randomly produces an invalid output containing something like `1 0 000000000000 +XXXXX-XXXX 00:00 00/00/2000 0 0 Error`, this should be ignored.
- Sometimes, mostly somewhere at the end of the day the inverter produces values for the total energy produced that are lower than previous values. These values will also be ignored.
- When the inverter is offline the sensors for total daily energy and current output would become `unknown` or `unavailable`. Now current output will be 0 and total daily energy will be the last valid value until 00:00 when it will reset to 0.
