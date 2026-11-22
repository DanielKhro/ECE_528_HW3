# ECE 528 Homework 3

# Question 1
The MSP432 uses the eUSCI_A modules for UART communication. Describe the function of the following registers:

• UCAxBRW (Baud Rate Control Word)
    
    This register is the prescaler for the actual baud rate generator.

• UCAxMCTLW (Modulation Control Word)
    
    This register controls specific modulation settings such as oversampling and a secondary stage timing adjustment all to ultimately set hte desired baud rate exactly.

• UCAxCTLW0 (Control Word 0)
    
    This register holds the main settings and configuration of the eUSCI_Ax. You would set and clear a majority of bits here to ensure the peripheral is handling data properly. Especially in this register, you also have to define Master and Slave mode.

• UCAxRXBUF (Receive Buffer)
    
    This register is a read-only register that will hold the data incoming. Reading from this register resets the receive-error bits, UCADDR, UCIDLE and UCRXIFG.

• UCAxTXBUF (Transmit Buffer)
    
    Like the RXBUF register but this is a RW register that holds the transmit data about to be transmitted by the transmit shift register. The reason why a write is allowed, is to clear the UCTXIFG known as the transmit interrupt flag so it's not constantly stuck on a pending interrupt.

# Question 2
What is the purpose of the UCAxSTATW status register in the UART module?

    This register indicates the status of the eUSCI_Ax module. For simplicity, it indicates if it's busy, there is a overrun where incoming data is received before the previous was read, loopback, and framing error.

Which bits indicate the following?

• Whether the UART module is currently transmitting

The UCBUSY bit which is bit 0.

• Whether a framing, parity, or overrun error has occurred

UCOE which is bit 5

• Whether the RX buffer contains unread data

None of the bits indicate this.

# Question 3

Assume that the eUSCI_A3 module is using SMCLK as its clock source, which has a frequency of 12 MHz. Calculate the value that is needed to be written to the UCAxBRW register to configure the eUSCI_A3 module for 115200 baud.

12,000,000 / 115,200 = 104.17

# Question 4
A)
Baud Rate: 115200 DONE
• Data Length: 8 bits DONE
• Parity: None DONE
• Stop Bits: 1 DONE
• Bit Order Most Significant Bit (MSB) First DONE
• Clock Source: SMCLK (12 MHz) DONE
• Pins: P3.2 (UCA2TXD) and P3.3 (UCA2RXD) DONE
void UART2_Init(){
    
    P3SEL0 |= 0x0C;
    P3SEL1 &= ~0x0C;
    EUSCI_A2->CTLW0 |= 0x01;
    EUSCI_A2->MCTLW &= ~0xFF;
    EUSCI_A2->CTLW0 &= ~0x8000;
    EUSCI_A2->CTLW0 &= ~0x0800;
    EUSCI_A2->CTLW0 |= 0x2000;
    EUSCI_A2->CTLW0 |= 0x0080;
    EUSCI_A2->CTLW0 &= ~0x1000;
    EUSCI_A2->BRW   = 104.17;
    EUSCI_A2->CTLW0 &= ~0x01;

}

B) DRAWING AT BOTTOM

# Question 5

eUSCI_B is serial communication which means that it can configure SPI or I2C.

Synchronization: SPI is synchronous since both devices use the same clock whereas UART is asynchronous, reliant on baud rate.

Clocking: SPI master gives a clock on a completely separate line where the UART has no clock line whatsoever to time the devices.

Signaling: Clock signal, data line bi-directional, and CS signal or device select. UART uses a TX and RX data line.

Data Framing: Bits are shifted according to clock edges as well as the CS signal. With UART, each chracter has a start bit, 7 or 8 data bits, parity bits, then stop bits.

# Question 6

A)

SCLK: The serial clock given by the master device, it is configured based on clock edge to shift and sample bits.

CS: Chip select is the line that selects which devices are the ones to be transferred with.

MOSI: Master Out Slave In carries the data from the master to the slave device selected by CS.

MISO: The opposite of MISO, the slave device data is sent back to the master device.

B)

If multiple are asserted at once, there would most likely be unreliable data that would be read or returning from the selected devices. 

# Question 7

A)

It is the main configuration register for the module. It determines SPI or an I2C kind of operation then configures the master/slave modes, clock source, etc. Like the A module, it needs to be held in a reset state.

B)

For the SPI mode you need UCSYNC to be set, clear UCMODE, and set UCMST for master or slave.
 UCSYNC is synchronous SPI operation.
UCMODE selects the specific type of operation between SPI or I2C.
UCMST is simply choosing slave or master.

C)

The UCSSELx field which is bit 7-6 is used.

00b = Reserved

01b = ACLK

10b & 11b = SMCLK

# Question 8

A)

void EUSCI_B0_SPI_Init(void)
{
    
    P9SEL0 &= ~0x01;
    P9SEL1 &= ~0x01;
    P9OUT  |=  0x01;
    P9DIR  |=  0x01;
    P1SEL0 |=  0xE0;
    P1SEL1 &= ~0xE0;
    EUSCI_B0->CTLW0 |=  0x0001;  
    EUSCI_B0->CTLW0 |=  0x8000;  
    EUSCI_B0->CTLW0 &= ~0x4000; 
    EUSCI_B0->CTLW0 |=  0x2000;  
    EUSCI_B0->CTLW0 &= ~0x1000; 
    EUSCI_B0->CTLW0 |=  0x0800;  
    EUSCI_B0->CTLW0 &= ~0x0600;  
    EUSCI_B0->CTLW0 |=  0x0100;  
    EUSCI_B0->CTLW0 &= ~0x00C0;  
    EUSCI_B0->CTLW0 |=  0x0080;  
    EUSCI_B0->BRW = 0x000C;      
    EUSCI_B0->CTLW0 &= ~0x0001;  

}

B) <img width="480" height="640" alt="IMG_8639" src="https://github.com/user-attachments/assets/873ba49b-fd32-4af2-812e-48675dc05788" />


