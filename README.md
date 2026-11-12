# SIGNALDuino uC v4.2.3 (development version) for Maple Mini and ESP32 with multi cc1101 and xFSK support

### Getting started

System to receive digital signals and provide them to other systems for demodulation.

Firmware files  are in https://github.com/Ralf9/SIGNALDuino/releases

Compile it or use the and have fun.  
- If you are using the Arduino IDE, you have to copy the folder "SIGNALduinoAdv" with its contents into your sketch folder and edit the file compile_config.h 
 - If you use LAN, you need the Ethernet Library
 - If you use the ESP32, you need the WifiManager by tablatronix/tzapu
- If you are using platformio, you have to copy the files "extra_script.py", "platformio.ini" and the folder "SIGNALduinoAdv" with its contents into your project folder. 

### Fork Reinhard-M: 4.2.3-wMBusFix

Dieser Zweig (`dev-r423_cc1101`) entspricht Ralf9s 4.2.3 mit zwei Ergänzungen:

- **wM-Bus-Fix** in `mbus.h`: Der Empfänger wird nicht mehr neu gestartet, solange ein Paket läuft. Vorher gingen dadurch je Stick 30–50 % der Telegramme ohne Meldung verloren.
- **Befehl `B`**: `BR` startet den Stick neu, `BS` springt in den STM32-ROM-Bootloader am Debug-Port (USART1). So kann ein Stick hinter einer seriellen Bridge per `stm32flash` aktualisiert werden.

Bei jedem Push auf diesen Zweig baut GitHub Actions die Firmware und legt ein Release `v4.2.3-wMBusFix-<Nummer>` an:

| Datei | für |
|---|---|
| `Maple_sduino_USB_…bin` | Maple-SIGNALduino am USB (z. B. USB-Sduino) |
| `Maple_cul_USB_…bin` | Maple am USB mit CC1101 in CUL-Belegung (SPI an Pin 4/5/6, CS an Pin 7) |
| `Maple_sduino_serial_…bin` | Maple-SIGNALduino an einer seriellen Bridge (z. B. LAN156) |
| `Maple_sduino_LAN_…bin` | Maple-SIGNALduino mit W5500-LAN (z. B. LAN134) |

Erkennt der Stick nach dem Flashen kein Funkmodul (`V` zeigt `R: a-`, die Meldung lautet `detect A: timeout, no cc1101`), passt die Belegung nicht. Dann die jeweils andere Variante (sduino ↔ cul) flashen.

#### Flashen am PC mit dfu-util (Maple-Bootloader 2.0)

Den Maple per USB an den PC anschließen. Das gilt auch für die serielle und die LAN-Variante, denn der Bootloader arbeitet immer über USB.

```
dfu-util -w -d 1eaf:0003 -a 2 -D Maple_sduino_USB_423wMBusFix_261112.bin -R
```

Danach die Reset-Taste am Maple drücken. `-w` wartet, bis der Bootloader erscheint (er ist nach einem Reset nur etwa 1,5 s aktiv). Nach `Download done.` startet der Stick mit der neuen Firmware.

- `-d 1eaf:0003` – USB-Kennung des Maple-Bootloaders
- `-a 2` – Flash ab 0x08002000 (Bereich hinter dem Bootloader)
- `-R` – Stick nach dem Schreiben neu starten

Klappt das Timing nicht: die Taste **BUT** gedrückt halten, kurz **RESET** drücken und BUT erst loslassen, wenn die LED dauerhaft schnell blinkt. Dann bleibt der Bootloader aktiv, bis geflasht wird. Mit `dfu-util -l` lässt sich prüfen, ob `[1eaf:0003] … alt=2` sichtbar ist.

Hinweis: Mit dem ersten Flashen ändert sich der USB-Name des Sticks (z. B. von `MAPLEMINI_F103CB` auf `MAPLE_MINI_B20`). Programme, die den Port über `/dev/serial/by-id/…` öffnen, brauchen dann den neuen Namen.

### Using SIGNALDuino in FHEM

If you want to use the SIGNALDuino with FHEM, you can use it directly from FHEM. No need to compile any sourecode.
You find more Information here: https://wiki.fhem.de/wiki/Maple-SignalDuino  
https://forum.fhem.de/index.php/topic,106278.0.html

### Tested microcontrollers

* STM32 F103CBT6 (Maple Mini, bootloader v2.0)
* ESP32 (DEVKIT V1, D1 mini) 

### Signal from my device ist not detected

We have a pattern detection engine, that detect serval signal types. May not all, but most of them.

