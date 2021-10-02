# bscpylgtv
Library to control webOS based LG TV devices. Basic, smaller and faster version of
 [aiopylgtv](https://github.com/bendavid/aiopylgtv) (without calibration related features),
 optimized for command line usage (it looks for the same `.aiopylgtv.sqlite` key config file).

## Requirements
- Python >= 3.7

## Install from Source
Run the following command inside this folder
```bash
pip install --upgrade .
```

## Examples

```bash
# Get list of apps (including hidden ones as well)
bscpylgtvcommand 192.168.1.18 get_apps_all
# Push info button
bscpylgtvcommand 192.168.1.18 info_button
# Get values of backlight and contrast of the current picture preset (using list)
bscpylgtvcommand 192.168.1.18 get_picture_settings "[\"backlight\", \"contrast\"]"
# Swtich to SDR ISF Expert Dark picture preset
bscpylgtvcommand 192.168.1.18 set_current_picture_mode expert2
# Set values of backlight and contrast of the current picture preset (using JSON)
bscpylgtvcommand 192.168.1.18 set_current_picture_settings "{\"backlight\": 0, \"contrast\": 85}"
# Turn hdrDynamicToneMapping on in the current HDR10 picture preset (using JSON)
bscpylgtvcommand 192.168.1.18 set_current_picture_settings "{\"hdrDynamicToneMapping\": \"on\"}"
# Set colorGamut to "auto" in the current picture preset (using JSON)
bscpylgtvcommand 192.168.1.18 set_current_picture_settings "{\"colorGamut\": \"auto\"}"
# Set mpegNoiseReduction off in the current picture preset (using JSON)
bscpylgtvcommand 192.168.1.18 set_current_picture_settings "{\"mpegNoiseReduction\": \"off\"}"
# Turn PC Mode on for HDMI2 (using JSON)
bscpylgtvcommand 192.168.1.18 set_other_settings "{\"hdmiPcMode\": {\"hdmi2\": true}}"
# Launch and close screensaver
bscpylgtvcommand 192.168.1.18 launch_app com.webos.app.screensaver
bscpylgtvcommand 192.168.1.18 close_app com.webos.app.screensaver
# Launch hidden software updater on older firmwares
bscpylgtvcommand 192.168.1.18 launch_app com.webos.app.softwareupdate
# Launch hidden software updater on newer firmwares, useful to downgrade (using JSON)
bscpylgtvcommand 192.168.1.18 launch_app_with_params com.webos.app.softwareupdate "{\"mode\": \"user\", \"flagUpdate\": true}"
# Launch In-Start Service Menu (code: 0413) (using JSON)
bscpylgtvcommand 192.168.1.18 launch_app_with_params com.webos.app.factorywin "{\"id\":\"executeFactory\", \"irKey\":\"inStart\"}"
# Launch Ez-Adjust Service Menu (code: 0413) (using JSON)
bscpylgtvcommand 192.168.1.18 launch_app_with_params com.webos.app.factorywin "{\"id\":\"executeFactory\", \"irKey\":\"ezAdjust\"}"
# Get config values of "tv.model" category (using list)
bscpylgtvcommand 192.168.1.18 get_configs "[\"tv.model.*\"]"
# Activate "OLED Motion Pro" on C9 (using JSON)
bscpylgtvcommand 192.168.1.18 set_configs "{\"tv.model.motionProMode\": \"OLED Motion Pro\"}"
# Turn the TV off (standby)
bscpylgtvcommand 192.168.1.18 power_off
```

## Optional command line switches

```bash
# -k <client_key> : specifying a client key
bscpylgtvcommand -k ef6858b2133d68854612831e3df8e495 192.168.1.18 info_button
# -p <path_to_key_file> : specifying path to key file
bscpylgtvcommand -p "D:\config\.aiopylgtv.sqlite" 192.168.1.18 info_button
# -l : get list of all saved client keys per ip (otionally from a specified key file)
bscpylgtvcommand -l
bscpylgtvcommand -l -p "D:\config\.aiopylgtv.sqlite"
```

## Basic Scripting Example

```python
import asyncio
from bscpylgtv import WebOsClient

async def runloop():
    client = await WebOsClient.create('192.168.1.18')
    await client.connect()
    apps = await client.get_apps_all()
    for app in apps:
        print(app)

    await client.disconnect()

asyncio.get_event_loop().run_until_complete(runloop())
```

## Development of `bscpylgtv`

We use [`pre-commit`](https://pre-commit.com) to keep a consistent code style, so ``pip install pre_commit`` and run
```bash
pre-commit install
```
to install the hooks.

## Forum

Forum [topic](https://forum.doom9.org/showthread.php?t=175007).
