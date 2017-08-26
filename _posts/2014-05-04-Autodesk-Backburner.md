---
layout: post
title: Autodesk Backburner,un tool decisamente migliorabile!
---
<img src="{{ site.baseurl }}/images/backburner/backburner.jpg" alt="Backburner" style="width: auto;"/>
Scrivo questo post come reference personale, per poterla riusare tra qualche mese visto che non mi capita spesso di renderizzare sequenze animate
In ogni caso quello di cui voglio parlare è l’utility per la gestione del rendering in rete di casa Autodesk:
Autodesk Backburner
Vi dico subito che questo non è un vero software come quelli a cui l’Autodesk ci ha abituati ma piuttosto è una utility che ci permette di gestire e perlopiù di monitorare il rendering di rete, inoltre è importante per chiunque legga questo articolo, sapere che non sono certo un esperto è questa la mia prima esperienza con questo software.
La cosa di cui però sono sicuro al 99% è che Backburner non ci permette di costruire una renderfarm vera e propria ma piuttosto permette solo di suddividere una sequenza di x frame in task più piccoli e di assegnare poi questi task ai vari computer messi in rete, in pratica non fa la cosa più ovvia o perlomeno quello che mi aspettavo facesse e cioè ESEGUIRE IL CALCOLO DI UN SINGOLO FRAME SU PIU MACCHINE
Se vogliamo perciò gestire questo tipo di lavoro allora Backburner non fa per noi (almeno fino alla versione integrata con maya 2014 e almeno per quanto ne so io)
Se invece quello che cerchiamo e proprio di suddividere il rendering di un’animazione per più macchina allora Backburner tanto male non è, e quindi possiamo inizia con la parte pratica

 <!--more-->
 
<h3>Un po’ di teoria</h3>
In pratica Backburner funziona così abbiamo una macchina che fa da manager e le altre che fano da server.
Se trovate strana questa cosa non vi preoccupate è normale, infatti chi conosce un po’ di reti sa che di solito la macchina che sta a monte si chiama serve e chi sta sotto di essa si chiama client, bene qui hanno chiamato i client come “server” giusto per complicare un po’ la vita
I server quindi hanno bisogno di eseguire solo la parte server di Backburner, mentre il manager ovviamente deve eseguire la parte manager
Una volta installato inoltre vediamo che Backburner mette a disposizione anche un monitor che ovviamente va eseguito sulla macchina che sta alla radice della rete e ci permette di monitorare appunto il progresso di rendering
 
<h3>Installazione</h3>
Molto semplice, sulla macchina master installiamo ovviamente Backburner e il software dal quale lanciamo il processo, io uso maya
Sui server basta installare solo Backburner
<h4>ATTENZIONE</h4>
Una volta installati conviene aprire Backburner server per vedere se è tutto ok, ma qui cominciano le prime bestemmie, nel 50% dei casi avrete installato sulla macchina un buon firewall allora con molta probabilità questo sarà quello che vedrete: NULLA
Infatti se BB server trova problemi di coesistenza con il firewall non parte proprio senza dire nulla
La cosa bella è che ovviamente cercando un po’ su internet scoprirete che basta disattivare il firewall e riaprire BB Server……magari
Il punto infatti è che a quel punto il software avrà già inizializzato la propria cartella personale dentro APPDATA e quella rimarrà fin quando non cancellate a mano
Quindi se BB server non si apre staccate il firewall, andate in (nel mio caso)
C:\Users\Luca\AppData\Local e cancellate la cartella “backburner”,
Dopo di che potete riaprire il software, solo a quel punto potrete riattivare il firewall
Configurazione della rete
Bene ora che abbiamo verificato il corretto funzionamento dei vari software, dobbiamo creare una rete locale tra i nostri pc
Non sono un esperto di reti e questo passaggio si può fare in vari modi, io però per comodità personale avendo tutti pc Windows creo un gruppo Home nel mio PC master e ci collego tutti gli altri pc, inoltre un altro accorgimento che uso è di settare tutte le macchine con indirizzi locali fissi, in questo modo so come referenziarli senza perdere troppo tempo quando ne avrò bisogno
A questo punto mi è rimasta un’ultima cosa da fare per quanto riguarda la condivisione della rete, ovvero condividere la cartella del progetto da renderizzare con tutto il gruppo Home e consentire permessi sia in scrittura che in lettura. Ovviamente questa operazione va fatto dal pc master
Per comodità io ho condiviso e dato i permessi a tutta la cartella “project” di maya
 
<h3>Configurazione di Backburner</h3>
Ora se tutto è andato liscio e non ho dimenticato niente bisogna aprire,
nel master:
BB manager e impostare una cartella di lavoro per Backburner stesso (io ne ho creata una dentro la cartella “maya” chiamandola backburner_job)
LA CARTELLA DI LAVORO DA IMPOSTERE IN “WIN32 PATH”  DEVE ESSERE UN PERCORSO DI RETE
Ovviamente Backburner sfruttando il protocollo TCP/IP sfrutta delle porte, io ho lasciato quelle di default di BB ma potete cambiarle
<img src="{{ site.baseurl }}/images/backburner/manager.jpg" alt="Manager" style="width: auto;"/>
Nel master e nei server:
Dovete lanciare BB Server che vi chiederà un nome da assegnare alla macchina corrente e l’ip della macchina su cui è aperto il manager
<img src="{{ site.baseurl }}/images/backburner/server.jpg" alt="Server" style="width: auto;"/>
A questo punto se aprite il monitor dovreste vedere i server connessi alla rete in uno stato di attesa
<img src="{{ site.baseurl }}/images/backburner/monitor.jpg" alt="Monitor" style="width: auto;"/>
nella foto i server sono 1 occupato e un inattivo,nel vostro caso c’è scritto “idle”
Creazione del Backburner job
A questo punto non ci resta che impostare il lavoro da dare in pasto al renderer e da far gestire a Backburner, il tutto ovviamente dalla macchina master e all’interno di maya
La cosa importante in questo passaggio e che sia il settaggio del progetto maya sia l’apertura del file devono avvenire tramite PERCORSO DI RETE
Quindi tipo:
\\HOME-PC\Users\Home\Documents\maya\projects\RenderJob
E non:
C:\Users\Home\Documents\maya\projects\RenderJob
Andiamo ora in Render->Create Backburner job
Impostiamo i parametri e clicchiamo su submit job
<img src="{{ site.baseurl }}/images/backburner/BBJOB.jpg" alt="Backburner Job" style="width: auto;"/>
Un discorso a parte andrebbe fatto sui setting,in generale però basta sapere task size sta per il  numero di frame da assegnare ad ogni task,e che sono importantissime le opzioni aggiuntive dove io scritto -mr:art
ovvero che il renderer deve essere mental ray e che deve usare tutti i core della CPU a disposizione.
Questa riga ovviamente e a vostra discrezione.
Ora apriamo il monitor e monitoriamo la situazione da lì, connetto e disconnettendo più o meno server…infatti solo a questo punto Backburner tornerà utile
 
A questo punto vediamo la sorpresa e da qui si capisce il motivo del titolo,infatti BB non solo non utilizza più server per un singolo frame ma ne utilizza uno per task il che vuol dire che sarà come spezzare il file e lanciarlo su due o più macchine diverse.
Altra nota negativa è il fatto che se dividiamo per esempio un task in 50 frame e dopo 25  dobbiamo staccare uno dei server,quando il task verrà ripreso da qualsiasi altro server o da quello di prima,il task ricomincerà da capo,ecco perchè non conviene mai dividere in task di troppi frame(questo però non avviene se il software 3d primario utilizzato permette di skippare i frame gia calcolati)
La cosa positiva invece è che possiamo in questo modo batchare più renderer,più software,più scene ecc e gestirle con una GUI abbastanza minimale che ci permette di fare le operazioni basilari che altrimenti dovremmo fare tramite prompt dei comandi