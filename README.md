#include <avr/io.h>
#include <util/delay.h>
#include <avr/pgmspace.h>

// Macros to set SS line LOW (selected) or HIGH (deselected)
#define SLAVE_SELECT    PORTB &= ~(1 << PB2)
#define SLAVE_DESELECT  PORTB |= (1 << PB2)**************************************************

// Array holding arrays of 5 Bytes for each representation of an ASCII character, stored in flash
char characters[96][5] PROGMEM = { 
 {0b00000000,0b00000000,0b00000000,0b00000000,0b00000000}, // space
 {0b00000000,0b00000000,0b01001111,0b00000000,0b00000000}, // !
 {0b00000000,0b00000111,0b00000000,0b00000111,0b00000000}, // "
 {0b00010100,0b01111111,0b00010100,0b01111111,0b00010100}, // #
 {0b00100100,0b00101010,0b01111111,0b00101010,0b00010010}, // $
 {0b00100011,0b00010011,0b00001000,0b01100100,0b01100010}, // %
 {0b00110110,0b01001001,0b01010101,0b00100010,0b01010000}, // &
 {0b00000000,0b00000101,0b00000011,0b00000000,0b00000000}, // '
 {0b00000000,0b00011100,0b00100010,0b01000001,0b00000000}, // (
 {0b00000000,0b01000001,0b00100010,0b00011100,0b00000000}, // )
 {0b00010100,0b00001000,0b00111110,0b00001000,0b00010100}, // *
 {0b00001000,0b00001000,0b00111110,0b00001000,0b00001000}, // +
 {0b00000000,0b01010000,0b00110000,0b00000000,0b00000000}, // ,
 {0b00001000,0b00001000,0b00001000,0b00001000,0b00001000}, // -
 {0b00000000,0b01100000,0b01100000,0b00000000,0b00000000}, // .
 {0b00100000,0b00010000,0b00001000,0b00000100,0b00000010}, // /
 {0b00111110,0b01010001,0b01001001,0b01000101,0b00111110}, // 0
 {0b00000000,0b01000010,0b01111111,0b01000000,0b00000000}, // 1
 {0b01000010,0b01100001,0b01010001,0b01001001,0b01000110}, // 2
 {0b00100001,0b01000001,0b01000101,0b01001011,0b00110001}, // 3
 {0b00011000,0b00010100,0b00010010,0b01111111,0b00010000}, // 4
 {0b00100111,0b01000101,0b01000101,0b01000101,0b00111001}, // 5 
 {0b00111100,0b01001010,0b01001001,0b01001001,0b00110000}, // 6
 {0b00000011,0b01110001,0b00001001,0b00000101,0b00000011}, // 7
 {0b00110110,0b01001001,0b01001001,0b01001001,0b00110110}, // 8
 {0b00000110,0b01001001,0b01001001,0b00101001,0b00011110}, // 9
 {0b00000000,0b01101100,0b01101100,0b00000000,0b00000000}, // :
 {0b00000000,0b01010110,0b00110110,0b00000000,0b00000000}, // ;
 {0b00001000,0b00010100,0b00100010,0b01000001,0b00000000}, // <
 {0b00010100,0b00010100,0b00010100,0b00010100,0b00010100}, // =
 {0b00000000,0b01000001,0b00100010,0b00010100,0b00001000}, // >
 {0b00000010,0b00000001,0b01010001,0b00001001,0b00000110}, // ?
 {0b00110010,0b01001001,0b01111001,0b01000001,0b00111110}, // @
 {0b01111110,0b00010001,0b00010001,0b00010001,0b01111110}, // A
 {0b01111111,0b01001001,0b01001001,0b01001001,0b00111110}, // B
 {0b00111110,0b01000001,0b01000001,0b01000001,0b00100010}, // C
 {0b01111111,0b01000001,0b01000001,0b01000001,0b00111110}, // D
 {0b01111111,0b01001001,0b01001001,0b01001001,0b01001001}, // E
 {0b01111111,0b00001001,0b00001001,0b00001001,0b00000001}, // F
 {0b00111110,0b01000001,0b01001001,0b01001001,0b00111010}, // G
 {0b01111111,0b00001000,0b00001000,0b00001000,0b01111111}, // H
 {0b01000001,0b01000001,0b01111111,0b01000001,0b01000001}, // I
 {0b00110000,0b01000001,0b01000001,0b00111111,0b00000001}, // J
 {0b01111111,0b00001000,0b00010100,0b00100010,0b01000001}, // K
 {0b01111111,0b01000000,0b01000000,0b01000000,0b01000000}, // L
 {0b01111111,0b00000010,0b00001100,0b00000010,0b01111111}, // M
 {0b01111111,0b00000100,0b00001000,0b00010000,0b01111111}, // N
 {0b00111110,0b01000001,0b01000001,0b01000001,0b00111110}, // O
 {0b01111111,0b00001001,0b00001001,0b00001001,0b00000110}, // P
 {0b00111110,0b01000001,0b01010001,0b00100001,0b01011110}, // Q
 {0b01111111,0b00001001,0b00001001,0b00011001,0b01100110}, // R
 {0b01000110,0b01001001,0b01001001,0b01001001,0b00110001}, // S 
 {0b00000001,0b00000001,0b01111111,0b00000001,0b00000001}, // T
 {0b00111111,0b01000000,0b01000000,0b01000000,0b00111111}, // U
 {0b00001111,0b00110000,0b01000000,0b00110000,0b00001111}, // V
 {0b00111111,0b01000000,0b00111000,0b01000000,0b00111111}, // W
 {0b01100011,0b00010100,0b00001000,0b00010100,0b01100011}, // X 
 {0b00000011,0b00000100,0b01111000,0b00000100,0b00000011}, // Y
 {0b01100001,0b01010001,0b01001001,0b01000101,0b01000011}, // Z
 {0b01111111,0b01000001,0b01000001,0b00000000,0b00000000}, // [
 {0b00000010,0b00000100,0b00001000,0b00010000,0b00100000}, // '\'
 {0b00000000,0b00000000,0b01000001,0b01000001,0b01111111}, // ]
 {0b00000100,0b00000010,0b00000001,0b00000010,0b00000100}, // ^
 {0b01000000,0b01000000,0b01000000,0b01000000,0b01000000}, // _
 {0b00000000,0b00000001,0b00000010,0b00000100,0b00000000}, // `
 {0b00100000,0b01010100,0b01010100,0b01010100,0b01111000}, // a
 {0b01111111,0b01001000,0b01000100,0b01000100,0b00111000}, // 0b
 {0b00111000,0b01000100,0b01000100,0b01000100,0b00100000}, // c
 {0b00111000,0b01000100,0b01000100,0b01001000,0b01111111}, // d
 {0b00111000,0b01010100,0b01010100,0b01010100,0b00011000}, // e
 {0b00001000,0b01111110,0b00001001,0b00000001,0b00000010}, // f
 {0b00001100,0b01010010,0b01010010,0b01010010,0b00111110}, // g
 {0b01111111,0b00001000,0b00000100,0b00000100,0b01111000}, // h
 {0b00000000,0b01000100,0b01111101,0b01000000,0b00000000}, // i
 {0b00100000,0b01000000,0b01000100,0b00111101,0b00000000}, // j
 {0b01111111,0b00010000,0b00101000,0b01000100,0b00000000}, // k
 {0b00000000,0b01000001,0b01111111,0b01000000,0b00000000}, // l
 {0b01111000,0b00000100,0b00001000,0b00000100,0b01111000}, // m
 {0b01111100,0b00001000,0b00000100,0b00000100,0b01111000}, // n
 {0b00111000,0b01000100,0b01000100,0b01000100,0b00111000}, // o
 {0b01111100,0b00010100,0b00010100,0b00010100,0b00001000}, // p
 {0b00001000,0b00010100,0b00010100,0b01111100,0b00000000}, // q
 {0b01111100,0b00001000,0b00000100,0b00000100,0b00001000}, // r
 {0b01001000,0b01010100,0b01010100,0b01010100,0b00100000}, // s
 {0b00000100,0b00111111,0b01000100,0b01000000,0b00100000}, // t
 {0b00111100,0b01000000,0b01000000,0b00100000,0b01111100}, // u
 {0b00011100,0b00100000,0b01000000,0b00100000,0b00011100}, // v
 {0b00111100,0b01000000,0b00110000,0b01000000,0b00111100}, // w
 {0b01000100,0b00101000,0b00010000,0b00101000,0b01000100}, // x
 {0b00001100,0b01010000,0b01010000,0b01010000,0b00111100}, // y
 {0b01000100,0b01100100,0b01010100,0b01001100,0b01000100}, // z          
 {0b00000000,0b00001000,0b00110110,0b01000001,0b00000000}, // {
 {0b00000000,0b00000000,0b01111111,0b00000000,0b00000000}, // |
 {0b00000000,0b01000001,0b00110110,0b00001000,0b00000000}, // }
 {0b00001000,0b00000100,0b00000100,0b00001000,0b00000100} // ~    
}; // characters[95]

// Message to be displayed, stored in flash 
const char message[] PROGMEM = "Creintots Automation Solutions Pvt. Ltd.";

// Function Prototypes

void SystemInit(void);

void writeByte(uint8_t byte);

void writeWord(uint8_t address, uint8_t data);
  
void initMatrix(void);

void clearMatrix(void);

void initBuffer(void);

void pushBuffer(uint8_t x);

void pushCharacter(uint8_t c);

void displayMessage(const char *arrayPointer, uint16_t arraySize);

void displayBuffer(void);





#include "max7219_8x8.h"

#define NUM_DEVICES     3	 // Number of cascaded max7219's, or just 1
#define DEL	14000	 // Delay for scrolling speed in microseconds

// Buffer array of bytes to store current display data for each column in each cascaded device
uint8_t buffer [NUM_DEVICES*8];		







Void SystemInit( )
 {
LPC_PINCON->PINSEL0 |= 0xC0000000;	//Pin P0.15 allocated to function 4 SCLK
LPC_PINCON->PINSEL1 |= 0x03<<0;	//Pin P0.16 allocated to function 4 NSS
LPC_PINCON->PINSEL1 |= 0x03<<2;	//Pin P0.17 allocated to function 4 MISO
LPC_PINCON->PINSEL1 |= 0x03<<4;	//Pin P0.18 allocated to function 4 MOSI
//p0.15, P0.18. P0.23 are outputs
LPC_GPIO0->FIODIR |= (1<<15)| (1<<18)| (1<<23);
LPC_SC->PCONP |= 1 <<8;	//enable POWER to SPI (redundant following reset)
LPC_SC->PCLKSEL0 |= 1<<16;	//pclk = cclk
LPC_SPI->SPCCR |= 8;	//clock divider
LPC_SPI->SPCR |= 3<<3;	//clock phase
LPC_SPI->SPCR |= 1<< 5;	//master mode
dummy=LPC_SPI->SPSR;
}
------------------------------------------------------------------------------------------------------------------------------------------
/*
Int main ( )
 {	 
while(1)
 {	 
LPC_GPIO0->FIOCLR = 1<<23;	//select slave
LPC_SPI->SPDR = 0x5A;	//write willl initiate transfer
while (!(LPC_SPI->SPSR & (1<<7)));	//wait until transfer complete
LPC_GPIO0->FIOSET = 1<<23;	//release slave
}	 
}
*/
------------------------------------------------------------------------------------------------------------------------------------------


// Send byte through SPI
void writeByte(uint8_t byte)
{
	SPDR = byte;			// SPI starts sending immediately  
	while (!(SPSR & (1 << SPIF)));	// Loop until complete bit set
}





// Sends word through SPI
void writeWord(uint8_t address, uint8_t data) 
{
	writeByte(address);		// Write first byte
	writeByte(data);		// Write Second byte
}







// Initializes all cascaded devices
void initMatrix() 
{
	uint8_t i;		// Var used in for loops

	// Set display brighness
	
	LPC_GPIO0->FIOCLR = 1<<23;	//select slave
	for(i = 0; i < NUM_DEVICE; i++)   // Loop through number of cascaded devices
	{
		writeByte(0x0A); // Select Intensity register
		writeByte(0x07); // Set brightness
	}
	LPC_GPIO0->FIOSET = 1<<23;	//release slave

	
	// Set display refresh
	LPC_GPIO0->FIOCLR = 1<<23;	//select slave
	for(i = 0; i < NUM_DEVICES; i++)
	{
		writeByte(0x0B); // Select Scan-Limit register
		writeByte(0x07); // Select columns 0-7
	}
	LPC_GPIO0->FIOSET = 1<<23;	//release slave

	 
	// Turn on the display
	LPC_GPIO0->FIOCLR = 1<<23;	//select slave
	for(i = 0; i < NUM_DEVICES; i++)
	{
		writeByte(0x0C); // Select Shutdown register
		writeByte(0x01); // Select Normal Operation mode
	}
	LPC_GPIO0->FIOSET = 1<<23;	//release slave

	 
	// Disable Display-Test
	LPC_GPIO0->FIOCLR = 1<<23;	//select slave
	for(i = 0; i < NUM_DEVICES; i++)
	{
		writeByte(0x0F); // Select Display-Test register
		writeByte(0x00); // Disable Display-Test
	}
	LPC_GPIO0->FIOSET = 1<<23;	//release slave
}




// Clears all columns on all devices
void clearMatrix(void)
{
	uint8_t x, i;
	for(x = 1; x < 9; x++) // for all columns
	{   
        LPC_GPIO0->FIOCLR = 1<<23;	//select slave
        for(i = 0; i < NUM_DEVICES; i++)
		{
			writeByte(x);    // Select column x
			writeByte(0x00); // Set column to 0
		}
		LPC_GPIO0->FIOSET = 1<<23;	//release slave
	}
}





// Initializes buffer empty
void initBuffer(void)
{   
	uint8_t i;
	for(i = 0; i < NUM_DEVICES*8; i++)
		buffer[i] = 0x00;
}  






// Moves each byte forward in the buffer and adds next byte in at the end
void pushBuffer(uint8_t x)
{
	uint8_t i;
	for(i = 0; i < NUM_DEVICES*8 - 1; i++)
		buffer[i] = buffer[i+1];
	
	buffer[NUM_DEVICES*8 - 1] = x;
}







// Pushes in 5 characters columns into the buffer.
void pushCharacter(uint8_t c)
{
	uint8_t j;
	for(j = 0; j < 5; j++)		// For 5 bytes representing each character
	{
		pushBuffer(pgm_read_byte(&characters[c][j]));   // Push the byte from the characters
							array to the display buffer
		displayBuffer();			// Display the current buffer on the devices
		delay_us(DEL);			// delay
	}
}


// Takes a pointer to the beginning of a char array holding message, and array size, scrolls message.
void displayMessage(const char *arrayPointer, uint16_t arraySize)
{
	uint16_t i;
	for(i = 0; i < arraySize; i++)
	{
		pushCharacter(pgm_read_byte_near(arrayPointer + i) - 32);	// Send converted ASCII
								value of character in
						message to index in characters array
						(-32 sends corrent index to characters array)

		pushBuffer(0x00);		// Add empty column after character for letter spacing
		displayBuffer();		// Display
		delay_us(DEL); 		// Delay
	}
	
}







// Displays current buffer on the cascaded devices
void displayBuffer()
{
	uint8_t i, j, k   
	for(i = 0; i < NUM_DEVICES; i++) // For each cascaded device
	{
		for(j = 1; j < 9; j++) // For each column
		{		
			LPC_GPIO0->FIOCLR = 1<<23;	//select slave
		   
		   	for(k = 0; k < i; k++) // Write Pre No-Op code
			
				writeWord(0x00, 0x00);
		   
		   	writeWord(j, buffer[j + i*8 - 1]); // Write column data from buffer
		   
		   		for(k = NUM_DEVICES-1; k > i; k--) // Write Post No-Op code
			   	
					writeWord(0x00, 0x00);
		   
		   	LPC_GPIO0->FIOSET = 1<<23;	//release slave
		}
	}
}
// Main Loop
int main(void) 
{
  // Inits
	SystemInit();		//initialization of main clock and SPI  
	initMatrix();
	clearMatrix();
	initBuffer();
  
  // Pointer to beginning of message

	const char *messagePointer = &message[0];
  
  // Size of message matrix
  	uint16_t messageSize = sizeof(message);
  
  // Event loop
  	while (1) 
  	{
   
		displayMessage(messagePointer, messageSize);	// Display the message
   
  	}                                                 
  	return (0);                          
}







