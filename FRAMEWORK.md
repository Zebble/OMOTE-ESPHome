# Remote control framework

The configuration is split so that the parts describing the hardware can be
replaced when the framework is updated, without touching your own setup.

```
omote.yaml          entry point: device name, network, and which files to load
core/               the framework - replace on update, do not edit
  hardware.yaml     the OMOTE rev5 board: display, touch, keypad, IMU, IR, power
  ir.yaml           the IR engine, the actions Home Assistant calls, and learning
user/               yours - the framework never overwrites these
  activities.yaml   what the remote can be used for, and the screen for each
  keymap.yaml       what the physical buttons do
```

## Home Assistant

Home Assistant is the main way to control things, and there are three routes:

**Every button is an entity.** All 24 keys are published as binary sensors, so a
Home Assistant automation triggered by a button press can do anything Home
Assistant can - control network devices, run scripts, set scenes. Nothing is
rebuilt when you change your mind.

**The activity is a select entity.** Home Assistant can read it and set it, so an
activity can be started from a dashboard, a voice command or an automation, and
the remote follows. Changing activity also fires an `esphome.omote_activity_changed`
event.

**The remote is an IR blaster Home Assistant can drive.** Rather than compiling
codes into the firmware, Home Assistant passes the code to send:

```yaml
action: esphome.omote_send_pronto
data:
  code: "0000 006D 0022 0002 015B 00AD ..."
```

There are `send_nec` and `send_sony` actions too. Because the code is data,
devices can be added without rebuilding the firmware - keep the codes in Home
Assistant and the remote stays generic.

## Direct control

Sending IR through Home Assistant costs a round trip and stops working when the
network is down. For buttons where that matters - volume, usually - map the code
directly to the button in `user/keymap.yaml`, and it is sent from the device.

Network controlled devices are handled by Home Assistant, since it already knows
how to talk to them.

## Learning IR codes

Home Assistant has no general purpose IR learning; what exists is tied to
particular hardware (the Broadlink integration), or is a database of codes
(SmartIR). The OMOTE has its own IR receiver, so it can learn codes itself:

1. Turn on the **IR Learning** switch. This powers the receiver.
2. Point the original remote at the OMOTE and press a button.
3. The code appears in the **Last IR Code** sensor, in Pronto format, and in the
   log in every protocol the receiver could decode.
4. Store it in Home Assistant and send it back with `send_pronto`, or paste it
   into `user/keymap.yaml` for a directly mapped button.
5. Turn the switch off when finished, so the receiver is not left powered.

Pronto is worth preferring: it is what IR databases and learning tools produce,
so codes can be moved between them and the remote.

## Adding an activity

1. Add the name to the `options` of the `activity` select in `user/activities.yaml`.
2. Add a page for it in the same file.
3. Add a branch to the page router in the select's `on_value`.
