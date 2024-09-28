## Cracking Diffie-Hellman
### Steps taken to crack the code:


First, I listed out the known variables and how they relate to each other

```python
# ===== KNOWN VARIABLES ===== #
PRIME_G = 7
PRIME_P = 97
A = 53
B = 82

## we know:
##### A = PRIME_G**b % PRIME_P
##### B = PRIME_G**a % PRIME_P
## we want to find:
##### b, a
##### KEY = PRIME_G**(a*b) % PRIME_P
g = PRIME_G
p = PRIME_P
```

Then I defined some functions to compute the secret exponents, a and b, chosen by Alice and Bob, respectively.

``` python
# tries all exponents, a, less than p until g**a % p = A = 53
def find_a(g, p):
    a = 0
    while a < p:
        a += 1
        if pow(g, a, p) == A:   # g**a % p == A
            return a

# tries all exponents, b, less than p until g**b % p = B = 82
def find_b(g, p):
    b = 0
    while b < p:
        b += 1
        if pow(g, b, p) == B:  # g**b % p == B
            return b
```

Next, I simply ran the functions. Once a and b are computed, we calculate the key through the formula: 

$$key = g^{ab} \hspace{3pt} mod \hspace{3pt} p$$

```python     
# ===== FINDING THE VARIABLES ===== #
a = find_a(g, p)
b = find_b(g, p)

key = pow(g, a*b, p)   # g**(a*b) % p
```

From that we have:

```python
a = 22
b = 41
key = 65  # shared secret
```
#### Steps that would have failed:

The part that would be much harder if the primes were larger would be calculating `a` and `b` using the `find_a` and `find_b` functions. Right now the method is simply to try out every possible exponent less than `p` until one sticks. But if `p` is sufficiently large and `a` and `b` are sufficiently large, that process could span years, lifetimes, the age of the universe.

## Cracking RSA
### Steps taken to crack the code:

Once again, I listed out the various known elements and how they relate to the other variables I would need to find.

```python
# ===== KNOWN VARIABLES ===== #
N_BOB = 162991
E_BOB = 13

## we know:
##### N_BOB = p * q
##### E_BOB = 1 < E_BOB < delta s.t gcd(E_BOB, delta) = 1
## we want to find:
##### p, q
##### delta = (p-1) * (q-1) > 13
##### d_bob but we know
##### E_BOB*d_bob % delta = 1
```

The next step was to calculate the primes `p` and `q` that make up `N_BOB`. To do that, it was a matter of iterating through all number up to the square root of `N_BOB`.

```python
def find_p_q(n):
    for i in range(3, math.floor(math.sqrt(n)+1)):
        if n % i == 0:
            return i, n//i
```

Once `p` and `q` where found, I found `delta` by calculating the lcm of `p-1` and `q-1`.

```python
def lcm(a, b):
    return int((a*b) / math.gcd(a, b))
``` 

From there I could calculate `d_bob` by iterating until I find a number such that `E_BOB * d_bob % delta = 1`

```python
def find_d(e, delta):
    result = 0
    d = 0
    while result != 1:
        d += 1
        result = (e*d) % delta
    return d
```

The values for `p`, `q` and `d_bob` were as follows:

```python
p = 389
q = 419
delta = 81092
d_bob = 43665
```

Once `d_bob` was found, I could decrypt the message by taking each message chunk, raising it to the power of `d_bob` and computing its value modulo `N_BOB`

```python
# takes in the message and the secret key, sk = [d, n]
def decrypt(message, sk):
    decrypted = []
    for char in message:
        dec_char = pow(char, sk[0], sk[1])  # char**sk[0] % sk[1]
        decrypted.append(dec_char)
    return decrypted
```

The decrypted but encoded message was:

```python
encoded_message = [17509, 24946, 8258, 28514, 11296, 25448, 25955, 27424, 29800, 26995, 8303, 30068, 11808, 26740, 29808, 29498, 12079, 30583, 30510, 29557, 29302, 25961, 27756, 24942, 25445, 30561, 29795, 26670, 26991, 12064, 21349, 25888, 31073, 11296, 16748, 26979, 25902]
```

However, because the message was encoded, the next step was to decode the message. Looking at the encoded message, I could see that there was a decimal encoding that had happened but I wasn't sure where to go from there. However, in class, Jeff mentioned that the encoding was from ascii to hex to decimal. That is the ASCII characters were represented using hexadecimal. Each character was then paired together. That new 4 digit hexadecimal number was then converted to decimal. 
Thus, to decode the message, I changed the decimal numbers to their hexadecimal representation and then decoded the hex back to ascii.

```python
def decode_Alice(message):
    plaintext = []
    for dec in message:
        hex_num = hex(dec).split('x')[-1]  # gets rid of the 0x 
        plaintext.append(bytearray.fromhex(hex_num).decode()) # converts hex to ascii
    return "".join(plaintext) # turns the list back into a string
```

The resulting plaintext was:

```python
plaintext = "Dear Bob, check this out. https://www.surveillancewatch.io/ See ya, Alice."
```

#### Steps that would have failed:
The step that would have failed is the first step of calculating the primes `p` and `q`. Every other step depends on what the 2 primes are however, if `N_BOB` was sufficiently large, it would be incredibly difficult to factor it into the 2 primes that made it up. Thus you would have a loop going through all those numbers which would, if the numbers are massive, take a very, very long time.

#### Why the encoding is insecure:
The encoding is still insecure because it using pairs of letters. Each message chunk is encrypted in the same way so the same pair of letters would have the same ciphertext. At that point, it may as well just be like the caesar cipher because the numbers woould just be another name for a specific pair of letters. Thus, a frequency table could easily be drawn up and the characters could be figured out without having to know what the original primes are. 