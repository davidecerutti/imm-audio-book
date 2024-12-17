Feature audio
====

---

## Introduzione

L'estrazione di feature è un processo di elaborazione e trattamento delle informazioni latenti o nascoste nel segnale rappresentato da dati raw, catturati direttamente da un sensore. Lo scopo di estrazione di feature è quello di eliminare ridondanze e rumore, o addirittura componenti che renderebbero inefficaci la rappresentazione dell'informazione presente nei campioni del segnale, senza far perdere quella utile, come pattern complessi dati dalle emozioni, ad esempio. Inoltre, le feature estratte aiutano a sviluppare modelli di elaborazione o apprendimento più efficaci e veloci rispetto ai dati grezzi. Se ci soffermiamo sul caso del parlato, il punto principale da capire è che i suoni generati da un essere umano sono filtrati dalla forma del tratto vocale che comprende cavo orale, con lingua e denti, laringe, epiglottide, ecc. Questa forma determina il suono che viene emesso. Se riusciamo a determinare con precisione la forma, dovremmo avere una rappresentazione accurata del fonema prodotto. La forma del tratto vocale si manifesta nell'inviluppo dello spettro di potenza a breve termine e il compito delle feature che originano da questo paradigma è quello di rappresentare accuratamente questo inviluppo. 

Lo spettrogramma Mel è una feature importante nell'ambito audio ed è una variante dello spettrogramma comunemente utilizzata nell'elaborazione audio, nonché nell'apprendimento automatico. È simile a uno spettrogramma in quanto mostra il contenuto di frequenza di un segnale audio nel tempo, ma su un asse di frequenza diverso. In uno spettrogramma standard, l'asse della frequenza è lineare e viene misurato in hertz (Hz). Tuttavia, il sistema uditivo umano è più sensibile alle variazioni delle frequenze più basse rispetto a quelle più alte e questa sensibilità diminuisce logaritmicamente all'aumentare della frequenza. La scala Mel è una scala percettiva che approssima la risposta in frequenza non lineare dell'orecchio umano. Per esempio, nella tradizione della musica occidentale i suoni così chiamati consonanti vengono descritti come armoniosi, gradevoli e stabili, mentre i suoni dissonanti vengono descritti come spiacevoli e poco armoniosi. La consonanza e la dissonanza possono essere identificate visibilmente su uno spettrogramma Mel analizzando le distribuzioni delle frequenze. Gli intervalli consonanti mostrano strutture armoniche, chiare e stabili, mentre gli intervalli dissonanti mostrano schemi più irregolari e meno stabili che sembrano lasciare un’ombra. I modelli di intelligenza artificiale possono apprendere questi schemi visivi allenandosi su ampie serie di dati musicali, consentendo loro di riconoscere e replicare le relazioni armoniche che producono suoni piacevoli e composizioni esteticamente pi`u gradevoli.

Un altro esempio di feature divenuto oramai classico in ambito elaborazione del segnale audio, è rappresentato dallo spettrogramma e i coefficienti cepstrali in scala Mel, i cosiddetti mel-spectrogram e  Mel Frequency Cepstral Coefficients (MFCC). Quest'ultimi in particolare, rappresentano una tecnica per l'estrazione di feature da un segnale audio e, con maggiore efficacia, da segnali che catturano la voce umana. In sostanza, si fornisce una rappresentazione dello spettro di potenza a breve termine di un suono basata sulla trasformata coseno dello spettro di potenza logaritmico su una scala di frequenza non lineare, detta scala Mel.  Quest'ultima mira a replicare la risoluzione in frequenza tipica degli esseri umani, adeguando l'analisi a cogliere le differenza che sussistono nel percepire frequenze basse e frequenze alte nei suoni che arrivano all'udito. Gli MFCC sono una caratteristica ampiamente utilizzata nel riconoscimento automatico del parlato e dei parlanti. Sono stati introdotti da Davis e Mermelstein negli anni '80 e da allora sono sempre stati lo stato dell'arte. Prima dell'introduzione delle MFCC, veniva impiegata la predizione lineare o classificatori HMM (Hidden Markov Models). Di seguito si illustrano gli aspetti principali degli MFCC, i motivi per cui sono una feature audio affidabile e come implementarli.

---
## Lo spettrogramma in scala Mel

Un segnale audio varia costantemente, ma per semplificare l’analisi si assume che su scale temporali brevi il segnale non cambi in modo significativo (statisticamente, ossia stazionarietà statistica, anche se i campioni continuano a variare). Per questo motivo il segnale viene suddiviso in finestre temporali di 20-40 ms. Se la finestra è troppo corta, non si raccolgono abbastanza campioni per ottenere una stima spettrale affidabile; se è troppo lunga, il segnale cambia eccessivamente all’interno della finestra stessa.

Il passo successivo consiste nel calcolare lo spettro di potenza di ciascuna finestra. Questo è ispirato al funzionamento della coclea umana (un organo nell’orecchio) che vibra in punti diversi a seconda della frequenza dei suoni in ingresso. A seconda del punto della coclea che vibra (attivando accurati organi ciliati), si attivano diversi nervi che informano il cervello sulla presenza di determinate frequenze. La stima spettrale ottenuta con il periodogramma svolge un ruolo analogo, identificando le frequenze presenti nella finestra.

Tuttavia, la stima spettrale del periodogramma contiene ancora molte informazioni non necessarie per l'elaborazione del segnale audio. In particolare, la coclea non distingue tra frequenze molto vicine, e questa limitazione diventa più pronunciata con l’aumento delle frequenze. Per questo motivo, i valori dei bin del periodogramma vengono raggruppati e sommati per ottenere un’indicazione dell’energia presente in diverse regioni di frequenza. Questo processo è realizzato con il filtro Mel: il primo filtro è molto stretto e misura quanta energia si trova vicino a 0 Hertz. Man mano che le frequenze aumentano, i filtri diventano più ampi poiché le variazioni di dettaglio perdono importanza. Ci interessa solo una stima approssimativa dell’energia in ciascuna regione. La scala Mel specifica come distanziare i filtri e quanto renderli larghi.

Una volta calcolate le energie dei filtri Mel, si applica il logaritmo. Anche questa operazione è ispirata al funzionamento dell’udito umano: non percepiamo la sonorità in modo lineare. Per raddoppiare il volume percepito di un suono, è necessario fornire circa 8 volte più energia. Ciò significa che grandi variazioni di energia possono sembrare trascurabili se il suono è già molto forte. Questa operazione di compressione rende le feature estratte più simili alla percezione umana. Si utilizza il logaritmo anziché, ad esempio, la radice cubica perché il logaritmo consente di applicare la sottrazione della media cepstrale, una tecnica di normalizzazione del canale.

L’ultimo passaggio consiste nel calcolo della Trasformata Discreta del Coseno (DCT) delle energie logaritmiche dei filtri Mel. Questo è fatto per due motivi principali. Poiché i filtri Mel si sovrappongono, le energie dei filtri risultano altamente correlate. La DCT decorrela le energie, permettendo di utilizzare matrici di covarianza diagonali per modellare le feature. Tuttavia, vengono mantenuti solo 12 dei 26 coefficienti DCT, perché i coefficienti DCT più alti rappresentano variazioni rapide delle energie dei filtri che, in realtà, degradano le prestazioni. Eliminandoli, si ottiene un piccolo miglioramento delle prestazioni.

### Scala Mel

La scala Mel è una scala di percezione dell'altezza di un suono. È stata proposta da Stanley Smith Stevens, John Volkman e Edwin Newman nel 1937 nel Journal of the Acoustical Society of America. La scala Mel è una funzione definita a tratti che divide la banda di frequenza di un segnale in sottobande e quindi estrae i coefficienti cepstrali usando la trasformata coseno discreta (DCT) a valle di una trasformazione logaritmica delle frequenze del segnale stesso. L'idea centrale di questa trasformazione è che i suoni di uguale distanza sulla scala Mel sono percepiti come di uguale distanza dagli umani.

Gli studi hanno dimostrato che gli umani non percepiscono le frequenze su una scala lineare. Siamo più bravi a rilevare differenze nelle frequenze più basse rispetto alle frequenze più elevate. Ad esempio, possiamo facilmente dire la differenza tra 500 e 1000 Hz, ma difficilmente saremo in grado di dire una differenza tra 10.000 e 10.500 Hz, anche se la distanza tra le due coppie è la stessa. Quindi, anche se la distanza tra le due serie di suoni è la stessa, la nostra percezione della distanza non lo è. Questo è ciò che rende la scala Mel fondamentale nelle applicazioni di signal processing e machine learning per l'audio, poiché mira a riprodurre la nostra percezione del suono.

La trasformazione dalla scala Hertz alla scala Mel è la seguente:

$$\label{eq:mel}
m= M(f)=1125 \ln\left(1+𝑓/700\right)
$$

mentre la trasformazione inversa risulta:

$$
f=M^{-1}(m)=700\left( e^{m/1125}-1\right)
$$

dove $f$ è la frequenza espressa in Hz ed $m$ è la frequenza espressa in Mel, per l'appunto. Come mostrato in [](eq:mel), la formula per la scala Mel è una trasformazione logaritmica della frequenza in Hz di un segnale, visualizzata di seguito

&nbsp;
![MEL.png](images/mel_scale.png)
&nbsp;

Dalla figura si nota innanzitutto la corrispondenza di 1000 Hz e 1000 Mel: la scala è spesso considerata approssimativamente lineare fino a 1 kHz e logaritmica in seguito. E' poi messo in evidenza quanto detto sopra circa la progressione lineare sulla scala Mel e quella esponenziale sulla scala in frequenza: uguali differenze di punti sulla scala Mel proiettano bande di ampiezza crescente nella scala delle frequenze.

### Banchi di filtri Mel

Innanzitutto costruiamo i punti di centro-banda di ogni filtro del banco, che determinano l'inizio e fine di ogni banda di frequenze coperto dal banco di filtri stesso. La frequenza minima scelta di norma è $f_{min}=1000$ (lasciando inalterati i primi 1000 Hz) e $f_{min}=F_s/2$, con $F_s$ la frequenza di campionamento del segnale. Per fare ciò, convertiamo prima i due estremi del range $f_{min}$ e $f_{max}$ nello spazio Mel, cioè $M(f_{min})$ e $M(f_{max})$. Successivamente costruiamo un insieme di punti equidistanziati tra le due frequenze Mel estreme $M(f_{min})$ e $M(f_{max})$. Questo processo è mostrato nell'immagine qui sotto in cui sono evidenziate i centro-banda in frequenza Hz proiettati da equa ripartizione delle scala mel in 6 bande distinte, con passo 263 Mel, $f_{min}=1000$ e $f_{max}=8000$, avendo fissato $F_s=16000$. Gli estremi Mel risultano dunque $M(f_{min})=1000$ e $M(f_{max})=2840$, con valori intermedi: 

$$
M(f)=[1000,\quad 1262,\quad 1525,\quad 1788,\quad 2051,\quad 2314,\quad 2577,\quad 2840].
$$

I corrispondenti centro-banda in Hz sono pertanto: 

$$
M^{-1}(f)=[1000,\quad 1446,\quad 2010,\quad 2722,\quad 3621,\quad 4756,\quad 6190,\quad 8000].
$$

&nbsp;
![MEL.png](images/mel_freq.png)
&nbsp;

Il banco di filtri Mel viene descritto come una sequenza di filtri triangolari ognuno dei quali è costruito su 3 valori di frequenza contigui: della terna i due estremi rappresentano la base e quello centrale il vertice. Va aggiunto ogni coppia di triangoli, e quindi le bande corrispondenti, sono sovrapposti al 50%, come mostra la figura. Notare altresì che i valori riportati dell'asse orizzontale superiore e quello inferiore riportano le sequenze di frequenze Mel e Hz, rispettivamente ed elencate sopra. 

&nbsp;
![alt text](images/mel_bins.png)
&nbsp;

Un passo importante da sottolineare è che dopo la conversione delle frequenze Mel nello spazio di frequenza in Hz occorre normalizzare i valori di frequenza alla dimensione (risoluzione in frequenza) usata nel calcolo della FFT, cioè numero $N$ di campioni usati nel calcolo dello spettrogramma per ogni frame temporale (e pari alla dimensione delle finestre applicate al segnale), come mostrato nel Capitolo 3. Nella figura sopra si mostra questo aspetto evidenziando $N/2+1=513$ bin che derivano dall'aver usato una finestra di $N=1024$ campioni per il calcolo dello spettro di potenza di un frame dei quali, per la simmetria della FFT, la metà copre l'intervallo in frequenza $[0 - F_s/2]$. 


 La funzione di trasferimento $H_m(k)$ del filtro triangolare $m$-esimo è definita come segue:

$$
\begin{align*} H_m(k)=\begin{cases} \displaystyle 0 ,& k < f\left ({m-1 }\right)\\ \displaystyle \frac {k-f\left ({m-1 }\right)}{f\left ({m }\right)-f\left ({m-1 }\right)}, & f\left ({m-1 }\right)\le k < f\left ({m }\right)\\ \displaystyle 1 & k=f\left ({m }\right)\\ \displaystyle \frac {f\left ({m+1 }\right)-k}{f\left ({m+1 }\right)-f(m)}, & f\left ({m }\right) < k\le f\left ({m+1 }\right)\\ \displaystyle 0 & k>f(m+1) 
\end{cases}
\end{align*}
$$

con il vincolo di normalizzazione $\sum_k H_m(k)=1$ per ogni filtro triangolare che agisce sull'$m$-sima banda, come mostra la figura. 

&nbsp;
![alt text](images/mel_normaliz.png)
&nbsp;

L'azione di ogni singolo filtro del banco è quella di catturare l'energia data dallo spettro di potenza $S(k)$ di un frame del segnale che insiste nella banda di frequenze corrispondente. La figura sotto mostra - a titolo di esempio - il terzo filtro del banco in azione: si vede che il prodotto del filtro con lo spettro, cioè $H_3(k)S(k)$, svolge proprio il ruolo inteso, ossia quello di preservare l'energia in banda e tagliare quella complementare.

&nbsp;
![alt text](images/mel_filtro3_azione.png)
&nbsp;

### Lo spettrogramma Mel

Uno spettrogramma Mel è uno spettrogramma in cui le frequenze vengono convertite nella scala Mel. Qui di seguito è mostrato l'uso del banco di filtri che trasforma lo spettro di potenza (spettrogramma) usuale nello spettro di potenza su scala Mel (spettrogramma Mel). L'output per ogni slot dello spettro di potenza su scala Mel rappresenta l'energia del segnale ristretto alla banda di frequenza che ogni filtro copre. Questa mappatura viene anche detta Mel binning. Il processo è mostrato in figura sotto. Naturalmente il numero di bin nelle due scale può essere diverso e in generale quello in scala Mel è molto minore (dell'ordine di qualche decina, ma può variare a seconda delle applicazioni), mentre nella scala in Hz si hanno risoluzioni tipicamente associate alla taglia della FFT impiegata (anche dell'ordine di qualche migliaia e qui in particolare 1024). 

&nbsp;
![alt text](images/mel-binning.png)
&nbsp;

Vediamo un esempio di applicazione del banco di filtri Mel allo spettrogramma di un segnale audio come la scala di Do maggiore nella figura sotto, campionato con $F_s=22050$.  Utilizzando una lunghezza della finestra di $N = 1024$ e un hop size di $H = N/2=512$, questo risulta in una risoluzione in frequenza (bin) di $F_s/N \approx 21.53 \, \text{Hz}$ e una risoluzione temporale di $H/F_s\approx 23.21 \, \text{ms}$, per un totale di 143 frame temporali. In definitiva, lo spettrogramma è una matrice di dimensioni $(\#\text{bin\_freqs}, \#\text{frames})$, cioè avente $\#\text{bin\_freqs}$ righe e $\#\text{frames}$ colonne.

&nbsp;
![alt text](images/spettro-all.png)
&nbsp;

Poiché lo spettrogramma è in forma matriciale, per ottenere lo spettrogramma Mel occorre passare in forma matriciale anche il banco di filtri triangolari Mel. Quelle che segue è un esempio di filtro con 6 bin in scala Mel, cioè $\#\text{bin\_mels}=6$, e 513 bin, cioè $\#\text{bin\_freqs}=513$, in scala di Hz e mostrato tramite un'immagine, i cui pixel hanno valori coerenti con quelli degli spettrogrammi. 

&nbsp;
![](images/mel-filtrobin6.png)
&nbsp;

Infine si ottiene lo spettrogramma Mel attraverso la moltiplicazione della matrice del filtro $M$ con lo spettrogramma $S$, con dimensioni rispettivamente:

$$
M\in\mathbb{R}^{(\#\text{bin\_mels})\times(\#\text{bin\_freqs})}\qquad\quad
S\in\mathbb{R}^{(\#\text{bin\_freqs})\times(\#\text{frames})}
$$

che nel caso qui descritto diventano:

$$
M\in\mathbb{R}^{6\times 513} \qquad\quad S\in\mathbb{R}^{513\times 143}
$$

da cui si ottiene lo spettrogramma Mel 

$$
\text{Mel spectrogram} = M\,S\in \mathbb{R}^{6\times 143}
$$

e mostrato in figura.

&nbsp;
![](images/mel-mat6.png)
&nbsp;

L'intero processo è sintetizzato nella seguente immagine.

&nbsp;
![alt text](images/mel-all.png)
&nbsp;

Se si aumenta il numero di bin nella scala Mel si ottiene maggior risoluzione proprio in quella scala di frequenze, come il caso sottostante in cui si considerano 120 bin Mel e si mostrano in sequenza il filtro e lo spettrogramma Mel, rispettivamente.

&nbsp;
![](images/mel-filter120.png)
&nbsp;

&nbsp;
![alt text](images/mel_spectrogram.png)
&nbsp;


---
## Mel Frequency Cepstral Coefficient (MFCC)

Il concetto di `Mel-Frequency Cepstral Coefficients` (`MFCC`) può essere descritto in modo intuitivo come una rappresentazione compatta delle caratteristiche principali di un segnale audio, particolarmente utile per riconoscere suoni, come il parlato, imitando in parte il funzionamento del sistema uditivo umano. Siamo infatti più sensibili a differenze di frequenza nelle gamme basse rispetto alle gamme alte e per questa ragione gli MFCC usano la scala Mel di frequenze. Gli MFCC hanno anche lo scopo di ridurre la complessità, infatti non conservano tutte le informazioni del segnale audio: anziché rappresentare ogni dettaglio del suono, estraggono solo i tratti essenziali, eliminando quelli meno importanti per l’elaborazione, come variazioni molto sottili e rumori irrilevanti. Ecco come funziona intuitivamente.

-	Suddivisione in parti piccole: un suono complesso, come una parola, viene diviso in piccoli segmenti temporali (frame), che è un po' come spezzare una frase in singole sillabe.
- Identificazione delle frequenze principali: per ogni segmento, viene analizzata l’energia presente a diverse frequenze, identificando quelle più significative. È un po’ come guardare una canzone con un equalizzatore audio e notare quali frequenze “spiccano” di più.
- Compressione delle informazioni: per rendere i dati più gestibili, le frequenze vengono raggruppate in bande e convertite usando una scala logaritmica (simile a come il nostro orecchio interpreta l’intensità del suono).
- Decorrelazione: infine, un’operazione matematica (la trasformata discreta del coseno, o DCT) viene applicata per rendere i dati meno correlati tra loro. È come trasformare un’immagine in bianco e nero, dove si mantengono solo le forme essenziali eliminando i dettagli ridondanti.

Gli MFCC possono essere visti come un “riassunto” del suono in ogni piccolo intervallo temporale. Questo riassunto conserva le informazioni chiave necessarie per distinguere suoni diversi, come le caratteristiche della voce umana o di un particolare strumento musicale. Per esempio, quando ascoltiamo una parola, gli MFCC codificano i suoni delle vocali e delle consonanti in modo tale che un sistema di riconoscimento vocale possa capire cosa è stato detto.
Quando analizziamo una melodia, aiutano a identificare il tipo di strumento che la sta suonando. In sintesi, gli MFCC rappresentano il “DNA” del suono, distillando il segnale audio in una forma più compatta e rappresentativa, ottimizzata per l’analisi e il riconoscimento.


### Cepstrum

Il `cepstrum` è un concetto fondamentale nell’elaborazione dei segnali, particolarmente utile per analizzare segnali audio e vocali. Nasce come trasformazione che permette di separare le informazioni relative alla componente spettrale del segnale (come ampiezza e frequenza) da quelle che riguardano la periodicità di tali componenti, rendendolo uno strumento versatile per l’analisi dei segnali non stazionari. Il cepstrum si ottiene applicando una trasformazione logaritmica e una trasformazione inversa a un segnale trasformato tramite la Fourier Transform. I passaggi principali sono:
1.	**Calcolo dello spettro**: si parte dal segnale nel dominio del tempo e si calcola la sua trasformata di Fourier ￼, ottenendo lo spettro di ampiezza e fase del segnale.
2.	**Logaritmo dello spettro di ampiezza**: si prende il logaritmo del modulo dello spettro ￼. Questo step converte le moltiplicazioni nel dominio delle frequenze in somme, semplificando la separazione delle componenti spettrali.
3.	**Trasformata inversa**: si applica una trasformata inversa di Fourier al logaritmo dello spettro. Il risultato è il cepstrum, un segnale nel dominio chiamato “quefrenziale” (in modo scherzoso, dal termine “frequenza”).

La `quefrency` è un concetto introdotto nel contesto del cepstrum, e rappresenta una sorta di “tempo” o scala nel dominio quefrenziale, analogo ma non identico al dominio temporale o a quello delle frequenze. Il termine “quefrency” è un gioco di parole derivato dalla parola “frequency” (frequenza), con una permutazione delle lettere. È usato per indicare una nuova dimensione interpretativa nel dominio del cepstrum, che non rappresenta né una frequenza né un tempo in senso stretto, ma una misura della periodicità delle variazioni spettrali.

In termini formali il cepstrum viene calcolato come segue:

![](images/def_cepstrum.png)￼

dove $c(n)$ è il cepstrum, $\mathcal{F}$ è la trasformata di Fourier e $x(n)$ è il segnale originale. Il processo di calcolo nei suoi vari stadi è descritto in figura.

&nbsp;
![alt text](image-3.png)
&nbsp;

Il cepstrum può essere interpretato come una rappresentazione del segnale che mette in evidenza i suoi elementi periodici:
- Bassa quefrency: rappresenta variazioni lente nello spettro, come l’inviluppo spettrale. Per esempio, nella voce umana, queste componenti sono legate alle caratteristiche del tratto vocale.
- Alta quefrency: evidenzia variazioni rapide, come la periodicità dei segnali armonici. Ad esempio, nella voce umana, queste componenti corrispondono alla frequenza fondamentale della voce (pitch). In parole semplici, il cepstrum permette di distinguere tra le componenti del segnale relative alla struttura generale (come il timbro) e quelle legate alla periodicità o al tono.

Il cepstrum trova applicazione in vari campi, tra cui:
1.	Analisi della voce: separare le informazioni sulla frequenza fondamentale (pitch) e il filtro rappresentato dal tratto vocale; l'estrazione di parametri come i Mel-Frequency Cepstral Coefficients (MFCC), ampiamente usati in sistemi di riconoscimento vocale.
2.	Elaborazione audio: analisi della struttura armonica di segnali musicali e rimozione di echi o riverberi (deconvoluzione).
3.	Diagnostica meccanica: identificazione di difetti in macchine rotanti attraverso l’analisi della periodicità delle vibrazioni.

Per comprendere il cepstrum intuitivamente, immagina di analizzare il canto di un uccello: lo spettro ci dice quali frequenze sono presenti nel canto (ad esempio, le armoniche); il cepstrum ci dice ogni quanto tempo queste frequenze si ripetono, evidenziando il “ritmo” del canto, separandolo dal “timbro”.


### Il processo di estrazione degli MFCC

Gli MFCC possono essere calcolati conducendo i seguenti passi consecutivi:
1. Pre-elaborazione del segnale
2. Framing e finestratura
3. Trasformata di Fourier (FFT)
4. Mappatura sulla scala Mel
5. Logaritmo dell’energia
6. Trasformata discreta del coseno (DCT)
7. Delta e Delta-Delta (opzionale)

La figura ne mostra la relazione funzionale.

&nbsp;
![alt text](images/MFCC-diagram.png)
&nbsp;

#### 1. Pre-emphasis (preprocessing)
 
La pre-enfasi si riferisce al filtro che enfatizza le frequenze più elevate. Il suo scopo è bilanciare lo spettro dei suoni espressi che hanno un ripido decadimento nella regione ad alta frequenza (a volte viene soppressa durante processi di elaborazione o cattura del segnale). Per esempio, un segnale vocale se registrato con un microfono ad una certa distanza arriva a perdere +6dB/ottava rispetto al vero spettro del tratto vocale. Pertanto, la pre-enfasi rimuove alcuni degli effetti glottali dai parametri del tratto vocale. Il filtro pre-enfasi più comunemente usato è dato dalla seguente funzione di trasferimento

$$
H(z) = 1 − bz^{−1} 
$$

che rappresenta un filtro passa-alto a cui vengono spesso associati i coefficienti $[1, 0.97]$ e che si traduce nella seguente equazione alle differenze:

$$
y(n)=x(n)-bx(n-1).
$$

#### 2. Framing (windowing) del segnale 

L'idea alla base della partizione in frame del segnale raw è quella di avere segmenti brevi in cui il segnale tende ad essere più stazionario al fine di avere proprietà acustiche stabili. Per quanto riguarda il segnale vocale, il periodo di 20-30 ms è riferito come segmento quasi stazionario (QSS) dal momento che il tempo tra due eventi glottali è di circa 20 ms. In aggiunta, la sovrapposizione di frame di 10 ms consentono di catturare meglio le feature temporali del segnale vocale. In generale, le finestre di Hanning e Hamming sono tra le più usate. Queste finestre possono migliorare le armoniche, e la riduzione dell'effetto di bordo durante il calcolo della DFT sul segnale. 

Per la finestratura vengono impiegate finestre come la finestra di Hamming, che è una funzione utilizzata per ridurre gli effetti delle discontinuità ai bordi di un segnale quando viene suddiviso in segmenti (o frame) per l’analisi. La finestra moltiplica il segnale $x(n)$ elemento per elemento, attenuando gradualmente i valori ai bordi del frame. La funzione della finestra di Hamming è definita come:

$$
w(n) = 0.54 - 0.46 \cos\left( \frac{2\pi n}{N-1} \right), \qquad 0 \leq n \leq N-1,
$$

dove $N$ è la lunghezza del frame (numero di campioni nel segmento), $n$ è l’indice temporale all’interno del frame (figura sotto).

&nbsp;
```{image} images/hamming-win.png
:width: 450px
:align: center
```
&nbsp;


Per applicare la finestra di Hamming al segnale $x(n)$, il segnale viene moltiplicato punto per punto con la finestra ottenendo il frame temporale pronto per la trasformata di Fourier o altre analisi:

$$
x_i(n) = x(n) \cdot w(n), \qquad 0 \leq n \leq N-1, \quad 0 \leq i \leq T.
$$

La figura sotto rappresenta bene questa operazione per il calcolo dello spettro di potenza a livello di frame $i$.

&nbsp;
![alt text](images/MFCC-1.png)
&nbsp;

#### 3. Spettro di potenza del segnale 

Lo spettro di potenza può essere descritto come la distribuzione della potenza dei componenti alle varie frequenze di cui è composto il segnale. Tradizionalmente, la trasformata discreta di Fourier (DFT) viene utilizzata per calcolare lo spettro di potenza. Lo spettro di potenza di ciascun frame $x_i(n)$ è ottenuto elevando al quadrato il valore assoluto della risposta in frequenza della DFT, cioè:

$$
|X_i(k)|^2=\left|\sum \limits _{n=0}^{N-1} x_i(n)\, e^{-j\frac{2\pi}{N}nk}\right|^2 \qquad k=0,2,3\ldots N-1.
$$

Complessivamente questo passaggio, applicato a tutti i frame temporali, implica calcolare lo spettrogramma come mostrato nelle sezioni precedenti e riassunto qui in figura. Quello che si ottiene alla fine di questo passo è lo spettrogramma completo per l'intero segnale, finestra su finestra e la sua rappresentazione matriciale.

&nbsp;
![alt text](images/MFCC-2.png)
&nbsp;

#### 4. Spettrogramma Mel

Lo spettro Mel si ottiene facendo passare il segnale trasformato mediante Fourier attraverso l'insieme di filtri passa-banda chiamati Mel-filter bank. Per il calcolo degli MFCC, i filtri sono generalmente implementati nel dominio della frequenza. Lo spettro Mel dello spettro in modulo si ottiene moltiplicando il modulo dello spettro di un frame per ciascuno dei filtri triangolari ponderati sulla scala Mel. Questo passaggio è fondamentale per avvicinare la rappresentazione del segnale alle caratteristiche dell’udito umano e per estrarre informazioni pertinenti alla successiva fase di calcolo degli MFCC. In questa fase si applica il banco di filtri Mel e in seguito si calcola l'energia contenuta in ciascun filtro triangolare sommando i valori della densità spettrale (ottenuta dalla FFT) pesati dal filtro:

$$
S_i(m)=\sum_{k=0}^{N-1} |X(k)|^2 \cdot H_m(k),\quad\qquad 0\leq m\leq M-1.
$$

dove $M$ è il numero totale di filtri. $H_m(k)$ è il peso attribuito al $k$-esimo intervallo di spettro energetico che contribuisce alla medesima banda in uscita. Nella figura è rappresentato il passaggio centrale con uso del banco di filtri passa-banda triangolari.

&nbsp;
![alt text](images/MFCC-3.png)
&nbsp;

#### 5. Logaritmo dello spettro
La compressione logaritmica è una trasformazione che si applica ai valori energetici ottenuti dopo il passaggio attraverso i filtri triangolari sulla scala Mel. Questo step è cruciale per avvicinare la rappresentazione del segnale audio al modo in cui l’udito umano percepisce i suoni. Dati i valori di energia  $s(m)$ calcolati per ciascun filtro triangolare, si applica il logaritmo naturale per ottenere i valori compressi:

$$
\hat{S}_i(m) = \log(S_i(m) + \epsilon),
$$

dove $S_i(m)$ è l’energia associata al filtro $m$ per il frame $i$ ed $\epsilon$ è una costante positiva molto piccola per evitare problemi con il logaritmo di zero (valori nulli).

&nbsp;
![alt text](images/MFCC-5.png)
&nbsp;


#### 6. Discrete Cosine Transform (DCT)

In molti casi i livelli di energia nelle bande adiacenti tendono a essere correlati. Applicando la DCT ai coefficienti di frequenza di Mel trasformati, si ottiene un insieme di coefficienti cepstrali. Prima di calcolare la DCT, lo spettro di Mel viene solitamente rappresentato su una scala di log (come mostrato sopra). In questo modo si ottiene un segnale nel dominio cepstrale con un picco di quefrequenza corrispondente all'altezza del segnale e un numero di formanti che rappresentano i picchi di bassa quefrequenza. Poiché la maggior parte delle informazioni del segnale è rappresentata dai primi coefficienti MFCC, il sistema può essere reso robusto estraendo solo quei coefficienti, ignorando o troncando le componenti DCT di ordine superiore.

La DCT esprime una sequenza finita di punti dati dalla somma di funzioni coseno oscillanti a frequenze diverse, multiple di una frequenza base. Nel processo MFCC, la DCT viene applicata a valle del banco di filtri Mel per selezionare la maggior parte dei coefficienti utili a catturare l'informazione massima nelle log ampiezze spettrali dei blocchi filtrati. La DCT è calcolata dall'equazione seguente:

$$
c_i(n)=\sum_{n=0}^{N-1} \hat{S}_i(m) \cos\left({2\pi jnk}/ N\right),\quad n=0,1,\dots, C-1, \quad i=0,1,\dots, T
$$

dove $c_i(n)$ sono i coefficienti cepstrali associati al frame $i$-mo e $C$ è il numero totali di MFCC. I sistemi MFCC tradizionali utilizzano solo 8-13 coefficienti cepstrali. Il coefficiente zeresimo viene spesso escluso perché rappresenta la log-energia media del segnale di ingresso. Tutto questo è sintetizzato nella figura.

&nbsp;
![alt text](images/MFCC-6.png)
&nbsp;

Dal punto di vista matriciale quello che si ottiene è rappresentato in figura.

&nbsp;
![alt text](images/MFCC-7.png)
&nbsp;

Alla fine alcune considerazioni sull'uso degli MFCC in generale nelle applicazioni audio, comprese quelle basate su apprendimento di feature per i task classici come il riconoscimento e la classificazione.

&nbsp;
![alt text](images/MFCC-8.png)
&nbsp;

#### 7. Delta e Delta-Delta

I coefficienti MFCC dinamici arricchiscono le caratteristiche statiche degli MFCC includendo informazioni sulla dinamica temporale del segnale. Questo si ottiene calcolando le derivate prime e seconde dei coefficienti cepstrali, note rispettivamente come coefficienti delta e coefficienti delta-delta. I coefficienti delta rappresentano la derivata prima dei coefficienti cepstrali e forniscono informazioni sul tasso di variazione del discorso. Sono calcolati utilizzando una finestra temporale centrata sul frame di interesse. Una formula comunemente utilizzata è:

$$
\Delta c_i(n) = \frac{\sum_{j=-\tau}^{\tau} k_j \cdot c_i(n+j)}{\sum_{j=-\tau}^{\tau} |j|}
$$

dove $c_i(n)$ è $i$-esimo coefficiente cepstrale dell' $i$-simo frame temporale, $k_j$ è un peso associato al frame $i$ e $\tau$ (tipicamente pari a 2) è il numero di frame successivi utilizzati per il calcolo.

I coefficienti delta-delta, o accelerazione, rappresentano la derivata seconda dei coefficienti cepstrali e forniscono informazioni sull'accelerazione del discorso. Si ottengono calcolando la derivata prima dei coefficienti delta, applicando la stessa formula utilizzata per i coefficienti delta, ma sostituendo $c_i(n)$ con $\Delta c_i(n)$. L’inclusione di coefficienti delta e delta-delta nelle caratteristiche MFCC migliora le prestazioni dei sistemi di riconoscimento vocale, poiché fornisce informazioni sulla dinamica del segnale, essenziali per catturare le variazioni nel tempo tipiche del parlato. ￼