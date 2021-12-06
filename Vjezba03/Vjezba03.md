# Sigurnost Računala i Podataka

# 3. Laboratorijska vježba

### ***Message authentication and integrity***

November 2, 2021 

**Cilj vježbe je primjeniti teoreteske spoznaje o osnovnim kritografskim mehanizmima za autentikaciju i zaštitu integriteta poruka u praktičnom primjerima. Pri tome ćemo koristiti simetrične i asimetrične krito mehanizme**

- *message authentication code (MAC)*
- *digitalne potpise*

 **zasnovane na javnim ključevima.**

### ***Izazov 1***

**Implementirajte zaštitu integriteta sadržaja dane poruke primjenom odgovarajućeg *message authentication code (MAC)* algoritma. Koristite pri tome HMAC mehanizam iz Python biblioteka `[cryptography](https://cryptography.io/en/latest/hazmat/primitives/mac/hmac/)`.**

**Na početku se stvara virtualno okruženje u Pythonu imena `env` u radnom direktoriju**

```bash
python -m venv env
```

---

<aside>
💡 Ukoliko se javi error:
*.\activate : File C:\Users\mateo\Desktop\Vjezba3\env\Scripts\Activate.ps1 cannot be loaded because running scripts is disabled on this system.*
Potrebno je pokrenuti terminal u administrativnom modu i pokrenuti slijedeći kôd

</aside>

```bash
Set-ExecutionPolicy RemoteSigned
```

---

**Zatim se ulazi u radni direktorij `Scripts` te se pokrene datoteka `activate`**

```bash
cd env
cd .\Scripts
.\activate
```

**U početnom direktoriju, gdje se nalazi stvoreno okruženje, instalira se paket `cryptography`**

```bash
pip install cryptography
```

**Pokreće se, u istom direktoriju, Visual Studio Code**

```bash
code .
```

**Unutar VSC se otvori nova datoteka naziva `message_integrity.py` i napiše se slijedeći kôd**

```python
import os
from cryptography.hazmat.primitives import hashes, hmac
from cryptography.exceptions import InvalidSignature

def generate_MAC(key, message):
    if not isinstance(message, bytes):
        message = message.encode()

    h = hmac.HMAC(key, hashes.SHA256())
    h.update(message)
    signature = h.finalize()
    return signature

# mac je signature

def verify_MAC(key, signature, message):
    if not isinstance(message, bytes):
        message = message.encode()

    h = hmac.HMAC(key, hashes.SHA256())
    h.update(message)
    try:
        h.verify(signature)
    except InvalidSignature:
        return False
    else:
        return True

if __name__ == "__main__":

    key = "stankovic_mateo".encode()

    for ctr in range(1, 11):

        msg_filename = f"order_{ctr}.txt"
        sig_filename = f"order_{ctr}.sig"

        print(msg_filename)

        msg_filename_path = os.path.join(f"C:\\Users\mateo\Desktop\Vjezba3\env\challenges\stankovic_mateo\mac_challenge", msg_filename)
        sig_filename_path = os.path.join(f"C:\\Users\mateo\Desktop\Vjezba3\env\challenges\stankovic_mateo\mac_challenge", sig_filename)

        with open(msg_filename_path, "rb") as file:
            message = file.read()

        with open(sig_filename_path, "rb") as file:
            signature = file.read()

        is_authentic = verify_MAC(key, signature, message)

        print(f'Message {message.decode():>45} {"OK" if is_authentic else "NOK":<6}')
```

**Dobiven je output:**

order_1.txt
Message    Sell 84 shares of Tesla (2021-11-21T18:22) NOK

order_2.txt
Message     Buy 51 shares of Tesla (2021-11-18T18:51) OK

order_3.txt
Message     Buy 77 shares of Tesla (2021-11-15T20:00) NOK

order_4.txt
Message     Buy 10 shares of Tesla (2021-11-19T04:15) OK

order_5.txt
Message     Buy 22 shares of Tesla (2021-11-19T02:44) OK

order_6.txt
Message    Sell 83 shares of Tesla (2021-11-17T16:49) NOK

order_7.txt
Message      Buy 6 shares of Tesla (2021-11-16T05:26) OK

order_8.txt
Message    Sell 39 shares of Tesla (2021-11-15T21:00) NOK

order_9.txt
Message    Sell 89 shares of Tesla (2021-11-16T17:37) NOK

order_10.txt
Message    Sell 55 shares of Tesla (2021-11-22T09:02) NOK

**Iz outputa se vidi da nije u potpunosti ispravan redosljed transakcija sa odgovarajućim dionicama.**

### **Digital signatures using public-key cryptography**

**U ovom izazovu treba se odrediti autentična sliku (između dvije ponuđene) koju je profesor potpisao svojim privatnim ključem. Odgovarajući javni ključ dostupan je na gore navedenom serveru.**

**Slike i odgovarajući digitalni potpisi nalaze se u direktoriju `prezime_ime\public_key_challenge`.** 

## *Nastavak ubrzo slijedi...*