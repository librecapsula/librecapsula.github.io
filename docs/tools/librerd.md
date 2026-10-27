# librerd

> Репозиторий: [librecapsula/librerd](https://github.com/librecapsula/librerd)
> (Python, GPL-3.0).

Утилита полного readback флеша Rockchip-плат: обходит ограничение чтения
RockUSB-лоадера и снимает честные дампы всех GPT-разделов с помощью
xrock - **без единой записи во флеш** (патчится только RAM
загруженного usbplug).

## Зачем это нужно

Заводской usbplug в loader-режиме отдаёт по rockusb-чтению константу
**0xCC** вместо данных для разделов `boot_b, system_a/b, vbmeta_a/b,
security, userdata` (честно читаются только `uboot, trust, misc,
boot_a` + зеркало idblock). В usbplug из OTA-лоадера стоит uint32-предел
чтения (init `0x10000` = 32 МиБ); обработчик -
`if (lba >= limit) memset(buf, 0xCC, …)` в двух местах.

librerd патчит этот предел прямо в RAM живого usbplug - после него
readback честный по всему чипу (447 488 секторов для Capsule-14).

!!! danger "Маска ≠ бэкап"

    Перезапись замаскированного раздела без снятого реального дампа -
    безвозвратная потеря данных. Перед любыми записями во флеш снимайте
    полный readback.

## Как это работает (адреса для usbplug `rk3308_usbplug_v1.3` из OTA 44.90)

- Живая копия usbplug - в DRAM `0x03001000..0x0301DE18` (SRAM-копия на
  0x0 - мёртвый исходник).
- Предел чтения - uint32 @ `0x3343EC4`.
- Живые адреса двух `memset`-обработчиков: `0x3012290` и `0x3012390`.
- Перед патчем обязательна сверка сигнатур (`--loader-check`).

## Процедура (Capsule-14, ~40 с на 218 МБ)

1. Войти в MaskROM, из loader-режима (`330d`) - `xrock reset maskrom`.
2. Загрузить подписанный лоадер **родной платформы**:

   ```sh
   rkdeveloptool db loader_from_ota.bin
   ```

   После ре-энумерации проверьте `rkdeveloptool td` (OK = loader).
3. Снять полный дамп:

   ```sh
   python3 librerd.py --xrock ./xrock --limit-address 0x3343ec4 \
     --loader-check '0x03012290=29 c5 4e b9 1f 01 09 6b 02 03 00 54' \
     --loader-check '0x03012390=29 c5 4e b9 1f 01 09 6b c2 00 00 54' \
     --output <каталог>
   ```

Альтернативные способы снять реальный дамп (без librerd): из
работающего Linux через `/dev/rkflash0p*` (нужен root на плате) или
физический - программатором с распайкой NAND (крайний случай).
