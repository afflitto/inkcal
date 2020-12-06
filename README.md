# Inkcal

Shows a live Google Calendar on a Waveshare e-ink panel

## Requirements

### Hardware

* Raspberry Pi (recommended model 3B or newer)
* [Waveshare 9.7inch E-Ink display with hat](https://www.waveshare.com/product/displays/e-paper/9.7inch-e-paper-hat.htm)

### Software
* python3
* lightdm
* tightvncserver
* firefox
* unclutter (hides the mouse cursor)
* [PaperTTY](https://github.com/joukos/PaperTTY) and it's requirements

## Instructions

This step-by-step guide should get you up and running! Tested on Raspberry Pi OS Lite released August 20, 2020.

### Install requirements:

```
$ sudo apt install git python3-pip lightdm xfce4 tightvncserver firefox-esr unclutter
$ sudo python3 -m pip install papertty
```

Make sure to install `papertty` with `sudo`!

`vncdotool` version `1.0.0` has a bug in python3 that may cause issues ([#174](https://github.com/sibson/vncdotool/issues/172)).
I had to downgrade to version `0.13.0` by running:

```
$ sudo python3 -m pip install vncdotool==0.13.0
```

### Enable SPI in `raspi-config`:

```
$ sudo raspi-config
```
Enable SPI under Interfacing Options>SPI>Yes


### Set up `tightvncserver` password:

```
$ tightvncpasswd
```

Set the password to `calendar`, or whatever you want. If you use a different password then `calendar`, 
you must update `services/inkcal-screen.service` with your new password.

You do not need to set a view-only password (press `n`).

### Install unit files

If you used a different vnc password, make sure to edit `services/inkcal-screen.service` with your new password. 
You can also change the resolution and e-ink driver here if you are using a different e-ink panel. You'll also need
to change the resolution in `services/tightvnc.service`.

If you haven't already, clone this repository to your Pi:

```
$ cd ~
$ git clone https://github.com/afflitto/inkcal
$ cd inkcal
```

Install and enable the services to make everything start at boot:

```
$ sudo cp services/* /etc/systemd/system
$ sudo systemctl enable tightvnc
$ sudo systemctl enable inkcal-calendar
$ sudo systemctl enable inkcal-screen
```

Now you can start the services:

```
$ sudo systemctl start tightvnc
$ sudo systemctl start inkcal-calendar
$ sudo systemctl start inkcal-screen
```

The `inkcal-calendar` service takes 45 seconds to start, be patient! If everything worked, you should see the
Google login screen on your eink panel.

### Set up Google Calendar

You should now be able to VNC into your Pi at `<pi's IP address>:1` with the password `calendar` and log into your 
Google account. The text starts out a bit small, but you can use my `style.css` with the Firefox extension 
[stylish](https://addons.mozilla.org/en-US/firefox/addon/stylish/).

Firefox is started in fullscreen mode with the `-kiosk` flag, so you might want to open Firefox normally for now:

```
$ sudo systemctl stop inkcal-calendar
$ DISPLAY=:1 firefox
```

I also recommend unchecking `Restore previous session` in Firefox preferences so that a new calendar tab isn't 
opened each time.

## Finishing notes

That's all the software steps! Reboot your Pi to make sure everything comes up at boot.

The e-ink panel (and especially the flex cables) are very fragile, so be careful when assembling everything. Also 
make sure the Pi is powered down before attaching the HAT or plugging in the cables, make sure you have the cables
the right way around, etc.

I haven't put this in a frame yet, but this project was inspired by
[/u/ThePhotoChemist](https://www.reddit.com/r/RTLSDR/comments/jd172o/i_crammed_an_eink_screen_into_an_ikea_picture/)'s 
e-ink project. They used an IKEA Ribba frame which housed everything nicely. Definitely check out their reddit post!

