# ThinkPad P14s Gen4 Ubuntu (22.04) configuration

Some Lenovo laptops have had issues for several years that have not been fixed.

Many thanks to [whoenig](https://github.com/whoenig/thinkpad-p14s-gen2-ubuntu).

---

This is my setup for i7-1360P + RTX A500.

---

## Fan Control aka Fan Noise

### Installation

Install build-essential package to be able to build the fan control program. Alternatively, you may encounter compilation errors regarding missing packages.
```sh
sudo apt install build-essential
```

Clone latest release:
```sh
git clone -b 1.3.1 --single-branch https://github.com/vmatare/thinkfan
```

Create `build` directory:
```sh
mkdir thinkfan/build && cd thinkfan/build
```

Compile:
```sh
cmake -DCMAKE_BUILD_TYPE=Release .. && make
```

Install:
```sh
sudo make install
```

### Configuration

Copy `thinkfan.yaml` to `/etc/thinkfan.yaml`.
You may use `thinkfan.yaml` from current repository or from [this repository](https://github.com/whoenig/thinkpad-p14s-gen2-ubuntu).

Login in to the root environment to have permission to change files:
```sh
sudo su
```

Enable fan control:
```sh 
echo "options thinkpad_acpi fan_control=1" > /etc/modprobe.d/thinkfan.conf && modprobe thinkpad_acpi && exit
```
Make sure you have left the root environment.

Enable on start up:
```sh
sudo systemctl enable thinkfan.service
```

Open `/etc/modules` (with your favorite editor) and add the following lines to the end of the file:
```
thinkpad_acpi
coretemp
```

Reboot and check if service is running:
```sh
service thinkfan status
```

After editing the `/etc/thinkfan.yaml` file, you can reload the configuration using this command:
```sh
sudo service thinkfan reload
```

### Your own configuration 

As I understand it, you want to achieve several goals:
- Avoid constantly turning it on and off (this is irritating and probably unhealthy for the fan).
- Fan stops at complete idle (for example, when the lock screen is activated).
- The fan maintains the lowest possible RPM (`level` in `thinkfan.yaml`), maintaining an acceptable temperature.

To achieve this, you will need to monitor your system for probably several days.

You can use this command:
```sh
watch -n .5 'cat /proc/acpi/ibm/fan | head -n3 | tail -n1 && sensors |  grep -E "CPU|fan1"'
```
or [tempwatch](https://github.com/whoenig/thinkpad-p14s-gen2-ubuntu?tab=readme-ov-file#monitoring-wattage-fan-speed-cpu-temps-gpu-usage) script.

---

List of contents:
- https://github.com/whoenig/thinkpad-p14s-gen2-ubuntu
- https://github.com/daniel-kristjansson/smart-fancontrol
- https://github.com/vmatare/thinkfan

---

## High Pitch Noise aka Fan Whistling

The airflow causes a whistling sound (high pitch noise) due to the unfortunate perforation of the bottom panel (probably).

I solved this problem by lifting the back of the laptop. This position somehow isolates the whistling from my ears.

Other possible [solution](https://forums.lenovo.com/t5/ThinkPad-P-and-W-Series-Mobile-Workstations/P15-gen2-T15g-gen2-High-pitched-whistling-fan-noise/m-p/5122920?page=3#6008469).

---

## CPU Power

TODO:

List of contents:
- https://github.com/whoenig/thinkpad-p14s-gen2-ubuntu
- https://github.com/erpalma/throttled
- https://www.reddit.com/r/thinkpad/comments/870u0a/t480s_linux_throttling_bug/
- https://github.com/daniel-kristjansson/smart-fancontrol
- https://gist.github.com/terdon/e1dc9cdec58d0351a0f6bbeb0051474b
