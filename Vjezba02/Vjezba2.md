# Sigurnost Računala i Podataka

# 2. Laboratorijska vježba

### ***Symmetric key cryptography - a crypto challenge***

November 2, 2021 

**U sklopu vježbe riješen je odgovarajući *crypto* izazov, odnosno dešifrirarani je odgovarajući *ciphertext* u kontekstu simetrične kriptografije. Izazov je počivao na činjenici da student nije imao pristup enkripcijskom ključu.**

- **Izazov je rezultat enkripcije odgovarajućeg personaliziranog *plaintext*a korištenjem Fernet sustava.**

**Na početku se stvara virtualno okruženje u Pythonu imena `env`**

```bash
python -m venv env
```

**Zatim se ulazi u radni direktorij `Scripts` te se pokrene datoteka `activate`**

```bash
cd .\Scripts

.\activate
```

**U pokrenutom okruženju instalira se paket `cryptography`**

```bash
pip install cryptography
```

**Izlazi se iz virtualnog okruženja i postavi se radni direktorij u datoteci u kojoj se nalazi datoteka `Scripts` i stvori se *python* datoteka imena `brute_force`** 

```bash
code brute_force.py
```

**Da bi se otkrila odgovarajuća personalizirana datoteka, potrebno je pokrenuti u `brute_force.py`   sljedeći kôd**

```python
from cryptography.hazmat.primitives import hashes

def hash(input):
    if not isinstance(input, bytes):
        input = input.encode()

    digest = hashes.Hash(hashes.SHA256())
    digest.update(input)
    hash = digest.finalize()

    return hash.hex()

if __name__ == "__main__":

    hash_value = hash("stankovic_mateo")
    filename = hash_value + ".encrypted"
    print(hash_value)
```

**Dobivena je hash vrijednost: *6f046a3737577dcf427a04990653776e844d9bba35fb82bd54a643f64d8bcf94* koja uistinu odgovara jednoj od ponuđenih datoteka na serveru.**

**Na kraju se pokreće funkcija brute_force(), kôd u nastavku, da bi se saznao enkripcijski ključ maksimalne entropije 22 (2^22 ≈ 4 200 000 ključeva) i vidjelo da li je ta datoteka uistinu odgovarajuća personalizirana datoteka**

```python
import base64
from cryptography.hazmat.primitives import hashes
from cryptography.fernet import Fernet

def hash(input):
    if not isinstance(input, bytes):
        input = input.encode()

    digest = hashes.Hash(hashes.SHA256())
    digest.update(input)
    hash = digest.finalize()

    return hash.hex()

def test_png(header):
    if header.startswith(b"\211PNG\r\n\032\n"):
        return True

def brute_force():
    filename = "6f046a3737577dcf427a04990653776e844d9bba35fb82bd54a643f64d8bcf94.encrypted"
    # Reading from a file
    with open(filename, "rb") as file:
        ciphertext = file.read()

    ctr = 0
    while True:
        key_bytes = ctr.to_bytes(32, "big")
        key = base64.urlsafe_b64encode(key_bytes)

        if not (ctr + 1) % 1000:
            print(f"[*] Keys tested: {ctr + 1:,}", end="\r")

        try:    
            plaintext = Fernet(key).decrypt(ciphertext)
            
            header = plaintext[:32]
            if test_png(header):
                print(f"[+] KEY FOUND: {key}")
                # Writing to a file
                with open("BINGO.png", "wb") as file:
                    file.write(plaintext)         
                break

        except Exception:
            pass
            
        ctr += 1

if __name__ == "__main__":

    #hash_value = hash("stankovic_mateo")
    #print(hash_value)
    #filename = hash_value + ".encrypted"
    brute_force()
```

`brute_force`  **funkcijom je dobijen ključ *AAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAcnsc=* i ujedno dekriptirana datoteka i spremljena pod imenom `BINGO.png`  čiji je sadržaj uistinu dokazuje uspješno dekriptirani personalizirani izazov (datoteku):**

![BINGO.png](Img/BINGO.png)