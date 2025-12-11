# Script per la Discussione della Tesi
**Candidato:** Marco Frattarola
**Tempo:** 12 Minuti

---

## Slide 1: Titolo
**Tempo:** 0:00 - 0:30

Buongiorno a tutti.

Il mio lavoro di tesi è stato svolto con il prof Gianluca Aguzzi come relatore e il prof Danilo Pianini come co-relatore. Il titolo è **"Type-Safe Complex Specifications in Kotlin: a DSL to Configure the Alchemist Simulator."**

---

## Slide 2: Obiettivi (Goals)
**Tempo:** 0:30 - 1:10

L'obiettivo principale di questa tesi è stato analizzare i benefici e le `sfide` dell'adozione di un **Domain-Specific Language (DSL) type-safe** per la configurazione di sistemi software complessi.

Questo obiettivo mira a dimostrare come le moderne funzionalità di alcuni linguaggi di programmazione possano conciliare i benefici di un linguaggio type safe con le esigenze di estensibilità e modularità di sistemi complessi.

Per validare questa analisi, il secondo obiettivo è stato fornire un'implementazione concreta: ovvero sostituire il sistema di configurazione del simulatore **Alchemist**, con un dsl.

---

## Slide 3: Background: Domain-Specific Languages
**Tempo:** 1:10 - 1:45

Un **Domain-Specific Language (DSL)** è un linguaggio di programmazione con una espressività limitata, focalizzato su uno specifico dominio applicativo.

A differenza dei linguaggi General-Purpose come Java o Kotlin, i DSL permettono di colmare il divario semantico tra il problema e l'implementazione. Si concentrano su **cosa** il sistema deve fare (l'intento) piuttosto che su **come** implementarlo, nascondendo ai loro utilizzatori i dettagli tecnici e implementativi. 

---

## Slide 4: Background: Alchemist
**Tempo:** 1:45 - 2:30

**Il caso di studio per questa tesi** è  **Alchemist**, un simulatore per pervasive computing usasto in scenari come come swarm robotics o IOT su larga scala.

Alchemist è un simulatore molto **estensibile** e che permette di gestire simulazioni complesse. Si basa su un meta-modello che puo essere adattato a diversi domini  attraverso specifiche **incarnazioni**.

Attualmente, queste simulazioni vengono configurate tramite **file YAML**.

---

## Slide 4.1: Background: Alchemist - Meta-Model
**Tempo:** 2:30 - 3:00

Qui possiamo vedere un esempio di un file di configurazione, che ci permette di definire gli elementi e i comportamenti di una simulazione sulla base del meta-modello di Alchemist, mostrato nell'immagine a sinistra.



## Slide 5: Motivazione
**Tempo:** 2:30 - 3:00

È proprio questa dipendenza da YAML a introdurre diverse criticità, che hanno motivato 
questo lavoro di tesi.

In primo luogo, YAML è **non tipizzato** (untyped), di conseguenza, non ha alcuna conoscenza del modello di dominio sottostante. Questo comporta che la validazione delle configurazioni avviene solo a runtime, il che rende difficile la diagnostica degli errori.

In secondo luogo, mancano meccanismi per il riutilizzo del codice e la modularità  e Man mano che la complessità aumenta, i file YAML diventano monolitici e difficili da mantenere.

---

## Slide 6: Esempio - YAML (Verbosità)
**Tempo:** 3:00 - 3:30

In questo esempio vogliamo posizionare dei nodi all'interno dela laguna di venezia. Defnire il perimetro della laguna richiede un gran numero di coordinate, il che rende la configurazione poco leggibile.

Se più simulazioni richiedono lo stesso perimetro, è necessario copiare ed incollare le coordinate. Inoltre, una futura modifica di alcune coordinate richiederebbe di modificare tutte le simulazioni che lo utilizzano.

---

## Slide 7: Esempio - YAML (L'Errore di Indentazione)
**Tempo:** 3:30 - 4:15

In questo esempio, invece, possiamo vedere come un singolo errore di indentazione può portare a risultati diversi. 

Nella configurazione a sinistra, abbiamo una configurazione corretta.
Nella configurazione a destra, si è verificato un singolo errore di indentazione nell'ultima riga.

Poiché YAML si basa sull'indentazione per definire la struttura, il parser interpreta il `network-model` come una proprietà dell'`environment` anziché come un componente di primo livello.

Entrambi i file sono però sintatticamente validi, ma quello a sinistra produrrà una simulazione con un modello di rete come quello specificato, mentre quello a destra produrrà una simulazione senza un modello di rete, senza produrre errori.

Un DSL type-safe renderebbe questo errore strutturalmente impossibile.

---

## Slide 8: Requisiti
**Tempo:** 4:15 - 4:50
Per lo sviluppo del nuovo sistema, sono stati stabiliti dei requisiti, suddivisi in funzionali e non funzionali:

Dal punto di vista **Funzionale**,
 è importante che una configurazione YAML produca un modello di simulazione identico del suo equivalente in DSL.
* [cite_start]**Integrazione:** Il nuovo sistema deve integrarsi con il sistema di caricamento esistente, e supportare sia il caricamento di file YAML che script Kotlin .kts.
* [cite_start]**Type Safety:** Il nuovo sistema deve sfruttare il type-system di Kotlin per garantire la validità sintattica e semantica delle configurazioni.

Dal punto di vista **Non funzionale**, abbiamo dato priorità a:
* [cite_start]**Usabilità:** del DSL che deve essere usabile e garantire una buona esperienza lato sviluppatore.
* [cite_start]**Estensibilità:** Il DSL deve supportare nuove estensioni di Alchemist senza richiedere modifiche al core del DSL.
* [cite_start]**Performance:** Il nuovo sistema deve mantenere prestazioni comparabili a quello esistente.

---

## Slide 9: Kotlin DSL
**Tempo:** 4:50 - 5:20

La soluzione che ho sviluppato è un **DSL in Kotlin**.

Utilizzando Kotlin come linguaggio host del DSL, è possibile istanziare direttamente gli oggetti di dominio di Alchemist.

Il nuovo sistema supporta la **Batch Execution** tramite l'utilizzo un modello di lazy evaluation.

 Inoltre, offre gli stessi meccanismi di modularità e riuso di un linguaggio di programmazione, permettendo agli utenti di definire funzioni e variabili per riutilizzare e organizzare meglio il codice. 

 Gli utenti possono anche definire una sintassi personalizzata per i loro domini specifici.

 ## Slide 9.1: Kotlin Symbol Processing (KSP)
**Tempo:** 7:00 - 8:15

Una delle sfide maggiori è stata quella di mantenere un buon livello di **Usabilità** e **Estensibilità**. Per evitare agli utenti la gestione manuale di dipendenze interne al sistema, ho implementato un processore con ksp che scansiona il codice sorgente di Alchemist alla ricerca di classi annotate con una specifica annotazione. come mostrato nell esempio in alto.

Questo processore poi genera automaticamente una helper function che permette di istanziare l'oggetto Point  occupandosi di iniettare automaticamente le dipendenze interne presenti nel contesto.

Nel esempio in basso vediamo come l'utente può in questo modo semplicemente scrivere `deploy(point(0.0, 0.0))`, altrimenti sarebbe stato necessario passare al costruttore di point anche l'environment preso dal contesto.

## Slide 10: Kotlin DSL — Esempio (Modularità)
**Tempo:** 5:20 - 6:10
Questo è un esempio di configurazione scritta con il nuovo DSL.

Vediamo come è possile definire variabili che verranno utilizzate per la batch execution.

Qui invece viene definito il comportamento di una simulazione, con una sintassi tipica dei DSL in Kotlin.

## Slide 11: Kotlin DSL — Esempio (Modularità)
**Tempo:** 6:10 - 7:00

**A sinistra**, possiamo vedere come viene definita una funzione personalizzata `lagoon(500)`. Si puo notare come a differenza del file YAML visto in precedenza, dove bisognava fare copia-incolla delle coordinate, qui possiamo incapsulare il componenti all'interno di funzioni e renderle riutilizzabili.

**A destra**, invece possiamo vedere come è possibile usare anche i meccanismi di base di un linguaggio di programmazione, come cicli `for` e istruzioni `if` per definire gestire in maniera più flessibile le configurazioni più complesse.

---



---

## Slide 13: Validazione
**Tempo:** 8:15 - 9:00

Per validare la soluzione, ho implementato sia unit tests, ma anche un framework di test che a partire da un file YAML e il suo equivalente DSL, carica entrambi e confronta gli stati interni delle simulazioni. Il confronto viene effettuato sia a livello statico, sia dopo aver eseguito la simulazione per un certo numero di step. Questo test è fondamentale per garantire che il DSL produca un modello di simulazione identico a quello del suo equivalente YAML.


## 13.1
Ho anche verificato che il nuovo sistema  abbia delle performance simili al sistema esistente.
Qui ho riportato i riultati di un confronto dove ho osservato che il tempo di caricamento dei due formati sono piuttosto simili.

---

## Slide 14: Confronto
**Tempo:** 9:00 - 10:30

In questo esempio vediamo un file YAML e il suo equivalente in dsl.

**A sinistra (YAML):** si può notare come gli oggetti del dominio vengono definiti trmaite stringhe e i parametri sono liste non tipizzate.

**A destra (Kotlin DSL):** invece possiamo vedere come il programma è strutturato, tipizzato e dove non è possibile sintatticamente definire degli elementi dove non dovrebbero essere. 

---

## Slide 15: Conclusioni
**Tempo:** 10:30 - 11:30

Per concludere, questa tesi dimostra che è possibile sostituire formati di configurazione non tipizzati con DSL type-safe, anche per sistemi complessi ed estensibili come Alchemist.

Tramite l'utilizzo di KSP, il sistema riesce poi a fornire una migliore esperienza di sviluppo, e la possibilità di supportare future estensioni.

 **Sviluppi futuri**, possono includere lo sviluppo di un **convertitore da YAML a DSL** per facilitare la migrazione, e di estendere il DSL per supportare sintassi personalizzate per le diverse incarnazioni.

---