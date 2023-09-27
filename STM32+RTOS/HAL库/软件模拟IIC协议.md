## 使用 HAL 库软件模拟 IIC

&emsp;

### 0.底层实现：

```c
// 函数原型声明
void i2c_sda_low(void);
void i2c_sda_high(void);

// 控制SDA线为低电平
void i2c_sda_low(void)
{
    HAL_GPIO_WritePin(GPIOx, GPIO_PIN_x, GPIO_PIN_RESET); // GPIOx 和 GPIO_PIN_x 根据你的硬件配置而定
}

// 控制SDA线为高电平
void i2c_sda_high(void)
{
    HAL_GPIO_WritePin(GPIOx, GPIO_PIN_x, GPIO_PIN_SET); // GPIOx 和 GPIO_PIN_x 根据你的硬件配置而定
}
```

&emsp;

### 1. 软件延时：

```c
/**
 * @brief 模拟I2C延时
 * @retval none
 * @author Treelefe
 * @date 2023-9-25
 */
static void analog_i2c_delay(void)
{
    volatile uint8_t i;

    for (i = 0; i < 10; i++);
}
```

&emsp;

### 2. IIC 引脚配置：

```c
/**
 * @brief 软件模拟I2C初始化
 * @retval none
 * @author Treelefe
 * @date 2023-9-25
 */
void bsp_analog_i2c_init(void)
{
    GPIO_InitTypeDef GPIO_InitStruct = {0};

    __HAL_RCC_GPIOB_CLK_ENABLE();

    /*Configure GPIO pins : PB6 PB7 */
    GPIO_InitStruct.Pin = GPIO_PIN_6|GPIO_PIN_7;
    GPIO_InitStruct.Mode = GPIO_MODE_OUTPUT_OD;
    GPIO_InitStruct.Pull = GPIO_NOPULL;
    GPIO_InitStruct.Speed = GPIO_SPEED_FREQ_HIGH;
    HAL_GPIO_Init(GPIOB, &GPIO_InitStruct);
    bsp_analog_i2c_stop();
}
```

&emsp;

### 3. IIC 发送起始信号：

```c
/**
 * @brief I2C 开始,SCL为高电平的时候SDA产生一个下降沿信号
 * @retval none
 * @author Treelefe
 * @date 2023-9-25
 */
void bsp_analog_i2c_start(void)
{
    /*    _____
     *SDA      \_____________
     *    __________
     *SCL           \________
     */
    i2c_sda_high();
    i2c_scl_high();
    analog_i2c_delay();
    i2c_sda_low();
    analog_i2c_delay();
    i2c_scl_low();
    analog_i2c_delay();
}
```

&emsp;

### 4. IIC 发送停止信号：

```c
/**
 * @brief I2C 停止,SCL为高电平的时候SDA产生一个上升沿信号
 * @retval none
 * @author Treelefe
 * @date 2023-9-25
 */
void bsp_analog_i2c_stop(void)
{
    /*               _______
     *SDA __________/
     *          ____________
     *SCL _____/
     */
    i2c_sda_low();
    i2c_scl_high();
    analog_i2c_delay();
    i2c_sda_high();
    analog_i2c_delay();
}
```

&emsp;

### 5. IIC 发送应答信号：

````c
/**
 * @brief I2C 响应
 * @retval none
 * @author Treelefe
 * @date 2023-9-25
 */
void bsp_analog_i2c_ack(uint8_t ack)
{
    /*           ____
     *SCL ______/    \______
     *    ____         _____
     *SDA     \_______/
     */
      /*         ____
     *SCL ______/    \______
     *    __________________
     *SDA
     */
    if(ack == 0){
        i2c_sda_low();
    }
    else
        i2c_sda_high();

    analog_i2c_delay();
    i2c_scl_high();
    analog_i2c_delay();
    i2c_scl_low();

}

&emsp;
### 6. IIC发送1个字节：
```c
/**
 * @brief I2C 发送一个字节数据
 * @retval none
 * @author Treelefe
 * @date 2023-9-25
 */
void bsp_analog_i2c_send_byte(uint8_t data)
{
    uint8_t i;

    for(i = 0; i < 8; i++)
    {
        if(data & 0x80)
        {
           i2c_sda_high();
        }
        else
        {
            i2c_sda_low();
        }

        analog_i2c_delay();
        i2c_scl_high();
        analog_i2c_delay();
        i2c_scl_low();
        if(i == 7)
        {
            i2c_sda_high();
        }
        data <<= 1;
        analog_i2c_delay();
    }
}
````

&emsp;

### 7. IIC 接收 1 个字节：

```c
/**
 * @brief I2C 读一个字节数据
 * @retval none
 * @author Treele
 * @date 2023-9-25
 */
uint8_t bsp_analog_i2c_read_byte(void)
{
    uint8_t i, data = 0;

    for(i = 0; i < 8; i++ )
    {
        data <<= 1;
        i2c_scl_high();
        analog_i2c_delay();
        if(i2c_read_sda())
        {
            data++;
        }
        i2c_scl_low();
        analog_i2c_delay();
    }

    return data;
}
```

> 以上就是软件模拟 IIC 的基本代码，I2C 通信的具体功能实现会依赖于你连接到 I2C 总线上的外设，每个外设都有特定的内部指令和通信协议。你可以根据外设的数据手册或文档来编写相应的函数来实现特定的功能，包括发送地址、读写数据等操作。通常，这些功能会涉及到生成正确的数据帧和控制信号来与外设进行通信。
