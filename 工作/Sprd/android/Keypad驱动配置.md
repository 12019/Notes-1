## u-boot配置

### 在u-boot的pinmap设置gpio

`u-boot15/board/spreadtrum/sp9832e_1h10_32b/pinmap-sp9832e_32b.c`

```c
//GPIO131  5key------FM

 {REG_PIN_IIS1CLK,                       BITS_PIN_AF(0)},

 {REG_MISC_PIN_IIS1CLK,                  BITS_PIN_DS(1)|BIT_PIN_NULL|BIT_PIN_NUL|BIT_PIN_SLP_ALL|BIT_PIN_SLP_NUL|BIT_PIN_SLP_OE},///BCK

 {REG_PIN_IIS1LRCK,                      BITS_PIN_AF(0)},

 {REG_MISC_PIN_IIS1LRCK,                 BITS_PIN_DS(1)|BIT_PIN_NULL|BIT_PIN_NUL|BIT_PIN_SLP_ALL|BIT_PIN_SLP_NUL|BIT_PIN_SLP_OE},//FS

 {REG_PIN_SCL2,                          BITS_PIN_AF(0)},

  {REG_MISC_PIN_SCL2,                     BITS_PIN_DS(1)|BIT_PIN_WPUS|BIT_PIN_WPU|BIT_PIN_SLP_AP|BIT_PIN_SLP_WPU|BIT_PIN_SLP_Z},//I2C2_SCL

  {REG_PIN_SDA2,                          BITS_PIN_AF(0)},

  {REG_MISC_PIN_SDA2,                     BITS_PIN_DS(1)|BIT_PIN_WPUS|BIT_PIN_WPU|BIT_PIN_SLP_AP|BIT_PIN_SLP_WPU|BIT_PIN_SLP_Z},//I2C2_SDA

  {REG_PIN_CLK_AUX0,                      BITS_PIN_AF(3)},
```

对于key,GPIO应设置为输入上拉，其他可保持不变



### 设置电压

`u-boot15/board/spreadtrum/sp9832e_1h10_32b/ldo_sleep.c`

```c
void DCDC_ldo_power_on() 
```

`u-boot15/board/spreadtrum/sp9832e_1h10_32b/regulator_init.c`

```c
static int power_on_voltage_init(void)
```

### 设置按键功能

`u-boot15/include/configs/sp9832e_1h10_32b.h`

```c
#define CONFIG_7S_RST_SHORT_MODE	1	//0:long press then release key to trigger;1:press key some time to trigger
#define CONFIG_7S_RST_2KEY_MODE		1	//0:1Key--Normal mode; 1:2KEY
#define CONFIG_7S_RST_2KEY_MODE		0	//0:1Key--Normal mode; 1:2KEY
#define CONFIG_7S_RST_THRESHOLD		7	//7S, hold key down for this time to trigger
```



## kernel的配置

### 设置电平生效

代码位置如下

`kernel/arch/arm/boot/dts/sc2721.dtsi `

```dtd
vddsim1: ldo@8 {
	reg = <8>;
	regulator-name = "vddsim1";
	sprd,default-microvolt = <1800000>;
	sprd,step-microvolt = <10000>;
	regulator-min-microvolt = <1200000>;
	regulator-max-microvolt = <3750000>;
	regulator-microvolt-offset = <0>;
	sprd,hide-offset = <1000>;
	status = "disabled";
};
```

### 设置gpio_key

`kernel/arch/arm/boot/dts/sp9832e-1h10-go-ll.dts`

```c
gpio_keys {
 		compatible = "gpio-keys";
 		input-name = "sprd-gpio-keys";
 		status = "ok";
 
		key-volumndown {
			label = "Volume Down Key";
			linux,code = <114>;
			gpios = <&ap_eic 2 1>;
			debounce-interval = <2>;
			gpio-key,wakeup;
			gpio-key,level-trigger;
		};

		key-volumeup {
			label = "Volume Up Key";
			linux,code = <115>;
			gpios = <&pmic_eic 10 0>;
			debounce-interval = <2>;
			gpio-key,wakeup;
			gpio-key,level-trigger;
		};

		key-power {
			label = "Power Key";
			linux,code = <116>;
			gpios = <&pmic_eic 1 1>;
			gpio-key,wakeup;
			gpio-key,level-trigger;
		};
//TOP_KEY_FPC_START
        key-power {
            label = "Power Key";
            linux,code = <116>;
            gpios = <&pmic_eic 1 1>;
            gpio-key,wakeup;
            gpio-key,level-trigger;
        };
        key-previoussong{
            label = "Previoussong Key";
            linux,code = <165>;
            gpios = <&ap_gpio 124 1>;
            debounce-interval = <2>;
            gpio-key,wakeup;
        };
}
```



### 修改gpio_key代码