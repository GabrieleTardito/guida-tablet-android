# Guida al Setup: Ambiente Python Completo su Android tramite Termux, Code-Server e Termux-X11
Questa guida illustra la configurazione di un ambiente di sviluppo Python su tablet Android, basato su Termux, Code-Server e Termux-X11, per consentire l'esecuzione di script con supporto all'interfaccia grafica.

---

## FASE 1: Installazione delle Applicazioni Necessarie

Prima di aprire i terminali, installare le seguenti 3 applicazioni (essendo 2 di questi apk bisogna dare l'autirizzazione per l'installazionedi app da fonti sconosciute) sul dispositivo Android:

1. **Termux:** Scaricare e installare l'applicazione **Termux** (scegliere da [F-Droid](https://f-droid.org/en/packages/com.termux/) o da [GitHub Releases](https://github.com/termux/termux-app/releases); evitare il Google Play Store in quanto non aggiornato).
2. **Termux-X11:** Scaricare il pacchetto Android `termux-x11-universal-debug.apk` dalla pagina ufficiale [GitHub di Termux-X11](https://github.com/termux/termux-x11/releases) e installarlo sul tablet.
3. **Browser Web:** Un qualsiasi browser moderno come **Google Chrome** o il browser default del tablet (quasi sicuramente almeno uno è già installato).

---

## FASE 2: Configurazione Iniziale dell'Ambiente (Da fare una sola volta)

### 1. Cambio del Mirror e Aggiornamento Repository (su Termux)
Aprire l'app **Termux** per aggiornare l'elenco dei server di pacchetti e il sistema base:

1. Eseguire il comando e premere **Invio**:
   ```bash
   termux-change-repo
   ```
2. Selezionare **"Single mirror"** (o **"Mirrors by Grimler"**) dalla schermata a scelta multipla e confermare con **OK**.
3. Aggiornare i pacchetti di sistema:
   ```bash
   pkg update && pkg upgrade -y
   ```
   *(Premere Invio per accettare le opzioni predefinite in caso di conferme richieste).*

### 2. Abilitazione Repository X11/TUR e Installazione Programmi (su Termux)
Copiare, incollare in Termux ed eseguire questo comando per abilitare i repository per i pacchetti grafici e installare Python, Tkinter, Code-Server e Termux-X11:

```bash
pkg install x11-repo tur-repo -y && pkg install python python-tkinter code-server termux-x11-nightly -y
```

### 3. Installazione Librerie Scientifiche Python (su Termux)
Installare le librerie per l'analisi dati e il plotting tramite i pacchetti precompilati di Termux per garantire la massima stabilità:

```bash
pkg install python-numpy python-scipy python-matplotlib -y
```

### 4. Recupero della Password di Code-Server (su Termux)
Al primo avvio, Code-Server genera una chiave di accesso casuale. Per visualizzarla:

```bash
cat ~/.config/code-server/config.yaml
```
Annotare il valore riportato alla voce `password: xxxxxxxx` per effettuare il primo login dall'interfaccia web.

### 5. Configurazione dell'Ambiente Grafico (DA ESEGUIRE DENTRO CODE-SERVER)
1. Avviare temporaneamente Code-Server da Termux digitando:
   ```bash
   code-server
   ```
2. Aprire il browser web all'indirizzo `http://localhost:8080` e inserire la password recuperata al punto 4.
3. Aprire il **terminale integrato di Code-Server** (**Terminal -> New Terminal**) ed eseguire i seguenti comandi per impostare il backend di Matplotlib e il server X11 a livello di ambiente globale:

```bash
# Imposta TkAgg come backend predefinito per tutti gli script Python
echo "backend: TkAgg" > ~/.config/matplotlib/matplotlibrc

# Rende permanente l'indirizzamento della grafica verso Termux-X11
echo "export DISPLAY=:0" >> ~/.bashrc
source ~/.bashrc
```

---

## FASE 3: Procedura di Avvio Quotidiano

Per iniziare la sessione di lavoro, eseguire i seguenti **3 passaggi in sequenza**:

### Passaggio 1: Avvio dei Server da Termux
Aprire l'app **Termux** e lanciare il comando di avvio per i processi in background:

```bash
termux-x11 :0 & code-server > /dev/null 2>&1 &
```
*(Questo comando avvia in background sia il server per i grafici pop-up sia l'IDE Code-Server, lasciando il terminale libero).*

### Passaggio 2: Apertura Interfacce
1. Aprire l'app **Termux-X11**: verrà visualizzata una schermata nera con il logo `X_` o un cursore centrale. Lasciare l'app aperta o riducila ad icona in background.
2. Aprire il **Browser web** (Chrome o qualunque altro browser) e navigare all'indirizzo:
   ```text
   http://localhost:8080
   ```
   (N.B.: molti browser permettono di salvare una pagina come applicazione sul dispositivo, è consigliato farlo per comodità)
