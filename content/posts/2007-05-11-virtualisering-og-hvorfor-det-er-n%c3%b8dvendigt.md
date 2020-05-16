---
categories:
- Tech Stuff
date: "2007-05-11T00:59:02Z"
excerpt: test 1234
meta:
  dsq_thread_id: "6099514218"
status: publish
tags: []
title: Virtualisering og hvorfor det er nødvendigt.
---
Nathan Winters holdt et excellent foredrag om hvad og hvorfor man skal begynde at overveje at bruge virtualisering, jeg vil her prøve at opsummere nogle af de ting, som han lagde vægt på under sit foredrag.  
Moderne servere har enormt mange kræfter, og det sker derfor ofte at det langt overstiger hvad en applikation reelt skal bruge, derfor er det lavet flere undersøgelser, som rent faktisk har vist, at en gennemsnits server idag, kun bruger ca 5% CPU kraft, det vil sige at der er en masse spildte ressourcer.  
Fordele ved at virtualisere flere servere gør, at man vil få en langt højere udnyttelsesgrad af sine server ressourcer, og yderligere muligheder for at sikre højere oppetid på sine applikationer ved at have flere servere kørende. Hvis man kigger på Vmware som idag er markedsførende, understøtter det blandt andet clustering i deres ESX server produkt, det gør at man kan have flere servere til at køre samme applikation, hvis den ene går ned, kan en anden tage over. Jeg vil prøve at gå lidt tættere på nogle af de ting som Vmware’s 3 generation software er i stand til.  
En virtuel maskine i kan idag have 16GB RAM og 4 virtuelle CPU’er, Vmware ESX understøtter fysiske servere med op til 32 fysiske CPU’er (Med idags Quad-core CPU’er betyder det reelt 128 CPU’er) samt 64GB RAM.  
Vmware har en feature de kalder Vmotion, som tillader at flytte en kørende virtuel maskine over på en anden server, det vil sige at har man en CPU tung opgave, og man har en server, der ikke bliver brugt ret meget, kan man uden at slukke den virtuelle server, flytte den over på en anden maskine. Vi testede dette, ved at pinge fra den virtuelle maskine, og pingede den virtulle maskine fra en af host maskinerne, det eneste det resulterede i var at 1 ping ikke kom igennem. Det var meget imporende, og det fleste applikationer idag, kan håndtere sådan et lille netværksudfald. Det er også muligt at få Vmware til selv at håndtere flytningen af serverene, så hvis man har en server der ikke laver noget, og en virtuel maskine på en anden server bruger mange ressourcer, så kan Vmware selv flytte den krævende maskiner, over hvor der er ledige ressourcer. Det kan også bruges hvis en host server rapporterer der er problemer med f.eks en harddisk, så kan man flytte alle kørende virtuelle servere over på en anden server, reparere den pågældende host server, og så flytte de virtuelle maskiner tilbage igen.  
Vmware håndterer ressourcer, ved at lave en ”resource pool” det vil sige at har man en ”server gruppe” kan denne f.eks have 36Ghz CPU ressourcer og 64GB RAM, en anden server gruppe kan have 20Ghz CPU og 32GB RAM. Det betyder at man så kan har en aggregeret logisk ressource på 56Ghz samt 96GB RAM, det skal dog igen siges, at det ikke betyder man kan give en server hele clusterets ressourcer, men kun maks 4 CPU’er og 16GB RAM. Det betyder også at hvis man mangler ressourcer i sit cluster, så installerer man bare en ekstra server, og melder den ind i clusteret, og så bliver dens ressourcer automatisk tilføjet den aggregerede ”Resource pool.”

Hvis man skal køre VmWare hvad enten det er Workstation eller ESX server, er det alfa og omega at have hurtige diske, derfor kan man med stor fordel bruge et SAN (Ihvertfald ved server udgaverne)  
Ved at bruge et SAN, giver det muliged for let at tilføje flere diske, og dermed tilføje mere plads til virtuelle servere, samt gøre det lettere for andre host servere at overtage fra en evt. crashet host server.  
En anden fordel ved Vmware er backup, når man kører Vmware, har man reelt bare en fil som ligger på serveren, denne fil kan kopieres rundt, og startes på hvilken som helst Vmware host. Der findes idag også flere 3 parts produkter, som kører og tager snapsshots af disse filer, selv når serveren kører, og dermed har man hele tiden en fuld kopi af sit produktions miljø hvis noget skulle gå galt.  
Nathan fortalte også at man med nogle af de ovenstående fordele faktisk var begyndt at virtualisere både Exchange & SQL servere.

