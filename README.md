### Un kernel module per sperimentare con linux embedded
---

Per la compilazione serve avere in locale gli header del kernel della "giusta" versione (uname -r).

Per installarli occorre decommentare la riga seguente in */etc/apt/sources.list*:
```
deb-src [arch=armhf] http://repos.rcn-ee.com/debian/ stretch main
```

per poi fare un

```
sudo apt-get update
sudo apt-get install linux-headers-$(uname -r)
```
(gli header verranno installati sotto /usr/src)

La compilazione viene avviata con un semplice "make".

Il modulo può essere installato con:
```
sudo insmod <nome-modulo>.ko
```

comodi anche

```
sudo rmmod <nome-modulo> (per la rimozione)
lsmod (lista i moduli installati)
modinfo <nome-modulo>.ko
```

Per dare accesso al device generato (in /dev/<nome-modulo>) occorre copiare il file <NN-nome-modulo>.rules in /etc/udev/rules.d e successivamente usare insmod (oppure rmmod e poi insmod) per reinstallare il modulo.

L'applicazione di test può essere avviata con:
```
./test
```

e il suo scopo è inivare qualcosa dalla userland al modulo kernel e successivamente ricevere qualcos'altro da quest'ultimo. L'accesso simultaneo al modulo kernel è inibito da un apposito mutex.

L'output di debug relativo ai *printk* è visibile sul kern.log, visualizzabile (meglio se in un'altra sessione ssh) con:
```
sudo tail -f /var/log/kern.log
```

Il file .rules può essere generato sulla base di quello nel repository, in cui le entries in sysfs del modulo possono essere ottenute eseguando da /dev:

```
find . -name "<nome-modulo>"
```

che dovrebbe dare qualcosa del tipo:

```
./devices/virtual/<classe-modulo>/<nome-modulo>
 ./class/<classe-modulo>/<nome-modulo>
 ./module/<nome-modulo>
 ```
 
 ed eseguendo un
 
 ```
 udevadm info -a -p /sys/class/<classe-modulo>/<nome-modulo>
 ```
 
 il che dovrebbe portare a:
 
 ```
 KERNEL=="<nome-modulo>"
 SUBSYSTEM=="<classe-modulo>"
 DRIVER==""
 ```
 
 Ricordarsi anche del tool *strace*:
 ```
 sudo apt-get install strace
 sudo strace ./test
 ```
 
 #### Riferimenti
 
 [Blog di Derek Molloy](http://derekmolloy.ie/category/general/linux/)
 
 [Mastering Embedded Linux Programming](https://www.amazon.it/Mastering-Embedded-Linux-Programming-Simmonds/dp/1784392537)
