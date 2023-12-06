# PKI Lab

### Task 1 - Becoming a Certificate Authority (CA)

In this task we are asked to create and configure a directory following a provided structure.

After we did this we ran the command that is in the lab to generate the self-signed certificate for the CA:

`openssl req -new -x509 -keyout ca.key -out ca.crt -config openssl.cnf`

![Alt text](images/lb11i1.png)

We get asked to choose a passphrase and we chose `dees`. After configuration the other fields that are required we end up with the following directory structure at the end of this task.

![Alt text](images/lb11i2.png)

### Task 2 - Creating a Certificate for SEEDPKILab2020.com

- Step 1 : We run the command `openssl genrsa -aes128 -out server.key 1024` to generate an RSA key pair (public and private keys).

- Step 2 : We run the command `openssl req -new -key server.key -out server.csr -config openssl.cnf` to generate a Certificate Signing Request (CSR) and we get a `server.csr` file in our directory.

![Alt text](images/lb11i3.png)

- Step 3 : We run the command `openssl ca -in server.csr -out server.crt -cert ca.crt -keyfile ca.key \-config openssl.cnf` to turn the signing request into a signature. 

We get an error saying the organization info doesn't match.
To fix this we changed the policy in the configuration file from `policy_match`to `policy_anything`.
After this change we are able to sign the request:

![Alt text](images/lb11i5.png)

### Task 3 - Deploying Certificate in an HTTPS Web Server

- Step 1 : In this step we just mapped the SEEDPKILab2020.com to our localhost in the /etc/hosts file.

- Step 2 : In this step we are asked to configure the web server using the openssl `s_server` command.
First we copy the secret key and the certificate into one file and we run the server using that file.

We ran into an error web trying to serve the server saying that the key is too small:

![Alt text](images/lb11i6.png)

To fix this, we genearted the key and signed the certificate again, but this time used 2048 bits instead of the 1024 suggested by the lab. And now we were able to run the server:

![Alt text](images/lb11i7.png)
![Alt text](images/lb11i8.png)



# This week's CTF

In this week we were tasked to decrypt the flag encrypted with RSA encryption.

When we ran the command `nc ctf-fsi.fe.up.pt 6004`, we were given the `n` for the ciphertext, as well as the public exponent `e`.

We also know that the values of `p` and `q` will be around `2^512` and `2^513` respectively, which makes a brute-force attack viable to use.

Our process will then be to create 2 lists of primes close to `p` and `q` within a certain `delta` of distance to their respective powers of 2, then afterwards calculate the `d` value used in the RSA encryption formula `ed % (p-1)(q-1) = 1` by calculating the modular inverse using the extended greatest common denominator algorithm. To finish, we will use the included decrypting function `dec` with a few changes in order to test all the calculated values until the plaintext is readable and starts with "flag".

To achieve this, we developed a few functions:

### testPrime(n)

```python
def testPrime(n):
    if n == 2 or n == 3:
        return True

    if n <= 1 or n % 2 == 0:
        return False

    r, d = 0, n - 1
    while d % 2 == 0:
        r += 1
        d //= 2

    for _ in range(5):
        a = random.randrange(2, n - 1)
        x = pow(a, d, n)
        if x == 1 or x == n - 1:
            continue
        for _ in range(r - 1):
            x = pow(x, 2, n)
            if x == n - 1:
                break
        else:
            return False
    return True
```

### findD(e, p, d) and the necessary functions

```python
def extended_gcd(a, b):
    if a == 0:
        return b, 0, 1
    else:
        g, x, y = extended_gcd(b % a, a)
        return g, y - (b // a) * x, x

def modinv(a, m):
    g, x, y = extended_gcd(a, m)
    if g != 1:
        return None
    else:
        return x % m

def findD(e, p ,q):
   return modinv(e, (p-1)*(q-1))
```

### dec(msg, d, n)

```python
def dec(msg, d, n):
    int_msg = int.from_bytes(unhexlify(msg), "little")
    res = pow(int_msg,d,n)
    return res.to_bytes(256, 'little')
```

### Main loop

This is the loop to find primes close to `p` and `q`, and the main brute-force decryption to test

```python
list_of_p_primes= []
for i in range(2**512 - 10000, 2**512 + 10000):
    if testPrime(i):
        list_of_p_primes.append(i)

list_of_q_primes= []
for i in range(2**513 - 10000, 2**513 + 10000):
    if testPrime(i):
        list_of_q_primes.append(i)

for p in list_of_p_primes:
    for q in list_of_q_primes:
        if p * q == n:
            print("p: ", p)
            print("q: ", q)
            d = findD(65537, p, q)
            res = dec(unhexlify(msg), d, n)
            if res[:4] == b"flag":
                print(res)
                exit()
```

For the msg value of `3066643239656336303839353539303064303761376566636462313165616137313330356439633165623662633731636536653938386662373639366563623032656433316231663439333335636664303063323963666533366433386236323366386261616663313366633763623736616330353763636136396363313262386661303034306334653631316639626631613933336331343935666437653639306262356138636631636338643462393961383364326230373839633637373131333435313332633332663963333266393833373430643231626538646231623864383737313161333263383039656135613561643661643939323066363930313030303030303030303030303030303030303030303030303030303030303030303030303030303030303030303030303030303030303030303030303030303030303030303030303030303030303030303030303030303030303030303030303030303030303030303030303030303030303030303030303030303030303030303030303030303030303030303030303030303030303030303030303030303030303030303030303030303030303030303030303030303030303030303030303030303030303030303030303030303030303030303030303030303030303030303030303030303030303030303030303030303030303030303030303030`, n value of `359538626972463181545861038157804946723595395788461314546860162315465351611001926265416954644815072042240227759742786715317579537628833244985694861278997871052684504401596494019122799039009989371808178352060966438579515613360227960308969957859170183912581829276320781354104234997246446865042463594219967161283` and public exponent value of `65537`, we got the flag in about 11 seconds:


![Alt text](images/lb11i9.png)