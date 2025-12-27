### Main programs
These are needed to configure fan control by userspace programs, as the Dell BIOS suddenly starts and stops the fan
- To configure sensors; first install `dell-bios-fan-control`:
```bash
sudo dell-bios-fan-control 0
sudo sensors-detect
sudo pwmconfig
```
#### In detail
- Install `lm_sensors` or `lm-sensors` package; it provides `fancontrol`
- Create a `udev` rule to properly get the Dell sensor name and locaiton
```bash
  sudo nano /etc/udev/rules.d/99-fancontrol-stable.rules
 ```
Copy this in:
```
  # Create a stable symlink for Dell SMM (Fans)
ACTION=="add|change", SUBSYSTEM=="hwmon", ATTR{name}=="dell_smm", RUN+="/usr/bin/ln -sfT /sys/class/hwmon/$name /dev/hwmon-dell"

# Create a stable symlink for Coretemp (CPU Sensors)
ACTION=="add|change", SUBSYSTEM=="hwmon", ATTR{name}=="coretemp", RUN+="/usr/bin/ln -sfT /sys/class/hwmon/$name /dev/hwmon-coretemp"
```
- Restart `udev` to apply new rules
```bash
sudo udevadm control --reload-rules && sudo udevadm trigger
```
- Edit the `fancontrol` file:
```bash
sudo nano /etc/fancontrol 
```
Copy this in:
```
# Configuration using stable udev symlinks
INTERVAL=2

# Mapping
FCTEMPS=/dev/hwmon-dell/pwm1=/dev/hwmon-coretemp/temp1_input /dev/hwmon-dell/pwm2=/dev/hwmon-coretemp/temp1_input
FCFANS=/dev/hwmon-dell/pwm1=/dev/hwmon-dell/fan1_input /dev/hwmon-dell/pwm2=/dev/hwmon-dell/fan2_input

# Smoothing (Keeps it steady)
AVERAGE=/dev/hwmon-dell/pwm1=4 /dev/hwmon-dell/pwm2=4

# THE FIX: Separate Start and Stop temps
# Fans start spinning at 60°C
MINTEMP=/dev/hwmon-dell/pwm1=60 /dev/hwmon-dell/pwm2=60
# Fans stay pinned to MAX at 85°C
MAXTEMP=/dev/hwmon-dell/pwm1=85 /dev/hwmon-dell/pwm2=85

# MINSTOP is the key: It tells the fan to keep spinning until 50°C
# Note: In some versions of fancontrol, you must set this logic via MINPWM and MINSTART
MINSTART=/dev/hwmon-dell/pwm1=128 /dev/hwmon-dell/pwm2=128
MINSTOP=/dev/hwmon-dell/pwm1=100 /dev/hwmon-dell/pwm2=100

# Speed limits
MINPWM=/dev/hwmon-dell/pwm1=100 /dev/hwmon-dell/pwm2=100
MAXPWM=/dev/hwmon-dell/pwm1=255 /dev/hwmon-dell/pwm2=255
```

- Create the file `fancontrol-resume` and make it executable:
```bash
sudo nano /lib/systemd/system-sleep/fancontrol-resume
```
Copy this in:
```
#!/bin/bash
#if [ "$1" = "post" ]; then
#  /usr/bin/dell-bios-fan-control 0
#fi

case "$1" in
    pre)
        # Before suspend:
        # 1. Stop fancontrol to prevent it from fighting with BIOS.
        # 2. Enable BIOS fan control so it can turn off the fans during suspend.
        systemctl stop fancontrol.service
        /usr/bin/dell-bios-fan-control 1
        ;;
    post)
        # After resume:
        # 1. Disable BIOS fan control to give control back to OS.
        # 2. Restart fancontrol.
        /usr/bin/dell-bios-fan-control 0
        systemctl start fancontrol.service
        ;;
esac
```
- Make it executable:
```bash
sudo chmod +x /lib/systemd/system-sleep/fancontrol-resume
```
- Create `system/dell-bios-fan-control.service`:
```bash
sudo nano /etc/systemd/system/dell-bios-fan-control.service
```
Copy this in:
```
[Unit]
Description=Disable Dell BIOS fan arbitration
DefaultDependencies=no
Before=fancontrol.service

[Service]
Type=oneshot
ExecStart=/usr/bin/dell-bios-fan-control 0
RemainAfterExit=true
```
- Create `fancontrol.service`:
```bash
sudo nano /usr/lib/systemd/system/fancontrol.service
```
Copy this in:
```
[Unit]
Description=Start fan control, if configured
ConditionFileNotEmpty=/etc/fancontrol
After=lm_sensors.service

[Service]
Type=simple
PIDFile=/run/fancontrol.pid
ExecStart=/usr/sbin/fancontrol

[Install]
WantedBy=multi-user.target
```
- Enable it
```bash
sudo systemctl enable --now fancontrol.service
  ```
- Create `10-bios-override.conf`
```bash
sudo nano /etc/systemd/system/fancontrol.service.d/10-bios-override.conf
```
Copy this in:
```
[Unit]
After=dell-bios-fan-control.service
Wants=dell-bios-fan-control.service
```
- Create `/etc/modprobe.d/dell_smm_hwmon.conf` (only if issues, not needed normally)
```
options dell_smm_hwmon force=1
```
- Run the below to clone and install `Dell BIOS fan control` executable
```bash
git clone https://github.com/TomFreudenberg/dell-bios-fan-control
cd dell-bios-fan-control/
make
chmod +x dell-bios-fan-control
sudo cp dell-bios-fan-control /usr/bin
```
### Useful software to debug
- `pwmtest.sh` to test fan RPM
```
#!/bin/bash
# pwmtest.sh — test MINSTART/MINSTOP empirically

# pwmtest.sh — updated for stable symlinks
PWM_DEV="/dev/hwmon-dell/pwm1"
FAN_DEV="/dev/hwmon-dell/fan1_input"

test_pwm() {
    local pwmval="$1"
    echo "$pwmval" > "$PWM_DEV"
    sleep 2
    local rpm=$(cat "$FAN_DEV")
    printf "PWM %3d → RPM %5d\n" "$pwmval" "$rpm"
}

# Ramp up
echo "Testing MINSTART sequence..."
for pwm in $(seq 0 10 100); do
    test_pwm "$pwm"
done

# Ramp down
echo "Testing MINSTOP sequence..."
for pwm in $(seq 100 -10 0); do
    test_pwm "$pwm"
done

# Reset
echo 0 > "$PWM_DEV"
```
- `monitor_fans.sh` to check fans speed and CPU temperature; use `./monitor_fans.sh | tee fan-cpu.txt` to get an onscreen and log output
```
#!/bin/bash

# Configuration for your sensors and fans
# monitor_fans.sh — updated for stable symlinks

CPU_TEMP_DEV="/dev/hwmon-coretemp/temp1_input"
FAN1_RPM_DEV="/dev/hwmon-dell/fan1_input"
FAN2_RPM_DEV="/dev/hwmon-dell/fan2_input"
FAN1_PWM_DEV="/dev/hwmon-dell/pwm1"
FAN2_PWM_DEV="/dev/hwmon-dell/pwm2"

# --- Define fixed widths for formatting ---
# Max expected RPM is around 5000-6000, so 5 digits. Plus " RPM" and a space.
# 5000 RPM -> " 5000 RPM" (space for padding)
FAN_RPM_WIDTH=9 # e.g., " 5000 RPM" or "    0 RPM"

# PWM values are 0-255, so 3 digits. Plus " PWM" and a space.
PWM_WIDTH=7 # e.g., "  80 PWM" or " 255 PWM"

# Loop indefinitely to provide continuous, scrolling output
echo "Monitoring CPU/GPU Fan RPMs, PWM Values, and CPU Temperature (Ctrl+C to exit)..."
echo "---------------------------------------------------------------------------------"

while true; do
    # Get raw sensor data
    cpu_raw=$(cat "$CPU_TEMP_DEV" 2>/dev/null)
    fan1_raw=$(cat "$FAN1_RPM_DEV" 2>/dev/null)
    fan2_raw=$(cat "$FAN2_RPM_DEV" 2>/dev/null)
    fan1_pwm_raw=$(cat "$FAN1_PWM_DEV" 2>/dev/null)
    fan2_pwm_raw=$(cat "$FAN2_PWM_DEV" 2>/dev/null)

    # Process and format data
    cpu_temp_c="N/A"
    if [[ -n "$cpu_raw" ]]; then
        cpu_temp_c=$((cpu_raw / 1000))
    fi

    # Format Fan 1 RPM
    fan1_rpm_display="N/A"
    if [[ -n "$fan1_raw" ]]; then
        printf -v fan1_rpm_display "%5d RPM" "$fan1_raw" # Pad RPM to 5 digits, then add " RPM"
    else
        printf -v fan1_rpm_display "%${FAN_RPM_WIDTH}s" "N/A" # Pad N/A to full width
    fi

    # Format Fan 2 RPM
    fan2_rpm_display="N/A"
    if [[ -n "$fan2_raw" ]]; then
        printf -v fan2_rpm_display "%5d RPM" "$fan2_raw" # Pad RPM to 5 digits, then add " RPM"
    else
        printf -v fan2_rpm_display "%${FAN_RPM_WIDTH}s" "N/A" # Pad N/A to full width
    fi

    # Format Fan 1 PWM
    fan1_pwm_display="N/A"
    if [[ -n "$fan1_pwm_raw" ]]; then
        printf -v fan1_pwm_display "%3d PWM" "$fan1_pwm_raw" # Pad PWM to 3 digits, then add " PWM"
    else
        printf -v fan1_pwm_display "%${PWM_WIDTH}s" "N/A" # Pad N/A to full width
    fi

    # Format Fan 2 PWM
    fan2_pwm_display="N/A"
    if [[ -n "$fan2_pwm_raw" ]]; then
        printf -v fan2_pwm_display "%3d PWM" "$fan2_pwm_raw" # Pad PWM to 3 digits, then add " PWM"
    else
        printf -v fan2_pwm_display "%${PWM_WIDTH}s" "N/A" # Pad N/A to full width
    fi

    # Print with timestamp, and updated labels with fixed column widths
    printf "[%s] CPU Temp: %2d°C | CPU Fan: %s (%s) | GPU Fan: %s (%s)\n" \
           "$(date +%H:%M:%S)" \
           "$cpu_temp_c" \
           "$fan1_rpm_display" \
           "$fan1_pwm_display" \
           "$fan2_rpm_display" \
           "$fan2_pwm_display"

    # Wait for 2 seconds before the next reading
    sleep 2
done
```
### To check
```bash
systemctl status fancontrol.service
```
```
  ● fancontrol.service - Start fan control, if configured
     Loaded: loaded (/usr/lib/systemd/system/fancontrol.service; enabled; preset: disabled)
    Drop-In: /etc/systemd/system/fancontrol.service.d
             └─10-bios-override.conf
             /usr/lib/systemd/system/service.d
             └─10-timeout-abort.conf
     Active: active (running) since Tue 2025-07-29 11:48:31 BST; 1h 8min ago
 Invocation: 3c5f6c564e434c3bb956ed86685eb911
   Main PID: 2320 (fancontrol)
      Tasks: 2 (limit: 4915)
     Memory: 1.4M (peak: 7.2M)
        CPU: 6.769s
     CGroup: /system.slice/fancontrol.service
             ├─2320 /usr/bin/bash /usr/sbin/fancontrol
             └─7995 sleep 10

Jul 29 11:48:31 fedora fancontrol[2320]:   Controls hwmon7/fan1_input
Jul 29 11:48:31 fedora fancontrol[2320]:   MINTEMP=20
Jul 29 11:48:31 fedora fancontrol[2320]:   MAXTEMP=80
Jul 29 11:48:31 fedora fancontrol[2320]:   MINSTART=80
Jul 29 11:48:31 fedora fancontrol[2320]:   MINSTOP=80
Jul 29 11:48:31 fedora fancontrol[2320]:   MINPWM=0
Jul 29 11:48:31 fedora fancontrol[2320]:   MAXPWM=255
Jul 29 11:48:31 fedora fancontrol[2320]:   AVERAGE=6
Jul 29 11:48:31 fedora fancontrol[2320]: Enabling PWM on fans...
Jul 29 11:48:31 fedora fancontrol[2320]: Starting automatic fan control...
  ```
```bash
systemctl status dell-bios-fan-control.service
```
```
● dell-bios-fan-control.service - Disable Dell BIOS fan arbitration
     Loaded: loaded (/etc/systemd/system/dell-bios-fan-control.service; static)
    Drop-In: /usr/lib/systemd/system/service.d
             └─10-timeout-abort.conf
     Active: active (exited) since Mon 2025-07-28 23:36:25 BST; 13h ago
 Invocation: b3cd8065ed794db3a47cd541ca26e21a
    Process: 653 ExecStart=/usr/bin/dell-bios-fan-control 0 (code=exited, status=0/SUCCESS)
   Main PID: 653 (code=exited, status=0/SUCCESS)
   Mem peak: 1.1M
        CPU: 4ms

Jul 28 23:36:25 fedora dell-bios-fan-control[653]: BIOS CONTROL DISABLED
```
