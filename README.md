# GIF2LEDBadge

This project tries to automate the process of writing custom 'animations' from GIFs to a 11x44 LED matrix badge.

As of now this project is developed on Debian 12 and hence does not have support for other operating systems. This should work on most Linux distros, but YMMV.

### Background

During the 37C3 conference, one could acquire 11x44 LED matrix badges. These badges could be customized with a mobile app through bluetooth, or then over a Micro USB cable. However, if one would like to implement a custom 'animation' for the badge, this could only be done over USB. This project aims to automate the process of creating aforementioned animation from a GIF.

### The conversion process

The process of converting a GIF into an animation goes as follows:

1. A GIF in 11x48 dimensions is required (Note that the GIF needs to be in **11x48** dimensions even though the matrix is **11x44**. The contents of the GIF need to be aligned to the left because only the first **44** pixels are visible on the badge). For best results, it is recommended that this GIF is black & white because the LED matrix effectively is a binary system (Black = led off & White = led on).
2. The frames of the GIF are separated.
3. Each frame is merged into a 1 long image file with dimensions **11x48n**, where **n** is the total amount of frames in the GIF.
4. This long image is uploaded to the badge. (Note that the badge has only **8192** bytes of memory, so the GIF data cannot exceed that length or it will damage the display. There is an alert for this so you cannot accidentally brick your badge with this script.)

### Installation

Install the python dependencies. This is recommended to be done in a virtual environment.

Create a virtual environment (with name `g2lb`):
```
$ python3 -m venv g2lb
```

Activate virtual environment:
```
$ source g2lb/bin/activate
```

Installing dependencies
```
(g2lb) $ python3 -m pip install pyhidapi pillow
```

---

#### Bugs with pyhidapi in Debian

##### Missing shared object

You can try to initialize pyhidapi to see whether you get an error due to a missing shared object:
```
>>> import pyhidapi
>>> pyhidapi.hid_init()
Traceback (most recent call last):
  File "<stdin>", line 1, in <module>
...
OSError: /usr/local/lib/libhidapi-hidraw.so.0: cannot open shared object file: No such file or directory
```

This shared object originates from the `libhidapi-hidraw0` package.

```
$ sudo apt install libhidapi-hidraw0
```

However, the shared object is saved to `/usr/lib/x86_64-linux-gnu/libhidapi-hidraw.so.0` instead of `/usr/local/lib/libhidapi-hidraw.so.0`.

This can be fixed with symlinking the shared object to the correct path:

```
$ sudo ln -s /usr/lib/x86_64-linux-gnu/libhidapi-hidraw.so.0 /usr/local/lib/libhidapi-hidraw.so.0
```

The error should now be fixed and pyhidapi can be initialized correctly:

```
>>> import pyhidapi
>>> pyhidapi.hid_init()
>>> 
```

---

Writing to badge taken from https://github.com/jnweiger/led-name-badge-ls32/blob/master/lednamebadge.py
