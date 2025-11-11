## Interfacce

Un'interfaccia è l'insieme di funzionalità che viene offerto da ogni livello di una architettura.

Le interfaccie sono divise in due tipi:
- Interfaccie di servizio: API che vengono offerte ai livelli superiori
- Interfaccie peer: che servono a comunicare tra i servizzi allo stesso livello


> [!info] Importante
> È importante sottolineare come in ogni architettura le interfaccie sono uguali, questo permette a tutti gli host di poter comunicare liberamente

---
## Protocolli

Un protocollo è un insieme di regole che servono a definire come devono essere le interfaccie, in particolare come i servizzi vengono offerti e utilizzati.

## Architettura ISO/OSI

![[Architettura_ISO_OSI.excalidraw|1000]]

Il trasport service è implementato dai sistemi operativi mentre l'upper layer gestito dalle applicazioni (_user space_)

Dei tre livelli:
- la parte in verde è implementata su tutti i dispositivi
- la parte in giallo ed in rosso sono presenti sugli host finali

