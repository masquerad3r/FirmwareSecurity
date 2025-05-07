# Notes while performing tutorial

## Audio files location

`rootfs_data/lib/audio`: This contains all `.g711` files

## Possible binary

Debug script at `./rootfs_data/sbin/debug_speaker_test.sh` allows testing all sounds on the system via `system_state_audio` command

Possible paths of exploitation:

1. Directly running the `debug_speaker_test.sh` file

    Command to use:

    ```bash
    # Provided exmaple inside the file
    debug_speaker_test.sh 1 50 1
    ```

    **NOTE:** This will loop through all available sounds and play them

2. Invoking the actual command (<mark>Better Option</mark>)
    Command to use:

    ```bash
    ubus call system_state_audio audio_play "{\"id\":0,\"vol\":40,\"interrupt\":0}"
    ```

    **NOTE:** replace ID with whatever file was replaced with our custom audio file (`.g711`)

## Steps of Exploitation

1. Convert the audio file to `.g711` format

    Download any funny audio file and use  `ffmpeg` to convert them:

    ```bash
    # for mp3
    ffmpeg -i file.mp3 -ar 8000 -ac 1 -f alaw output.g711

    # for wav
    ffmpeg -i file.wav -ar 8000 -ac 1 -f alaw output.g711
    ```

2. Connect the SD card to your attacker machine (linux host) and transfer the file to it.
3. Now, connect the SD to the camera.
4. Connect the USB to TTY wire to your attacker machine and interface with the camera using `UART` via `screen` or `minicom`

    Screen exmple:

    ```bash
    # Format: screen TTYDevice Baud-rate
    sudo screen /dev/ttyUSB0 115200
    ```

5. Power cycle the camera and wait for login prompt and login using provided root creds.

    **NOTE:** It will keep displaying bunch of errors, just ignore them and press `Enter`.

6. Now, mount the SD inside the camera and transfer the audio file and replace whatever audio file you would like.

7. Now, issue the `ubus call` command with the ID of the audio file you replaced :)
