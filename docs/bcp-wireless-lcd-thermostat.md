

# Bezdrátový LCD termostat

<!-- toc -->

## Popis funkce

Praktická ukázka jak s BigClown vytvořit základní termostat.
Tento projekt se skládá z bezdrátové části `remote` a základní jednotky `base`.
Remote jednotka je bateriově napájená.
V pravidelných intervalech měří teplotu a zobrazuje ji na svém displeji.
Na displeji lze taky nastavit enkodérem požadovanou teplotu.
Naměřená a nastavená teplota se bezdrátově posílá na base module každé 3 vteřiny.

Na base jednotce je power module s výkonovým relé, které sepne v případě, že je potřeba zapnout vytápění.

Zároveň je možné base jednotku připojit přes USB například do Raspberry Pi nebo jiného počítače a veškeré veličiny sledovat i na dálku.

![Remote node](images\bcp-wireless-lcd-thermostat\placeholder.jpg)


## Popis zařízení


1. Remote obsahuje
    * [Core Module](https://obchod.bigclown.cz/products/core-module)
    * [LCD Module](https://obchod.bigclown.cz/collections/moduly)
    * [Enkodér Module](https://obchod.bigclown.cz/collections/moduly)
    * [Battery Module](https://obchod.bigclown.cz/products/battery-module)

* Base obsahuje
    * [Core Module](https://obchod.bigclown.cz/products/core-module)
    * [Power Module](https://obchod.bigclown.cz/products/power-module)
    * Volitelně [Raspberry Pi](https://obchod.bigclown.cz/products/raspberry-pi-3-set) s [USB kabelem](https://obchod.bigclown.cz/products/usb2-0-cable-am-b-micro-0-6m) pro vzdálené sledování


## Firmware


Projekt nalzenete na GitHubu. Každý node má svou vlastní podsložku `base` a `remote`.

https://github.com/bigclownlabs/bcp-wireless-lcd-thermostat


## Sestavení a programování modulů


1. Sestavte remote module podle obrázku
* Sestavte base module podle obrázku
* Naprogramujte oba node firmwary z repozitářů z předchozí kapitoly. Můžete postupovat [podle návodu jak nastavit vývojové prostředí](core-module-setup.md) a [jak naprogramovat firmware](core-module-flashing.md).
* Po zapnutí je třeba oba moduly spárovat. Stiskněte na 3 vteřiny tlačítko `B` na base module. Rozbliká se rychle červená LED. Nyní je base module ve stavu párování. Remote module spárujete tak, že stisknete enkodér taky na 3 vteřiny (nebo tlačítko `B`).
* Nyní začnou z remote jednotky chodit rádiové pakety. Každý paket je signalizován krátkým bliknutím červené LED na Base Module.


## Integrace s Raspberry Pi


1. Připojte base module USB kabelem do Raspberry Pi.
* *TBD*


## Testování


Nyní vyzkoušejte funkci termostatu.
Enkodérem nastavte požadovanou teplotu na vyšší, než je aktuálně naměřená.
Na Base Module spne relé a vedle něj se rozsvítí zelená signalizační LED.

Poté nastavte enkodérem nižší teplotu, než je naměřená. Za pár vteřin signalizační LED na Base Module zhasne a relé vypne.


## Tipy a triky


Jednoduchou úpravou v kódu je možné projekt využít i na spouštění klimatizace. Nebo lze ke měření teploty využít senzoru na base module a na remote budete jen nastavovat požadovanou teplotu.
