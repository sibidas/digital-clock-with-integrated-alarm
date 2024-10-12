# digital-clock-with-integrated-alarm
In this project we design and implement a digital clock in 12 hr format with integrated alarm.The programming language used is verilog (board: Artix 7).
Algorithm

Step 1: Initialize clock and alarm values.
Step 2: Generate a 1 Hz clock signal from the system clock.
Step 3: Increment time every second, updating seconds, minutes, and hours.
Step 4: Toggle AM/PM when hours roll over from 12 to 1.
Step 5: Compare the current time with the alarm time and trigger the alarm if they match.
Step 6: Implement a reset function to restart the system.

module digital_clock_with_alarm_12hr(
    input wire clk,             
    input wire reset,           
    input wire set_alarm,      
    input wire [7:0] alarm_hours_in,  
    input wire [7:0] alarm_minutes_in, 
    input wire [7:0] alarm_seconds_in,
    input wire alarm_am_pm_in,    // AM/PM input for alarm (0 for AM, 1 for PM)
    output reg [7:0] hours,      // 8-bit output for hours (1-12)
    output reg [7:0] minutes,    // 8-bit output for minutes (0-59)
    output reg [7:0] seconds,    // 8-bit output for seconds (0-59)
    output reg am_pm,            // AM/PM indicator (0 for AM, 1 for PM)
    output reg alarm_trigger     // Output signal to indicate alarm state
);

    reg [31:0] counter;    // 32-bit counter for generating 1 Hz clock
    reg one_hz_clk;              // 1 Hz clock signal

    reg [7:0] alarm_hours;
    reg [7:0] alarm_minutes;
    reg [7:0] alarm_seconds;
    reg alarm_am_pm;

    // Parameter for 50 MHz clock (adjust based on your clock frequency)
    parameter CLOCK_FREQ = 50000000;  // System clock frequency (50 MHz)
    parameter ONE_SEC_COUNT = CLOCK_FREQ - 1; // Count for 1 second (50 million cycles)

    // Counter to generate 1 Hz clock signal
    always @(posedge clk or posedge reset) begin
        if (reset) begin
            counter <= 32'b0;
            one_hz_clk <= 0;
        end else begin
            if (counter == ONE_SEC_COUNT) begin
                counter <= 32'b0;
                one_hz_clk <= ~one_hz_clk; // Toggle 1 Hz clock
            end else begin
                counter <= counter + 1;
            end
        end
    end

    // Clock logic for hours, minutes, and seconds with AM/PM and alarm functionality
    always @(posedge one_hz_clk or posedge reset) begin
        if (reset) begin
            seconds <= 8'b0;
            minutes <= 8'b0;
            hours <= 8'd12; // Start at 12
            am_pm <= 1'b0; // Default to AM
            alarm_trigger <= 1'b0;
        end else begin
            // Seconds logic
            if (seconds == 8'd59) begin
                seconds <= 8'b0;
                // Minutes logic
                if (minutes == 8'd59) begin
                    minutes <= 8'b0;
                    // Hours logic
                    if (hours == 8'd12) begin
                        hours <= 8'd1; // Roll over to 1
                        am_pm <= ~am_pm; // Toggle AM/PM
                    end else begin
                        hours <= hours + 1;
                    end
                end else begin
                    minutes <= minutes + 1;
                end
            end else begin
                seconds <= seconds + 1;
            end
            
            // Check if the current time matches the alarm time
            if (hours == alarm_hours && minutes == alarm_minutes && 
                seconds == alarm_seconds && am_pm == alarm_am_pm) begin
                alarm_trigger <= 1'b1; // Alarm triggered
            end else begin
                alarm_trigger <= 1'b0;
            end
        end
    end

    // Set alarm time
    always @(posedge set_alarm or posedge reset) begin
        if (reset) begin
            alarm_hours <= 8'b0;
            alarm_minutes <= 8'b0;
            alarm_seconds <= 8'b0;
            alarm_am_pm <= 1'b0; // Default to AM
        end else begin
            alarm_hours <= alarm_hours_in;
            alarm_minutes <= alarm_minutes_in;
            alarm_seconds <= alarm_seconds_in;
            alarm_am_pm <= alarm_am_pm_in;
        end
    end

endmodule
