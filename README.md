# How-to-Use-Any-STM32-Library-Standard-Peripheral-Library-and-CMSIS
Leverage Any STM32 Library, Standard Peripheral Library(SPL), and CMSIS when register programming!

STM32 Standard Peripheral Library, CMSIS, and Any Library	June 27, 2020

Writing application code from scratch for microcontrollers is not an easy task. It is 
painstakingly long and very drudgery as you go. But not to say anything cannot go wrong.
On the other hand, anything can go astray.

Whether you program for microcontrollers or Systems-On-Chip (SOC), to be successful, you
have to meticulously read & look-up reference manuals, programming manuals, datasheet, 
and schematics. You get information about the registers to use, clock/timing issues, 
rise/fall/hold times, sequence as how the logical circuits are used. When you progress
through understanding the hardware aspects, we develop data structures to effectively
navigate & program or do regiser programming.

To hardware engineers, they would understand multiplexer, basic logic gates like AND, OR,
XOR, Inverter and the like. To enable/disable a circuit mapped to a register, you set
or clear a bit in the register entry. To set choose a single circuit from multiple 
inputs you multiplex hence you write a hex number say 0x3 to those bits. By the way,
explained in other tutorials, peripheral registers are memory-mapped to bit-banded areas
in a processor's adressable space. CMSIS had standardized the specs specially in assigning
the memory regions but you are still free to use your own personal designs as a programmer.

Software is making things hardware transparent so enter HAL aka hardware abstraction
layer. Register programming is the norm. But it is still a long way to have a working
device. Software and hardware advances have progressed from machine code to assembly,
and to high-level C. Using mnenomics, symbols, EQUs, defines they have lessened 
programming labor, increased readability, and simplified portability.

We now write high-level code in C. For the common tasks identified to be useful, we use
libraries. We don't have to reinvent things but reuse them. And this is where the 
standard peripheral library comes into the picture. The STM32 used to have and still have
the STM32 SPL. Because software is an art and a task can be written in so many ways, we
have a plethora of libraries to choose from CMSIS, HAL, SPL, etc. Many depend on each
other so dependecies matter unless you write another but that would mean a huge effort.
In short, using libraries helps you save a lot of effort and time.

The beauty of libraries is, they are pre-written for you. To use them, all you need is to
include the appropriate header and c files. I'm using the STM32L1 family so I'll have to
include:

stm32l1xx_gpio.h

stm32l1xx_gpio.c

If we look inside these files, we read the comments, defines, function prototypes, and the
implementation details. The defines are usually in the header files. The application code
usually include the header files and not the c files to hide the details. The defines are 
usually made to make it more readable and easier to maintain. You only have to change the
defines and anything that uses are instantly updated and doesn't need to be re-written.

Sample defines:

#define GPIO_Pin_0        ((uint16_t)0x0001)  /*!< Pin 0 selected */

#define GPIO_Pin_1        ((uint16_t)0x0002)  /*!< Pin 1 selected */

#define GPIO_Pin_2        ((uint16_t)0x0004)  /*!< Pin 2 selected */

#define GPIO_Pin_3        ((uint16_t)0x0008)  /*!< Pin 3 selected */

#define GPIO_Pin_4        ((uint16_t)0x0010)  /*!< Pin 4 selected */

#define GPIO_Pin_5        ((uint16_t)0x0020)  /*!< Pin 5 selected */

#define GPIO_Pin_6        ((uint16_t)0x0040)  /*!< Pin 6 selected */

#define GPIO_Pin_7        ((uint16_t)0x0080)  /*!< Pin 7 selected */

#define GPIO_Pin_8        ((uint16_t)0x0100)  /*!< Pin 8 selected */

#define GPIO_Pin_9        ((uint16_t)0x0200)  /*!< Pin 9 selected */

#define GPIO_Pin_10       ((uint16_t)0x0400)  /*!< Pin 10 selected */

#define GPIO_Pin_11       ((uint16_t)0x0800)  /*!< Pin 11 selected */

#define GPIO_Pin_12       ((uint16_t)0x1000)  /*!< Pin 12 selected */

#define GPIO_Pin_13       ((uint16_t)0x2000)  /*!< Pin 13 selected */

#define GPIO_Pin_14       ((uint16_t)0x4000)  /*!< Pin 14 selected */

#define GPIO_Pin_15       ((uint16_t)0x8000)  /*!< Pin 15 selected */

#define GPIO_Pin_All      ((uint16_t)0xFFFF)  /*!< All pins selected */

I have seen custom-written defines like these:

#define NC_GPIO_PIN_0     ((uint16_t)0x0001)  /*!< Pin 0 selected */

#define NC_GPIO_PIN_1     ((uint16_t)0x0002)  /*!< Pin 1 selected */

#define NC_GPIO_PIN_2     ((uint16_t)0x0004)  /*!< Pin 2 selected */

...

#define GPIO_PINSOURCE0   ((uint8_t)0x00)

#define GPIO_PINSOURCE1   ((uint8_t)0x01)

#define GPIO_PINSOURCE2   ((uint8_t)0x02)

...

...

The short story is you can write it any way you like as long as it suits and accomplishes
your objectives as the programmer.

For the functions to use, you have to digest them in the header and c files like:

void GPIO_Init(GPIO_TypeDef* GPIOx, GPIO_InitTypeDef* GPIO_InitStruct)
{

  uint32_t pinpos = 0x00, pos = 0x00 , currentpin = 0x00;
  
  uint32_t tmpreg = 0x00;
  
  ...
  
Inside void GPIO_DeInit(GPIO_TypeDef* GPIOx), 

  if(GPIOx == GPIOA)
  {
  
    RCC_AHBPeriphResetCmd(RCC_AHBPeriph_GPIOA, ENABLE);
    
    RCC_AHBPeriphResetCmd(RCC_AHBPeriph_GPIOA, DISABLE);  
    
  }
  
  else if(GPIOx == GPIOB)
  {
  
    RCC_AHBPeriphResetCmd(RCC_AHBPeriph_GPIOB, ENABLE);
    
    RCC_AHBPeriphResetCmd(RCC_AHBPeriph_GPIOB, DISABLE);
    
    ...
    
If you don't use any libraries, you will use this kind of code, as a trivial example, to
enable the GPIO port B clock. From the reference manual RM0038 and in the STM32L1 
architecture schematic pages 42-46, the GPIO port registers are connected to the AHB bus.

/* Enable GPIO port B clock in 3 ways */

RCC->AHBENR |= 0x00000002;	      /* Set bit 1 to enable. pages 153-154. We bitwise

					 OR RCC->AHBENR register with 0b0010. Method 1. */
					 
RCC->AHBENR |= 1UL << 1;	      /* 1UL << 1. Set bit 1. Method 2 */

RCC->AHBENR |= RCC_AHBENR_GPIOBEN;    /* Using 	line 2803 of stm32l1xx.h so don't forget

					 to include it. Method 3. See below to illustrate. */
					 
#define  RCC_AHBENR_GPIOBEN  ((uint32_t)0x00000002)  /*!< GPIO port B clock enable. */

If we need to disable the clock, we clear, instead of set, bit 1 to 0x0. In code, it is:

RCC->AHBENR |= 0x0;                   /* Clear bit 1 to disable */

Correspondingly, to enable GPIOA clock, we set bit 0 and in code:

RCC->AHBENR |= 1UL << 0;	      /* 1UL << 0. Set bit 0. */

The safest is to include everything but the downside is it bloats the code, unnecessarily.
		
Fortunately, it is only a single line of code so it is rather easy and straighforward. 
But for more complex peripherals, I have found it more taxing and takes a long time
going back and forth the reference & programming manuals plus the datasheet and testing
out if the bit assignments are working. You can really imagine the meticulous code effort
doing this methodology for the rather more complicated peripheral registers. Hence, the
rationale for libraries. Saving days or weeks of effort and time.

You must love indexed addressing in assembly and pointers in C to appreciate data 
structures used in the libraries. Pointers are just addresses and they are made to point
to structures used in manipulating them. We use offsets to reference a particular member.
Once we've identified the struct member, we dereference to get the value at that offset
address(plus the base address).

To illustrate one, the data structure used to access the GPIO peripheral is mentioned 
starting on line 411 of stm32l1xx.h.

/** 
  * @brief General Purpose IO
  */

typedef struct
{

  __IO uint32_t MODER;
  
  __IO uint16_t OTYPER;
  
  uint16_t RESERVED0;
  
  __IO uint32_t OSPEEDR;
  
  __IO uint32_t PUPDR;
  
  __IO uint16_t IDR;
  
  uint16_t RESERVED1;
  
  __IO uint16_t ODR;
  
  uint16_t RESERVED2;
  
  __IO uint16_t BSRRL; /* BSRR register is split to 2 * 16-bit fields BSRRL */
  
  __IO uint16_t BSRRH; /* BSRR register is split to 2 * 16-bit fields BSRRH */
  
  __IO uint32_t LCKR;
  
  __IO uint32_t AFR[2];
  
} GPIO_TypeDef;

We define and typedef struct to create an alias for our new type GPIO_TypeDef. It is not
a built-in type so we typedef. We next declare a struct pointer, cast it to memory
address for GPIOB.

#define GPIOB            ((GPIO_TypeDef *) GPIOB_BASE)  /* line 815 */

#define GPIOB_BASE       (AHBPERIPH_BASE + 0x0400)      /* line 743 */

Note the earlier defines.

#define PERIPH_BASE      ((uint32_t)0x40000000)    /*!< Peripheral base address in the alias region. line 703 */

#define AHBPERIPH_BASE   (PERIPH_BASE + 0x20000)   /* line 711 */

Now we have a pointer and we proceed with pointer methods to do and complete the register
programming.
  
As an aside, you write to use them in your application code. Try to follow and 
leverage the example/sample codes.

Learn to love the registers. The keys to proficiency are read, understand and write code.

Happy coding!

