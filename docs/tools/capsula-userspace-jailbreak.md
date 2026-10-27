# capsula-userspace-jailbreak

> Репозиторий:
> [librecapsula/capsula-userspace-jailbreak](https://github.com/librecapsula/capsula-userspace-jailbreak)
> (Rust, MIT). CLI: `capsula-jb`.

Джейлбрейк перезаписью userspace: раздел `userdata` не подписан - в него
кладётся оверлей с фабричным `adbd`, стоковая прошивка продолжает
работать как есть. Остальные разделы не изменяются, dm-verity и
голосовой стек живут как в стоке. Подробнее про метод -
[Джейлбрейк: userspace](../firmware/jailbreak.md#userspace).

## Поддерживаемые платы

- **Capsule-14, Capsule-15** и **Capsula Мини на Rockchip RK3308B**
  (ранние ревизии `CapsulaDot_Main_V1.0`) - полный сценарий. Лоадер
  один на все платы: подписанный usbplug из OTA Capsule-14, скачивается
  и кешируется автоматически.
- **Capsula Мини на Amlogic A113X** (`MRC02B_Main`, `1b8e:c003`) -
  распознавание платы, bring-up в download-режим u-boot и passthrough
  команд u-boot (`capsula-jb aml`); сценарий прошивки в разработке.

## Установка

Готовые сборки - в [Releases](https://github.com/librecapsula/capsula-userspace-jailbreak/releases):
Windows (x64, с установщиком USB-драйверов Rockchip), Linux (x64/arm64),
macOS (x64/arm64). На macOS при блокировке Gatekeeper:
`xattr -d com.apple.quarantine capsula-jb`. На Linux для работы без
root - udev-правила из `resources/udev/`.

Из исходников: `cargo build --release` (нужен Rust и C-компилятор -
lwext4 собирается через `cc`).

## Быстрый старт

```sh
capsula-jb          # полный сценарий: дамп → оверлей → запись userdata
adb shell           # root по USB, без авторизации

capsula-jb backup   # только дампы, без записи на плату
```

ADB доступен **и по USB, и по Wi-Fi одновременно**: наш adbd слушает
USB-гаджет и TCP:5555. Узнайте IP колонки и:

```sh
adb connect 192.168.1.42:5555
adb shell
```

## Сценарий (команда по умолчанию)

1. **Ожидание платы** - следит за шиной; что подключится (Rockchip в
   download-режиме или Amlogic-мини), тот путь и выбирается.
2. **Лоадер** (Rockchip): скачивается OTA Capsule-14, из неё - подписанный
   лоадер (кеш `ota/loader.bin`).
3. **Плата**: usbplug загружается в RAM (флеш не трогается), сигнатуры
   сверяются в DRAM.
4. **Дамп**: RAM-патчем поднимается предел чтения usbplug (обход маски
   `0xCC` выше 32 МиБ), снимается полный дамп всех GPT-разделов в
   `backup/` с SHA256-суммами.
5. **Оверлей**: на основе копии дампа userdata собирается образ с
   файлами в `/overlay/upper` (ext2 правится
   [ext4-lwext4](https://crates.io/crates/ext4-lwext4)).
6. **Запись**: собранный userdata пишется в раздел - единственная
   запись во флеш за сценарий.
7. **Перезагрузка**: ожидание устойчивого adb-гаджета `2207:0006`.

Откат - сброс к заводским настройкам (вычищает `/data` с оверлеем).

## Полезное

- `--wait SEC` - таймаут ожидания платы (0 = бесконечно, по умолчанию);
- `--yes` - не спрашивать подтверждение перед записью userdata
  (автоматизация);
- `--format-userdata` - не патчить дамп, а отформатировать userdata как
  чистый ext2 и положить только оверлей;
- `capsula-jb monitor` - живой лог событий download-режимов на шине
  (`2207:*` Rockchip и `1b8e:c003` Amlogic);
- `capsula-jb aml "<u-boot команда>"` - passthrough команд u-boot на
  Amlogic-мини (окно ~1 с, перед каждым запуском передёргивать
  питание).

Каталоги данных: `ota/` - OTA-пакеты и лоадер (кеш), `backup/` - дампы
разделов (`NN_имя.img`, `partition_table.tsv`, `SHA256SUMS.txt`),
`work/` - промежуточные образы.
