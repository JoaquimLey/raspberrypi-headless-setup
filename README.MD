# Setup

1. Insert SD card into your local machine
2. Flash the SD with the appropriate OS, I recommend using [raspberry-imager](https://www.raspberrypi.org/blog/raspberry-pi-imager-imaging-utility/) (you may need to reconnect your SD card after the flash process)
3. When the SD write (and verification) is complete, open the terminal

## Enable ssh
```bash
touch /Volumes/boot/ssh
```

## Add WIFI for headless

### 1. Create the config file

```bash
touch  /Volumes/boot/wpa_supplicant.conf
```

### 2. Add your WIFI details

Adjusting for your [ISO 3166 alpha-2 country code](https://en.wikipedia.org/wiki/List_of_ISO_3166_country_codes), network name and network password:

Using `vscode`:
```bash
code /Volumes/boot/wpa_supplicant.conf
```

For example:

```
ctrl_interface=DIR=/var/run/wpa_supplicant GROUP=netdev
update_config=1
country=PT

network={
	ssid="An-example-wifi"
	psk="this_pw_is_fake1"
	key_mgmt=WPA-PSK
	priority=1
	id_str="home"
}

network={
	ssid="Another-wifi_name"
	psk="anotherFakePassword!"
	key_mgmt=WPA-PSK
	priority=2
	id_str="office"
}

network={
	ssid="Yet_another"
	psk="super_secret_password"
	key_mgmt=WPA-PSK
	priority=3
	id_str="factory"
}
```

### Verify all went well

```bash
cat /Volumes/boot/wpa_supplicant.conf
```

# Boot up

1. Eject your SD card
2. Insert the SD card to your raspi
3. Connect power source
4. The LEDs should start flashing, wait for your pi to boot up

### SSH into your pi

```
ssh pi@raspberrypi.local
```

_Default password for `pi` user is `raspberry`_

-----------------------------

#### Recommended

Change default password

```
sudo passwd
```

1. Type your password
2. Confirm new password

You should see a message 
```
passwd: password updated successfully
```

_If you get an error:_

```bash
passwd: Authentication token manipulation error
passwd: password unchanged
```

It means the filesystem was mounted as read-only, which prevents changing the password. A way to fix this issue is to remount the filesystem and then to check permissions of `/etc/shadow` file.

```
sudo mount -rw -o remount /
```

or

```
sudo mount -o remount,rw /
```

### Expand storage

```bash
sudo raspi-config
```

  1. Advanced options

  2. Select first option 
  
```
A1 Expand File System
```

#### (Optional) Change your pi's hostname

This might come in handy for two reasons:
1. Easily distinguish between devices

2. Easily ssh locally with `ssh pi@your_custom_hostname.local`

```bash
sudo raspi-config
```

  1. Network Options

  2. N1 Hostname -> OK
  
  3. Set the new hostname

### Remove Bloatware on the Raspberry Pi

```
sudo curl -fsSL https://raw.githubusercontent.com/JoaquimLey/raspberrypi-headless-setup/master/pi_bloat_remove.sh | sudo bash
```

### Setup ngrok

I use ngrok to remotely ssh to my pi's. The free account is more than enough for this use case. Beware though, the port is constantly changing and the connections drop fairly frequently, can be a little bit frustrating.

1. Create a free account at https://ngrok.com/
2. Download  ngrok for `Linux ARM`

```bash
sudo wget https://bin.equinox.io/c/4VmDzA7iaHb/ngrok-stable-linux-arm.zip
```

3. Configure and start the service on boot using a symlink: https://github.com/JoaquimLey/ngrok-install

### Install git

```bash
sudo apt-get install git
```


### Update packages to the latest version

```bash
sudo apt-get update && sudo apt-full-upgrade
```
