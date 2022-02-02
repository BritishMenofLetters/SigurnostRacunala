# Sigurnost Računala i Podataka

# 6. **Laboratorijska vježba**

### ***Linux permissions and ACLs***

**Cilj vježbe je upoznavanje s osnovnim postupkom upravljanja korisničkim računima na Linux OS-u. Pri tome će se poseban naglasak staviti na kontrolu pristupa (eng. *access control*) datotekama, programima i drugim resursima Linux sustava**

**Najprije se ulazi u Linux okruženje naredbom**

```bash
wsl
```

**Zatim se stvaraju dva korisnička raučna pri čemu treba imati prava privilegiranog korisnika**.

```bash
sudo adduser alice5
```

- *Ispis:*
    
    [sudo] password for student:
    Adding user alice5' ... Adding new group ‘alice5' (1010) ...
    Adding new user alice5' (1007) with group ‘alice5' ...
    Creating home directory ‘/home/alice5' ... Copying files from ‘/etc/skel' ...
    Enter new UNIX password:
    Retype new UNIX password:
    passwd: password updated successfully
    Changing the user information for alice5
    Enter the new value, or press ENTER for the default
    Full Name []:
    Room Number []:
    Work Phone []:
    Home Phone []:
    Other []:
    Is the information correct? [Y/n] y
    

**Zatim ide pokušaj prijave `alice5`**

```bash
su - alice5
Password: alice
```

**Nakon uspješne prijave izađe se iz `alice5` računa i isti postupak se napravi i za drugi korisniči račun `bob5`**

```bash
exit
```

**Kako je potrebno korištenje dva korisnička računa u isto vrijeme, terminal se prepolovi prečacom `alt + shift + -`**

**Provjerom id korisničkog računa `alice5`**

```bash
id
```

**Dobije se ispis**

uid=1007(alice5) gid=1010(alice5) groups=1010(alice5)ž

---

**Za `bob5`**

uid=1008(bob5) gid=1011(bob5) groups=1011(bob5)

---

**Te se vrati u home direktorij i stvori novi txt fil**

```bash
cd
mkdir srp
cd srp/
dir
echo Hello world > security.txt
```

**Provjerom dopuštenja, ispis podsjeća na access control liste koja se veže iu objekt (prava). Postoje 3 vrste subjekata (`owner` resursa, `grupa` resursa, `ostali`)**

```bash
ls -l
```

- *Ispis*:
    
    total 4
    -rw-rw-r-- 1 alice5 alice5 12 Jan 18 11:14 security.txt
    

**Malo eksplicitniji način bi bio**

```bash
getfacl security.txt
```

- *Ispis:*
    
    # file: security.txt
    # owner: alice5
    # group: alice5
    user::rw-
    group::rw-
    other::r--
    

**Može se izlistati svojstvo trenutnog direktorija, koji također izgleda kao ACL.**

```bash
getfacl .
```

- *Ispis:*
    
    # file:  .
    # owner: alice5
    # group: alice5
    user::rwx
    group::rwx
    other::r-x
    

**Primjeti se razlike u file-u oi direktoriju. File se ne može executati od strane user i group i ostali dok direktorij može - kao može se mijenjati direktorij**

**Oduzme li se pravo pristupa datoteci owneru (samom sebi u ovom slučaju)**

```bash
chmod u-r security.txt
```

**pristup neće biti dopušten**

```bash
cat security.txt
```

*Ispis:* cat: security.txt: Permission denied

**Vraćamo oduzeto pravo**

```bash
chmod u+r security.txt
```

**Može se na još jedan način onemogućiti user-u pristup. a da i dalje ostane pravo čitanja. Ideja je maknuti pravo exe direktoriju gdje se nalazi file. Time je rješeno ulazak u direktorij.**

**Ukoliko se pokuša pristupiti file-u kao korisnik `bob5`**

```bash
cat /home/alice5/srp/security.txt
```

*Ispis: Hello world*

**Iz čega se vidi da `bob5` ima pravo jer on spada kao `ostali` koji imaju pravo čitanja**

**Ukoliko owner (`alice5`) makne pristup `bob5`**

```bash
chmod o-r security.txt
getfacl security.txt
```

- *Ispis:*
    
    # file:  security.txt
    # owner: alice5
    # group: alice5
    user::rw-
    group::rw-
    other::---
    

**Te `bob5` pokuša pristupiti fileu:**

```bash
cat /home/alice5/srp/security.txt
```

*Ispis:* cat: /home/alice5/srp/security.txt: Permission denied

**Pristup od strane `bob5` nije moguć-**

**Ukoliko se `bob5`  ubaci u grupu `alice5`, gdje su pritom potrebne admin ovlasti**

```bash
sudo usermod -aG alice5 bob5
/mnt/c/Users/A507$ cat /etc/group | grep alice
```

- *Ispis:*
    
    alice2:x:1002:
    alice3:x:1004:
    alice_reading_group:x:1006:
    alice4:x:1007:
    alice_reading_group4:x:1009:
    alice5:x:1010:bob5
    

**Gdje se iz zadnjeg reda ispisa vidi `bob5`**

**Ukoliko `bob5` pokuša pristupiti**

```bash
cat /home/alice5/srp/security.txt
```

*Ispis:* cat: /home/alice5/srp/security.txt: Permission denied

**`Bob5` ne može pristupiti iako ima prava. Razlog tomu je što su tokeni ostali stari pa je potrebno ponovno uligrati `bob5`. Ponovnim pokušajem pristupa, `bob5` uspješno pristupa.**

`Bob5` se miče iz grupe `alice5`

```bash
sudo gpasswd -d bob5 alice5
```

**Sad je potrebno eksplicitno dati `bob5` pravo pristupa modificiranjem ACL**

```bash
sudo setfacl -m u:bob5:r /home/alice5/srp/security.txt
```

**Ukoliko `bob5` pokuša pristupiti (iako se nije ponovno logirao), pristup bude uspješan. Razlog tome je**

```bash
getfacl /home/alice5/srp/security.txt
```

- *Ispis:*
    
    # file:  home/alice5/srp/security.txt
    # owner: alice5
    # group: alice5
    user::rw-
    user:bob5:r--
    group::rw-
    mask::rw-
    other::---
    

**Dodavanje nove grupe**

```bash
sudo groupadd alice_reading_group5
```

**Te provjera je li se kreirala grupa**

```bash
**cat /etc/group**
```

- *Ispis:*
    
    root:x:0:
    daemon:x:1:
    bin:x:2:
    sys:x:3:
    adm:x:4:syslog,student
    tty:x:5:
    disk:x:6:
    lp:x:7:
    mail:x:8:
    news:x:9:
    uucp:x:10:
    man:x:12:
    proxy:x:13:
    kmem:x:15:
    dialout:x:20:student
    fax:x:21:
    voice:x:22:
    cdrom:x:24:student
    floppy:x:25:student
    tape:x:26:
    sudo:x:27:student
    audio:x:29:student
    dip:x:30:student
    www-data:x:33:
    backup:x:34:
    operator:x:37:
    list:x:38:
    irc:x:39:
    src:x:40:
    gnats:x:41:
    shadow:x:42:
    utmp:x:43:
    video:x:44:student
    sasl:x:45:
    plugdev:x:46:student
    staff:x:50:
    games:x:60:
    users:x:100:
    nogroup:x:65534:
    systemd-journal:x:101:
    systemd-network:x:102:
    systemd-resolve:x:103:
    input:x:104:
    crontab:x:105:
    syslog:x:106:
    messagebus:x:107:
    lxd:x:108:
    mlocate:x:109:
    uuidd:x:110:
    ssh:x:111:
    landscape:x:112:
    admin:x:113:
    netdev:x:114:student
    student:x:1000:
    docker:x:1001:student
    alice2:x:1002:
    bob2:x:1003:
    alice3:x:1004:
    bob3:x:1005:
    alice_reading_group:x:1006:
    alice4:x:1007:
    bob4:x:1008:
    alice_reading_group4:x:1009:
    alice5:x:1010:
    bob5:x:1011:
    alice_reading_group5:x:1012:
    

**Zatim `bob5` se ubacuje u tu grupu**

```bash
sudo setfacl -m g:alice_reading_group5:r /home/alice5/srp/security.txt
```

i **provjera:**

```bash
getfacl /home/alice5/srp/security.txt
```

- *Ispis:*
    
    # file:  home/alice5/srp/security.txt
    # owner: alice5
    # group: alice5
    user::rw-
    user:bob5:r--
    group::rw-
    group:alice_reading_group5:r—
    mask::rw-
    other::---
    

---

**U primjerima se vidilo da kernel doušta/nedopušta pokretanje. Svaki proces koji je aktivan ima id vlasnika. U odnosu na id i ACL gleda hoće li pokrenuti proces**

**Ispis trenutnih procesa:**

```bash
ps -ef
```

- *Ispis:*
    
    UID        PID  PPID  C STIME TTY          TIME CMD
    root         1       0    0 10:58 ?        00:00:00 /init
    root       112     1    0 10:58 ?        00:00:00 /init
    root       113   112  0 10:58 ?        00:00:00 /init
    root       114   113  0 10:58 pts/0  00:00:00 /mnt/wsl/docker-desktop/docker-desktop-proxy --distro-name Ubuntu-
    root       122   112  0 10:58 ?         00:00:00 /init
    student  123   122  0 10:58 pts/1  00:00:00 docker serve --address unix:///home/student/.docker/run/docker-cli
    root       140      1   0 11:03 ?         00:00:00 /init
    root       141   140  0 11:03 ?         00:00:00 /init
    student  142   141  0 11:03 pts/2  00:00:00 -bash
    root        238     1    0 11:09 ?         00:00:00 /init
    root        239   238  0 11:09 ?         00:00:00 /init
    student   240   239  0 11:09 pts/3  00:00:00 -bash
    root        442   240   0 11:38 pts/3  00:00:00 su - bob5
    bob5       443   442  0 11:38 pts/3  00:00:00 -su
    student   502   142   0 11:49 pts/2  00:00:00 ps -ef
    

**Oduzimanje prava `bob5`, gdje je najlakše izbrisati cijeli ACL**

```bash
sudo setfacl -b /home/alice5/srp/security.txt
```

**Te provjera**

```bash
getfacl /home/alice5/srp/security.txt
```

- *Ispis:*
    
    # file:  home/alice5/srp/security.txt
    # owner: alice5
    # group: alice5
    user::rw-
    group::rw-
    other::---
    

**Zatim je potrebno kreirati datoteku (skrišta koja otvara file) `lab6_g5.py` , u kojoj je sljedeći kôd**

```python
import os

print('Real (R), effective (E) and saved (S) UIDs:') 
print(os.getresuid())

with open('/home/alice/srp/security.txt', 'r') as f:
    print(f.read())
```

**Ukoliko `admin` pokuša pokrenuti `lab_g5.py`, uspješno pristupa fileu**

```bash
sudo python lab6_g5.py
```

 **A ukoliko `bob5` pokuša napraviti isto, pristup mu je odbijen-**

```bash
python lab6_g5.py
```

---

**Ukoliko `bob5` pokrene**

```bash
passwd
```

**proces se pokreće što je problem jer ovako `bob5` piše u mapu `shadow`, a nema pravo pristupa.**

```bash
getfacl /etc/shadow
```

- *Ispis:*
    
    # file:  etc/shadow
    # owner: root
    # group: shadow
    user::rw-
    group::rw-
    other::---
    

**`Bob5` iako spada u other, uspješno mijenja sadržaj shadow mape. Razlog tome je što postoji funkcionalnost u Linuxu, gdje kernel gleda efektivni id koji pripada ne onom tko ga je pokrenuo, već vlasnik tog file-a iako je real id onog tko prokreće**

```bash
getfacl $(which passwd)
```

- *Ispis:*
    
    # file:  usr/bin/passwd
    # owner: root
    # group: root
    # flags: s—
    user::rwx
    group::r-x
    other::r-x
    

**Realni id nije bitan, već efektivni id vlasnika resursa. Kako pripada `root`, moguće je mijenjati `shadow` mapu. Takvi programi moraju biti pažljivo pisani**

```bash
ps -eo pid,ruid,euid,suid,cmd | grep passwd
```

Ispis:

897 1008 0 0 passwd

904 1000 1000 1000 grep --color=auto passwd

**I iz ispisa se vidi zašto je moguća primjena `shadow` mape.**