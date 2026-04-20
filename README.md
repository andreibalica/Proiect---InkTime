# InkTime Smartwatch - Documentatie Hardware

Acest repository contine fisierele de proiectare hardware (scheme electronice, layout PCB) pentru InkTime, un smartwatch open-source. Proiectul este axat pe eficientizarea consumului energetic si integrarea unui afisaj bi-stabil.



## 1. Principiu de Functionare si Eficienta Energetica

Sistemul elimina limitarile de autonomie specifice smartwatch-urilor clasice prin utilizarea unui ecran tip E-Paper (electroforetic). 

Datorita proprietatii de bi-stabilitate, matricea display-ului isi pastreaza starea fara a necesita alimentare continua. Consumul de energie are loc exclusiv pe durata ciclului de refresh (schimbarea informatiei afisate). Aceasta caracteristica tehnica permite microcontroller-ului sa ramana in modul "Deep Sleep" pe perioade lungi, asigurand o autonomie extinsa si eliminand totodata necesitatea iluminarii de fundal (Backlight).

## 2. Subsisteme Componente

Arhitectura sistemului este impartita in urmatoarele blocuri functionale majore:

* **Microcontroller (MCU):** Implementat in jurul SoC-ului nRF52840 (Nordic Semiconductor). Ofera suport nativ pentru protocolul Bluetooth 5.0 (BLE) si asigura procesarea generala, gestionand atat magistralele de date, cat si modurile de operare ultra-low power.
* **Power Management Unit (PMU):**
  * **Circuit de Incarcare:** Controller dedicat pe portul USB-C, echipat cu protectie la supratensiune si descarcari electrostatice (ESD / diode TVS).
  * **Regulator DC/DC:** Ofera o tensiune stabilizata de 3.3V necesara liniilor digitale, compensand variatiile de tensiune ale acumulatorului LiPo.
  * **Fuel Gauge:** Integrat de masurare a capacitatii reziduale (State of Charge). Citeste datele celulei LiPo fara a necesita un rezistor de shunt si raporteaza valorile prin I2C.
* **Senzoristica si Interfata Utilizator:**
  * **IMU (Inertial Measurement Unit):** Senzor accelerometric utilizat pentru numararea pasilor si detectia gestului de activare a ecranului.
  * **Driver Haptic:** Asigura comanda prin PWM a motorului de vibratii pentru generarea alertelor tactile.
  * **Input Hardware:** Trei butoane mecanice conectate pe porturile GPIO pentru operarea dispozitivului.

## 3. Interfete de Comunicatie

Comunicatia in interiorul sistemului este structurata pentru a separa transferul de date rapid de citirea senzorilor:

* **Magistrala SPI:** Alocata strict modulului E-Paper. Faciliteaza transferul rapid de date catre controller-ul de display pe parcursul refresh-ului.
* **Magistrala I2C:** Utilizata ca port comun de comunicatie pentru senzorul IMU, integratul Fuel Gauge si driver-ul haptic.
* **Semnale EXTI (Intreruperi):** Butoanele mecanice si pinul de interrupt al senzorului IMU trezesc in mod direct microcontroller-ul din starea de repaus atunci cand se inregistreaza un eveniment fizic.

## 4. Note de Fabricatie si Asamblare (PCBA)

Acest repository include fisierele de productie (Gerber / Pick and Place) si lista de materiale (BOM) necesare pentru fabricarea placii. Pentru a garanta functionarea la parametri optimi a ansamblului, procesul de productie trebuie sa respecte urmatoarele cerinte tehnice:

* **Grosime Substrat:** Placa de baza (PCB) trebuie fabricata cu o grosime stricta de 1.0mm. Utilizarea grosimii standard de 1.6mm va compromite asamblarea mecanica in interiorul carcasei printate 3D.
* **Optimizare PCBA:** Designul a fost conceput pentru a facilita productia automatizata. Toate componentele SMD sunt plasate exclusiv pe stratul superior (TOP Layer), reducand atat complexitatea, cat si costurile procesului de asamblare.
* **Integritate RF si Rutare:** S-a implementat tehnica de "via stitching" intre planurile de masa (TOP si BOTTOM) in vecinatatea circuitului radio. Acest detaliu este critic pentru performanta antenei ceramice la 2.4GHz. De asemenea, erorile de tip "Board Outline Clearance" din zona antenei sunt intentionate, antena necesitand lipsa totala a cuprului pe layerele adiacente.