# Инструменты

Инструменты сообщества для работы с Capsula. Протоколы реализованы
нативно, libusb/root не нужны (на Linux для работы без root ставятся
udev-правила).

| Инструмент | Язык | Назначение |
|---|---|---|
| [capsula-userspace-jailbreak](capsula-userspace-jailbreak.md) | Rust | джейлбрейк перезаписью userdata + дампы; распознавание Amlogic-мини, passthrough u-boot |
| [librerd](librerd.md) | Python | полный readback флеша: обход лимита чтения RockUSB патчем в RAM, без записи во флеш |
