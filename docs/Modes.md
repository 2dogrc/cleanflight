# Modes

Cleanflight has various modes that can be toggled on or off.  Modes can be enabled/disabled by stick positions,
auxillary receiver channels and other events such as failsafe detection.

| MSP ID  | CLI ID | Short Name | Function                                                             |
| ------- | ------ | ---------- | -------------------------------------------------------------------- |
| 0       | 0      | ARM        | Enables motors and flight stabilisation                              |
| 1       | 1      | ANGLE      | Legacy auto-level flight mode                                        |
| 2       | 2      | HORIZON    | Auto-level flight mode                                               |
| 3       | 3      | BARO       | Altitude hold mode (Requires barometer sensor)                       |
| 5       | 4      | MAG        | Heading lock                                                         |
| 6       | 5      | HEADFREE   | Head Free - When enabled yaw has no effect on pitch/roll inputs      |
| 7       | 6      | HEADADJ    | Heading Adjust - Sets a new yaw origin for HEADFREE mode             |
| 8       | 7      | CAMSTAB    | Camera Stabilisation                                                 |
| 9       | 8      | CAMTRIG    |                                                                      |
| 10      | 9      | GPSHOME    | Autonomous flight to HOME position                                   |
| 11      | 10     | GPSHOLD    | Maintain the same longitude/lattitude                                |
| 12      | 11     | PASSTHRU   | Pass roll, yaw, and pitch directly from rx to servos in airplane mix |
| 13      | 12     | BEEPERON   | Enable beeping - useful for locating a crashed aircraft              |
| 14      | 13     | LEDMAX     |                                                                      |
| 15      | 14     | LEDLOW     |                                                                      |
| 16      | 15     | LLIGHTS    |                                                                      |
| 17      | 16     | CALIB      |                                                                      |
| 18      | 17     | GOV        |                                                                      |
| 19      | 18     | OSD        | Enable/Disable On-Screen-Display (OSD)                               |
| 20      | 19     | TELEMETRY  | Enable telemetry via switch                                          |
| 22      | 21     | SONAR      | Altitude hold mode (sonar sensor only)                               |
| 26      | 25     | BLACKBOX   | Enable BlackBox logging                                              |
| 27      | 26     | GTUNE      | G-Tune - auto tuning of Pitch/Roll/Yaw P values                      |
| 28      | 27     | AIRMODE    | Alternative mixer and additional PID logic for more stable copter    |

## Mode details

### Headfree

In this mode, the "head" of the multicopter is always pointing to the same direction as when the feature was activated. This means that when the multicopter rotates around the Z axis (yaw), the controls will always respond according the same "head" direction.

With this mode it is easier to control the multicopter, even fly it with the physical head towards you since the controls always respond the same. This is a friendly mode to new users of multicopters and can prevent losing the control when you don't know the head direction. 

### GPS Return To Home

WORK-IN-PROGRESS.  This mode is not reliable yet, please share your experiences with the developers.

In this mode the aircraft attempts to return to the GPS position recorded when the aircraft was armed.

This mode should be enabled in conjunction with Angle or Horizon modes and an Altitude hold mode.

Requires a 3D GPS fix and minimum of 5 satellites in view.

### GPS Position Hold

WORK-IN-PROGRESS.  This mode is not reliable yet, please share your experiences with the developers.

In this mode the aircraft attempts to stay at the same GPS position, as recorded when the mode is enabled.

Disabling and re-enabling the mode will reset the GPS hold position.

This mode should be enabled in conjunction with Angle or Horizon modes and an Altitude hold mode.

Requires a 3D GPS fix and minimum of 5 satellites in view.

## Airmode

In the standard mixer / mode, when the roll, pitch and yaw gets calculated and saturates a motor, all motors
will be reduced equally. When motor goes below minimum it gets clipped off.
Say you had your throttle just above minimum and tried to pull a quick roll - since two motors can't go
any lower, you essentially get half the power (half of your PID gain).
If your inputs would asked for more than 100% difference between the high and low motors, the low motors
would get clipped, breaking the symmetry of the motor balance by unevenly reducing the gain.
Airmode will enable full PID correction during zero throttle and give you ability for nice zero throttle
gliding and actobatics. But also the cornering / turns will be much tighter now as there is always maximum
possible correction performed. Airmode can also be enabled to work at all times by always putting it on the
same switch like your arm switch or you can enable/disable it in air. Additional things and benefits: Airmode
will additionally fully enable Iterm at zero throttle. Note that there is still some protection on the ground
when throttle zeroed (below min_check) and roll/pitch sticks centered. This is a basic protection to limit
motors spooling up on the ground. Also the Iterm will be reset above 70% of stick input in acro mode to prevent
quick Iterm windups during finishes of rolls and flips, which will provide much cleaner and more natural stops
of flips and rolls what again opens the ability to have higher I gains for some.
Note that AIRMODE will also overrule motor stop function! It will basically also act as an idle up switch.
Things to know about Airmode: There is an optional cli parameter "airmode_saturation_limit" what is a hard
limit in percentage to where airmode will still try to provide the maximum possible correction. 0 means no limit,
but that also means maximum possible motor correction during crashes and during equipment failure. In worse case
that can cause effects like best called "tasmanian devil effect" or slight spazzing out. Default value of 50
has a limit of 50% saturation. After that it will assume crash occured or equipment failed what will again remove
airmode seeking for the best possible protection and act as a normal mode. It should be fine in most cases.



## Auxillary Configuration

Spare auxillary receiver channels can be used to enable/disable modes.  Some modes can only be enabled this way.

Configure your transmitter so that switches or dials (potentiometers) send channel data on channels 5 and upwards (the first 4 channels are usually occupied by the throttle, aileron, rudder, and elevator channels).

_e.g. You can configure a 3 position switch to send 1000 when the switch is low, 1500 when the switch is in the middle and 2000 when the switch is high._

Configure your tx/rx channel limits to use values between 1000 and 2000.  The range used by mode ranges is fixed to 900 to 2100.

When a channel is within a specifed range the corresponding mode is enabled.

Use the GUI configuration tool to allow easy configuration when channel.

### CLI 

There is a CLI command, `aux` that allows auxillary configuration.  It takes 5 arguments as follows:

* AUD range slot number (0 - 39)
* mode id (see mode list above)
* AUX channel index (AUX1 = 0, AUX2 = 1,... etc)
* low position, from 900 to 2100. Should be a multiple of 25.
* high position, from 900 to 2100. Should be a multiple of 25.

If the low and high position are the same then the values are ignored.

e.g.

Configure AUX range slot 0 to enable ARM when AUX1 is withing 1700 and 2100.
 
```
aux 0 0 0 1700 2100
```

You can display the AUX configuration by using the `aux` command with no arguments.

