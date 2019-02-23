vediamo come sia possibile eseguire un riconoscimento facciale di tutti i componenti della nostra famiglia ( e non solo ) e, quindi, creare dei sensori relativi alla persona riconosciuta da poter inserire in automazioni o script di Home Assistant.

Gestirò il face-recognition utilizzando Python, versione minima 3.5, facendomi aiutare da alcune librerie appositamente create per questo scopo.

Lista della spesa:
1 raspeberry Pi3 o Pi3+, relativo alimentatore e micosd 1 modulo camera per raspberry, io ho utilizzato quello ufficiale ma probabilmente anche quello di AZdelivery funziona uguale Raspi-cam. Non fatevi attirare dalla risoluzione, noi utilizzeremo una definizione molto bassa, altrimenti ci vorrebbe troppo tempo per elaborare le immagini col Raspberry

Fase 1: Collegamento Pi-camera e installazione pacchetti Raspberry
Prima di alimentare il nostro Raspberry colleghiamo la nostra Pi-camera, avendo l’accortezza di tenere la parte colorata di blu rivolta verso lo slot per la connessione di rete. Installiamo Rasbian, anche la versione lite va benissimo.

Da terminale richiamiamo il menù di configurazione del Raspberry:

sudo raspi-config

scegliamo Interfacing options quindi camera, confermiamo

Ora dovremo andare ad installare tutta una serie di pacchetti lanciando i comandi che vi elenco sotto:

sudo apt-get install cmake git python3 python3-pip

sudo pip3 install --upgrade picamera[array]

sudo pip3 install dlib

sudo apt-get install libgtk2.0-dev libatlas3-base libopenjp2-7

sudo pip3 install pyyaml paho-mqtt

sudo pip3 install face_recognition

Ora scarichiamo i file necessari dalla mia repository Github e spostiamo la cartella di nostro interesse nella home:

git clone --single-branch https://github.com/nicola-bertelli/face-ha.git

cp -R face-ha/riconoscimento ~/

sudo rm -r face-ha

Fase 2: Configurazione del sistema e creazione delle immagini campione
Ora possiamo procedere eseguendo lo script fotografia.py che andrà a scattare una fotografia appunto e la salverà nella cartella corrente:

cd riconoscimento

python3 fotografia.py

Se l’immagine non è orientata correttamente, possiamo entrare nel file di configurazione che ho creato (config.yaml), per modificare il valore dei gradi, sotto a rotazione

stato_camera:
    risoluzione:
        x: 320 
        y: 240
    rotazione:
        gradi: 180

ripetiamo lo script fino a che l’orientamento e l'inquadratura del nostro viso sono corretti. Vi consiglio di posizionare la camera in un luogo abbastanza luminoso, e magari a sfondo chiaro, non sarebbe male anche ritagliare un minimo l’immagine in modo da ottenere un file più leggero e quindi veloce da elaborare.

Il file ottenuto andrà rinominato a piacere, visto che verrà accostato ad una persona di cui vogliamo eseguire il riconoscimento come andremo a dichiarare proseguendo la compilazione del config.yaml:

https://github.com/nicola-bertelli/face-ha/blob/master/riconoscimento/config.yaml

Nella sezione “persone_conosciute” mettiamo il nome della persona, il file di riferimento e il topic_sensore, cioè il topic dove andremo a pubblicare un messaggio MQTT, necessario per la creazione del sensore in Home Assistant. Non dimentichiamoci di mettere l’indirizzo ip del server MQTT.

Fase 3: Avvio dello script confronto.py
Bene, ora è il momento di testare se tutto va per il meglio:

Lanciamo lo script:

python3 confronto.py

Vi avverto che l’avvio non è velocissimo (circa un minuto), ma una volta che tutto si è inizializzato senza errori, l’elaborazione è sufficientemente veloce.

Ora non ci resta che lanciare al boot del sistema il file avvio.sh:

sudo crontab -e

andiamo in fondo al file e aggiungiamo:

@reboot sh /home/pi/avvio.sh

usciamo e riavviamo.

Fase 3: Creazione sensore Home Assistant

Ora dovremo creare un sensore per ogni persona seguendo l’esempio sotto:

- platform: mqtt
    name: "Riconoscimento Nicola"
    state_topic: "/riconoscimento_facciale/nicola"
    expire_after: 10
    icon: mdi:face

Da notare che dopo 10 secondi il sensore torna allo stato iniziale, quando qualcuno viene riconosciuto lo stato è “presente”
