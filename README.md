# hwc - Hardware Control for IPMI

## Overview

This is a wrapper for using "ipmitool" commands that will work on many servers that support
IPMI with the "lanplus" interface per the IPMI v2.0 specification.  This is implemented as
a simple bash function with command completion.

## Requirements

Install the "ipmitool" from your Linux distribution.  You'll also need username/password
credentials to your network-enabled IPMI devices.

## Usage

Save a copy the "hwc-rc" file and then source it in the shell to define the "hwc" function.
Running "hwc" by itself will present some basic usage help.  If you've used the "ipmitool"
commands you will recognize many of the supported commands:
```
$ hwc
Usage: hwc <command> <target>
Supported commands: env, on (power_on), off (power_off), status, mc_info, fru, chassis, sensor,
                    session, sel, sel_elist (elist), sdr, sol (sol_start), sol_stop, pxe, shell
```

The first time the "hwc" function is run it will prompt for username/password credentials and
store them as environment variables "IPMIUSER" and "IPMIPASS".  This means you aren't prompted
for credentials for subsequent "hwc" commands.  This is intended for use where most or all of
the IPMI devices have common credentials.  If you need to switch credentials for a new connection
you can run "hwc-reset" to clear the stored variables.

The target server is set as the variable "IPMIADDR" so it can be omitted when running more than
one command against the same target.

### Supported commands
- **env** Display the environment variables to be used with ipmitool
- **on (power_on)** Turn on power at the target
- **off (power_off)** Turn off power at the target
- **status** Display power status of the target
- **mc_info** Display management controller status
- **fru** Display built-in FRU and scan for FRU locators
- **chassis** Display chassis status
- **sensor** Display platform sensor data (temperature, fan speed)
- **session** Display details of the IPMI connection
- **sel** Display system event log (SEL) information
- **sel_elist (elist)** Display SEL events
- **sdr** Display sensor data repository (SDR) entries and readings
- **sol (sol_start)** Configure and connect IPMIv2.0 Serial-over-LAN
- **sol_stop** Disconnect an IPMI Serial-over-LAN connection
- **pxe** Override the next system boot to use PXE
- **shell** Launch interactive IPMI shell

## Examples

Check the power status of the system with IPMI address "192.168.1.5".  Since this is the first run
of "hwc" it prompts for the credentials:
```
$ hwc status 192.168.1.5
Enter the IPMI username: admin
Enter the IPMI user password: ********
Chassis Power is on
```

At this point the IPMI credentials and target address are saved in the shell.  To see the IPMI
shell variables you can run "hwc env" which produces output like this:
```
IPMIADDR=192.168.1.5
IPMIUSER=admin
IPMIPASS is set in the shell
```

To run another command on the same target we can now just run "hwc <command>"
```
$ hwc chassis
System Power         : on
Power Overload       : false
Power Interlock      : inactive
Main Power Fault     : false
Power Control Fault  : false
Power Restore Policy : always-off
Last Power Event     : 
Chassis Intrusion    : inactive
Front-Panel Lockout  : inactive
Drive Fault          : false
Cooling/Fan Fault    : false
```

You can specify a new target by adding the hostname or IP address after the command.  This
will also update the IPMIADDR variable for subsequent calls to "hwc".
```
$ hwc status 192.168.1.6
Chassis Power is on
```

