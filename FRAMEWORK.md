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

**The remote is an infrared adapter.** It publishes `infrared` entities, which the
Home Assistant [Infrared integration](https://www.home-assistant.io/integrations/infrared)
(2026.4 and later) drives. Home Assistant learns the codes, keeps them, and sends
them through the remote, so devices can be added without rebuilding the firmware.

There are also `send_pronto` and `send_nec` actions, for sending a code you
already know from a script or automation.

## Direct control

Sending IR through Home Assistant costs a round trip and stops working when the
network is down. For buttons where that matters - volume, usually - map the code
directly to the button in `user/keymap.yaml`, and it is sent from the device.

Network controlled devices are handled by Home Assistant, since it already knows
how to talk to them.

## Learning IR codes

Home Assistant does the learning, through the Infrared integration and the
remote's **IR Receiver** entity:

1. Turn on the **IR Receiver Power** switch. The receiver is unpowered by default
   so it does not drain the battery.
2. Start learning in Home Assistant against the OMOTE's IR Receiver entity.
3. Point the original remote at the OMOTE and press the button to learn.
4. Home Assistant stores the code and can send it back through the IR
   Transmitter entity.
5. Turn the receiver power off when finished.

The codes stay in Home Assistant, so nothing has to be rebuilt to add a device.
If you would rather keep a code on the device - for a button that has to be fast
or work offline - put it in `user/keymap.yaml` instead.

## Adding an activity

1. Add the name to the `options` of the `activity` select in `user/activities.yaml`.
2. Add a page for it in the same file.
3. Add a branch to the page router in the select's `on_value`.

## What a button does

A button says what it does rather than which device it is for: the volume key
sends `volume_up`, not "turn up the amplifier". The `send_command` script in
`user/activities.yaml` works out where it should go, so the same button can reach
different devices as the activity changes, and some buttons can ignore the
activity entirely - volume and mute belong to the amplifier whether the
television or the streamer is playing.

The command then goes to Home Assistant as an `esphome.omote_command` event
carrying the command, the device and the activity, and one automation there
decides how to deliver it: as infrared through this remote, or over the network.
Adding a device is a change in Home Assistant rather than a rebuild here.

```yaml
triggers:
  - trigger: event
    event_type: esphome.omote_command
actions:
  - choose:
      - conditions: "{{ trigger.event.data.device == 'amplifier' }}"
        sequence:
          - action: esphome.omote_send_pronto
            data:
              code: "{{ amplifier_codes[trigger.event.data.command] }}"
```

A button that has to be fast, or keep working when the network is down, can skip
all of that and send infrared straight from the device - see the comment at the
top of `user/keymap.yaml`.

## The screen

`core/ui.yaml` owns the display, the touchscreen, and a bar drawn on top of every
page showing the current activity, the battery (with a `+` while charging), and
the last button pressed. The last-button readout is there to make testing easy:
press a button and read it off the screen, rather than connecting to the log.

Pages are yours, in `user/activities.yaml`.

## To do

**Repeat while a button is held.** Volume, channel and the arrow keys should be
able to repeat while held, rather than sending one command per press, and stop on
release. It should be per button, since a button like power must never repeat.
Physical buttons report press and release separately, so the repeat can be driven
from those; on-screen buttons have their own repeat setting.
