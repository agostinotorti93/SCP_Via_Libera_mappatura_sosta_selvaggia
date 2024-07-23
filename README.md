# SCP_Via_Libera_mappatura_sosta_selvaggia

Repository sviluppata per il progetto di Via Libera sulla città di Milano al fine di mappare il fenomeno della sosta selvaggia in città (https://saichepuoi.it/vialibera/). In caso di dubbi o domande, scrivere a milano@saichepuoi.it.

Si è scelto di mappare la città via per via, così da semplificare la raccolta dei dati e ottenere un risultato facilmente restituibile al Comune, oltre che di immediata lettura e di utilizzo per le persone. Dal punto di vista tecnico, ci siamo appoggiati a OpenStreetMap e, tramite diversi step di preprocessing, abbiamo ottenuto l'intera rete stradale della città di Milano – ad esclusione di tangenziali, superstrade, cavalcavia, ecc. – per un totale di circa 3.870 vie e 1.700 km. 

Nel dettaglio: tramite il sito Geofabrik (https://download.geofabrik.de/europe/italy/nord-ovest.html ), abbiamo scaricato l’intero grafo del nord-ovest dell’Italia. Successivamente, tramite il software Python - con la sua libreria Geopadas per la manipolazione di dati geospaziali - e l’utilizzo dello shapefile contenente i confini amministrativi comunali ( https://geoportale.comune.milano.it/ATOM/SIT/DBT2012/DBT2012_STRATO_09_Service.xml ) abbiamo estratto le sole strade che cadono all’interno del comune di Milano. L’ultimo step di preprocessing è stato quello di filtrare le strade in base alla loro “tipologia”, così da escludere tutte le strade ad alto scorrimento, ovvero tangenziali, superstrade, cavalcavia, che oltre ad essere poco sicure per la rilevazione da parte dei volontari, compongono parte della rete in cui si esclude possa esserci la presenza di sosta irregolare. Nel dettaglio, per ogni link stradale, è stata utilizzata l’informazione ‘highway’ - ovvero la chiave principale per etichettare le strade ( https://wiki.openstreetmap.org/wiki/IT:Key:highway ) - includendo nel grafo stradale finale solamente i link con chiave ‘highway’  appartenente alla rete stradale urbana: 'living_street', 'primary', 'primary_link', 'residential', 'secondary','secondary_link', 'tertiary','tertiary_link' e 'unclassified'.

Una volta ottenute le numeriche in termini di vie e km, ci siamo dedicati alla parte di progettazione. Abbiamo quindi suddiviso la città in più di 500 cluster uniformi in termini di vie e chilometri, cosi da avere cluster di vie facilmente mappabili da un gruppo di persone in un tempo ristretto sia a piedi che in bici. Il punto di partenza sono stati gli 88 NIL (Nuclei di Identità Locale) del Comune di Milano ( https://dati.comune.milano.it/dataset/ds964-nil-vigenti-pgt-2030 ); ad essi abbiamo applicato gli strumenti di Google Earth, che permettono di disegnare manualmente dei poligoni e salvarli all’interno di un file KML: ne abbiamo creati 524.

A quel punto abbiamo predisposto un algoritmo in Python per interpolare il grafo stradale di Milano con i 524 poligoni, così da assegnare ogni via a uno e a un solo poligono. Risultato: 524 cluster uniformi in termini di vie e chilometri.

Dopodiché ci si occupati della parte di matching fra cluster di vie e gruppi di volontari iscritti al progetto di ViaLibera. Infine è stata sviluppata una Web App per permettere agli utenti iscriti di mappare le diverse zone della città.

In questa repository, sono riportati i codici che permettono di estrarre il grafo stradale di una città e suddividere le vie della città in "cluster". Non sono invece riportati gli step di assegnazione delle vie ai volontari e gli step di sviluppo della Web App. Tutti i codici sono stati scritti per essere replicabili ed applicabili ad altre realtà e comuni.


### step_0_vialibera_preprocessing_grafo.ipynb

Notebook che permette di estrarre un grafo stradale contenente le vie di una città da mappare al fine di identificare il fenomeno della sosta selvaggia.

Il notebook prende in input:
- il grafo stradale di OSM di un area geografica (e.g., tutto il nord ovest dell'italia)
- un file contentente i confini poligonali di un'area circoscritta (e.g., la città di milano)

Il notebook restituisce in output:
- il grafo stradale relativo alla sola area circoscritta (e.g., la città di milano) preprocessato (contenente solo le strade da mappare poiché possono ospitare la sosta di auto)


### step_1_vialibera_definizione_n_sottografi_da_file_kml.ipynb
Notebook che permette - interpolando un grafo stradale e un insieme di n poligoni - di estrarre n cluster, ognuno composto da un insieme di vie.

Il notebook prende in input:
    - grafo_stradale_OSM_milano_final(grafo stradale di milano)
    - poligoni SCP final.kml (file kml contenente un layer di poligoni che suddividono milano in n aree)

Il notebook restituisce in output:
-il grafo stradale con aggiunta l'informazione del cluster di appartenenza per ogni via (grafo_stradale_OSM_comune_with_groups)
