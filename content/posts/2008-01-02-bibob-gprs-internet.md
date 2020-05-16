---
categories:
- Tech Stuff
date: "2008-01-02T15:09:49Z"
meta:
  _edit_last: "1"
  dsq_thread_id: "6218641077"
status: publish
tags:
- bibob
- e65
- gprs
- gsm
- modem
- nokia
title: BiBoB GPRS Internet
---
Efter jeg er flyttet har jeg ikke fået bestilt internet&nbsp; i god nok tid :(

&nbsp;Hvad skal man da bruge al den tid til... Så tænkte jeg, at jeg ville bruge min nye Nokia mobil til at gå på nettet på (E65). Jeg forbinder den til computeren via Bluetooth, og får installeret modem driveren uden de store problemer...Her efter&nbsp;begynder det at gå skævt.. Jeg finder ud af at jeg skal ringe op til \*99#, jeg laver så&nbsp;en opkaldsforbindelse uden brugernavn og kodeord, som så ringer op til \*99#.. Det går også fint, den forbinder til "netværket" og prøver at registrere computeren derpå.. Det ender med en fejlmeddelelse PPP: 734 fejl... Jeg prøver flere gange uden held.

Idag har jeg så haft&nbsp;snakket med Bibob&nbsp;support, tænkte det kunne jo være, at man skulle ringe op til et andet nummer, eller måske skulle bruge et brugernavn/kodeord... Men dette var ikke tilfældet, og fik at vide af den flinke dame, at hvis det virkede på telefonen burde det også virke på computeren... Efter lang tids prøven frem og tilbage, fandt jeg selv ud af det..

Når man installerer opkaldsforbindelsen henter den noget information fra telefonen om adgangspunktet, dette gemmes som en modem initialiseringsstreng. (Egenskaber for modem, Avanceret, yderligere initialiseringskommandoer)

Her stod der&nbsp; CGDCONT=,"","Internet.bibob.dk"

Dette virker ikke, og resultererede i en "PPP:734; ppp-netværksprotokollen blev afsluttet" fejl.

Jeg rettede ovenstående initialiseringsstreng til

> at+CGDCONT=1,"IP","Internet.bibob.dk"

derefter har jeg ikke haft nogle problemer med at forbinde til nettet. (Ud over telefon regningen)

