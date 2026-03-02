# Building a Custom 3‑Key Keyboard for Alt+Arrow Shortcuts

This guide will walk you through creating a simple, dedicated three‑key USB keyboard that sends the key combinations **Alt + Left Arrow**, **Alt + Space**, and **Alt + Right Arrow**. The design uses an Arduino Pro Micro, direct wiring, and a short Arduino sketch – perfect for a weekend project with oversized 3D‑printed keys.

---

## 📦 Electronics Parts List

| Part | Quantity | Purpose | Example Link |
|------|----------|---------|--------------|
| **Arduino Pro Micro (ATmega32U4)** | 1 | Acts as the USB keyboard controller. Its built‑in USB HID support makes it plug‑and‑play. | [Adafruit Pro Micro](https://www.adafruit.com/product/2488) · [SparkFun Pro Micro](https://www.sparkfun.com/products/12640) |
| **Mechanical Key Switches** (Cherry MX compatible) | 3 | The actual keys. Choose any type – linear, tactile, or clicky. | [Cherry MX Switches (Amazon)](https://www.amazon.com/s?k=cherry+mx+switches) · [Kailh Box Switches (NovelKeys)](https://novelkeys.com/collections/switches) |
| **1N4148 Diodes** | 3 | Prevents ghosting and ensures each key press is registered correctly. | [1N4148 Diodes (Amazon)](https://www.amazon.com/s?k=1n4148+diode) · [Adafruit 1N4148](https://www.adafruit.com/product/2038) |
| **Hook‑up Wire** (22‑26 AWG) | – | For making all the connections. | [Stranded Wire (Amazon)](https://www.amazon.com/s?k=hookup+wire) |
| **Soldering Iron & Solder** | – | Required to assemble the electronics. | [Soldering Iron Kit (Amazon)](https://www.amazon.com/s?k=soldering+iron+kit) |
| **3D Printer** (optional) | – | To print the case and oversized keycaps. You can also design a simple enclosure yourself. | [Your own 3D printer or a service like Shapeways](https://www.shapeways.com/) |

> **Note:** The Arduino Pro Micro is specifically chosen because it uses the ATmega32U4 chip, which can emulate a keyboard without any extra drivers. Other Arduino boards (like the Uno) cannot do this natively.

---

## 🔌 Wiring Diagram

We will use a **direct wiring** method – each switch connects to one digital input pin and to a common ground. The diode is placed in series with the switch to ensure current flows only in the correct direction.

### Wiring Overview

| Arduino Pro Micro Pin | Connected To | Via Diode (orientation) | Also Connected To |
|------------------------|--------------|-------------------------|-------------------|
| Pin 8                  | Switch 1 (Alt+Left)  | Anode (non‑banded end) to Pin 8<br>Cathode (banded end) to switch | Common **GND** |
| Pin 9                  | Switch 2 (Alt+Space) | Anode to Pin 9<br>Cathode to switch                      | Common **GND** |
| Pin 10                 | Switch 3 (Alt+Right) | Anode to Pin 10<br>Cathode to switch                     | Common **GND** |
| Any **GND** pin        | Common ground wire   | Direct connection                                         | The other terminal of every switch |

### Step‑by‑Step Wiring

1. **Prepare the switches** – If your switches have two terminals, solder one wire to each terminal. (For PCB‑mount switches, you may need to bend the pins slightly.)

2. **Add the diode** – For each switch, solder the **cathode** (the end with the black band) to one of the switch’s terminals. The cathode is the side that lets current out. Then solder a wire from the diode’s **anode** (the end without the band) to the corresponding Arduino pin (8, 9, or 10).  
   *Why?* This orientation ensures that when the switch is pressed, current flows from the Arduino pin, through the diode, through the switch, and to ground.

3. **Connect to ground** – Solder a wire from the **other terminal** of each switch to a common ground point. Finally, connect that common ground to any **GND** pin on the Pro Micro (e.g., the pin marked GND).

4. **Double‑check** – Before soldering everything permanently, verify all connections with a multimeter in continuity mode. Make sure no solder bridges short adjacent pins.

> **Tip:** For a cleaner build, you can solder the diodes directly to the switch terminals and then use heat‑shrink tubing to insulate the exposed leads.

---

## 💻 The Code

The Arduino sketch uses the `Keyboard.h` library to send the desired key combinations. When a key is pressed (pin goes LOW because it is connected to ground), the code simulates holding down the Alt key together with the appropriate arrow or space key. When the key is released, all keys are released.

Upload this code to your Pro Micro using the Arduino IDE (select board “Arduino Micro” or “SparkFun Pro Micro”).

```cpp
#include <Keyboard.h>

// Define the pins for each switch
const int leftPin = 8;
const int spacePin = 9;
const int rightPin = 10;

void setup() {
  // Configure the switch pins as inputs with internal pull-up resistors.
  // The pin will read HIGH when the switch is open, and LOW when pressed (connected to GND).
  pinMode(leftPin, INPUT_PULLUP);
  pinMode(spacePin, INPUT_PULLUP);
  pinMode(rightPin, INPUT_PULLUP);

  // Initialize the USB HID keyboard library
  Keyboard.begin();
}

void loop() {
  // Check if the Left switch is pressed (pin reads LOW)
  if (digitalRead(leftPin) == LOW) {
    // Press and hold the Alt key and Left Arrow key
    Keyboard.press(KEY_LEFT_ALT);
    Keyboard.press(KEY_LEFT_ARROW);
    delay(50); // Small delay to ensure the computer registers the press
  }
  // Check if the Space switch is pressed
  else if (digitalRead(spacePin) == LOW) {
    Keyboard.press(KEY_LEFT_ALT);
    Keyboard.press(' '); // The space character
    delay(50);
  }
  // Check if the Right switch is pressed
  else if (digitalRead(rightPin) == LOW) {
    Keyboard.press(KEY_LEFT_ALT);
    Keyboard.press(KEY_RIGHT_ARROW);
    delay(50);
  }
  // If no keys are pressed, release all keys
  else {
    Keyboard.releaseAll();
  }
}
```

### How It Works
- `INPUT_PULLUP` enables the internal pull‑up resistor, so the pin reads `HIGH` when the switch is open. Pressing the switch connects the pin to ground, making it `LOW`.
- `Keyboard.press()` simulates pressing a key and holding it until `Keyboard.releaseAll()` is called.
- The `delay(50)` gives the computer time to recognise the key press; you can adjust it if needed.

---

## 🖨️ Final Assembly

1. **Design your 3D printed case** – Create a simple box with three switch cutouts and a compartment for the Pro Micro. Ensure the switch mounting holes match the dimensions of your chosen switches (typically 14mm x 14mm for Cherry MX). You can find ready‑made models on [Thingiverse](https://www.thingiverse.com/) or design your own.

2. **Mount the switches** – Insert the switches into the case. They may snap in or require a small amount of glue.

3. **Install the electronics** – Place the wired Pro Micro inside the case. Use a small piece of double‑sided tape or a hot‑glue gun to secure it.

4. **Close the case** – Make sure no wires are pinched, and close the case. If you printed a bottom plate, screw or snap it on.

5. **Test** – Plug the keyboard into any USB port on your computer. It should be instantly recognised as a keyboard. Press each key and verify that the correct shortcuts are sent (e.g., in a text editor or file explorer).

---

## ⚡ Quick Summary Table

| Switch | Arduino Pin | Action Sent              | Diode Orientation (Anode to Pin) |
|--------|-------------|--------------------------|-----------------------------------|
| Left   | 8           | Alt + Left Arrow         | Anode → Pin 8, Cathode → Switch  |
| Space  | 9           | Alt + Space              | Anode → Pin 9, Cathode → Switch  |
| Right  | 10          | Alt + Right Arrow        | Anode → Pin 10, Cathode → Switch |
| Ground | Any GND pin | Common ground for all switches | Direct connection            |

---

## 🔗 References & Further Reading

- [Arduino Keyboard Library Reference](https://www.arduino.cc/reference/en/language/functions/usb/keyboard/)
- [Arduino Pro Micro Pinout Diagram](https://learn.sparkfun.com/tutorials/pro-micro--fio-v3-hookup-guide/hardware-overview-pro-micro)
- [Using Diodes in Keyboard Matrices (Deskthority Wiki)](https://deskthority.net/wiki/Keyboard_matrix#Diodes)
- [How to Solder – Beginner’s Guide](https://learn.adafruit.com/adafruit-guide-excellent-soldering)

---

Originally created for https://github.com/Cfomodz/Open-edX-Accessibility-Reader but I am sure you will come up with even cooler ideas with your own!
