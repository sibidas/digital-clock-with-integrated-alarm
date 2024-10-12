In this project we design and implement a digital clock in 12 hr format with integrated alarm.The programming language used is verilog (board: Artix 7).
Algorithm

Step 1: Initialize clock and alarm values.

Step 2: Generate a 1 Hz clock signal from the system clock.

Step 3: Increment time every second, updating seconds, minutes, and hours.

Step 4: Toggle AM/PM when hours roll over from 12 to 1.

Step 5: Compare the current time with the alarm time and trigger the alarm if they match.

Step 6: Implement a reset function to restart the system.
