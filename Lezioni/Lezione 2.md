## Performance

La _performance_ di una rete viene misurata attraverso:
- Il _throughput_: il numero di bit che vengono inviati ogni secondo.
- La _latency_: il tempo che passa dall'invio dei dati al loro effettivo arrivo.

> [!warning]
> Più dati vengono mandati più aumenta il ritardo, specialmente in reti a commutazione a pacchetto.

### Definizione di bandwidth

Le onde sinusoidali sono composte da:
- Ampiezza ($A$), viene solitamente espressa in Volt e influenza quando l'onda è alta;
- Frequenza ($f$), espressa in Hertz (1/s) rappresenta quanto velocemente l'onda oscilla / si ripete;
- Fase ($\varphi$) quanto l'onda è spostata verso destra/sinistra.

La frequenza può essere scritta anche come l'inversa del tempo, $T = \frac1f$.

Una sinusoide è quindi definita come:
$$
x(t) = A \sin (2 \pi\ f\ t + \phi)
$$
![[Sinusoide.excalidraw|1000]]

Secondo la definizione di Fourier ogni segnale $x(t)$ periodico è definito dalla sommatorie di infinite sinusoidi:
$$
x(t) = \sum_{i=1}^{\infty} x_i(t) = \sum_{i=1}^{\infty} A_i \sin (2 \pi\ f\ t + \phi_i)
$$

Inoltre, studiare il segnale secondo le sue componenti ci porta a rappresentare il segnale come una sommatoria di tante sinusoidi, ognuna delle quali delinea un valore sull'ampiezza.

![[Bandwidth.excalidraw|1000]]

La bandwidth è quindi la parte occupata dal segnale nello spazio delle frequenze e viene calcolata tramite la differenza tra le frequenze massime e quelle minime (con ampiezza non nulla).

> [!info]
> L'insieme delle frequenze che compongono un segnale periodico è detto spettro delle frequenze.

### Latenza

