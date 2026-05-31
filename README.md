
## Proiect: Sistem Autotune Live cu Interfață Web

#### Membrii echipei:
* Apostu Alina
* Buimac Delia Cristina
* Ignat Ionuț Sebastian
  
## Obiectivele Proiectului:
Obiectivul principal este realizarea unui sistem digital de procesare a semnalului audio în timp real, implementat pe platforma Raspberry Pi Pico 2 WH. Dispozitivul va capta vocea utilizatorului, va aplica un algoritm de corecție a tonalității (Autotune) și va reda sunetul procesat cu latență minimă.
## Utilitate practică: 
Sistemul poate fi utilizat ca procesor portabil de voce pentru artiști sau ca instrument educațional pentru înțelegerea procesării digitale a semnalelor și a protocoalelor de comunicație hardware.

## Cerințe Funcționale:
•	Captare Audio: Sistemul va capta vocea analogică prin intermediul unui modul microfon(MAX9814), prin utilizarea perifericului ADC al Raspberry Pi Pico 2W. Se va realiza eșantionarea semnalul la o rată constantă pentru a asigura fidelitatea sunetului.

•	Procesare Digitală în Timp Real: Corecția tonului (pitch correction) în timp real folosind procesorul RP2350 pentru a obține diferite efecte: Robot, Deep Voice/Chipmunk, Echo, etc.

•	Redare High-Fidelity: Sunetul procesat va fi trimis către un DAC extern (UDA1334A) prin protocolul de comunicație digitală I2S. Acesta va acționa elementul de execuție (boxe/căști), transformând datele digitale înapoi în unde sonore.

•	Control Utilizator: Activarea funcțiior Mute si Active prin butoane fizice;

•	Interfață Web: Panou de control în browser pentru selectarea instantanee a efectelor, monitorizarea Real-Time a stării (Mod Active/Mute) și controlul parametrilor(ex: intesitatea efectului aplicat) prin Wi-Fi.

•	Feedback Vizual(LED-uri): Semnalizarea stării sistemului prin coduri de culori:

  - **Verde**: Modul Active: confirmă alimentarea corectă a plăcii și starea Active, gata de recepționarea comenzilor a serverului Wi-Fi.
  - **Albastru**: Indică faptul că un efect este selectat din interfața Web (apare atunci când un efect este activ). Dacă LED-ul albastru nu este aprins, vocea rămâne în modul normal (fără efecte).
  - **Galben**: Indică prezența semnalului audio, rămâne aprins pe toată durata captării vocii
  - **Roșu**: Indică activarea funcției Mute.
 
---
## Cerințe Non-Funcționale:
•	Latență: Timpul de procesare (input-to-output) trebuie să fie sub 20ms pentru a fi imperceptibil urechii umane.

•	Eficiență: Utilizarea DMA (Direct Memory Access) pentru transferul datelor audio fără a bloca nucleele procesorului.

•	Fiabilitate: Accesarea și utilizarea interfeței web (schimbarea parametrilor) nu trebuie să blocheze sau să încetinească firul de execuție principal (thread-ul) responsabil de procesarea audio.

•	Calitate: Rata de eșantionare de minim 44.1kHz pentru o claritate acceptabilă a vocii(standardul pentru CD).

•	Filtrarea zgomotului: Algoritmul de detectare a pitch-ului trebuie să fie capabil să ignore zgomotul de fundal.

---

## Scenariu de Testare:
Titlu: Verificarea lanțului audio și a funcției de Mute.

| Pas | Acțiune Utilizator                   | Rezultat Așteptat                                                        |
|-----|--------------------------------------|--------------------------------------------------------------------------|
| 1   | Conectare alimentare USB             | LED-ul verde de Power se aprinde; Raspberry Pi Pico 2W pornește serverul web.           |
| 2   | Vorbire în microfon fără a utiliza efecte | Sunetul se aude în căști/boxe (redat prin DAC); LED-ul galben de semnal clipește. |
| 3   | Apăsare buton "Mute"                 | Sunetul în căști/boxe se oprește instantaneu; Se va aprinde LED-ul roșu. |
| 4   | Accesare interfață web de pe PC      | Interfața web afișează "Status: Muted".                                   |
| 5   | Dezactivare "Mute" din interfața web | LED-ul roșu se stinge, cel verde rămâne aprins; sunetul revine în boxe.   |
| 6   | Selectare efect "Echo"               | LED-ul albastru se aprinde.Se activează linia de întârziere (Delay Line); Vocea se aude cu repetiție. |
| 7   | Modificare slider "Intensitate Efect" | Se observă schimbarea adâncimii modulației sau a pitch-ului în timp real. |
| 9   | Verificare latență                   | Sunetul procesat este sincronizat cu mișcarea buzelor.                   |

## Schemă bloc:
<img width="1732" height="741" alt="image" src="https://github.com/user-attachments/assets/8ae6da08-804a-4e38-8299-eab2c7788c1b" />


## Schemă electrică:
<img width="2090" height="1438" alt="image" src="https://github.com/user-attachments/assets/37b7c43d-1de1-4e34-bd9d-e51cbcd82d79" />


## Schemă software:
<img width="6404" height="7284" alt="image" src="https://github.com/user-attachments/assets/530899fa-1023-4904-9d20-898dffa0ce1f" />





## Documentare foto:
<img width="1600" height="1200" alt="image" src="https://github.com/user-attachments/assets/033acd61-a7a4-4bf8-b9d5-08ff73e8675b" />

<img width="1536" height="2048" alt="image" src="https://github.com/user-attachments/assets/613ec32b-e93e-44df-9519-97a5b27f61e3" />






## Parametrii relevanți ai componentelor pentru integrarea lor în sistem:

### 1. Modul MAX9814
Parametrii relevanți pentru integrare:

•	Tensiune de alimentare: 2.7–5.5V, alimentat direct din 3.3V al Pico, fără regulator suplimentar

•	Ieșire analogică: 2Vpp, centrat la 1.25V DC bias (0.25V min si 2.25V max), compatibil direct cu intrarea ADC ului Pico (0–3.3V)

•	Gain configurat la 40dB

•	AGC activ: atac 1.1ms, revenire 4400ms (pin AR neconectat)

Argumentare:
Cipul Wi-Fi al Pico 2W induce zgomot electric pe linia ADC. S-a ales MAX9814 tocmai pentru funcția sa de Control Automat al Amplificării ACG, care previne clipping ul și atenuează automat interferențele. Gain-ul de 40dB, obținut prin conectarea pinului GAIN la VDD, asigură captarea clară a vocii de aproape fără distorsiuni. Bias-ul DC de 1.25V este anulat software prin algoritmul static_offset, eliminând necesitatea unui circuit de filtrare hardware suplimentar.

### 2. DAC UDA1334A
Parametrii relevanți pentru integrare:

•	Tensiune de alimentare: 2.4–3.6V, alimentat direct din pinul de 3.3V al Pico

•	Protocol de comunicare: I2S standard pe 3 fire (BCLK, WSEL, DIN)

•	Pin MUTE: conectat la un GPIO al Pico pentru control software instant

Argumentare:
UDA1334ATS a fost ales pentru compatibilitatea electrică directă cu Pico 2W (3.3V, fără convertor de nivel) și pentru suportul nativ al protocolului I2S, care este generat de blocul PIO al Pico. Pinul MUTE hardware permite oprirea instantanee a ieșirii audio din software, fără latență.

### 3. Raspberry Pi Pico 2W - procesor RP2350
Parametrii relevanți pentru integrare:

•	ADC intern: 12 bit, 48MHz clock pe pinul GP26, canalul 0

•	Programmable I/O: 2 blocuri hardware independente, capabile să genereze protocolul I2S direct în firmware

•	Alimentare: pin 36 (3V3) furnizează 3.3V atât pentru DAC, cât și pentru amplificatorul de microfon, butoane

•	Conectivitate: Wi-Fi integrat pentru interfața web

Argumentare:
Pico 2W  integrează într-un singur cip tot ce este necesar sistemului: conversie analog-digitală pentru captarea vocii, procesare semnal în firmware, generare protocol I2S prin PIO și conectivitate Wi-Fi pentru control remote. 

### 4. Condensatoare polarizate electrolitice
Parametrii relevanți pentru integrare:

•	Valoare: 100µF/componentă

•	Plasament: direct pe pinii VDD și GND ai MAX9814 și UDA1334A

Argumentare: Condensatoarele plasate direct pe pinii VDD–GND ai fiecărei componente acționează ca rezervoare locale de energie, absorb fluctuațiile si reduc zgomot electric.


## Referințe:

https://cdn-shop.adafruit.com/product-files/3678/UDA1334ATS.pdf

https://cdn-learn.adafruit.com/downloads/pdf/adafruit-i2s-stereo-decoder-uda1334a.pdf

https://cdn-shop.adafruit.com/datasheets/MAX9814.pdf

https://cdn-learn.adafruit.com/downloads/pdf/adafruit-agc-electret-microphone-amplifier-max9814.pdf

https://pip-assets.raspberrypi.com/categories/1088-raspberry-pi-pico-2-w/documents/RP-008304-DS-1-pico-2-w-datasheet.pdf?disposition=inline

https://pip-assets.raspberrypi.com/categories/1214-rp2350/documents/RP-008373-DS-2-rp2350-datasheet.pdf?disposition=inline

https://github.com/raspberrypi/pico-examples/tree/master/pico_w/wifi/access_point/dhcpserver





