# Arduino Stimulation Codes for Neuromodulation: Technology at the Neural Interface Article

This repository contains the Arduino code used in the **JNmod article**.  
Both *attractor* and *probing* protocols implement the same logic, with variation only in timing parameters and channel configurations.

## Code Organization

The project is divided into two main files:

1. **Main Arduino Sketch**  
   - Implements the *logic of the stimulator*.  
   - Handles timing, pulse generation, synchronization, and external ON/OFF switching.  

2. **Header File: `Stimulator_header.h`**  
   - Defines the hardware configuration (pins, channels, synchronization).  
   - Contains global variables, interval arrays, and stimulation parameters.  
   - Provides optimized helper functions for direct I/O access (faster than `digitalWrite()`).

## Features

The code implements a **precise electrical stimulator code for arduino coupled with a standalone stimulator** with the following characteristics:

- Biphasic, charge-balanced pulses.  
- Multiple stimulation patterns setted to different arduino pins with its respective highs and lows:  
  - **Periodic**
  - **Non-periodic**
  - **Fixed low-frequency stimulation**  
- External trigger input for real-time ON/OFF control.  
- Optimized I/O for accurate microsecond-level timing.

## Arduino variables

The code exposes several variables that are used to trigger interrupts, including the **interrupt pin** (digital pin 21 by default) and the **synchronization pin** (digital pin 20 by default), variables to set the stimulation parameters such as pulse widths and intervals and which channels are going to be used for low and high parts of the stimulation pulse. These are described below should the need to modify arises.

- **`interruptPin`** Digital input pin used to externally start/stop stimulation.
  - Configured with `attachInterrupt()` to detect rising/falling edges.
  - Allows real-time ON/OFF control via an external switch, in the case of the publication a manual button attached to the interrupt pin.

- **`synchronizationPin`**  Digital output pin used to synchronize the stimulator with external equipment, in the case of the publication, an Open Ephys system.  
  - Can output a marker pulse aligned with stimulation events, which is shown in the Open Ephys system timeline.

- **`square_length_positive`**  Duration (in microseconds) of the **positive phase** of a biphasic pulse.  
  - Determines how long the pulse is driven high.

- **`square_length_negative`**  Duration (in microseconds) of the **negative phase** of a biphasic pulse.  
  - Determines how long the pulse is driven low.

- **`square_interval`**  Inter-phase delay (in microseconds) between the positive and negative phases of the pulse.  
  - Controls the gap between pulses.

- **`bytePosPinChannel1`**  Direct reference to the microcontroller pin assigned as the **positive electrode output** for Channel 1.  
  - Optimized with direct port access (`digital_write_direct`) for fast switching.

- **`byteNegPinChannel1`**  Direct reference to the microcontroller pin assigned as the **negative electrode output** for Channel 1.  
  - Completes the biphasic stimulation pair with `bytePosPinChannel1`.

---

## How these files should be used

This is a template guide on how to use the provided files:

1. **Prepare the files**  
    - Configure your stimulation parameters in the header file:
        - All variables listed above should be customized as needed, however the default values are set as per the publication.
        - Attractor and Probing protocols are both configured in the header file in the `//DATA PER EACH CHANNEL` section, only the channels differ.
   - Place both files in the same Arduino project folder:  
     - `Stimulator.ino` → Main sketch (contains the stimulation logic)  
     - `STIMULADOR.h` → Header file (hardware definitions, timing arrays, helper functions)  
   - Arduino IDE automatically links `.ino` with `.h` files in the same folder.

2. **Open the project**  
   - Launch the Arduino IDE.  
   - Open `Stimulator.ino`.

3. **Select your board and port**  
   - Go to **Tools → Board** and choose your Arduino model (e.g., Arduino Mega 2560).  
   - Go to **Tools → Port** and select the correct COM/USB port.

4. **Upload the code**  
   - Click the **Upload** button (right arrow in the IDE).  
   - The sketch will compile and transfer to the board.

5. **Wire the stimulator hardware**  
   - Connect the defined output pins (`bytePosPinChannelX`, `byteNegPinChannelX`) to your stimulation circuitry.  
   - Connect the `interruptPin` to a switch or TTL signal to externally start/stop stimulation.  
   - Optionally, use the `synchronizationPin` to send timing markers to external devices (e.g., data acquisition).

- The stimulation can be triggered using the assigned **interrupt pin** (digital pin 21 by default).
