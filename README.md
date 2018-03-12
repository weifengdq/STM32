# STM32
Some STM32 Examples

- [Flash](#flash)  



## Flash

Reference:  
- STM32Cube_FW_F1_V1.6.0\Projects\STM32F103RB-Nucleo\Examples\FLASH\FLASH_EraseProgram\Src\main.c  
- [【STM32】使用STM32cubeMX的库读写FLASH数据](http://blog.csdn.net/yannanxiu/article/details/52837411)  

**Write flash**, Four steps is needed:  

1. Unlock the Flash to enable the flash control register access: `HAL_FLASH_Unlock();`  
2. Erase the user Flash area:
```C
#define ADDR_FLASH_PAGE_48    ((uint32_t)0x0800C000) /* Base @ of Page 48, 1 Kbytes */
#define ADDR_FLASH_PAGE_63    ((uint32_t)0x0800FC00) /* Base @ of Page 63, 1 Kbytes */

#define FLASH_USER_START_ADDR   ADDR_FLASH_PAGE_48   /* Start @ of user Flash area */
#define FLASH_USER_END_ADDR     ADDR_FLASH_PAGE_63 + FLASH_PAGE_SIZE   /* End @ of user Flash area */

uint32_t PAGEError = 0;

FLASH_EraseInitTypeDef EraseInitStruct;
EraseInitStruct.TypeErase   = FLASH_TYPEERASE_PAGES;
EraseInitStruct.PageAddress = FLASH_USER_START_ADDR;
EraseInitStruct.NbPages     = (FLASH_USER_END_ADDR - FLASH_USER_START_ADDR) / FLASH_PAGE_SIZE;
HAL_FLASHEx_Erase(&EraseInitStruct, &PAGEError);    //return HAL_OK if success
```
3. Program the user Flash area word by word:
```C
#define DATA_32 ((uint32_t)0x12345678)
uint32_t Address = 0;

Address = FLASH_USER_START_ADDR;

while (Address < FLASH_USER_END_ADDR) {
    if (HAL_FLASH_Program(FLASH_TYPEPROGRAM_WORD, Address, DATA_32) == HAL_OK) {
      Address = Address + 4;
    }
}
```  
4. Lock the Flash to disable the flash control register access: `HAL_FLASH_Lock();`   

**Read Flash** is easy, use pointer to fetch the value of address:  
```C
__IO uint32_t data32 = 0;

Address = FLASH_USER_START_ADDR;

while (Address < FLASH_USER_END_ADDR) {
    data32 = *(__IO uint32_t *)Address;
    printf("addr:0x%x, data:0x%x\r\n", Address, data32);
    Address = Address + 4;
  }
```

PA9(TX) -> USB2Serial RX -> PC.  

![flash](/Assets/flash.png)  

The [Flash](/Flash) Project Write a string to flash, read it and print the string to USART1.  



