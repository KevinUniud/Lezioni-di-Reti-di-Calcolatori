## Link e frequenze

Un _link_ è un qualsiasi mezzo che permette di far comunicare due dispositivi. 

Ogni mezzo di comunicazione ha una banda in cui può trasmettere il segnale.

| Mezzo trasmissivo          | Bandwidth         |
| -------------------------- | ----------------- |
| Doppino intrecciato (rame) | 0 - 600 MHz       |
| Cavo coassiale             | 0 - 500 MHz       |
| Fibra ottica               | 186 - 375 THz     |
| Onde radio                 | 3 KHz - 300 GHz   |
| Infrarosso                 | 300 GHz - 400 THz |

La distanza tra cui si ripete l'onda del segnale è chiamata _wavelenght_, espressa in metri e calcolata come:
$$
\frac{\textnormal{velocità della luce sul mezzo trasmissivo}}{\textnormal{frequenza}}
$$

> [!example]
> Un cavo di rame ha frequenze tra i 300Hz e 3300Hz
> La wavelenght per 300Hz su un cavo di rame è:
> $$\frac{\textnormal{velocità della luce sul rame}}{\textnormal{frequenza}} = \frac{2 \times 10^8}{300}=667\times10^3 \textnormal{m}$$

## Codifica e simboli

L'inserimento di dati in un segnale si chiama _encoding_.

Un simbolo è l'unità elementare di informazione trasmessa in un canale, rappresentata da un determinato stato del segnale (ampiezza, fase o frequenza), inoltre, può essere composto da più bit.

### Teorema di Nyquist-Shannon

Il massimo numero di simboli al secondo è definito come _Baud rate_ e viene espresso come simboli al secondo ($R$).

> [!important] Teorema del campionamento di **Nyquist-Shannon**
> Un canale con una certa larghezza di banda ($B$), indifferentemente dalle frequenze a cui opera, può trasmettere al massimo il doppio di simboli rispetto alla larghezza di banda, $R = 2B$.

Dato un segnale con una certa frequenza massima, per campionarlo, trasmetterlo e ricostruirlo correttamente, è necessario prelevare campioni a intervalli regolari con una frequenza almeno doppia rispetto alla massima frequenza del segnale. In questo modo si ottiene una serie di campioni sufficienti a ricostruire completamente l’onda originale.

La presenza di almeno due punti per periodo permette di interpolare matematicamente l'onda originale.

In un segnale ogni simbolo può portare $\lceil\log_2 (\Sigma)\rceil$ bit.

Per codificare un segnale si prende il range di tensione tra $-V$ e $+V$ e lo si divide nel numero di livelli desiderato, successivamente si assegna ad ogni livello una tupla di bit, il primo corrisponderà a tutti i bit a 0, il successivo a tutti i bit a 0 tranne l'ultimo che sarà ad 1 e così via fino ad ottenere tutte le possibili combinazioni.

Per trasmettere una tupla di bit al codificatore bastarà impostare la tensione al livello stabilito.

Il _noise_ ($N$) è un segnale casuale che si aggiunge al segnale ($S$), se il rumore è troppo forte un simbolo può essere modificato e quindi interpretato in modo errato dal decodificatore.

