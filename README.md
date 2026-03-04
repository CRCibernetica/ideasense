# IdeaSense Library

The `IdeaSense` library provides a streamlined, unified Python interface for the IdeaSense hardware board. It automatically initializes the I2C bus and abstracts the complexity of interacting with the onboard sensors, buttons, and LED matrix into a single, easy-to-use Python class.

## Supported Hardware Features

The `IdeaSense` class acts as a wrapper for the following hardware components:

* **LED Matrix**: A 5x5 LED matrix powered by the HT16K33 chip. The library includes a custom wrapper that corrects hardware orientation and coordinate offsets so you can interact with it using a standard `(x, y)` grid ranging from 0 to 4.
* **Temperature & Humidity**: Reads ambient data using the SHT31D sensor.
* **Light Sensing**: Reads visible plus infrared (IR) light using the LTR303 sensor.
* **Motion Tracking**: Captures acceleration and gyroscope data using the LSM6DS3TRC 6-axis IMU. * **User Inputs**: Reads the state of 3 onboard buttons mapped via the matrix I2C device. The library supports both continuous state reading and momentary "edge-triggered" presses to prevent ghost inputs.

## Dependencies

To use this library, ensure you have the appropriate Adafruit CircuitPython/Blinka libraries installed for the underlying hardware:

* `adafruit_sht31d`
* `adafruit_ltr329_ltr303`
* `adafruit_lsm6ds`
* `adafruit_ht16k33`

## Quick Start & API Reference

### Initialization

To start interacting with the board, simply instantiate the `IdeaSense` class. This automatically sets up the I2C connection.

```python
from ideasense import IdeaSense

idea = IdeaSense()

```

### Display / Matrix Operations

The matrix is accessible via the `idea.matrix` property. It accepts coordinates between `0` and `4`.

```python
# Clear the screen
idea.matrix.fill(0) 

# Turn on the top-left pixel
idea.matrix[0, 0] = 1 

# Turn on the bottom-right pixel
idea.matrix[4, 4] = 1 

# Adjust brightness (0.0 to 1.0)
idea.matrix.brightness(0.5)

# Push your changes to the physical display
idea.matrix.show()

```

### Reading Sensors

Sensor properties return raw data directly from the hardware.

```python
# Environment
temperature = idea.temp      # Returns temperature in Celsius
humidity = idea.humid        # Returns relative humidity percentage
light_level = idea.light     # Returns visible + IR light levels

# Motion
accel_x, accel_y, accel_z = idea.accel  # Returns acceleration tuple
gyro_x, gyro_y, gyro_z = idea.gyro      # Returns gyroscope tuple

```

### Button Inputs

You can read the 3 onboard buttons in two different ways depending on your application:

1. **Continuous State (`idea.buttons`)**: Returns a list of three booleans `[Button_A, Button_B, Button_C]` representing whether the buttons are currently being held down.
2. **Edge-Triggered (`idea.pressed`)**: Returns `True` only on the exact moment the button is pushed down. It compares the current state to the last known state to prevent repeating inputs.

```python
import time
from ideasense import IdeaSense

idea = IdeaSense()

while True:
    # Use .pressed for actions that should only happen once per push
    buttons = idea.pressed 
    
    if buttons[0]:
        print("Button A was just pressed!")
    elif buttons[1]:
        print("Button B was just pressed!")
    elif buttons[2]:
        print("Button C was just pressed!")
        
    time.sleep(0.05)

```

## Included Examples

This repository includes several example scripts to help you get started:

* `ideasense_simpletest.py`: A basic diagnostic script that tests the matrix, prints all sensor readings, and detects button presses.
* `text_demo.py` & `font5x5.py`: Demonstrates how to use an external font dictionary to scroll alphanumeric characters across the 5x5 matrix smoothly.
* `weather_station.py`: An interactive application that maps buttons to scroll the current temperature or humidity across the display.
* `sand.py`: A fun physics toy that uses the accelerometer to simulate grains of sand falling across the 5x5 matrix based on how you tilt the board.
