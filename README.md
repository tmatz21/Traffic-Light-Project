Traffic Light IoT Projects
A collection of Internet of Things (IoT) coursework projects built around the PIC16F877A microcontroller, programmed in MikroC PRO for PIC and simulated in Proteus.
Projects
1. Graphical LCD Name Display
Connects a PIC16F877A to an AMPIRE128X64 graphical LCD and displays a name on the screen.

Key concepts:
- PIC microcontroller digital input/output
- Parallel communication with a graphical LCD
- LCD control signals and initialization
- Writing and compiling embedded C code in MikroC
2. Countdown-Controlled Traffic Light
Simulates a traffic-light sequence with a numerical countdown on a seven-segment display. The countdown is coordinated with the active traffic-light phase, following the assignment requirement for the displayed countdown colour to correspond to the traffic-light colour.

Key concepts:
- Digital output control for traffic-light signals
- Seven-segment number display
- Countdown logic and timing
- Coordinating multiple outputs in an embedded system
Note: The exact component models, wiring, display polarity, countdown durations, and pin assignments should match the Proteus schematic and the hardware available for the assignment.

Tools and Components
- Microcontroller: PIC16F877A
- IDE/compiler: MikroC PRO for PIC
- Simulation: Proteus Design Suite
- Display for Project 1: AMPIRE128X64 graphical LCD
- Display for Project 2: Seven-segment display and traffic-light device
- Crystal oscillator and supporting circuit components as required by each schematic
How to Use
1. Clone or download this repository.
2. Open the relevant project schematic in Proteus.
3. Open the corresponding source project or .c file in MikroC PRO for PIC.
4. Confirm that the MikroC device and clock frequency match the Proteus PIC settings.
5. Build the MikroC project to generate the PIC program's .hex file.
6. In Proteus, open the PIC16F877A component properties and select the generated .hex file as its program file.
7. Check that the schematic's pin connections match the source-code configuration, then run the simulation.
Suggested Repository Structure
PIC16F877A-IoT-Projects/
├── README.md
├── Assignment-1-GLCD-Name-Display/
│   ├── source/       # MikroC source/project files
│   ├── simulation/   # Proteus schematic files
│   └── screenshots/  # Evidence of a successful simulation
└── Assignment-2-Traffic-Light-Countdown/
    ├── source/       # MikroC source/project files
    ├── simulation/   # Proteus schematic files
    └── screenshots/  # Evidence of a successful simulation
This is a suggested structure; adjust it to match the files actually included in the repository.
Testing Checklist
- [ ] The MikroC project builds without errors.
- [ ] The generated HEX file is loaded into the correct PIC16F877A component in Proteus.
- [ ] The device clock setting matches the project's configured clock.
- [ ] Project 1 initializes the GLCD and displays the intended name clearly.
- [ ] Project 2 cycles through the required traffic-light phases and updates the countdown correctly.
- [ ] The countdown display colour follows the active traffic-light phase, as required by the assignment.
- [ ] Screenshots of the working simulations are saved for coursework evidence.
Learning Objectives
These projects provide practical experience with microcontroller programming, digital I/O, display interfacing, timing and countdown logic, circuit simulation, and debugging embedded systems.
Academic Context
Prepared as IoT coursework to practise interfacing a PIC16F877A with output devices and verifying embedded-system behaviour through simulation.
