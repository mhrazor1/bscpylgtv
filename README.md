# bscpylgtv
Library to control webOS based LG TV devices. Enhanced and faster version of
 [aiopylgtv](https://github.com/bendavid/aiopylgtv), it can be installed without calibration functionality,
 optimized for command line usage (it looks for the same `.aiopylgtv.sqlite` key config file).

## Requirements
- Python >= 3.7

## Install from package
```bash
# Install lite package without calibration functionality
pip install bscpylgtv
# Instal full package with calibration functionality (requires numpy package)
pip install bscpylgtv[with_calibration]
```

## Install from Source
Run the following command inside this folder:
```bash
# Install lite package without calibration functionality
pip install --upgrade .
# Instal full package with calibration functionality (requires numpy package)
pip install --upgrade .[with_calibration]
```

## Examples

```bash
# Get list of apps (including hidden ones as well)
bscpylgtvcommand 192.168.1.18 get_apps_all
# Push info button
bscpylgtvcommand 192.168.1.18 info_button
# Switch to HDMI2 input
bscpylgtvcommand 192.168.1.18 set_input HDMI_2
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
# Turn off/on screen (use turn_screen_off_wo4 and turn_screen_on_wo4 commands for WebOS v4.x)
bscpylgtvcommand 192.168.1.18 turn_screen_off
bscpylgtvcommand 192.168.1.18 turn_screen_on
# Launch installation app hidden menu (Hotel Mode, Password change, USB Cloning, Set ID setup, IP Control)
bscpylgtvcommand 192.168.1.18 launch_app com.webos.app.installation
# Display 3x MUTE button hidden menu (AVReset, Customer Support, etc)
bscpylgtvcommand 192.168.1.18 launch_app_with_params com.webos.app.tvhotkey "{\"activateType\": \"mute-hidden-action\"}"
# Display 7x GREEN button hidden Freesync info
bscpylgtvcommand 192.168.1.18 launch_app_with_params com.webos.app.tvhotkey "{\"activateType\": \"freesync-info\"}"
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
# -g : get system information (required by some of the calibration commands)
bscpylgtvcommand -g 192.168.1.18 upload_3d_lut_bt2020_from_file expert1 "test3d-2.cube"
# -d : disabling key file
# -k <client_key> : specifying a client key
bscpylgtvcommand -d -k ef6858b2133d68854612831e3df8e495 192.168.1.18 info_button
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
    client = await WebOsClient.create('192.168.1.18', ping_interval=None, getSystemInfo=False, skipStateInfo=True)
    await client.connect()
    apps = await client.get_apps_all()
    for app in apps:
        print(app)

    await client.disconnect()

asyncio.get_event_loop().run_until_complete(runloop())
```

## Subscribed State Updates Example

```python
import asyncio
from bscpylgtv import WebOsClient

async def on_state_change():
    print("State changed:")
    print(client.apps)
    print(client.inputs)
    print(client.power_state)
    print(client.current_appId)
    print(client.channels)
    print(client.current_channel)
    print(client.channel_info)
    print(client.muted)
    print(client.volume)
    print(client.sound_output)
    print(client.picture_settings)
    print(client.system_info)
    print(client.software_info)

async def runloop():
    global client
    client = await WebOsClient.create('192.168.1.18')
    await client.register_state_update_callback(on_state_change)
    await client.connect()

    ret = await client.volume_up()
    print(ret)
    await asyncio.sleep(30)

    await client.disconnect()

asyncio.get_event_loop().run_until_complete(runloop())
```

More useful examples can be found in [docs/scripts](https://github.com/chros73/bscpylgtv/docs/scripts) directory.

## Calibration functionality
WARNING: Messing with the calibration data COULD brick your TV in some circumstances, requiring a mainboard replacement.
All of the currently implemented functions SHOULD be safe, but no guarantees.

On supported models, calibration functionality and upload to internal LUTs is supported.  The supported input formats for LUTs are IRIDAS .cube format for both 1D and 3D LUTs, and ArgyllCMS .cal files for 1D LUTs.

Not yet supported:
-Dolby Vision config upload
-Custom tone mapping for 2019 models (functionality does not exist on 2018 models)

Supported models:
LG 2019 Alpha 9 G2 OLED R9 Z9 W9 W9S E9 C9 NanoCell SM99
LG 2019 Alpha 7 G2 NanoCell (8000 & higher model numbers)
LG 2018 Alpha 7 Super UHD LED (8000 & higher model numbers)
LG 2018 Alpha 7 OLED B8
LG 2018 Alpha 9 OLED C8 E8 G8 W8

Models with Alpha 9 use 33 point 3D LUTs, while those with Alpha 7 use 17 points.

n.b. this has only been extensively tested for the 2018 Alpha 9 case, so fixes may be needed still for the others.

WARNING:  When running the ddc_reset or uploading LUT data on 2018 models the only way to restore the factory
LUTs and behaviour for a given input mode is to do a factory reset of the TV.
ddc_reset uploads unity 1d and 3d luts and resets oled light/brightness/contrast/color/ to default values (80/50/85/50).
When running the ddc_reset or uploading any 1D LUT data, service menu white balance settings are ignored, and gamma,
colorspace, and white balance settings in the user menu are greyed out and inaccessible.

Calibration data is specific to each picture mode, and picture modes are independent for SDR, HDR10+HLG, and Dolby Vision.
Picture modes from each of the three groups are only accessible when the TV is in the appropriate mode.  Ie to upload
calibration data for HDR10 picture modes, one has to send the TV an HDR10 signal or play an HDR10 file, and similarly
for Dolby Vision.

For SDR and HDR10 modes there are two 3D LUTs which will be automatically selected depending on the colorspace flags of the signal
or content.  In principle almost all SDR content should be bt709 and HDR10 content should be bt2020 but there could be
nonstandard cases where this is not true.

For Dolby Vision the bt709 3d LUT seems to be active and the only one used.

Known supported picMode strings are:
SDR: cinema, expert1, expert2, game, technicolorExpert
HDR10(+HLG): hdr_technicolorExpert, hdr_cinema, hdr_game
DV: dolby_cinema_dark, dolby_cinema_bright, dolby_game

Calibration commands can only be run while in calibration mode (controlled by "start_calibration" and "end_calibration").

While in calibration mode for HDR10 tone mapping is bypassed.
There may be other not fully known/understood changes in the image processing pipeline while in calibration mode.

Calibration command line examples, modifying expert1 SDR preset (ISF Expert Bright Room):
```bash
# Switch to HDMI2 input
bscpylgtvcommand 192.168.1.18 set_input HDMI_2
# Start calibration mode
bscpylgtvcommand 192.168.1.18 start_calibration expert1
# Set oled light to 33
bscpylgtvcommand 192.168.1.18 set_oled_light expert1 33
# Set contrast to 85
bscpylgtvcommand 192.168.1.18 set_contrast expert1 85
# Do a DDC reset including uploading unity 1DLUT
bscpylgtvcommand -g 192.168.1.18 ddc_reset expert1 true
# Upload custom 1DLUT from file
bscpylgtvcommand -g 192.168.1.18 upload_1d_lut_from_file expert1 "test.cal"
# Upload custom 3DLUT from file into BT709 slot
bscpylgtvcommand -g 192.168.1.18 upload_3d_lut_bt709_from_file expert1 "test3d-1.cube"
# Upload custom 3DLUT from file into bt2020 slot
bscpylgtvcommand -g 192.168.1.18 upload_3d_lut_bt2020_from_file expert1 "test3d-2.cube"
# End calibration mode
bscpylgtvcommand 192.168.1.18 end_calibration expert1
```

Same calibration via scripting:
```python
import asyncio
from bscpylgtv import WebOsClient

async def runloop():
    client = await WebOsClient.create('192.168.1.18', skipStateInfo=True)
    await client.connect()

    await client.set_input("HDMI_2")
    await client.start_calibration(picMode="expert1")
    await client.set_oled_light(picMode="expert1", value=33)
    await client.set_contrast(picMode="expert1", value=85)
    await client.ddc_reset(picMode="expert1", reset_1d_lut=True)
    await client.upload_1d_lut_from_file(picMode="expert1", filename="test.cal")
    await client.upload_3d_lut_bt709_from_file(picMode="expert1", filename="test3d.cube")
    await client.upload_3d_lut_bt2020_from_file(picMode="expert1", filename="test3d.cube")
    await client.end_calibration(picMode="expert1")

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
