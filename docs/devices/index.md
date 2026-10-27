# Устройства

Карточки устройств проекта. Каждая карточка собирает всё известное об
устройстве: железо, ревизии, вход в режимы загрузки, прошивки и
поддержку инструментами.

| Устройство | Платформа | Статус изученности |
|---|---|---|
| [Капсула](capsula.md) (Capsule-14/15) | Rockchip RK3308 | полностью изучена |
| [Капсула Мини](capsula-mini.md) (Capsula-mini-2/4/5/6) | RK3308B · Amlogic A113X | mini-2 изучена, остальное - TODO |
| [Капсула Нео](capsula-neo.md) (capsula-neo-1) | BES TODO | заготовка |
| [Mi Smart Speaker](mi-smart-speaker.md) (mi-smart-speaker-2) | Amlogic A113X | заготовка |
| [Prestigio SmartVoice](prestigio-smartvoice.md) (prestigio-smartvoice-1) | RK3308 | заготовка |

## USB-идентификаторы

| VID:PID | Что это |
|---|---|
| `2207:330d` | Rockchip, режим loader (rockusb) |
| `2207:330e` | Rockchip, MaskROM |
| `2207:0006` | ADB-гаджет (сток мигает им <1 с; после джейлбрейка - постоянно) |
| `1b8e:c003` | Amlogic, BootROM/u-boot download-окно |

!!! note "330d/330e не всегда говорят сами за себя"

    На RK3308 загруженный usbplug может ре-энумерироваться с тем же PID,
    что и MaskROM. Отличить режимы можно так: `rkdeveloptool td`
    (ответ OK = loader-режим) или iProduct «USB-MSC» в `lsusb -v`
    (= usbplug уже загружен).

Платформенные страницы: [SoC RK3308](../hardware/rk3308.md),
[Отладочный UART](../hardware/uart.md),
[Разделы флеша](../hardware/partitions.md).
