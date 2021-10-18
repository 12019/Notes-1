### adb shell  查看

`cat proc/cmdline`

result:

```shell
earlycon=sprd_serial,0x70100000,115200n8 console=ttyS1,115200n8 loglevel=1 init=/init root=/dev/ram0 rw androidboot.hardware=sp9820e_2c10aov lcd_id=ID329306 lcd_base=8fc1f000 lcd_size=320x240   sysdump_magic=85500000 modem=shutdown ltemode=lcsfb 32k.less=1 androidboot.hardware.revision=V4.1 emmc.cid=700100454534494134011f6433be2500  androidboot.verifiedbootstate=green androidboot.flash.locked=1  androidboot.serialno=59727369867220
```



| 信息显示项 | 关键字                               | 值   | cmdline                                            |
| ---------- | ------------------------------------ | ---- | -------------------------------------------------- |
| Display_ID | lcd_id                               |      | lcd_id=ID329306 lcd_base=8fc1f000 lcd_size=320x240 |
| CAMERA_ID  |                                      |      |                                                    |
| HW_Version |                                      |      | androidboot.hardware.revision=V4.1                 |
| EMMC ID    | CONFIG_READ_EMMC_CID/emmc_cid2       |      | emmc.cid=700100454534494134011f6433be2500          |
| WIFI ADD   | 无wifi不显示                         |      |                                                    |
| SN1        | androidboot.serialno                 |      | androidboot.serialno                               |
| SN2        |                                      |      |                                                    |
| DDR ID     | sprd_get_ddrid                       |      | emmc.cid=700100454534494134011f6433be2500          |
| AUDIOPA ID | PA/audio/AUDIO/CONFIG_READ_AUDIO_PA/ |      |                                                    |
| IMEI       |                                      |      |                                                    |
| BT ADD     |                                      |      |                                                    |