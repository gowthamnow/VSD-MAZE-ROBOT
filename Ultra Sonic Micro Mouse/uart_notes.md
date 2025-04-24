# UART Transmitter Module (8N1) 

## Overview of UART
UART (Universal Asynchronous Receiver/Transmitter) is a hardware communication protocol that transmits data between devices without a clock signal. The "8N1" configuration means:
- 8 data bits
- No parity bit
- 1 stop bit

### Why Use UART?
UART is commonly used for:
- Simple device-to-device communication
- Debugging (sending data to a PC)
- Low-speed data transfer (like 9600 baud in this case)
- When you don't need high-speed communication

## Module Interface

| Signal    | Direction | Width | Description |
|-----------|-----------|-------|-------------|
| clk       | Input     | 1     | 9600 Hz clock (1 clock cycle per bit) |
| txbyte    | Input     | 8     | Data byte to transmit |
| senddata  | Input     | 1     | Trigger to start transmission |
| txdone    | Output    | 1     | Indicates transmission complete |
| tx        | Output    | 1     | Serial output line |

## State Machine Explanation

The module uses a state machine with 4 states to control the transmission process:

| State        | Description | TX Line Value |
|--------------|-------------|---------------|
| STATE_IDLE   | Waiting for data to send | High (1) |
| STATE_STARTTX| Sending start bit | Low (0) |
| STATE_TXING  | Sending 8 data bits | Current bit value |
| STATE_TXDONE | Transmission complete | High (1) |

### Detailed State Transitions

1. **IDLE State**
   - TX line is held high (idle state)
   - Waits for `senddata` signal
   - When triggered, stores the input byte in `buf_tx` and moves to STARTTX state

2. **STARTTX State**
   - Sends the start bit (0) for one clock cycle
   - This signals the receiver that data is coming
   - Then moves to TXING state

3. **TXING State**
   - The most complex state - sends 8 bits LSB first
   - Uses a counter (`bits_sent`) to track progress
   - For each clock cycle:
     - Puts the LSB of `buf_tx` on the TX line
     - Right-shifts `buf_tx` to get next bit
     - Increments `bits_sent` counter
   - After 8 bits, sends stop bit (1) and moves to TXDONE

4. **TXDONE State**
   - Sets `txdone` signal high for one clock cycle
   - Returns to IDLE state

## Key Implementation Details

### Bit Transmission Order
The code transmits the **Least Significant Bit (LSB) first**:
```verilog
txbit <= buf_tx[0];    // Send LSB first
buf_tx <= buf_tx >> 1; // Shift right to get next bit
```
This is standard for UART communication.

### Timing Considerations
- The module expects a 9600 Hz clock (1 clock cycle per bit)
- Each state transition happens on clock edges
- Total transmission time: 10 clock cycles (1 start + 8 data + 1 stop)

### Buffer Usage
The `buf_tx` register holds the data during transmission:
1. Initially loaded with `txbyte`
2. Gradually shifted right as bits are transmitted
3. This preserves the original data while allowing sequential access to each bit

## Example Transmission

Let's transmit the byte `0x55` (binary `01010101`):

| Cycle | State       | TX Bit | Action                          |
|-------|-------------|--------|---------------------------------|
| 0     | IDLE        | 1      | Waiting                         |
| 1     | STARTTX     | 0      | Start bit                       |
| 2     | TXING       | 1      | Bit 0 (LSB) of 01010101         |
| 3     | TXING       | 0      | Bit 1                           |
| 4     | TXING       | 1      | Bit 2                           |
| 5     | TXING       | 0      | Bit 3                           |
| 6     | TXING       | 1      | Bit 4                           |
| 7     | TXING       | 0      | Bit 5                           |
| 8     | TXING       | 1      | Bit 6                           |
| 9     | TXING       | 0      | Bit 7 (MSB)                     |
| 10    | TXDONE      | 1      | Stop bit, set txdone            |
| 11    | IDLE        | 1      | Ready for next transmission     |

## Common Pitfalls for Beginners

1. **Clock Frequency**: Must match baud rate (9600 Hz for 9600 baud)
2. **Bit Order**: Easy to confuse MSB-first vs LSB-first (this uses LSB-first)
3. **State Timing**: Each state lasts exactly one clock cycle except TXING (8 cycles)
4. **Start/Stop Bits**: Must remember to include these in addition to the 8 data bits

This implementation provides a simple but complete UART transmitter that can be used for basic serial communication tasks.
