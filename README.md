[![Made with Verilog](https://img.shields.io/badge/Made%20with-Verilog-1f425f.svg)](https://verilog.org/)
# FPGA-uart-and-canbus-transceiver
FPGA uart and canbus transceiver

<span id="en">FPGA-CAN-UART</span>
===========================

**FPGA** based  **CAN bus and UART**.

# Introducion

**CAN bus**, as the most commonly used communication bus in the industrial and automotive fields, has the advantages of simple topology, high reliability, and long transmission distance. The **non-destructive arbitration** mechanism of **CAN bus** relies on the **frame ID**. **CAN2.0A** and **CAN2.0B** respectively specify **11bit-ID (short ID)** for **standard frame** and **29bit-ID (long ID)** for **extended frame**. In addition, there is a data request mechanism called **remote frame**.

This repository implements a lightweight but complete **FPGA-based CAN controller**, the features are as follows:

* **Platform Independent**: pure Verilog design and can run on various FPGAs such as Altera and Xilinx.
* **Local ID** can be fixed to any **short ID**.
* **Send** : Only supports sending frames with data length of **4Byte** with **local ID**.
* **Receive** : Support to receive frames with **short ID** or **long ID**, the data length of the received frame is not limited (that is, **0~8Byte** is supported).
* **Receive frame ID filtering** : It will only receive data frames that match the filters, their are a short ID filter and a long ID filter independently.
* **Automatic respond to remote frame** : When the received **remote frame** matches the **local ID**, it will automatically send the next data in the send buffer. If the send buffer is empty, the data sent last time will be sent repeatedly.

UART Features:

- Configurable TX/RX buffer
- Configurable UART baud rate, parity bit, and stop bits
- Fractional frequency division: When the clock frequency cannot be divided by the baud rate, the cycles of each bit are different, thus rounding up a more accurate baud rate.
- Baud rate check report: During simulation, the baud rate accuracy will be printed using `$display`. If it is too imprecise, an error will be reported.

# Design Code

The [RTL](./RTL) folder contains 3 design source files, and the functions of each file are as follows. You only need to include these 3 files into the project, and call the top-level module **can_top.v** to develop your CAN communication applications.

| File Name               | Function                                                     | Note                              |
| :---------------------- | :----------------------------------------------------------- | :-------------------------------- |
| **fpga_top.v**          | top module of CAN and UART.                                |                                   |
| **can_top.v**          | top module of CAN controller.                                |              called by **fpga_top.v**                        |
| **can_level_packet.v** | Frame-level controller, responsible for parsing or generating frames, and implementing non-destructive arbitration. | called by **can_top.v**          |
| **can_level_bit.v**    | Bit-level controller, responsible for sending and receiving bits, with falling edge alignment mechanism against frequency offset. | called by **can_level_packet.v** |
| **uart_tx.v**    | CAN bus reading and writing, and feedback the results to the UART |   called by **fpga_top.v**|
| **uart_rx.v**    | CAN bus reading and writing, and feedback the results to the UART |   called by **fpga_top.v**|

- The local (aka the FPGA's) ID is configured as 0x456, so all outgoing data frames have an ID of 0x456.
- The ID filter is configured to only receive data frames with **short ID**=**0x123** or **long ID**=**0x12345678**.
- Send an incremental uart data to CAN bus.
- The data frame received on the CAN bus (which match the ID filter) will be send to the computer via UART.

> Note: In this demo, the baud rate of CAN is 1MHz; the configuration of UART is 115200,8,n,1

<img width="750" height="299" alt="image" src="https://github.com/user-attachments/assets/8fb82aa7-c96c-4258-9278-f47b8e7b6bfd" />

## Board Details
This project is designed for the **Zybo Z7-20** FPGA boards from Digilent.
- [Zybo Z7-20 Reference](https://digilent.com/reference/programmable-logic/zybo-z7/hardware)

　
