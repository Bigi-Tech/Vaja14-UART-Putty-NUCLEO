# Vaja 14 - UART serijska povezava s Putty in Nucleo L476RG

## 1. Cilj naloge
S pomočjo programskega okolja STM32CubeIDE in HAL knjižnicami sprogramirati ARM mikroprocesor tako, da bo preko serijskega vmesnika UART pošiljal podatke (preko USB) računalniku na odjemalec Putty.

## 2. Odgovori na vprašanja v Readme
* **Katere dva mostička bi morali odspajkati in kateri dve povezavi pospajkati, da bi lahko uporabili pina Tx/D1 in Rx/DO?**
  * **Odgovor:** 13 in 14, 62 in 63 (video od 0:45 naprej).
* **V področju Connectivity aktivirajte protokol USART2 kot asinhroni. Katera dva pina sta se pobarvala zeleno oz. se aktivirala?** 
  * **Odgovor:** PA2 in PA3.

## 3. Komentar na delovanje
Program omogoča asinhrono serijsko komunikacijo mikrokrmilnika z računalnikom preko vmesnika USART2, ki je nastavljen na hitrost 115200 Bits/s. Na razvojni plošči smo aktivirali pin PA5 kot digitalni izhod (za zeleno LED diodo) in pin PC13 kot digitalni vhod (za uporabniški gumb USER). 

Glavna funkcionalnost programa se izvede ob pritisku na uporabniški gumb. Program takrat prebere stanje vhoda in zgolj ob pritisku preko USART protokola pošlje besedilno polje (najini imeni) na terminal odjemalca Putty.

## 4. Posnetek delovanja
<a href="https://youtu.be/FtbXlniprVI">Posnetek</a>

## 5. Sprememba kode (Pošiljanje samo ob pritisku na tipko USER)
Za pošiljanje podatkov samo ob pritisku na tipko USER, smo v neskončni `while(1)` zanki v `main.c` uporabili pogojni stavek, ki preverja stanje pina PC13:

```c
/* Preverjanje stanja gumba na pinu PC13 */
if (HAL_GPIO_ReadPin(GPIOC, GPIO_PIN_13) == GPIO_PIN_RESET) {
    /* Če je gumb pritisnjen, pošlji podatke (najini imeni) */
    HAL_UART_Transmit(&huart2, myTxData, st_znakov, 10);
    
    /* Vklop/izklop zelene LED diode in zakasnitev za "debounce" oz. preprečevanje večkratnega pošiljanja */
    HAL_GPIO_TogglePin(GPIOA, GPIO_PIN_5);
    HAL_Delay(500); 
}
