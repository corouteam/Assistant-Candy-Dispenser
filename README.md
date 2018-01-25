# Assistant Candy Dispenser
## Ok Google, give me Candy!

[![](https://github.com/2coffees1team/Assistant-Candy-Dispenser/blob/master/video.png)](https://www.youtube.com/watch?v=V4A27NxeOGw)

--
## What you need
 * Rasperry PI board (Pi 3/Pi 2/Pi Zero);
 * USB Webcam;
 * A Candy Dispenser;
 * Servo
 * *Optionally: some candies :)*

## How to Install
Assistant Candy Dispenser is based on [GassistPi](https://github.com/shivasiddharth/GassistPi), a project that enables Google Assistant and other additional features on **Raspberry PI Boards**. We decided to use it because it's faster to configure and provides a good starting point to hack on the Assistant Platform using the official [Assistant SDK](https://developers.google.com/assistant/sdk/).


Before starting, please configure a working instance of GassistPi following the instructions [here](https://github.com/shivasiddharth/GassistPi#first-step--clone-the-project-on-to-pi). We tested this on **GassistPI v2.0** and a **Raspberry Pi 3**.

## Customize trigger invocation
Once you've configured [GassistPi](https://github.com/shivasiddharth/GassistPi#first-step--clone-the-project-on-to-pi), you need to make some changes to its scripts. By default, GassistPi already supports a few commands that will enable PIN on Raspberry to control a led or a servo. For example, if you say:

```
Ok Google, trigger servo 90
```

Assistant will rotate the servo connected to your board by 90 degrees (default PIN for servo control is **GPIO 27**, but you can change this as well in `actions.py`.

We're going to change the invocation word from *trigger* to *give me* (the complete phrase will be *"Give me a candy"*.

To do so, open the `main.py` script and add a new invocation on the event receiver:

```python
 for event in events:
            process_event(event, assistant.device_id)
            usrcmd=event.args
            if 'give me'.lower() in str(usrcmd).lower():
                assistant.stop_conversation()
                Action(str(usrcmd).lower())
```

# Add a new action "give me a candy"
Now that you have the custom trigger "give me", we need to create a new action as well that will control our servo.
To make this happen, open the `actions.py` file.

You'll notice there's a method already written to control the servo. In fact, with `SetAngle(angle)` you can automatically control a servo connected to **GPIO 27**. If you need to change the pin, just edit the pin declaration at the top of the script:

```python
# Servo pin declaration
GPIO.setup(27, GPIO.OUT)
pwm=GPIO.PWM(27, 50)
pwm.start(0)
```

Now, you need to declare your custom action. In the *GPIO Device Control* section, add a new condition:

```
def Action(phrase):
	if 'a candy' in phrase:
            # Do something
```

At this point, Assistant will automatically trigger your function when it recognizes *Give me a Candy* as voice input.

In our case we needed the opening angle to be at least 135 degrees to release the candies. After that, we wait 1 second for the candies to go out and then we close the hatch to stop the candies flow.

We can also add something the Assistant will say using the `say()` API.

Our code implementation is:

```python
def Action(phrase):
    if 'a candy' in phrase:
            say("Here is your candy!!")
            SetAngle(135)
            time.sleep(1)
            SetAngle(0)
```


## Wiring
Connect servo's three wires to 5V, Ground and GPIO27.

![](https://github.com/2coffees1team/Assistant-Candy-Dispenser/blob/master/assistant-candy-dispenser.png)

## Example
We've provided our full `main.py` and `action.py` files for reference.

Sweet hacking!


