# face-ha

wiki  https://github.com/nicola-bertelli/face-ha/wiki

<p dir="ltr"><span>Da terminale richiamiamo il men&ugrave; di configurazione del Raspberry:</span></p>
<ul>
<li dir="ltr">
<p dir="ltr"><strong>sudo raspi-config</strong></p>
</li>
</ul>
<p dir="ltr">scegliamo <strong>Interfacing options</strong> quindi <strong>camera</strong>, confermiamo</p>
<div><span>
		
	<div class="post_image" style="background-image:url('/getimage/i=1291-c=88580-w=1000-q=90/img2.jpg');">
		
	</div>
	
	</span></div>
<p dir="ltr"><span>Ora dovremo andare ad installare tutta una serie di pacchetti lanciando i comandi che vi elenco sotto:</span></p>
<ul>
<li dir="ltr">
<p dir="ltr"><strong>sudo apt-get install cmake git python3 python3-pip</strong></p>
</li>
</ul>
<ul>
<li dir="ltr">
<p dir="ltr"><strong><strong>sudo pip3 install --upgrade picamera[array]</strong></strong></p>
</li>
</ul>
<ul>
<li dir="ltr">
<p dir="ltr"><strong>sudo pip3 install dlib</strong></p>
</li>
</ul>
<ul>
<li dir="ltr">
<p dir="ltr"><strong>sudo apt-get install libgtk2.0-dev libatlas3-base&nbsp;<span id="docs-internal-guid-9444a3f4-7fff-32d6-bb3a-d364ec3515db"><span>libopenjp2-7</span></span></strong></p>
</li>
</ul>
<ul>
<li dir="ltr">
<p dir="ltr"><strong>sudo pip3 install pyyaml paho-mqtt</strong></p>
</li>
</ul>
<ul>
<li dir="ltr">
<p dir="ltr"><strong>sudo pip3 install face_recognition</strong></p>
</li>
</ul>
<p dir="ltr"><span>Ora scarichiamo i file necessari dalla mia repository Github e spostiamo la cartella di nostro interesse nella home:</span></p>
<ul>
<li dir="ltr">
<p dir="ltr"><strong>git clone --single-branch <a href="https://github.com/nicola-bertelli/ir_home_assistant.git" target="_blank">https://github.com/nicola-bertelli/</a>face-ha.git</strong></p>
</li>
</ul>
<ul>
<li dir="ltr">
<p dir="ltr"><strong>cp -R face-ha/riconoscimento ~/</strong></p>
</li>
</ul>
<ul>
<li dir="ltr">
<p dir="ltr"><strong>sudo rm -r face-ha</strong></p>
</li>
</ul>
<p style="text-align: center;"><strong>Fase 2: Configurazione del sistema e creazione delle immagini campione</strong></p>
<p dir="ltr"><span>Ora possiamo procedere eseguendo lo script </span><span>fotografia.py </span><span>che andr&agrave; a scattare una fotografia appunto e la salver&agrave; nella cartella corrente:</span></p>
<p dir="ltr"><strong>cd riconoscimento</strong></p>
<p dir="ltr"><strong>python3 fotografia.py</strong></p>
<p dir="ltr"><span> Se l&rsquo;immagine non &egrave; orientata correttamente, possiamo entrare nel file di configurazione che ho creato (</span><span>config.yaml), </span><span>per modificare il valore dei gradi, sotto a rotazione</span></p>
<div><span>
		
	<div class="post_image" style="background-image:url('/getimage/i=1292-c=83719-w=1000-q=90/img3.jpg');">
		
	</div>
	
	</span></div>
<div>&nbsp;</div>
<div>ripetiamo lo script fino a che l&rsquo;orientamento &egrave; corretto e soprattutto otteniamo una buona inquadratura del nostro viso.</div>
<div>
<p dir="ltr"><span>Vi consiglio di posizionare la camera in un luogo abbastanza luminoso, e magari a sfondo chiaro, non sarebbe male anche ritagliare un minimo l&rsquo;immagine in modo da ottenere un file pi&ugrave; leggero e quindi veloce da elaborare.</span></p>
<p dir="ltr"><span>L&rsquo;immagine ottenuta andr&agrave; rinominata a piacere, visto che verr&agrave; accostata ad una persona di cui vogliamo eseguire il riconoscimento come andremo a dichiarere proseguendo la compilazione del </span><span><strong>config.yaml</strong>:</span></p>
<div><span>
		
	<div class="post_image" style="background-image:url('/getimage/i=1293-c=22695-w=1000-q=90/img4.jpg');">
		
	</div>
	
	</span></div>
</div>
<p dir="ltr"><span>Nella sezione &ldquo;</span><strong>persone_conosciute</strong><span>&rdquo; mettiamo il </span><strong>nome</strong><span> della persona, il </span><strong>file</strong><span> di riferimento e il </span><strong>topic_sensore</strong><span>, cio&egrave; il topic dove andremo a pubblicare un messaggio MQTT, necessario per la creazione del sensore in Home Assistant.</span></p>
<p dir="ltr"><span>Non dimentichiamoci di mettere l&rsquo;indirizzo ip del server MQTT.</span></p>
<div style="text-align: center;"><strong><span id="docs-internal-guid-39555f76-7fff-214a-eec7-f8ebf4e15ef0">Fase 3: Avvio dello script confronto.py</span></strong></div>
<p dir="ltr"><span>Bene, ora &egrave; il momento di testare se tutto va per il meglio:</span></p>
<p dir="ltr">Lanciamo lo script:</p>
<div><strong><span id="docs-internal-guid-7e52d832-7fff-93d4-8596-00688a8fb678">python3 confronto.py</span></strong></div>
<p dir="ltr"><span>Vi avverto che l&rsquo;avvio non &egrave; velocissimo (circa un minuto), ma una volta che tutto si &egrave; inizializzato senza errori, l&rsquo;elaborazione &egrave; sufficientemente veloce.</span></p>
<div><span>
		
	<div class="post_image" style="background-image:url('/getimage/i=1294-c=73331-w=1000-q=90/img5.jpg');">
		
	</div>
	
	</span></div>
<p dir="ltr"><span>
		
	<div class="post_image" style="background-image:url('/getimage/i=1295-c=31242-w=1000-q=90/img6.jpg');">
		
	</div>
	
	</span></p>
<div>
<p dir="ltr"><span>Ora non ci resta che lanciare al boot del sistema il file avvio.sh:</span></p>
<ul>
<li dir="ltr">
<p dir="ltr"><strong>sudo crontab -e</strong></p>
</li>
</ul>
<p dir="ltr"><span>andiamo in fondo al file e aggiungiamo:</span></p>
<p dir="ltr"><strong>@reboot sh /home/pi/avvio.sh</strong></p>
<p dir="ltr"><span>usciamo e riavviamo.</span></p>
<div style="text-align: center;"><strong><span id="docs-internal-guid-2a5b514e-7fff-51da-b4af-d9a3090d75d3">Fase 4: Creazione sensore Home Assistant</span></strong></div>
</div>
<p dir="ltr"><span><span id="docs-internal-guid-374feb5d-7fff-b49b-9755-30c45baf59ae"><span>Ora dovremo creare un sensore per ogni persona seguendo l&rsquo;esempio sotto:</span></span></span></p>
<div><span>
		
	<div class="post_image" style="background-image:url('/getimage/i=1296-c=74263-w=1000-q=90/img7.jpg');">
		
	</div>
	
	</span></div>
<div>&nbsp;</div>
<div>Da notare che dopo 10 secondi il sensore torna allo stato iniziale, quando qualcuno viene riconosciuto lo stato &egrave; &ldquo;presente&rdquo;</div>
<div>&nbsp;</div>
<div>
		
	<div class="post_image" style="background-image:url('/getimage/i=1297-c=44861-w=1000-q=90/img8.jpg');">
		
	</div>
