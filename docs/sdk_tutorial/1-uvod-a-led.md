
# BigClown SDK #

BigClown SDK je modul�rn� API pro Core Module, kter� nab�z� pokro�il� funkce pro pr�ci s  periferiemi a hardwarov�mi moduly.

Bylo naps�no od nuly p�esn� na m�ru BigClown ekosyst�mu.
Pro dosa�en� co nejmen�� spot�eby energie a co nejni���ch hardwarov�ch n�rok� byla zvolena cesta objektov�ho programovac�ho modelu v jazyce C.
Ze stejn�ch d�vod� bylo tak� upu�t�no od RTOS a byl vytvo�en vlastn� pl�nova�.

Pokud jste n�kdy programovali Arduino (nadstavba C nazvan� Wiring), pak ur�it� naleznete spole�n� znaky s BigClown SDK.
Z d�vod� �spory energie ale hlavn� smy�ka neprob�h� neust�le dokola, ale vyu��v� se callback�, co� jsou funkce, kter� se zavolaj�, kdy� V�m SDK chce n�co ��ct.
N�kdy je to p�ijat� r�diov� zpr�va, nebo v�sledek m��en� teploty.
V okam�iku, kdy nen� t�eba nic vykon�vat se procesor na ur�it� �as usp� a probud� se v okam�iku, kdy p�ijde p�eru�en� ze senzoru, nebo z �asova�e.

V�echny moduly SDK si v tomto seri�lu pop�eme a vysv�tl�me.

Kde program za��n�?
V programovac�ch jazyc�ch m�te n�jak� vstupn� bod.
M��e to b�t funkce `main()`, `loop()`.
V BC SDK v jazyce C se k�d za��n� vykon�vat ve funkci `main()`.
Zde se ale prov�d� spousta syst�mov�ch nastaven� a �kol�.
Abychom pr�ci co nejv�ce zp�ehlednili a zp��jemnili, vytvo�ili jsme vstupn� bod u�ivatelsk� aplikace v `application_init()` v souboru `app/application.c`.

Obsah souboru `src/application.c` bez ��dn�ho dal��ho k�du vypad� tedy takto:

``` C
void application_init(void)
{
}
```

## LEDka ##

Poj�me na klasickou uk�zku rozblik�n� LED diody, kter� je na Core Module um�st�na.
Nejprve budu demonstrovat m�n� efektivn� zp�sob, kter� ale vysv�tl� z�klady pr�ce s GPIO piny.

``` C
void application_init(void)
{
    bc_gpio_init(BC_GPIO_LED);
    bc_gpio_set_mode(BC_GPIO_LED, BC_GPIO_MODE_OUTPUT);
}

void application_task()
{
    bc_gpio_toggle_output(BC_GPIO_LED);
	
	// N�sleduj�c� funkce prov�d� n�co, jako delay()
    bc_scheduler_plan_current_relative(500); // TODO: bude existovat delay()?
}
```

P�edchoz� uk�zka sice funguje, ale v bateriov�ch aplikac�ch n�m aktivn� �ek�n� ve funkci delay() nedovol� uspat procesor.
Nav�c blokuj�c� delay() ani nedovol� aby se b�hem �ek�n� prov�d�ly jin� �koly (tasky).

Poj�me p�edchoz� p��klad upravit tak, aby vyu��val maxim�ln� mo�nosti SDK.

``` C
bc_led_t led;

void application_init(void)
{
    bc_led_init(&led, BC_GPIO_LED, false, true);
    bc_led_set_mode(&led, BC_LED_MODE_BLINK);
}
```

V�sledn� k�d se smrskl do dvou ��dk� a vytvo�en� instance `bc_led_t led`.
Knihovna LED za n�s �e�� ve�kerou inicializaci a �asov�n� na pozad�.
Na pozad� si vytvo�� tak� vlastn� task, kter� b�� jen v okam�iku zm�ny stavu LED.
Po zbylou dobu je task deaktivn� a pl�nova� procesor usp�.

Blik�n� m��ete zrychlit, zpomalit, nebo LED �pln� vypnout.
Povolen� parametry pro funkci bc_led_set_mode naleznete v tabulce n�e.

| Enumerator | Popis |
| -----------|-------|
| BC_LED_MODE_OFF | LED has steady off state. |
| BC_LED_MODE_ON | LED has steady on state.|
| BC_LED_MODE_BLINK | LED blinks. |
| BC_LED_MODE_BLINK_SLOW | LED blinks slowly. |
| BC_LED_MODE_BLINK_FAST | LED blinks quickly.|
| BC_LED_MODE_FLASH | LED flashes repeatedly. |

Dal�� zaj�mav� funkce jsou:

| Funkce | Popis |
|  ---   |  ---  |
| bc_led_pulse | Jednou blikne LED na dobu ur�enou parametrem |
| bc_led_set_pattern | ��seln� parametr se pou�ije jako vzor pro rozblik�n� LEDky. Proch�zej� se jednotliv� bity parametru a pokud je bit v log. 1, pak se LED na rozsv�t�. Nap�. 0xF0F0FFF0 |
| bc_led_set_slot_interval | Ur�uje jak rychle se maj� jednotliv� bity z p�edchoz� funkce rychle posouvat. |

Na z�v�r tu m�m je�t� jednu h�danku. Zkuste p�ij�t na to, co za zpr�vu v morseovce vyblik�v� n�sleduj�c� p��klad. ��slo jsem z�m�rn� zapsal v des�tkov� soustav�, proto�e v hexadecim�ln� soustav� lze vzor blik�n� snadno odhalit.

``` C
bc_led_t led;

void application_init(void)
{
    bc_led_init(&led, BC_GPIO_LED, false, false);
    bc_led_set_slot_interval(&led, 200);
    bc_led_set_pattern(&led, 2849884800);
}
```
