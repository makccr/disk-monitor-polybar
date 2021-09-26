<a href="https://makc.co">
    <img src="https://makccr.github.io/images/github-header.svg" alt="MAKC lgoo" title="MAKC" align="right" height="50" />
</a>

# Disk Monitor
A simple polybar module that takes prints the percentage full on your boot drive (/). 

## Installation
You need two pieces to get this module to work: 
#### 1. A bash script to get you the input for polybar.
  * You need to use a script, because we need to be able to refresh the script on an interval in order for this to be effective in any way. 
  * Both the script and a sample polybar conifg are available for download. But you can also easily build a script by addging the two lines below into a text file, and running `chmod +x` on that file, like: `chmod +x filename.sh`:
```
#!/usr/bin/env bash
df -H / | grep -vE '^Filesystem|tmpfs|cdrom' | awk '{ print $5 }'
```

#### 2. You need to modify your actual polybar config
  * First you need to add the actual module, somwhere in your config, usually `.config/polybar/config`:
```
[module/disk]
    type = custom/script
    interval = 1800
    format-prefix = "﫭"
    format = <label>
    exec = "~/.config/polybar/disk.sh"
```
  * Then you'll just need to at the name of the module, *disk* by default to one of the postion values under your bar config:
```
[bar/sample]
...
modules-left = 
modules-center = 
modules-right = disk 
```

## Modification
By default the script will scan the boot drive, or the / directory of your os, but this can be easily changed by modifying the `df` command in the script: `df -H /'`

By default I'm also using [Nerd Fonts](https://www.nerdfonts.com/) to print the icon in front of the information, but this can obviously be changed quite easily to something else as well.

Additionally, my configuration of this module prints out the current percentage used of the drive, but this also can be changed quite easily. I'm using `df -H` to grab this info, but this particular command actually does provide much more information than just this one piece. You'll just have to modify the `awk` command that's being used here: `awk '{ print $5 }'`

```
Filesystem      Size  Used Avail Use% Mounted on
dev              17G     0   17G   0% /dev
run              17G  1.3M   17G   1% /run
/dev/nvme0n1p1  494G  327G  168G  67% /
tmpfs            17G     0   17G   0% /dev/shm
tmpfs            17G  4.1k   17G   1% /tmp
/dev/nvme0n1p2  207M   65M  142M  32% /boot
tmpfs           3.4G   17k  3.4G   1% /run/user/1000
```

I'm using `df -H /` here to print out just the info for the relevant drive: 
```
/dev/nvme0n1p1  494G  327G  168G  67% /
```

Then I'm using `awk '{ print $5 }''` to select just the percentage field. You could use `awk '{ print $4 }''` to select the space remaining, or `awk '{ print $3 }''` to print the space used, and so on. However, for some reason `awk` returns the label part of the field to the output:  
```
Use%
67%
```

So, adding `grep -vE '^Filesystem|tmpfs|cdrom'` in the middle: `df -H / | grep -vE '^Filesystem|tmpfs|cdrom' | awk '{ print $5 }'`, will clean up the output quite nicely:
```
67%
```

From there you just need to link to the script in your polybar config, and build out a module to handle the input.

There is also a tiny bit of customization that can be done to the actual module: 
```
[module/disk]
    type = custom/script
    interval = 1800
    format-prefix = "﫭"
    format = <label>
    exec = "~/.config/polybar/disk.sh"
```

You can customize the actual icon displayed by modifying the `format-prefix = "﫭"` line, and you can change the interval (by default polybar will check your disk every five minutes, 1800 seconds), but you can change this by modifying the `interval = 1800` line. You may also need to change the path to your script, by changing the exec line: `exec = "~/.config/polybar/disk.sh"`
