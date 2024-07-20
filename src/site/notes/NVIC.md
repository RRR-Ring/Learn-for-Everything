---
{"dg-publish":true,"dg-path":"MCU微控制器/STM32/NVIC.md","permalink":"/MCU微控制器/STM32/NVIC/","dgPassFrontmatter":true,"noteIcon":"","created":"2024-07-17T00:16:12.484+08:00","updated":"2024-07-19T19:30:17.102+08:00"}
---

**Nested Vectored Interrupt Controller**
**嵌套向量中断控制器**

内核外设


中断向量表
向量地址，相当于中断程序的跳板


是 STM32 微控制器中用于管理中断的内核外设。它负责处理来自内核和片上所有外设的中断请求，包括可屏蔽中断和非屏蔽中断


管理中断、分配优先级

使用 NVIC 统一管理中断，每个中断通道都拥有 16 个可编程的优先等级，可对优先级进行分组，进一步设置抢占优先级和响应优先级


![Pasted image 20240716180800.png](/img/user/%E5%8A%9F%E8%83%BD%E6%80%A7%E6%96%87%E4%BB%B6%E5%A4%B9/%E8%BD%BD%E5%85%A5%E7%9A%84%E5%AA%92%E4%BD%93%E8%B5%84%E6%BA%90/Pasted%20image%2020240716180800.png)

一个外设可能占据多个中断通道
NVIC 只有一个输出口，根据中断优先级分配中断的先后顺序


NVIC 的中断优先级由优先级寄存器的 4 位（0~15）决定，这 4 位可以进行切分，分为高 n 位的抢占优先级和低 4-n 位的响应优先级

值越小，优先级越高

- **响应优先级高**的可以优先排队
	排队时，紧急程度较高，排在前面
	**subpriority**
	
- **抢占优先级高**的可以中断嵌套
	正在执行中断程序时，**抢占优先级高的中断**可以中断当前的中断
	**pre-emption priority**
	
响应优先级、抢占优先级均相同的按中断号排队


|分组方式|抢占优先级|响应优先级|
|---|---|---|
|分组 0|0 位，取值为 0|4 位，取值为 0~15|
|分组 1|1 位，取值为 0~1|3 位，取值为 0~7|
|分组 2|2 位，取值为 0~3|2 位，取值为 0~3|
|分组 3|3 位，取值为 0~7|1 位，取值为 0~1|
|分组 4|4 位，取值为 0~15|0 位，取值为 0|

### misc. h

基本配置（一定要有的）

```C
void NVIC_PriorityGroupConfig(uint32_t NVIC_PriorityGroup);
//中断分组,注意整个芯片只能使用一种分组方式
//要么模块化编程时，所有分组一致；要么直接放在主程序，编译一次
//无特殊要求或结构简单  NVIC_PriorityGroup可以随便取

void NVIC_Init(NVIC_InitTypeDef* NVIC_InitStruct);//结构体初始化
```



特殊配置
```C
void NVIC_SetVectorTable(uint32_t NVIC_VectTab, uint32_t Offset);//设置中断向量表

void NVIC_SystemLPConfig(uint8_t LowPowerMode, FunctionalState NewState);  //系统低功耗配置

void SysTick_CLKSourceConfig(uint32_t SysTick_CLKSource);
```

#### IRQn 
nterrupt Request -n  中断请求的缩写，
它代表了中断源的编号。STM32的中断系统允许多个中断源被管理，每个中断源都通过一个唯一的中断号（IRQn）来识别。


### startup_stm32f10x_md. s
每个外设中断函数名称都是固定的
在启动文件中查找


### 具体实例
[[GPIO#^4d936c\|GPIO]]
[[TIM定时中断\|TIM定时中断]]
