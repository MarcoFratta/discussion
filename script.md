# Script per la Discussione della Tesi
**Candidato:** Marco Frattarola
**Tempo:** 12 Minuti

---

## Slide 1: Titolo
**Tempo:** 0:00 - 0:30

Buongiorno a tutti.

Il titolo del mio lavoro di tesi è **"Type-Safe Complex Specifications in Kotlin: a DSL to Configure the Alchemist Simulator."**

Il lavoro è stato svolto sotto la supervisione del Professor Gianluca Aguzzi e del Professor Danilo Pianini.

---

## Slide 2: Obiettivi (Goals)
**Tempo:** 0:30 - 1:10

L'obiettivo principale di questa tesi è stato analizzare i benefici e le sfide dell'adozione di un **Domain-Specific Language (DSL) type-safe** per la configurazione di sistemi software complessi.

Sebbene l'industria software sia ormai convergente su formati non tipizzati come YAML, JSON e XML, questa tesi mira a dimostrare come le moderne funzionalità di alcuni linguaggi di programmazione possano conciliare i benefici di un linguaggio type safe con le esigenze di estensibilità e modularità di sistemi complessi.

Per validare questo studio, il secondo obiettivo è stato fornire un'implementazione concreta: ovvero sostituire il sistema di configurazione legacy del simulatore **Alchemist**, con un dsl in Kotlin.

---

## Slide 3: Background: Domain-Specific Languages
**Tempo:** 1:10 - 1:45

Un **Domain-Specific Language (DSL)** è un linguaggio di programmazione con una espressività limitata, focalizzato su uno specifico dominio applicativo.

A differenza dei linguaggi General-Purpose, i DSL permettono di colmare il divario semantico tra il problema e l'implementazione. Si concentrano su **cosa** il sistema deve fare (l'intento) piuttosto che su **come** implementarlo. 
---

## Slide 4: Background: Alchemist
**Tempo:** 1:45 - 2:30

Il caso di studio per questa tesi è  **Alchemist**, un simulatore per aggregate e pervasive computing.

Alchemist è un simulatore **estensibile** e che permette di gestire simulazioni complesse. Si basa su un meta-modello — mostrato a destra — che puo essere adattato a diversi domini (come la biologia o la robotica) attraverso specifiche **incarnazioni**.

Attualmente, queste simulazioni complesse vengono configurate utilizzando **file YAML**.

---

## Slide 5: Motivazione
**Tempo:** 2:30 - 3:00

Questa dipendenza da YAML introduce criticità significative.

In primo luogo, YAML è **non tipizzato** (untyped). È un linguaggio di serializzazione dati ed è pensato per essere leggibile da umani. Di conseguenza, non ha alcuna conoscenza del modello di dominio sottostante. Questo comporta che la validazione delle configurazioni avviene solo a runtime, il che rende difficile la diagnostica degli errori.

In secondo luogo, mancano meccanismi per il riutilizzo del codice e la modularità. Man mano che gli scenari di simulazione crescono in complessità, i file YAML diventano monolitici e difficili da mantenere.

L'utilizzo di un **DSL type-safe** può risolvere questi problemi spostando il rilevamento degli errori dal runtime al compile-time e permettendo di sfruttare il supporto degli IDE (autocompletamento, refactoring e documentazione) per la riusabilità e la modularità.

---

## Slide 6: Esempio - YAML (Verbosità)
**Tempo:** 3:00 - 3:30

Ad esempio, consideriamo questa configurazione YAML reale per una simulazione all'interno Laguna di Venezia.

Come potete vedere, definire il perimetro della laguna richiede un gran numero di coordinate, il che rende la configurazione poco leggibile.

Se più simulazioni richiedono lo stesso perimetro, è necessario copiare e incollare le coordinate. Un futuro cambiamento nel perimetro della laguna richiederebbe di modificare tutte le simulazioni.

---

## Slide 7: Esempio - YAML (L'Errore di Indentazione)
**Tempo:** 3:30 - 4:15

In questo esempio, invece, possiamo vedere come un singolo errore di indentazione può portare a risultati diversi. 

Nella configurazione a sinistra, abbiamo una configurazione corretta.
Nella configurazione a destra, si è verificato un singolo errore di indentazione: un tab extra.

Poiché YAML si basa sull'indentazione per definire la struttura, il parser interpreta il `network-model` come una proprietà dell'`environment` anziché come un componente di primo livello.

Entrambi i file sono sintatticamente validi, ma quello a sinistra produrrà una simulazione con un modello di rete come quello specificato, mentre quello a destra produrrà una simulazione senza un modello di rete, senza produrre errori.

Un DSL type-safe renderebbe questo errore strutturalmente impossibile.
---

## Slide 8: Requisiti
**Tempo:** 4:15 - 4:50

Per lo sviluppo del nuovo sistema, sono stati stabiliti i seguenti requisiti, suddivisi in funzionali e non funzionali:

Dal punto di vista **Funzionale**, la soluzione deve garantire:
* [cite_start]**Equivalenza:** Una configurazione in YAML deve produrre un modello di simulazione identico del suo equivalente in DSL.
* [cite_start]**Integrazione:** Il nuovo sistema deve integrarsi con il sistema di caricamento esistente, e supportare sia il caricamento di file YAML che script Kotlin .kts.
* [cite_start]**Type Safety:** Il nuovo sistema deve sfruttare il type-system di Kotlin per garantire la validità sintattica e semantica delle configurazioni.

Dal punto di vista **Non funzionale**, abbiamo dato priorità a:
* [cite_start]**Usabilità:** Il DSL deve essere intuitivo e garantire una buona esperienza lato sviluppatore.
* [cite_start]**Estensibilità:** Il DSL deve supportare nuove estensioni di Alchemist senza richiedere modifiche al core del DSL.
* [cite_start]**Performance:** Il nuovo sistema deve mantenere prestazioni comparabili a quello esistente.

---

## Slide 9: Kotlin DSL
**Tempo:** 4:50 - 5:20

La soluzione che ho sviluppato è un **Internal DSL basato su Kotlin**.

[cite_start]Utilizzando Kotlin come linguaggio host del DSL, è possibile istanziare direttamente gli oggetti di dominio di Alchemist.

Il nuovo sistema supporta la **Batch Execution** (esecuzione in batch), cioe la possibilità di generare diverse simulazioni con diversi paramteri a partire da una singola configurazione, tramite l'utilizzo un modello di valutazione *lazy* (differita).
 Inoltre, offre gli stessi meccanismi di modularità e riuso di un linguaggio di programmazione, permettendo agli utenti di definire funzioni e variabili per riutilizzare il codice. Gli utenti possono anche definire una sintassi personalizzata per i loro domini specifici.

## Slide 10: Kotlin DSL — Esempio (Modularità)
**Tempo:** 5:20 - 6:10
Questo è un esempio di configurazione scritta con il nuovo DSL.

Qui vengono definite le variabili che verranno utilizzate per generare simulazioni con diverse combinazioni di parametri.

Qui invece viene definito il comportamento di una simulazione, con una sintassi tipica dei DSL in Kotlin.

## Slide 11: Kotlin DSL — Esempio (Modularità)
**Tempo:** 6:10 - 7:00

In questo esempio vediamo come il DSL fornisce strumenti per la personalizzazione e l'astrazione della sintassi per i loro domini specifici.

**A sinistra**, possiamo vedere come viene definita una funzione personalizzata `lagoon(500)`. Si puo notare come a differenza del file YAML visto in precedenza, dove bisognava fare copia-incolla delle coordinate, qui possiamo incapsulare la logica di deployment complessa in funzioni riutilizzabili.

**A destra**, invece un esempio che mostra come all'interno del DSL si possono usare le strutture di controllo standard di Kotlin, come cicli `for` e istruzioni `if` per definire gestire in maniera più flessibile le configurazioni più complesse.

---

## Slide 12: Kotlin Symbol Processing (KSP)
**Tempo:** 7:00 - 8:15

Una sfida maggiore è stata l'**Usabilità** e la **Estensibilità**. Per evitare gli utenti dovessero scrivere codice che richiedesse le gestione manuale di dipendenze interne al sistema, ho implementato un processore che scansiona il codice sorgente di Alchemist alla ricerca di componenti annotati con una specifica annotazione.

come mostrato nell'esempio, annotando la classe Point con una certa annotazione, il processore generata automaticamente una funzione helper che permette di istanziare l'oggetto Point senza doverlo fare manualmente.

L'utente può in questo modo semplicemente scrivere `deploy(point(0.0, 0.0))`, e il codice generato inietta automaticamente le dipendenze tecniche — come l'environment — dal contesto.

Oltre a migliorare drasticamente l'usabilità, questo approccio permette anche di rendere il DSL estensibile: un utente può aggiungere un *nuovo* componente, annotarlo e usarlo immediatamente nel DSL.

---

## Slide 13: Validazione
**Tempo:** 8:15 - 9:00

Per validare la soluzione, ho implementato un approccio di testing, che si basa su due tipi di test:

1.  [cite_start]**Unit Testing:** Per il funzionamento dei meccanismi architetturali core del DSL.
2.  **Test di Equivalenza:** Ho sviluppato un framework di test che carica un file YAML e il suo equivalente DSL, esegue entrambi e confronta lo stato interno della simulazione. Il confronto viene effettuato sia a livello statico, sia dopo aver eseguito la simulazione per un certo numero di step. Questo test è fondamentale per garantire che il DSL produca un modello di simulazione identico a quello del loader YAML.
3.  [cite_start]**Performance:** Abbiamo verificato che il tempo di caricamento rimanga entro limiti accettabili rispetto al sistema legacy.

---

## Slide 14: Confronto
**Tempo:** 9:00 - 10:30

[cite_start]Ecco il confronto finale tra i due approcci[cite: 887].

**A sinistra (YAML):** Abbiamo una struttura piatta dove le classi sono stringhe e i parametri sono liste non tipizzate.

**A destra (Kotlin DSL):** Abbiamo un programma strutturato, tipizzato e dove non è possibile sintatticamente definire degli elementi dove non dovrebbero essere. 
---

## Slide 15: Conclusioni
**Tempo:** 10:30 - 11:30

Per concludere, questa tesi dimostra che è possibile sostituire formati di configurazione non tipizzati con DSL type-safe, anche per sistemi complessi ed estensibili come Alchemist.

Tramite l'utilizzo di KSP, il sistema riesce poi a fornire una esperienza di sviluppo migliore, e la possibilità di supportare future estensioni.

 **Sviluppi futuri**, possono includere la sviluppo di un **convertitore da YAML a DSL** per facilitare la migrazione, e di estendere il DSL per supportare sintassi personalizzate per i programmi specifici delle incarnazioni.

---