---
layout: post
title: MentalRay Satellite network rendering!
---
<img src="{{ site.baseurl }}/assets/blog/mentalray_satellite/satellite-dish-lamit-hub.jpg" alt="mentalray satellite" style="width: auto;"/>

Come avevo promesso oggi parlo del network-rendering utilizzabile su uno stesso frame.
Anche questa è più una repository personale di appunti su come preparare la configurazione, visto che non mi capita molto spesso di farlo
Molto semplicemente lo scopo è utilizzare più PC messi in rete per lavorare sullo stesso frame quindi a differenza di Backburner, che per quanto ci potesse lasciare una maggiore libertà a livello di gestione di una intera animazione, era in ogni caso sostituibile dal più vecchio metodo in cui si preparavano i file per renderizzare frame diversi e si lanciavano su più macchine.
In questo caso infatti la logica è diversa, c’è una sola macchina che prende a prestito la potenza di calcolo delle altre per aumentare la propria capacità computazionale.
Per prima cosa dobbiamo creare una rete tra i pc di cui abbiamo bisogno ma questo non è l’oggetto dell’articolo quindi lo do per scontato.

<!--more-->
 
<h3>1-Mental Ray Satellite</h3>
Dobbiamo installare Mental Ray Satellite su ogni macchina di cui ci serviamo, sia quella master che tutte le slave
http://knowledge.autodesk.com/support/maya/downloads/caas/downloads/content/autodesk-maya-2014-service-pack-1.html
 
<h3>2-Configurazione Servizio</h3>
Ora dobbiamo verificare che non ci siano firewall che blocchino il satellite e che il servizio associato siano attivi in Windows
Per farlo andiamo in “C:\Windows\System32\drivers\etc\services” e controlliamo che la riga
“mi-raysat2014    7414/tcp” sia presente su ogni macchina.
Inoltre stiamo ben attenti a verificare che la porta utilizzata sia la stessa per tutte le macchine nella rete
 
<h3>3-Maya Rayhost</h3>
Infine nella macchina che fa da master, creiamo un file chiamato “maya.rayhosts” nel percorso
C:\Users\Luca\Documents\maya\20xx \pref
E all’interno del file referenziamo le macchine da sfruttare con la relativa porta
 
<h3>Esempio:</h3>
192.168.1.200:7414 ovvero “ip macchina slave: porta associata al servizio Mental Ray satellite”