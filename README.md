# Pico RGH 1.2 v2 CPLD Emulator for Xbox 360

This repository contains the source code for a Raspberry Pi Pico-based firmware that emulates a CPLD-based RGH 1.2 glitch chip for Phat Xbox 360 consoles, with a focus on the **Jasper/Tonasket** motherboard revision.

The goal of this project is to provide an open-source, accessible alternative to traditional glitch chips, allowing a standard Raspberry Pi Pico to perform the Reset Glitch Hack 1.2.

## ⚠️ Disclaimer

This is an **experimental project**. It requires advanced soldering skills and a thorough understanding of the Xbox 360 modification process. There is a real risk of permanently damaging your Xbox 360 console and/or Raspberry Pi Pico.

While this firmware aims to replicate the behavior of a CPLD, traditional, purpose-built glitch chips (like those from Xilinx or Lattice) are generally considered more reliable and are recommended for most users.

**Proceed at your own risk. The author is not responsible for any damage caused by the use of this code.**

## Features

*   **RGH 1.2 Logic**: Implements the classic Reset Glitch Hack 1.2 method.
*   **External Clock Synchronization**: Uses the Xbox 360's own clock signal for precise, stable timing, just like a CPLD.
*   **PIO-Powered**: Leverages the Raspberry Pi Pico's Programmable I/O (PIO) to handle all high-speed, real-time glitching operations.
*   **Tunable Parameters**: All critical timing values (`POST count`, `offset`, `width`) are easily configurable in the source code, allowing for fine-tuning to match your specific console.
*   **Open Source**: Freely available to use, study, and improve.

## Wiring Diagram

Precise and clean wiring is critical for success. Use short wires (AWG 28-30 recommended) for all connections.

| Raspberry Pi Pico Pin | Function         | Xbox 360 Jasper Solder Point      | Notes                                                              |
| --------------------- | ---------------- | --------------------------------- | ------------------------------------------------------------------ |
| **GP0**               | **CLK_IN**       | J2C3 Pin 1 (Standby Clock)        | **CRITICAL**: Must be a stable clock. 48MHz is ideal.              |
| **GP1**               | **POST_IN**      | FT2R2                             | The POST_OUT1 signal from the motherboard.                         |
| **GP2**               | **RESET_IN**     | J2C1 Pin 4                        | Monitors the console's reset line to arm/disarm the glitcher.      |
| **GP3**               | **GLITCH_OUT**   | R4B24 (CPU_RST)                   | The glitch pulse output. A 100 Ohm resistor on this line is advised. |
| **3V3 (OUT)** (Pin 36)    | **3.3V Power**   | Any 3.3V standby point on the MB  | Powers the Pico.                                                   |
| **GND** (Any GND Pin)     | **Ground**       | Any ground point on the MB        | Common ground is essential. Use a screw hole.                      |

*Optional but Recommended:* A 220pF to 270pF capacitor between **GLITCH_OUT (GP3)** and **GND** can help shape the glitch pulse for better stability.

## Compilation

You must have the [Raspberry Pi Pico C/C++ SDK](https://github.com/raspberrypi/pico-sdk) configured on your system.

1.  **Clone the repository:**
    ```bash
    git clone https://github.com/Kalmuna/Pico-RGH-1.2-v2-Jasper.git
    cd PicoRGH12
    ```

2.  **Prepare the build:**
    ```bash
    mkdir build
    cd build
    cmake ..
    ```

3.  **Compile the code:**
    ```bash
    make
    ```

This will generate a `pico_rgh12.uf2` file inside the `build` directory. This is the firmware you will flash to your Pico.

## Installation and Tuning

### 1. Flashing the Pico

1.  Press and hold the `BOOTSEL` button on your Raspberry Pi Pico.
2.  Connect the Pico to your computer via USB. It will mount as a mass storage device.
3.  Drag and drop the `pico_rgh12.uf2` file onto the Pico. It will automatically reboot and run the firmware.

### 2. Tuning for Your Console

**This is the most important step.** No two consoles glitch exactly the same. You will likely need to adjust the timing parameters to get a successful boot.

1.  Open the `pico_rgh12.c` file.
2.  Locate the timing parameter definitions at the top of the file:
    ```c
    #define POST_COUNT_TARGET 10
    #define GLITCH_OFFSET 1000
    #define GLITCH_WIDTH 100
    ```
3.  **Tuning Strategy:**
    *   Start with the default values.
    *   If the console does not boot into XELL (blue screen), try adjusting the `GLITCH_OFFSET` first. Increase or decrease it in steps of 50-100.
    *   If you get occasional boots but they are not stable, try adjusting the `GLITCH_WIDTH`. Increase or decrease it in steps of 10-20.
    *   The `POST_COUNT_TARGET` is generally stable for a given motherboard type, but can be adjusted if other tuning fails.
4.  After each change, you must **recompile** the code (`make` in the build directory) and **re-flash** the new `.uf2` file to the Pico.
5.  Repeat the process until you achieve fast and reliable boots into XELL.

## Credits and Acknowledgements

This project stands on the shoulders of giants. It would not be possible without the foundational work of:

*   **GliGli, Tiros, and the original RGH pioneers** for discovering and developing the Reset Glitch Hack.
*   **Octal450** for the `RGH1.2-V2-Phat` VHDL implementation, which served as a key reference for the CPLD logic.
*   The **Free60 project** for their extensive documentation of the Xbox 360 hardware.
*   The entire Xbox 360 homebrew and modding community.
