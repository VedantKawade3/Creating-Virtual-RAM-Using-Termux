# Creating Virtual RAM Using Termux
Since our lenovo tb-x306x is rooted & ~~don't have any twrp available on internet (as of now)~~ have [TWRP-unofficial](https://xdaforums.com/t/closed-discontinued-tb-x306x-android-10-unofficial-twrp-3-7-lenovo-tab-m10-hd-2nd-gen.4606369/) & have only 2GB Ram so we have to increase 
it by creating virtual ram so we can increase performance & decrease uneccessary lags in it

> If you don't know how to root lenovo tb-x306x, I have made process for that check it out [here](https://github.com/VedantKawade3/Rooting-of-lenovo_TB-X306X)!

## Prerequisites

- **Device:** [Lenovo TB-X306X (rooted)](https://github.com/VedantKawade3/Rooting-of-lenovo_TB-X306X)
- **Termux:** Install from [F-Droid](https://f-droid.org/packages/com.termux/)
- **Basic knowledge:** Familiarity with Linux commands but if you are not then you can proceed as i shown 

---

## Step 1: Install Termux and Required Packages

• Open Termux and run the following commands:

```bash
pkg update && pkg upgrade -y
```
```bash
pkg install tsu
```
```bash
pkg install nano
```
These commands update Termux and install nano (a text editor) and tsu (a utility that acts as ```sudo```).

## Step 2: Gain Root Access
• Enter root mode(superuser mode) by running: <br>
```bash
tsu
``` 
<br>

> **Important:** Do not use the ```su``` command because some commands may not run properly in that mode.


## Step 3: Create the Swap File
( if you are familiar or worked with linux then probably you know about this) <br>
• Navigate to the ```/data/``` directory and attempt to create a swap file:
```bash
cd /data/
```
```bash
dd if=/dev/zero of=swapfile bs=1M count=4096
```
This command creates a 4GB swap file (modify the ```count``` parameter for a different size).
> **Troubleshooting:** You may see an error like:
```dd: failed to open 'swapfile': read-only file system```

## Step 4: Fix the Read-Only File System Issue
• Check where the ```/data/``` directory is mounted:
```bash
mount | grep /data
```

If you will see many mounted points then you have to remount every point as ```ro``` to ```rw```. didn't understood?
let me simplify, you have to see only starting two letters of each bracket you see on screen & where it is written ```ro``` select that 
line and see somewhere it
. For example, in my case it appears as:
```/dev/block/mmcb1k0p40 on /data type ext4 (ro,nosuid,...)```

• Remount this partition as read-write:
```bash
mount -o rw,remount /dev/block/mmcb1k0p40 /data
```
• Repeat for any additional read-only mounted points means do same for all ro lines.

• verify all brackets contains ```rw``` or not.

Now issue is solved.
## Step 5: Recreate and Activate the Swap File
• Once ```/data/``` is remounted as read-write, recreate the swap file:

```bash
dd if=/dev/zero of=swapfile bs=1M count=4096
```
```bash
mkswap swapfile
```
This will Format the file as swap space
```bash
swapon swapfile
```
This will Activate the swap file
<br><br>

• Verify the swap activation with:
```bash
free -m
```
You should see a swap section with the allocated memory as shown in image
<add image here>


## Step 6: Make the Swap Permanent
• To have the swap file activate after every reboot, create scripts in ```/data/adb/service.d/```.

• Create ```swapfile``` Script:
```bash
cd /data/adb/service.d/
nano swapfile
```
Inside nano type or paste follow:
```bash
#!/system/bin/sh
swapon /data/swapfile
```
Press ```Ctrl+O``` (to save), then ```Enter```, and ```Ctrl+X``` (to exit).

Create ```swappiness.sh``` Script
```bash
nano swappiness.sh
```
Inside nano type or paste follow:
```bash
#!/system/bin/sh
sysctl -w vm.swappiness=60
```
Press ```Ctrl+O```, then ```Enter```, and ```Ctrl+X```.

> **Note:** The ```swappiness.sh``` script controls how aggressively the system uses the swap file, balancing performance and storage speed. And here value ```60``` is recommended


• Create ```io_sheduler.sh``` Script:
```bash
nano io_sheduler.sh
```
Inside nano type or paste follow:
```bash
#!/system/bin/sh
echo "deadline" > /sys/block/mmcb1k0/queue/sheduler
```
> **Note:** This command sets the I/O scheduler to ```"deadline"```, which is recommended for ```eMMC storage``` for a balance between performance and fairness. (i have created 4GB of virtual ram which is not recommended for 2GB ram device but i want more performance so i did this for balance)


• Make the Scripts Executable:
```bash
chmod +x swapfile.sh
```
```bash
chmod +x swappiness.sh
```
```bash
chmod +x io_sheduler.sh
```

## Step 7: Reboot and Verify
• Reboot your tablet:
```bash
reboot
```
After rebooting, open Termux, enter root mode:
```bash
tsu
```
And verify:
```bash
free -m
```
This will show the swap memory allocation
```bash
swapon --show
```
This will list the active swap file
<add image here>

<h2>Congrats 🎉 we sucessfully created virtual ram of 4GB but it will work as 2GB extra physical ram because virtual rams are slow.</h2>

This process will also help to reduce the hangness of tablet. I have done this with my tablet & i feel it less laggy mans it did not lag 
but it lag for once for 5 minutes after when we connect the tablet to internet & after 5 minutes laggyness goes & work normally whithout 
lag. And If you still feel laggy then remove uneccessary bloatware from tablet. I already done this, if you want process for that you can comment me!


# • If you want to delete that swapfile you can run following command:
```bash
tsu
```
```bash
rm -rf /data/swapfile
```
OR
```bash
rm /data/swapfile
```

# If you found this guide helpful, please give a ⭐ to my repository!
