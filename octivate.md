<project xmlns="http://maven.apache.org/POM/4.0.0" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 https://maven.apache.org/xsd/maven-4.0.0.xsd">
<modelVersion>4.0.0</modelVersion>
<parent>
<groupId>org.springframework.boot</groupId>
<artifactId>spring-boot-starter-parent</artifactId>
<version>3.4.2</version>
<relativePath/>
<!--  lookup parent from repository  -->
</parent>
<groupId>com.mursalin</groupId>
<artifactId>ai-mail-reply</artifactId>
<version>0.0.1-SNAPSHOT</version>
<name>ai-mail-reply</name>
<description>Demo project for Spring ai</description>
<url/>
<licenses>
<license/>
</licenses>
<developers>
<developer/>
</developers>
<scm>
<connection/>
<developerConnection/>
<tag/>
<url/>
</scm>
<properties>
<java.version>21</java.version>
</properties>
<dependencies>
<dependency>
<groupId>org.springframework.boot</groupId>
<artifactId>spring-boot-starter-web</artifactId>
</dependency>
<dependency>
<groupId>org.springframework.boot</groupId>
<artifactId>spring-boot-starter-webflux</artifactId>
</dependency>
<dependency>
<groupId>org.projectlombok</groupId>
<artifactId>lombok</artifactId>
<optional>true</optional>
</dependency>
<dependency>
<groupId>org.springframework.boot</groupId>
<artifactId>spring-boot-starter-test</artifactId>
<scope>test</scope>
</dependency>
<dependency>
<groupId>io.projectreactor</groupId>
<artifactId>reactor-test</artifactId>
<scope>test</scope>
</dependency>
</dependencies>
<build>
<plugins>
<plugin>
<groupId>org.apache.maven.plugins</groupId>
<artifactId>maven-compiler-plugin</artifactId>
<configuration>
<annotationProcessorPaths>
<path>
<groupId>org.projectlombok</groupId>
<artifactId>lombok</artifactId>
</path>
</annotationProcessorPaths>
</configuration>
</plugin>
<plugin>
<groupId>org.springframework.boot</groupId>
<artifactId>spring-boot-maven-plugin</artifactId>
<configuration>
<excludes>
<exclude>
<groupId>org.projectlombok</groupId>
<artifactId>lombok</artifactId>
</exclude>
</excludes>
</configuration>
</plugin>
</plugins>
</build>
</project>

## Lab 1 — Caesar Cipher

### Lab Title

**Encryption and Decryption Using Caesar Cipher**

### Theory

The **Caesar Cipher** is a simple substitution cipher in which every letter of the plaintext is shifted by a fixed number of positions in the alphabet.

It is called a Caesar Cipher because it was reportedly used by **Julius Caesar** to communicate with his military commanders.

For example, if the shift/key is **3**:

```text
Alphabet:  A B C D E F G H I J K L M N O ...
Encrypted: D E F G H I J K L M N O P Q R ...
```

Therefore:

```text
A → D
B → E
C → F
...
X → A
Y → B
Z → C
```

#### Encryption

Let:

* `P` = plaintext letter
* `C` = ciphertext letter
* `k` = shift/key
* `mod 26` = because there are 26 English letters

We represent letters using numbers:

```text
A = 0, B = 1, C = 2, ..., Z = 25
```

The encryption equation is:

$$
C = (P + k) \mod 26
$$

### Example

Suppose:

```text
Plaintext = HELLO
Key = 3
```

Convert letters to numbers:

```text
H = 7
E = 4
L = 11
L = 11
O = 14
```

Apply:

$$
C=(P+3)\mod26
$$

So:

```text
H → K
E → H
L → O
L → O
O → R
```

Therefore:

```text
Plaintext  : HELLO
Ciphertext : KHOOR
```

### Decryption

Decryption reverses the shift.

The equation is:

$$
P = (C-k) \mod 26
$$

For example:

```text
Ciphertext = KHOOR
Key = 3
```

Then:

```text
K → H
H → E
O → L
O → L
R → O
```

Therefore:

```text
KHOOR → HELLO
```

### Important Characteristics

* It is a **symmetric encryption technique** because the same key is used for encryption and decryption.
* There are only **26 possible keys** for the English alphabet.
* Because the key space is very small, Caesar Cipher is **not secure for modern communication**.
* It can easily be broken using a **brute-force attack**, which is exactly what Lab 6 demonstrates.

---

## Python Code

```python
def encrypt(text, key):
    result = ""

    for char in text:
        if char.isalpha():
            start = ord('A') if char.isupper() else ord('a')
            result += chr((ord(char) - start + key) % 26 + start)
        else:
            result += char

    return result


def decrypt(text, key):
    return encrypt(text, -key)


# Input
message = input("Enter message: ")
key = int(input("Enter key: "))

# Encryption
encrypted = encrypt(message, key)
print("Encrypted:", encrypted)

# Decryption
decrypted = decrypt(encrypted, key)
print("Decrypted:", decrypted)
```

# Lab 2 — Playfair Cipher

## Theory

The **Playfair Cipher** is a symmetric substitution cipher that encrypts **two letters at a time** instead of one letter at a time. It uses a **5×5 matrix** constructed from a keyword.

Since the English alphabet has 26 letters but the matrix contains only 25 cells, `I` and `J` are usually treated as the same letter.

### 1. Creating the Key Matrix

Suppose the key is:

```text
MONARCHY
```

First, remove duplicate letters and then fill the remaining alphabet letters, combining `I/J`.

The matrix becomes:

```text
M O N A R
C H Y B D
E F G I K
L P Q S T
U V W X Z
```

### 2. Preparing the Plaintext

The plaintext is divided into pairs of letters.

For example:

```text
HELLO
```

is divided as:

```text
HE LL O
```

But a pair cannot contain the same letter twice. Therefore, an `X` is inserted:

```text
HE LX LO
```

If the final plaintext has an odd number of letters, `X` is also added at the end.

For example:

```text
HELLO → HE LX LO
```

### 3. Encryption Rules

For every pair of letters, there are three cases.

#### Case 1: Same Row

If both letters are in the same row, replace each letter with the letter immediately to its **right**.

The row wraps around from the last column to the first.

Example:

```text
M O N A R
```

For:

```text
MO
```

we get:

```text
M → O
O → N
```

Therefore:

```text
MO → ON
```

#### Case 2: Same Column

If both letters are in the same column, replace each letter with the letter immediately **below** it.

The bottom wraps around to the top.

For example, in the matrix:

```text
M
C
E
L
U
```

we have:

```text
M → C
C → E
```

Therefore:

```text
MC → CE
```

#### Case 3: Different Row and Column

If the two letters are in different rows and columns, they form the corners of a rectangle.

Each letter is replaced by the letter in the **same row but the other letter's column**.

For example:

```text
H E
```

Positions:

```text
H → row 2, column 2
E → row 3, column 1
```

The opposite corners are:

```text
C F
```

Therefore:

```text
HE → CF
```

### Decryption

Decryption reverses the encryption rules.

* Same row → move **left**
* Same column → move **up**
* Rectangle rule → use the same rectangle rule

Thus, encryption and decryption use the same key matrix.

### Example

Using the key:

```text
MONARCHY
```

and plaintext:

```text
HELLO
```

After preparing the plaintext:

```text
HE LX LO
```

Each pair is encrypted according to the Playfair rules to produce the ciphertext.

The Playfair Cipher is stronger than a simple Caesar Cipher because it encrypts **pairs of letters** rather than individual letters.

---

## Code

```python
def create_matrix(key):
    key = key.upper().replace("J", "I")
    alphabet = "ABCDEFGHIKLMNOPQRSTUVWXYZ"

    chars = []

    for char in key + alphabet:
        if char in alphabet and char not in chars:
            chars.append(char)

    return [chars[i:i + 5] for i in range(0, 25, 5)]


def find_position(matrix, char):
    for row in range(5):
        for col in range(5):
            if matrix[row][col] == char:
                return row, col


def prepare_text(text):
    text = text.upper().replace("J", "I")
    text = "".join(c for c in text if c.isalpha())

    result = ""
    i = 0

    while i < len(text):
        a = text[i]

        if i + 1 == len(text):
            result += a + "X"
            i += 1

        elif text[i + 1] == a:
            result += a + "X"
            i += 1

        else:
            result += a + text[i + 1]
            i += 2

    return result


def process(text, matrix, decrypt=False):
    result = ""

    shift = -1 if decrypt else 1

    for i in range(0, len(text), 2):
        a, b = text[i], text[i + 1]

        r1, c1 = find_position(matrix, a)
        r2, c2 = find_position(matrix, b)

        if r1 == r2:
            result += matrix[r1][(c1 + shift) % 5]
            result += matrix[r2][(c2 + shift) % 5]

        elif c1 == c2:
            result += matrix[(r1 + shift) % 5][c1]
            result += matrix[(r2 + shift) % 5][c2]

        else:
            result += matrix[r1][c2]
            result += matrix[r2][c1]

    return result


# Input
key = input("Enter key: ")
message = input("Enter message: ")

matrix = create_matrix(key)

print("\nKey Matrix:")
for row in matrix:
    print(" ".join(row))

prepared = prepare_text(message)

encrypted = process(prepared, matrix)
decrypted = process(encrypted, matrix, True)

print("\nPrepared Text:", prepared)
print("Encrypted:", encrypted)
print("Decrypted:", decrypted)
```


# Lab 3 — Monoalphabetic Substitution Cipher

## Theory

A **Monoalphabetic Substitution Cipher** is a substitution technique in which each plaintext letter is replaced by exactly one corresponding ciphertext letter.

Unlike the Caesar Cipher, where the substitution follows a fixed shift, a monoalphabetic cipher uses a **random or predefined permutation of the alphabet**.

For example:

```text
Plain :  ABCDEFGHIJKLMNOPQRSTUVWXYZ
Cipher:  QWERTYUIOPASDFGHJKLZXCVBNM
```

This means:

```text
A → Q
B → W
C → E
D → R
...
```

The substitution remains the same throughout the entire message.

### Encryption

Let:

* `P` = plaintext letter
* `C` = ciphertext letter
* `f()` = substitution mapping

Then:

$$
C = f(P)
$$

For example, if:

```text
A → Q
B → W
C → E
```

then:

```text
ABC → QWE
```

### Decryption

For decryption, the reverse mapping is used:

$$
P = f^{-1}(C)
$$

For example:

```text
Q → A
W → B
E → C
```

Therefore:

```text
QWE → ABC
```

### Example

Suppose we use:

```text
Plain alphabet : ABCDEFGHIJKLMNOPQRSTUVWXYZ
Cipher alphabet: QWERTYUIOPASDFGHJKLZXCVBNM
```

For the plaintext:

```text
HELLO
```

we get:

```text
H → I
E → T
L → S
L → S
O → G
```

Therefore:

```text
Plaintext : HELLO
Ciphertext: ITSSG
```

The important difference from Caesar Cipher is that the substitution does **not** have to follow a numerical shift.

A monoalphabetic cipher has a very large key space:

$$
26!
$$

possible alphabet permutations.

However, it can still be attacked using **frequency analysis**, because the same plaintext letter is always represented by the same ciphertext letter.

---

## Code

```python
def encrypt(text, key):
    alphabet = "ABCDEFGHIJKLMNOPQRSTUVWXYZ"
    key = key.upper()

    result = ""

    for char in text:
        if char.isalpha():
            index = alphabet.index(char.upper())
            encrypted = key[index]

            if char.islower():
                encrypted = encrypted.lower()

            result += encrypted
        else:
            result += char

    return result


def decrypt(text, key):
    alphabet = "ABCDEFGHIJKLMNOPQRSTUVWXYZ"
    key = key.upper()

    result = ""

    for char in text:
        if char.isalpha():
            index = key.index(char.upper())
            decrypted = alphabet[index]

            if char.islower():
                decrypted = decrypted.lower()

            result += decrypted
        else:
            result += char

    return result


# Input
message = input("Enter message: ")
key = input("Enter 26-letter substitution key: ")

if len(key) != 26 or len(set(key.upper())) != 26:
    print("Invalid key! Key must contain 26 unique letters.")
else:
    encrypted = encrypt(message, key)
    decrypted = decrypt(encrypted, key)

    print("Encrypted:", encrypted)
    print("Decrypted:", decrypted)
```
# Lab 4 — One-Time Pad

## Theory

The **One-Time Pad (OTP)** is a symmetric encryption technique in which the plaintext is combined with a **random key of the same length as the plaintext**.

A proper One-Time Pad has three important properties:

1. The key must be **truly random**.
2. The key must be **at least as long as the message**.
3. The key must **never be reused**.

If these conditions are satisfied, the One-Time Pad provides **perfect secrecy** in the theoretical sense.

For simplicity, letters are represented as numbers:

```text
A = 0
B = 1
C = 2
...
Z = 25
```

### Encryption

Let:

* `P` = plaintext value
* `K` = key value
* `C` = ciphertext value

The encryption equation is:

$$
C=(P+K)\mod26
$$

### Example

Suppose:

```text
Plaintext = HELLO
Key       = XMCKL
```

Convert them to numbers:

```text
H E L L O
7 4 11 11 14

X M C K L
23 12 2 10 11
```

Apply:

$$
C=(P+K)\mod26
$$

For the first letter:

$$
C=(7+23)\mod26=4
$$

`4 = E`

Similarly:

```text
H + X → E
E + M → Q
L + C → N
L + K → V
O + L → Z
```

Therefore:

```text
Plaintext : HELLO
Key       : XMCKL
Ciphertext: EQNVZ
```

### Decryption

Decryption reverses the operation:

$$
P=(C-K)\mod26
$$

For example:

$$
P=(4-23)\mod26=7
$$

and:

```text
7 = H
```

Thus:

```text
EQNVZ → HELLO
```

The same key is required for decryption.

### Why It Is Called "One-Time"

The key must be used **only once**.

For example:

```text
Message 1 → Key 1
Message 2 → Key 2
Message 3 → Key 3
```

Reusing the same key for multiple messages can reveal relationships between the plaintexts and breaks the security property of the OTP.

---

## Code

```python id="q3bqf7"
def encrypt(text, key):
    result = ""

    for p, k in zip(text.upper(), key.upper()):
        if p.isalpha():
            value = (ord(p) - ord('A') +
                     ord(k) - ord('A')) % 26
            result += chr(value + ord('A'))

    return result


def decrypt(cipher, key):
    result = ""

    for c, k in zip(cipher.upper(), key.upper()):
        value = (ord(c) - ord('A') -
                 (ord(k) - ord('A'))) % 26
        result += chr(value + ord('A'))

    return result


# Input
message = input("Enter message: ").replace(" ", "")
key = input("Enter key: ").replace(" ", "")

if len(message) != len(key):
    print("Error: Key must be the same length as the message.")
else:
    encrypted = encrypt(message, key)
    decrypted = decrypt(encrypted, key)

    print("Encrypted:", encrypted)
    print("Decrypted:", decrypted)
```
# Lab 5 — GCD Using Euclidean Algorithm

## Theory

The **Greatest Common Divisor (GCD)** of two integers is the largest positive integer that divides both numbers without leaving a remainder.

For example:

$$
GCD(48,18)=6
$$

because 6 is the largest number that divides both 48 and 18.

### Euclidean Algorithm

The **Euclidean Algorithm** calculates the GCD by repeatedly replacing the larger number with the remainder obtained from division.

The fundamental equation is:

$$
a=bq+r
$$

where:

* `a` = larger number
* `b` = smaller number
* `q` = quotient
* `r` = remainder

The important property is:

$$
GCD(a,b)=GCD(b,a\bmod b)
$$

This process continues until the remainder becomes `0`.

The last non-zero remainder is the GCD.

### Example

Find:

$$
GCD(48,18)
$$

First:

$$
48=18\times2+12
$$

Therefore:

$$
GCD(48,18)=GCD(18,12)
$$

Next:

$$
18=12\times1+6
$$

Therefore:

$$
GCD(18,12)=GCD(12,6)
$$

Next:

$$
12=6\times2+0
$$

Since the remainder is now `0`:

$$
\boxed{GCD(48,18)=6}
$$

### Algorithm

1. Take two numbers `a` and `b`.
2. Calculate `a % b`.
3. Replace `a` with `b`.
4. Replace `b` with the remainder.
5. Repeat until `b = 0`.
6. The value of `a` is the GCD.

---

## Code

```python
def gcd(a, b):
    while b != 0:
        a, b = b, a % b

    return a


# Input
a = int(input("Enter first number: "))
b = int(input("Enter second number: "))

print("GCD =", gcd(a, b))
```
# Lab 6 — Brute-Force Attack on Caesar Cipher

## Theory

A **brute-force attack** is a method of trying every possible key until the original plaintext is found.

The Caesar Cipher is particularly vulnerable to brute-force attacks because there are only **26 possible shifts**. In practice, only 25 non-trivial shifts need to be tested because a shift of 0 produces the original message.

For Caesar Cipher encryption:

$$
C=(P+k)\mod26
$$

Therefore, to decrypt a ciphertext, we can try every possible value of `k`:

$$
P=(C-k)\mod26
$$

### Example

Suppose the encrypted message is:

```text
KHOOR
```

We don't know the key.

The program tries:

```text
Key 0 → KHOOR
Key 1 → JGNNQ
Key 2 → IFMMP
Key 3 → HELLO
...
```

When `key = 3`, we obtain:

```text
HELLO
```

So the original message can be identified by examining the possible plaintexts.

### Why Brute Force Works

The Caesar Cipher has a very small key space:

$$
26
$$

possible shifts.

Therefore, an attacker does not need to know the key. They can simply try all possible keys.

For example:

```text
Ciphertext: KHOOR
```

The program generates:

```text
Key 0  → KHOOR
Key 1  → JGNNQ
Key 2  → IFMMP
Key 3  → HELLO
Key 4  → GDKKN
...
```

The meaningful result is:

```text
HELLO
```

Thus, the Caesar Cipher is vulnerable to a **brute-force attack**.

---

## Code

```python id="8f5n5g"
def decrypt(cipher, key):
    result = ""

    for char in cipher:
        if char.isalpha():
            start = ord('A') if char.isupper() else ord('a')
            result += chr((ord(char) - start - key) % 26 + start)
        else:
            result += char

    return result


# Input
cipher = input("Enter encrypted message: ")

# Try all possible keys
for key in range(26):
    print("Key", key, ":", decrypt(cipher, key))
```
# Lab 7 — 2×2 Hill Cipher

## Theory

The **Hill Cipher** is a polygraphic substitution cipher based on **matrix multiplication**. Unlike Caesar Cipher, which encrypts one letter at a time, the Hill Cipher encrypts a group of letters together.

For a **2×2 Hill Cipher**, plaintext is divided into blocks of two letters.

We represent letters as numbers:

$$
A=0,\ B=1,\ C=2,\ldots,Z=25
$$

A 2×2 key matrix is used:

$$
K=
\begin{bmatrix}
a & b\\
c & d
\end{bmatrix}
$$

### Encryption

A plaintext pair is represented as a column vector:

$$
P=
\begin{bmatrix}
p_1\\
p_2
\end{bmatrix}
$$

The ciphertext is calculated using:

$$
C=KP\pmod{26}
$$

where:

$$
C=
\begin{bmatrix}
c_1\\
c_2
\end{bmatrix}
$$

Therefore:

$$
c_1=(ap_1+bp_2)\mod26
$$

$$
c_2=(cp_1+dp_2)\mod26
$$

### Example

Consider the key matrix:

$$
K=
\begin{bmatrix}
3&3\\
2&5
\end{bmatrix}
$$

Suppose the plaintext is:

```text id="h5j2b3"
HI
```

Convert the letters:

$$
H=7,\quad I=8
$$

Therefore:

$$
P=
\begin{bmatrix}
7\\
8
\end{bmatrix}
$$

Now:

$$
C=
\begin{bmatrix}
3&3\\
2&5
\end{bmatrix}
\begin{bmatrix}
7\\
8
\end{bmatrix}
\mod26
$$

First value:

$$
(3\times7+3\times8)\mod26
=45\mod26
=19
$$

Second value:

$$
(2\times7+5\times8)\mod26
=54\mod26
=2
$$

Therefore:

$$
C=
\begin{bmatrix}
19\\
2
\end{bmatrix}
$$

Since:

```text id="s3q22v"
19 = T
2  = C
```

we get:

```text id="l3m5aq"
HI → TC
```

### Decryption

To decrypt, we need the **inverse of the key matrix modulo 26**.

For:

$$
K=
\begin{bmatrix}
a&b\\
c&d
\end{bmatrix}
$$

the determinant is:

$$
\det(K)=ad-bc
$$

The inverse matrix is:

$$
K^{-1}
=
\det(K)^{-1}
\begin{bmatrix}
d&-b\\
-c&a
\end{bmatrix}
\mod26
$$

Here, \(\det(K)^{-1}\) means the **modular multiplicative inverse** of the determinant.

The decryption equation is:

$$
P=K^{-1}C\mod26
$$

For a key to be valid, its determinant must have an inverse modulo 26:

$$
GCD(\det(K),26)=1
$$

For our key:

$$
\det(K)=(3\times5)-(3\times2)=9
$$

Since:

$$
GCD(9,26)=1
$$

the matrix is invertible and can be used for encryption and decryption.

---

## Code

```python
def mod_inverse(a, m):
    for x in range(1, m):
        if (a * x) % m == 1:
            return x
    return None


def inverse_matrix(key):
    a, b = key[0]
    c, d = key[1]

    det = (a * d - b * c) % 26
    inv_det = mod_inverse(det, 26)

    if inv_det is None:
        raise ValueError("Invalid key matrix!")

    return [
        [(d * inv_det) % 26, (-b * inv_det) % 26],
        [(-c * inv_det) % 26, (a * inv_det) % 26]
    ]


def process(text, key):
    result = ""

    for i in range(0, len(text), 2):
        x = ord(text[i]) - ord('A')
        y = ord(text[i + 1]) - ord('A')

        result += chr(
            (key[0][0] * x + key[0][1] * y) % 26 + ord('A')
        )

        result += chr(
            (key[1][0] * x + key[1][1] * y) % 26 + ord('A')
        )

    return result


# Key matrix
key = [
    [3, 3],
    [2, 5]
]

message = input("Enter message: ").upper().replace(" ", "")

if len(message) % 2 != 0:
    message += "X"

# Encryption
encrypted = process(message, key)

# Decryption
inverse_key = inverse_matrix(key)
decrypted = process(encrypted, inverse_key)

print("Encrypted:", encrypted)
print("Decrypted:", decrypted)
```
# Lab 8 — Transposition Cipher with Double Encryption

## Theory

A **Transposition Cipher** is an encryption technique in which the letters of the plaintext are **rearranged** without changing the actual letters.

Unlike a substitution cipher, the characters themselves are not replaced. Their **positions are changed**.

In **double transposition**, the transposition process is performed **twice**, usually with two different keys.

### Basic Idea

Suppose the plaintext is:

```text
HELLOWORLD
```

The letters remain:

```text
H E L L O W O R L D
```

but their positions are rearranged according to a key.

For a **columnar transposition cipher**, the plaintext is first written into a grid.

For example, using 4 columns:

```text
H E L L
O W O R
L D X X
```

`X` is used as padding when necessary.

A key determines the order in which the columns are read.

### Column Ordering

Suppose the key is:

```text
KEY = ZEBRA
```

The letters are sorted alphabetically:

```text
A B E R Z
```

Their corresponding column order determines the order in which the columns are read.

The ciphertext is obtained by reading the columns in that order.

### Double Encryption

In double transposition, the first transposition produces an intermediate ciphertext:

$$
T_1=Transposition(P,K_1)
$$

Then the intermediate result is encrypted again:

$$
C=Transposition(T_1,K_2)
$$

Therefore:

$$
\boxed{C=T(T(P,K_1),K_2)}
$$

where:

* \(P\) = plaintext
* \(K_1\) = first key
* \(K_2\) = second key
* \(T\) = transposition operation
* \(C\) = final ciphertext

### Example

Suppose:

```text
Plaintext = HELLOWORLD
Key 1 = ZEBRA
Key 2 = CODE
```

First, the plaintext is arranged in a grid according to `ZEBRA`, and the columns are read according to their alphabetical order.

The resulting text becomes the input to the second transposition using `CODE`.

Thus:

```text
Plaintext
    ↓
First Transposition
    ↓
Intermediate Ciphertext
    ↓
Second Transposition
    ↓
Final Ciphertext
```

The important point is that **no letters are substituted**. Only their positions change.

Double transposition is more difficult to break than a single transposition because the permutation is applied twice.

---

## Code

```python id="5l7b4q"
def columnar_encrypt(text, key):
    text = text.upper().replace(" ", "")
    cols = len(key)

    # Padding
    while len(text) % cols != 0:
        text += "X"

    rows = [text[i:i + cols] for i in range(0, len(text), cols)]

    # Read columns according to alphabetical key order
    order = sorted(range(cols), key=lambda i: key[i])

    result = ""

    for col in order:
        for row in rows:
            result += row[col]

    return result


# Input
message = input("Enter message: ")
key1 = input("Enter first key: ").upper()
key2 = input("Enter second key: ").upper()

# First encryption
step1 = columnar_encrypt(message, key1)

# Second encryption
encrypted = columnar_encrypt(step1, key2)

print("After first transposition :", step1)
print("After second transposition:", encrypted)
print("Final Ciphertext:", encrypted)
```
# Lab 9 — ElGamal Cryptosystem

## Theory

**ElGamal** is a **public-key (asymmetric) cryptosystem** based on the mathematical difficulty of the **discrete logarithm problem**.

It uses two keys:

* **Public key** — used for encryption
* **Private key** — used for decryption

The basic ElGamal system works in a modular arithmetic group.

Choose:

* \(p\) = a prime number
* \(g\) = a primitive root modulo \(p\)
* \(x\) = private key

The private key is:

$$
x
$$

The public key is calculated as:

$$
y=g^x\mod p
$$

Therefore, the public key is:

$$
(p,g,y)
$$

and the private key is:

$$
x
$$

### Encryption

Suppose the message is represented by an integer \(m\), where:

$$
0<m<p
$$

The sender chooses a random temporary value \(k\).

Then calculate:

$$
c_1=g^k\mod p
$$

and:

$$
c_2=m\cdot y^k\mod p
$$

The ciphertext is the pair:

$$
\boxed{(c_1,c_2)}
$$

### Example

Suppose:

$$
p=23
$$

$$
g=5
$$

and the private key is:

$$
x=6
$$

Calculate the public key:

$$
y=5^6\mod23
$$

$$
y=8
$$

Therefore:

```text
Public key  = (23, 5, 8)
Private key = 6
```

Suppose:

$$
m=10
$$

and choose:

$$
k=3
$$

Then:

$$
c_1=5^3\mod23=10
$$

and:

$$
c_2=10\times8^3\mod23
$$

$$
c_2=14
$$

So the ciphertext is:

$$
\boxed{(10,14)}
$$

### Decryption

The receiver calculates:

$$
s=c_1^x\mod p
$$

This produces the shared secret.

Then the modular inverse of \(s\) is calculated:

$$
s^{-1}\mod p
$$

The original message is recovered using:

$$
m=c_2\times s^{-1}\mod p
$$

For the example:

$$
s=10^6\mod23=6
$$

The inverse of 6 modulo 23 is:

$$
6^{-1}=4
$$

because:

$$
6\times4=24\equiv1\pmod{23}
$$

Therefore:

$$
m=14\times4\mod23
$$

$$
m=10
$$

So the original message is recovered.

### Important Concept

ElGamal depends on the fact that calculating:

$$
y=g^x\mod p
$$

is easy when \(x\) is known, while recovering \(x\) from \(g\), \(y\), and \(p\) is computationally difficult for appropriately large parameters.

The small numbers used in a laboratory program are **not secure** for real-world cryptography.

---

## Code

```python
def mod_inverse(a, p):
    return pow(a, -1, p)


# Public parameters
p = 23
g = 5

# Private key
x = 6

# Generate public key
y = pow(g, x, p)

print("Public Key:", (p, g, y))
print("Private Key:", x)

# Input message
m = int(input("Enter message (number < p): "))

# Random/session key
k = 3

# Encryption
c1 = pow(g, k, p)
c2 = (m * pow(y, k, p)) % p

print("Ciphertext:", (c1, c2))

# Decryption
s = pow(c1, x, p)
s_inverse = mod_inverse(s, p)

decrypted = (c2 * s_inverse) % p

print("Decrypted:", decrypted)
```
# Lab 10 — DES (Data Encryption Standard)

## Theory

**DES (Data Encryption Standard)** is a symmetric-key block cipher used for encryption and decryption.

DES operates on:

* **64-bit plaintext blocks**
* **64-bit keys**, of which **56 bits are effectively used for encryption** and 8 bits are used as parity bits
* **16 rounds** of processing

Because DES uses the same secret key for encryption and decryption, it is a **symmetric encryption algorithm**.

### Basic Structure

The DES encryption process can be represented as:

```text
64-bit Plaintext
       ↓
Initial Permutation
       ↓
16 Feistel Rounds
       ↓
Final Permutation
       ↓
64-bit Ciphertext
```

The 64-bit plaintext is divided into two 32-bit halves:

$$
L_0,\ R_0
$$

Each round uses:

$$
L_i=R_{i-1}
$$

and:

$$
R_i=L_{i-1}\oplus f(R_{i-1},K_i)
$$

where:

* \(L_i\) = left half after round \(i\)
* \(R_i\) = right half after round \(i\)
* \(K_i\) = round key
* \(f\) = DES round function
* \(\oplus\) = XOR operation

### DES Round Function

The DES function expands the 32-bit right half to 48 bits:

$$
32\text{ bits}\rightarrow48\text{ bits}
$$

The expanded value is XORed with the 48-bit round key:

$$
E(R)\oplus K_i
$$

The result is divided into eight 6-bit blocks and passed through **8 S-boxes**.

Each S-box converts:

$$
6\text{ bits}\rightarrow4\text{ bits}
$$

Thus:

$$
8\times6=48\text{ bits}
$$

becomes:

$$
8\times4=32\text{ bits}
$$

Finally, a permutation is applied to produce the output of the round function.

### Key Generation

The original DES key is 64 bits long.

After removing parity bits:

$$
64\rightarrow56\text{ bits}
$$

The 56-bit key is divided into two 28-bit halves:

$$
C_0,\ D_0
$$

The halves are shifted and combined to generate 16 round keys:

$$
K_1,K_2,\ldots,K_{16}
$$

Each round key has 48 bits.

### Decryption

DES decryption uses the **same algorithm** as encryption, but the round keys are used in reverse order:

```text
K16, K15, ..., K2, K1
```

Therefore:

$$
Decrypt(Encrypt(P,K),K)=P
$$

### Example

Suppose:

```text
Plaintext = HELLO123
Key       = 8BYTESKY
```

DES processes the plaintext in 64-bit (8-byte) blocks.

The same secret key is used to decrypt the resulting ciphertext.

### Important Note

DES is historically important but is **not considered secure for modern applications** because its effective key size is only 56 bits, making exhaustive key-search attacks practical with modern computing resources.

For modern applications, algorithms such as **AES** are generally used instead.

---

## Code

```python
from Crypto.Cipher import DES
from Crypto.Util.Padding import pad, unpad


# Input
message = input("Enter message: ")
key = input("Enter 8-character key: ").encode()

if len(key) != 8:
    print("Key must be exactly 8 characters.")
else:
    # Create DES cipher
    cipher = DES.new(key, DES.MODE_ECB)

    # Encryption
    encrypted = cipher.encrypt(
        pad(message.encode(), DES.block_size)
    )

    print("Encrypted:", encrypted.hex())

    # Decryption
    cipher = DES.new(key, DES.MODE_ECB)

    decrypted = unpad(
        cipher.decrypt(encrypted),
        DES.block_size
    ).decode()

    print("Decrypted:", decrypted)
```

Install the required library if necessary:

```bash
pip install pycryptodome
```
# Lab 11 — RSA Algorithm

## Theory

**RSA (Rivest–Shamir–Adleman)** is a **public-key (asymmetric) cryptographic algorithm**. It uses two different keys:

* **Public key** → used for encryption
* **Private key** → used for decryption

RSA is based on the mathematical difficulty of **factoring a large number into its prime factors**.

### Key Generation

First, choose two prime numbers:

$$
p,\ q
$$

Calculate:

$$
n=pq
$$

Then calculate Euler's totient:

$$
\phi(n)=(p-1)(q-1)
$$

Choose a public exponent \(e\) such that:

$$
1<e<\phi(n)
$$

and:

$$
GCD(e,\phi(n))=1
$$

The private exponent \(d\) is calculated as the modular inverse of \(e\):

$$
ed\equiv1\pmod{\phi(n)}
$$

Therefore:

$$
\boxed{d=e^{-1}\mod\phi(n)}
$$

The keys are:

$$
\boxed{\text{Public Key}=(e,n)}
$$

$$
\boxed{\text{Private Key}=(d,n)}
$$

### Example

Choose:

$$
p=5,\quad q=11
$$

Then:

$$
n=5\times11=55
$$

and:

$$
\phi(n)=(5-1)(11-1)
$$

$$
\phi(n)=4\times10=40
$$

Choose:

$$
e=3
$$

Since:

$$
GCD(3,40)=1
$$

it is a valid public exponent.

Now calculate \(d\):

$$
3d\equiv1\pmod{40}
$$

Since:

$$
3\times27=81\equiv1\pmod{40}
$$

we get:

$$
d=27
$$

Therefore:

```text id="j1h3cy"
Public Key  = (3, 55)
Private Key = (27, 55)
```

### Encryption

If the message is represented by an integer \(m<n\), encryption is:

$$
\boxed{c=m^e\mod n}
$$

For example, if:

$$
m=7
$$

then:

$$
c=7^3\mod55
$$

$$
c=343\mod55=13
$$

So the ciphertext is:

$$
c=13
$$

### Decryption

The receiver uses the private key:

$$
\boxed{m=c^d\mod n}
$$

Therefore:

$$
m=13^{27}\mod55
$$

which gives:

$$
m=7
$$

So the original message is recovered.

### Important Concept

RSA's security depends on using sufficiently large parameters. The small numbers used in laboratory examples are only for understanding the algorithm and are **not secure for real-world use**.

RSA is different from symmetric algorithms such as DES because the encryption and decryption keys are different.

---

## Code

```python id="6g1kqv"
from math import gcd


def mod_inverse(e, phi):
    for d in range(1, phi):
        if (e * d) % phi == 1:
            return d


# Choose two prime numbers
p = 5
q = 11

# Calculate n and Euler's totient
n = p * q
phi = (p - 1) * (q - 1)

# Choose public exponent
e = 3

if gcd(e, phi) != 1:
    print("Invalid public exponent.")
else:
    # Calculate private exponent
    d = mod_inverse(e, phi)

    print("Public Key :", (e, n))
    print("Private Key:", (d, n))

    # Input message as a number
    m = int(input("Enter message (number < n): "))

    if m >= n:
        print("Message must be smaller than n.")
    else:
        # Encryption
        c = pow(m, e, n)
        print("Encrypted:", c)

        # Decryption
        decrypted = pow(c, d, n)
        print("Decrypted:", decrypted)
```
# Lab 12 — Diffie-Hellman Key Exchange

## Theory

**Diffie-Hellman (DH)** is a cryptographic key exchange algorithm that allows two parties to establish a **shared secret key over an insecure communication channel**.

The important idea is that Alice and Bob do **not directly send the secret key** to each other.

Instead, they exchange public values and independently calculate the same shared secret.

### Public Parameters

Both parties agree on two public numbers:

* \(p\) = a large prime number
* \(g\) = a primitive root modulo \(p\)

These values do not need to be secret.

### Key Generation

Suppose Alice chooses a private key:

$$
a
$$

and Bob chooses a private key:

$$
b
$$

The private keys are kept secret.

Alice calculates her public key:

$$
A=g^a\mod p
$$

Bob calculates his public key:

$$
B=g^b\mod p
$$

They exchange \(A\) and \(B\).

### Shared Secret

Alice receives Bob's public key \(B\) and calculates:

$$
S_A=B^a\mod p
$$

Bob receives Alice's public key \(A\) and calculates:

$$
S_B=A^b\mod p
$$

Both produce the same value because:

$$
S_A=(g^b)^a\mod p
$$

and:

$$
S_B=(g^a)^b\mod p
$$

Therefore:

$$
\boxed{S_A=S_B=g^{ab}\mod p}
$$

This common value becomes the shared secret.

### Example

Suppose:

$$
p=23
$$

$$
g=5
$$

These are public.

Alice chooses:

$$
a=6
$$

Bob chooses:

$$
b=15
$$

Alice calculates:

$$
A=5^6\mod23=8
$$

Bob calculates:

$$
B=5^{15}\mod23=19
$$

They exchange:

```text id="a7v4n5"
Alice → 8
Bob   → 19
```

Alice calculates:

$$
S_A=19^6\mod23
$$

$$
S_A=2
$$

Bob calculates:

$$
S_B=8^{15}\mod23
$$

$$
S_B=2
$$

Therefore:

$$
\boxed{S=2}
$$

Both parties now have the same shared secret without directly transmitting it.

### Important Concept

An attacker can know:

$$
p,\quad g,\quad A,\quad B
$$

but should not be able to efficiently determine:

$$
a,\quad b
$$

when sufficiently large parameters are used.

This relies on the difficulty of the **discrete logarithm problem**.

The small numbers in this laboratory example are only for demonstrating the process and are **not secure for real-world communication**.

---

## Code

```python id="1j6n7u"
# Public values
p = 23
g = 5

# Private keys
a = 6       # Alice's private key
b = 15      # Bob's private key

# Generate public keys
A = pow(g, a, p)
B = pow(g, b, p)

print("Alice's Public Key:", A)
print("Bob's Public Key:", B)

# Calculate shared secret
alice_secret = pow(B, a, p)
bob_secret = pow(A, b, p)

print("Alice's Shared Secret:", alice_secret)
print("Bob's Shared Secret:", bob_secret)

if alice_secret == bob_secret:
    print("Key exchange successful!")
else:
    print("Key exchange failed!")
```
# Lab 13 — Modular Arithmetic Operations Using a Primitive Root

## Theory

**Modular arithmetic** is a system of arithmetic where numbers wrap around after reaching a particular value called the **modulus**.

For example:

$$
17\mod5=2
$$

because:

$$
17=5\times3+2
$$

In cryptography, modular arithmetic is widely used in algorithms such as **Diffie-Hellman, ElGamal, and RSA**.

### Basic Modular Operations

For two numbers \(a\) and \(b\), modulo \(m\):

**Addition:**

$$
(a+b)\mod m
$$

**Subtraction:**

$$
(a-b)\mod m
$$

**Multiplication:**

$$
(a\times b)\mod m
$$

**Exponentiation:**

$$
a^b\mod m
$$

### Example

Let:

$$
a=17,\quad b=5,\quad m=7
$$

Then:

$$
(17+5)\mod7=22\mod7=1
$$

$$
(17-5)\mod7=12\mod7=5
$$

$$
(17\times5)\mod7=85\mod7=1
$$

and:

$$
17^5\mod7=3
$$

### Primitive Root

A **primitive root modulo \(p\)** is a number \(g\) whose powers generate every non-zero residue modulo \(p\).

For a prime \(p\), the numbers:

$$
1,2,3,\ldots,p-1
$$

are the non-zero residues.

A number \(g\) is a primitive root modulo \(p\) if:

$$
g^1,g^2,g^3,\ldots,g^{p-1}\mod p
$$

produces every value from:

$$
1\text{ to }p-1
$$

exactly once.

### Example

Consider:

$$
p=7
$$

Take:

$$
g=3
$$

Calculate the powers of 3 modulo 7:

$$
3^1\mod7=3
$$

$$
3^2\mod7=2
$$

$$
3^3\mod7=6
$$

$$
3^4\mod7=4
$$

$$
3^5\mod7=5
$$

$$
3^6\mod7=1
$$

The results are:

```text
3, 2, 6, 4, 5, 1
```

which contains every number from 1 to 6.

Therefore:

$$
\boxed{3\text{ is a primitive root modulo }7}
$$

Primitive roots are important in public-key cryptography because they allow powers of a generator to produce elements of the entire multiplicative group.

---

## Code

```python id="i3v4a9"
def find_primitive_root(p):
    required = set(range(1, p))

    for g in range(2, p):
        values = set()

        for i in range(1, p):
            values.add(pow(g, i, p))

        if values == required:
            return g

    return None


# Input
p = int(input("Enter a prime number: "))
a = int(input("Enter first number: "))
b = int(input("Enter second number: "))

# Find primitive root
g = find_primitive_root(p)

print("Primitive Root:", g)

# Modular operations
print("Addition       :", (a + b) % p)
print("Subtraction    :", (a - b) % p)
print("Multiplication :", (a * b) % p)
print("a^b mod p      :", pow(a, b, p))
print("g^a mod p      :", pow(g, a, p))
```
# Lab 14 — GCD Using Extended Euclidean Algorithm

## Theory

The **Extended Euclidean Algorithm** is an extension of the Euclidean Algorithm. It not only calculates the **GCD of two numbers**, but also finds two integers \(x\) and \(y\) such that:

$$
\boxed{ax+by=GCD(a,b)}
$$

This equation is called **Bézout's identity**.

The ordinary Euclidean Algorithm finds only:

$$
GCD(a,b)
$$

while the Extended Euclidean Algorithm finds:

$$
GCD(a,b),\ x,\ y
$$

such that:

$$
ax+by=GCD(a,b)
$$

### Example

Find the GCD of:

$$
48,\ 18
$$

Using the Euclidean Algorithm:

$$
48=18\times2+12
$$

$$
18=12\times1+6
$$

$$
12=6\times2+0
$$

Therefore:

$$
GCD(48,18)=6
$$

Now work backwards:

$$
6=18-12
$$

Since:

$$
12=48-18\times2
$$

substitute:

$$
6=18-(48-18\times2)
$$

$$
6=18-48+36
$$

$$
6=3(18)-48
$$

Therefore:

$$
48(-1)+18(3)=6
$$

So:

$$
\boxed{x=-1,\quad y=3}
$$

and:

$$
\boxed{48(-1)+18(3)=6}
$$

### Algorithm

Initially:

$$
x_0=1,\quad y_0=0
$$

$$
x_1=0,\quad y_1=1
$$

At every step, calculate:

$$
q=\left\lfloor\frac{a}{b}\right\rfloor
$$

and update:

$$
(a,b)=(b,a\bmod b)
$$

The coefficients are updated simultaneously.

At the end:

$$
ax+by=GCD(a,b)
$$

### Modular Inverse

One of the most important applications of the Extended Euclidean Algorithm is finding a **modular multiplicative inverse**.

If:

$$
GCD(a,m)=1
$$

then there exist \(x,y\) such that:

$$
ax+my=1
$$

Taking modulo \(m\):

$$
ax\equiv1\pmod m
$$

Therefore:

$$
\boxed{x=a^{-1}\mod m}
$$

This is widely used in cryptographic algorithms such as **RSA, ElGamal, and the Hill Cipher**.

---

## Code

```python id="c7d4p2"
def extended_gcd(a, b):
    if b == 0:
        return a, 1, 0

    gcd, x1, y1 = extended_gcd(b, a % b)

    x = y1
    y = x1 - (a // b) * y1

    return gcd, x, y


# Input
a = int(input("Enter first number: "))
b = int(input("Enter second number: "))

gcd, x, y = extended_gcd(a, b)

print("GCD =", gcd)
print("x =", x)
print("y =", y)

print("Verification:")
print(f"{a}({x}) + {b}({y}) =", a * x + b * y)
```
Absolutely. For this viva, **don't memorize the code line-by-line**. You should be able to explain what each algorithm does, the main formula, key terms, and why the code works.

# Cryptography Lab Viva Preparation

## 1. Caesar Cipher

### Q1. What is Caesar Cipher?

**Answer:** Caesar Cipher is a substitution cipher where every plaintext letter is shifted by a fixed number of positions in the alphabet.

### Q2. Is Caesar Cipher symmetric or asymmetric?

**Answer:** Symmetric, because the same key is used for encryption and decryption.

### Q3. Encryption formula?

$$
C=(P+K)\mod26
$$

### Q4. Decryption formula?

$$
P=(C-K)\mod26
$$

### Q5. Why is it insecure?

**Answer:** It has only 26 possible keys, so it can easily be broken by brute force.

### Q6. Give an example.

```text
HELLO, key = 3
HELLO → KHOOR
```

---

# 2. Playfair Cipher

### Q1. What is Playfair Cipher?

**Answer:** It is a symmetric substitution cipher that encrypts **two letters at a time** using a 5×5 matrix.

### Q2. Why is the matrix 5×5?

**Answer:** Because there are 25 cells, so `I` and `J` are usually combined.

### Q3. What are the three encryption rules?

**Same row:** Move each letter one position to the right.

**Same column:** Move each letter one position downward.

**Different row and column:** Form a rectangle and take the letters at the opposite corners.

### Q4. What happens if both letters in a pair are the same?

**Answer:** Insert `X` between them.

Example:

```text
LL → LX L...
```

### Q5. What if the plaintext length is odd?

**Answer:** Add `X` at the end.

### Q6. Is Playfair stronger than Caesar?

**Answer:** It provides more diffusion because it encrypts pairs of letters rather than individual letters, although it is still not secure by modern standards.

---

# 3. Monoalphabetic Substitution Cipher

### Q1. What is it?

Each plaintext letter is replaced by a corresponding ciphertext letter according to a fixed substitution alphabet.

Example:

```text
ABCDEFGHIJKLMNOPQRSTUVWXYZ
QWERTYUIOPASDFGHJKLZXCVBNM
```

### Q2. How is it different from Caesar Cipher?

Caesar uses a fixed shift.

Monoalphabetic substitution can use **any permutation of the alphabet**.

### Q3. How many possible keys are there?

$$
26!
$$

### Q4. Why can it still be broken?

**Frequency analysis.**

If `E` is the most frequent letter in English, its substituted ciphertext letter may also appear frequently.

### Q5. Is it symmetric?

Yes.

---

# 4. One-Time Pad

### Q1. What is OTP?

A symmetric encryption technique where a **random key of the same length as the plaintext** is used.

### Q2. Encryption formula?

$$
C=(P+K)\mod26
$$

### Q3. Decryption formula?

$$
P=(C-K)\mod26
$$

### Q4. What are the requirements of a true OTP?

Remember:

**R-S-N**

* **R**andom key
* **S**ame length as message
* **N**ever reused

### Q5. Why is OTP theoretically perfectly secure?

Because when a truly random key is used only once, the ciphertext does not reveal information about the plaintext.

### Q6. What happens if the key is reused?

Security is lost because relationships between different plaintexts can be revealed.

---

# 5. Euclidean Algorithm

### Q1. What does it calculate?

The **GCD** of two numbers.

### Q2. Main formula?

$$
GCD(a,b)=GCD(b,a\bmod b)
$$

### Q3. Example?

$$
48=18(2)+12
$$

$$
18=12(1)+6
$$

$$
12=6(2)+0
$$

Therefore:

$$
GCD(48,18)=6
$$

### Q4. When does the algorithm stop?

When the remainder becomes zero.

### Q5. What is the answer?

The last non-zero remainder.

---

# 6. Caesar Brute-Force Attack

### Q1. What is brute force?

Trying all possible keys until the correct plaintext is found.

### Q2. Why is Caesar easy to brute force?

There are only 26 possible shifts.

### Q3. How does the program work?

It tries:

```text
Key 0
Key 1
Key 2
...
Key 25
```

and prints the resulting plaintext for every key.

### Q4. Does the computer automatically know which result is correct?

Not necessarily.

**The attacker normally identifies the meaningful plaintext**, although automated language scoring can also be used.

### Q5. What is the relationship between this lab and Lab 1?

Lab 1 performs Caesar encryption/decryption using a known key.

Lab 6 demonstrates that the key can be discovered by trying all possible keys.

---

# 7. Hill Cipher

### Q1. What is Hill Cipher?

A polygraphic substitution cipher based on **matrix multiplication**.

### Q2. What does 2×2 mean?

Two plaintext letters are processed at a time using a 2×2 key matrix.

### Q3. Encryption formula?

$$
\boxed{C=KP\mod26}
$$

### Q4. Why do we use modulo 26?

Because the English alphabet has 26 letters.

### Q5. How is decryption performed?

Using the inverse key matrix:

$$
\boxed{P=K^{-1}C\mod26}
$$

### Q6. When is a key matrix valid?

Its determinant must have a modular inverse modulo 26.

Therefore:

$$
GCD(det(K),26)=1
$$

### Q7. How do you calculate the determinant of a 2×2 matrix?

For:

$$
K=
\begin{bmatrix}
a&b\\
c&d
\end{bmatrix}
$$

$$
det(K)=ad-bc
$$

### Q8. Why is Extended Euclidean Algorithm related to Hill Cipher?

It can be used to calculate the **modular inverse of the determinant**.

---

# 8. Double Transposition Cipher

### Q1. What is a transposition cipher?

It changes the **position/order of characters** without changing the characters themselves.

### Q2. What is the difference between substitution and transposition?

**Substitution:**

```text
A → Q
B → W
```

Characters are replaced.

**Transposition:**

```text
ABCDEF → reordered characters
```

Characters remain the same but their positions change.

### Q3. What is double transposition?

The transposition operation is performed twice:

$$
C=T(T(P,K_1),K_2)
$$

### Q4. Why use two keys?

To make the resulting permutation more complex than a single transposition.

### Q5. What is padding?

Extra characters, commonly `X`, are added to fill an incomplete grid.

---

# 9. ElGamal

### Q1. What type of cryptosystem is ElGamal?

**Asymmetric/public-key cryptosystem.**

### Q2. What mathematical problem is it based on?

The **discrete logarithm problem**.

### Q3. What are the public parameters?

Typically:

$$
p,\quad g
$$

where `p` is a prime and `g` is a generator/primitive root.

### Q4. How is the public key generated?

If private key is \(x\):

$$
y=g^x\mod p
$$

Public key:

$$
(p,g,y)
$$

Private key:

$$
x
$$

### Q5. Encryption formulas?

Choose a random ephemeral key \(k\):

$$
c_1=g^k\mod p
$$

$$
c_2=m y^k\mod p
$$

Ciphertext:

$$
(c_1,c_2)
$$

### Q6. Decryption formula?

First:

$$
s=c_1^x\mod p
$$

Then:

$$
m=c_2s^{-1}\mod p
$$

### Q7. Why must \(k\) be random?

Because it is an ephemeral value used to create the ciphertext. Reusing it improperly can compromise security.

---

# 10. DES

### Q1. What is DES?

**Data Encryption Standard**, a symmetric block cipher.

### Q2. What is its block size?

$$
64\text{ bits}
$$

### Q3. What is its effective key size?

$$
56\text{ bits}
$$

The original key is 64 bits, but 8 bits are used for parity.

### Q4. How many rounds?

$$
\boxed{16}
$$

### Q5. What structure does DES use?

A **Feistel structure**.

### Q6. What happens in a Feistel round?

$$
L_i=R_{i-1}
$$

$$
R_i=L_{i-1}\oplus f(R_{i-1},K_i)
$$

### Q7. What are S-boxes?

DES has **8 S-boxes**.

Each converts:

$$
6\text{ bits}\rightarrow4\text{ bits}
$$

### Q8. Why is DES no longer considered secure?

Its effective 56-bit key is too small against modern brute-force attacks.

### Q9. What is used instead?

**AES** is a modern symmetric encryption standard.

---

# 11. RSA

### Q1. What is RSA?

RSA is an **asymmetric public-key cryptosystem**.

### Q2. What are its keys?

```text
Public key  → (e, n)
Private key → (d, n)
```

### Q3. How are \(p\) and \(q\) used?

Choose two primes:

$$
p,q
$$

Then:

$$
n=pq
$$

### Q4. What is Euler's totient?

$$
\phi(n)=(p-1)(q-1)
$$

for distinct prime \(p,q\).

### Q5. How is \(e\) selected?

$$
GCD(e,\phi(n))=1
$$

### Q6. How is \(d\) calculated?

$$
ed\equiv1\pmod{\phi(n)}
$$

Therefore:

$$
d=e^{-1}\mod\phi(n)
$$

### Q7. Encryption?

$$
\boxed{c=m^e\mod n}
$$

### Q8. Decryption?

$$
\boxed{m=c^d\mod n}
$$

### Q9. What is RSA's security based on?

The difficulty of factoring a sufficiently large modulus \(n=pq\).

---

# 12. Diffie-Hellman

### Q1. What is Diffie-Hellman?

A **key exchange algorithm** that allows two parties to establish a shared secret over an insecure channel.

### Q2. Is it an encryption algorithm?

Not by itself.

It is primarily a **key agreement/key exchange mechanism**.

### Q3. Public parameters?

$$
p,g
$$

### Q4. Alice's private and public keys?

Private:

$$
a
$$

Public:

$$
A=g^a\mod p
$$

### Q5. Bob's private and public keys?

Private:

$$
b
$$

Public:

$$
B=g^b\mod p
$$

### Q6. How does Alice calculate the shared secret?

$$
S=B^a\mod p
$$

### Q7. How does Bob calculate it?

$$
S=A^b\mod p
$$

Both obtain:

$$
\boxed{S=g^{ab}\mod p}
$$

### Q8. What mathematical problem provides security?

The **discrete logarithm problem**.

### Q9. Does Diffie-Hellman itself authenticate Alice and Bob?

**No.**

Basic Diffie-Hellman is vulnerable to a **man-in-the-middle attack** unless combined with authentication.

That's a very good viva question.

---

# 13. Primitive Root & Modular Arithmetic

### Q1. What is modular arithmetic?

Arithmetic involving a modulus where results are reduced to the remainder.

Example:

$$
17\mod5=2
$$

### Q2. What is a primitive root?

For a prime \(p\), a number \(g\) is a primitive root modulo \(p\) if its powers generate every non-zero residue modulo \(p\).

### Q3. Example?

For:

$$
p=7,\quad g=3
$$

we get:

```text
3¹ mod 7 = 3
3² mod 7 = 2
3³ mod 7 = 6
3⁴ mod 7 = 4
3⁵ mod 7 = 5
3⁶ mod 7 = 1
```

All values:

```text
1,2,3,4,5,6
```

are generated.

Therefore 3 is a primitive root modulo 7.

### Q4. Why are primitive roots important?

They are used in cryptographic algorithms such as:

* Diffie-Hellman
* ElGamal

### Q5. What are common modular operations?

$$
(a+b)\mod m
$$

$$
(a-b)\mod m
$$

$$
(a\times b)\mod m
$$

$$
a^b\mod m
$$

---

# 14. Extended Euclidean Algorithm

### Q1. What is the difference between Euclidean and Extended Euclidean Algorithm?

**Euclidean:**

Finds only:

$$
GCD(a,b)
$$

**Extended Euclidean:**

Finds:

$$
GCD(a,b),x,y
$$

such that:

$$
ax+by=GCD(a,b)
$$

### Q2. What is Bézout's identity?

$$
\boxed{ax+by=GCD(a,b)}
$$

### Q3. Why is it important in cryptography?

It can calculate **modular multiplicative inverses**.

### Q4. When does a modular inverse exist?

The inverse of \(a\) modulo \(m\) exists when:

$$
\boxed{GCD(a,m)=1}
$$

### Q5. Example

Find inverse of 3 modulo 26.

We need:

$$
3x\equiv1\pmod{26}
$$

Since:

$$
3\times9=27\equiv1\pmod{26}
$$

therefore:

$$
\boxed{3^{-1}\mod26=9}
$$

---

# 🔥 Very Important Comparison Questions

These are the questions I would **especially memorize for the viva**.

| Algorithm          | Type         | Main Idea                             |
| ------------------ | ------------ | ------------------------------------- |
| Caesar             | Symmetric    | Shift letters                         |
| Playfair           | Symmetric    | Encrypt letter pairs                  |
| Monoalphabetic     | Symmetric    | Substitute using alphabet permutation |
| OTP                | Symmetric    | Random one-time key                   |
| Euclidean          | Mathematical | Find GCD                              |
| Caesar Brute Force | Attack       | Try all Caesar keys                   |
| Hill               | Symmetric    | Matrix multiplication                 |
| Transposition      | Symmetric    | Rearrange positions                   |
| ElGamal            | Asymmetric   | Discrete logarithm                    |
| DES                | Symmetric    | 64-bit block cipher, 16 rounds        |
| RSA                | Asymmetric   | Modular exponentiation/factoring      |
| Diffie-Hellman     | Key exchange | Establish shared secret               |
| Primitive Root     | Mathematical | Generate non-zero residues            |
| Extended Euclidean | Mathematical | GCD + coefficients/inverse            |

---

# 🔥 15 Questions Examiner May Ask From Your Code

### 1. Why do we use `% 26`?

Because the English alphabet has 26 letters, represented by numbers `0–25`.

### 2. Why do we use `ord()`?

`ord()` converts a character into its numerical Unicode/ASCII code.

Example:

```python
ord('A') = 65
```

### 3. Why do we use `chr()`?

It converts a numerical code back into a character.

```python
chr(65) = 'A'
```

### 4. Why do we use `pow(a, b, m)`?

It efficiently calculates:

$$
a^b\mod m
$$

For example:

```python
pow(5, 6, 23)
```

calculates:

$$
5^6\mod23
$$

### 5. Why do some programs use `X`?

Usually as **padding** or as a separator when two identical letters occur in Playfair.

### 6. What is symmetric encryption?

The same secret key is used for encryption and decryption.

Examples:

```text
Caesar
Playfair
OTP
Hill
DES
```

### 7. What is asymmetric encryption?

Different keys are used:

```text
Public key
Private key
```

Examples:

```text
RSA
ElGamal
```

### 8. Is Diffie-Hellman encryption?

Not directly. It is primarily a **key exchange/key agreement algorithm**.

### 9. What is a brute-force attack?

Trying possible keys until the correct key/plaintext is found.

### 10. What is a modular inverse?

A number \(x\) is the modular inverse of \(a\) modulo \(m\) if:

$$
ax\equiv1\pmod m
$$

### 11. What is a primitive root?

A number whose powers generate all non-zero residues modulo a prime.

### 12. Why is RSA called asymmetric?

Because it uses separate public and private keys.

### 13. Why is DES called symmetric?

Because the same secret key is used for encryption and decryption.

### 14. Which algorithms in your labs use matrices?

**Hill Cipher.**

### 15. Which labs involve GCD?

**Euclidean Algorithm, Extended Euclidean Algorithm, RSA, and Hill Cipher** all connect to GCD/modular inverse concepts.

---

# 🧠 One-Minute Final Revision

If the examiner suddenly says **"Explain all your labs briefly"**, say:

> **Caesar Cipher** shifts letters by a fixed key.
> **Playfair Cipher** encrypts pairs of letters using a 5×5 matrix.
> **Monoalphabetic Cipher** replaces each letter using a fixed substitution alphabet.
> **One-Time Pad** uses a random key equal in length to the plaintext and is theoretically perfectly secure when used correctly.
> **Euclidean Algorithm** finds GCD using repeated remainders.
> **Caesar Brute Force** tries all possible Caesar keys.
> **Hill Cipher** uses matrix multiplication modulo 26.
> **Double Transposition** rearranges the positions of characters twice.
> **ElGamal** is an asymmetric cryptosystem based on the discrete logarithm problem.
> **DES** is a symmetric 64-bit block cipher with 16 rounds.
> **RSA** is an asymmetric algorithm based on modular arithmetic and the difficulty of factoring large numbers.
> **Diffie-Hellman** allows two parties to establish a shared secret over an insecure channel.
> **Primitive Root** generates all non-zero residues modulo a prime.
> **Extended Euclidean Algorithm** finds GCD and the coefficients needed for modular inverses.

### The formulas you should absolutely remember

$$
\boxed{C=(P+K)\mod26}
$$

$$
\boxed{P=(C-K)\mod26}
$$

$$
\boxed{GCD(a,b)=GCD(b,a\bmod b)}
$$

$$
\boxed{C=KP\mod26}
$$

$$
\boxed{P=K^{-1}C\mod26}
$$

$$
\boxed{y=g^x\mod p}
$$

$$
\boxed{c_1=g^k\mod p,\quad c_2=my^k\mod p}
$$

$$
\boxed{n=pq,\quad \phi(n)=(p-1)(q-1)}
$$

$$
\boxed{c=m^e\mod n}
$$

$$
\boxed{m=c^d\mod n}
$$

$$
\boxed{S=g^{ab}\mod p}
$$

$$
\boxed{ax+by=GCD(a,b)}
$$

If you can explain **these formulas + the purpose of each algorithm + symmetric vs asymmetric + modular inverse + primitive root**, you should be able to handle most basic-to-intermediate questions from these 14 labs.
Yes — these are exactly the **“why this, why not that, what is used nowadays?”** questions examiners like to ask.

# 1. Why do we need different cryptographic algorithms?

Because different algorithms solve different problems.

Think of cryptography as having three major jobs:

| Problem                     | Typical solution               |
| --------------------------- | ------------------------------ |
| Encrypt actual data quickly | **AES / ChaCha20**             |
| Exchange a secret securely  | **X25519 / Diffie-Hellman**    |
| Digital signatures          | **Ed25519 / ECDSA / RSA-PSS**  |
| Password storage            | **Argon2id / bcrypt / scrypt** |
| Hashing                     | **SHA-256 / SHA-3**            |

A very important concept:

> **Modern systems usually combine several cryptographic algorithms rather than using one algorithm for everything.**

---

# 2. Why not use Caesar Cipher nowadays?

Caesar has only:

$$
26
$$

possible shifts.

An attacker can simply try every key.

For example:

```text
Key 0 → ...
Key 1 → ...
Key 2 → ...
...
Key 25 → ...
```

Therefore, Caesar is useful for **learning cryptography**, but not for security.

### Nowadays?

❌ Caesar is not used for serious encryption.

---

# 3. Why not use Monoalphabetic Substitution?

It has:

$$
26!
$$

possible mappings, which sounds huge.

But the problem is that the same plaintext letter is always mapped to the same ciphertext letter.

For example:

```text
E → Q
```

every time.

Therefore, attackers can use **frequency analysis**.

### Nowadays?

❌ Not used for secure communication.

It is mainly educational.

---

# 4. Why not use Playfair?

Playfair is stronger than simple single-letter substitution because it encrypts **pairs of letters**.

But it still leaks statistical information and has a relatively small structure compared with modern cryptographic systems.

### Nowadays?

❌ Not used for modern secure communication.

It is historically interesting and educational.

---

# 5. Why is One-Time Pad different?

OTP is actually theoretically **perfectly secure** if used correctly.

It requires:

* Truly random key
* Key as long as the message
* Key used only once
* Secure key distribution

The problem is **key management**.

Imagine you want to securely send:

```text
1 GB of data
```

You need approximately:

```text
1 GB of truly random key
```

and somehow both parties must securely possess that key beforehand.

That's impractical for most modern communication.

### So why study OTP?

Because it teaches an important theoretical concept:

$$
\boxed{\text{Perfect secrecy}}
$$

### Nowadays?

❌ Not normally used for general Internet communication.

✅ It can be useful in specialized situations where secure key distribution is possible.

---

# 6. Why do we use AES instead?

This is one of the **most important viva answers**.

**AES** is a modern symmetric block cipher designed to provide strong security while being efficient in software and hardware.

It supports:

```text
128-bit key
192-bit key
256-bit key
```

AES is much more practical than DES.

### Where is AES used?

Examples include:

* TLS/HTTPS
* VPNs
* Disk encryption
* File encryption
* Wi-Fi security
* Applications and databases

So if the examiner asks:

> **"Which symmetric encryption algorithm is commonly used nowadays?"**

A good answer is:

> **AES is one of the standard modern symmetric encryption algorithms.**

---

# 7. Why not DES?

DES has an effective key size of only:

$$
56\text{ bits}
$$

Modern computers can perform exhaustive key searches much more easily than when DES was designed.

Therefore DES is considered **obsolete for modern security**.

### What replaced DES?

**AES.**

Remember:

```text
DES → old
AES → modern standard
```

---

# 8. Why do we need asymmetric cryptography?

Symmetric encryption has a key-distribution problem.

Suppose Alice and Bob want to communicate securely.

They both need the same secret key:

```text
Alice ───── secret key ───── Bob
```

But how do they safely give the key to each other if the communication channel is insecure?

Asymmetric cryptography helps solve this problem.

There are two different keys:

```text
Public key  → can be shared
Private key → kept secret
```

---

# 9. Why RSA?

RSA allows public-key encryption and digital signatures.

But RSA has a major disadvantage:

### It is relatively computationally expensive.

You generally don't encrypt huge amounts of data directly with RSA.

Instead, systems typically use asymmetric cryptography to establish/protect a symmetric key, and then symmetric cryptography handles the actual bulk data.

---

# 10. Is RSA still used nowadays?

**Yes.**

But its role has changed somewhat.

RSA is still used in areas such as:

* Digital signatures
* Certificates
* Some key-transport systems
* Legacy and compatibility scenarios

However, modern protocols increasingly use **elliptic-curve cryptography (ECC)** for many public-key operations.

---

# 11. Why ECC?

ECC provides strong security with relatively small key sizes.

For example, roughly speaking:

```text
RSA 3072-bit
       ≈
ECC 256-bit
```

in commonly cited security-level comparisons.

Smaller keys can mean:

* Less storage
* Smaller certificates/signatures in some schemes
* Faster operations in appropriate contexts
* Lower bandwidth requirements

### Nowadays?

ECC is widely used.

Important examples include:

* **X25519** → key agreement
* **Ed25519** → digital signatures
* **ECDSA** → digital signatures

---

# 12. Why do we study Diffie-Hellman?

Diffie-Hellman solves a different problem.

It is primarily a **key agreement mechanism**, not a bulk encryption algorithm.

Alice and Bob can establish:

$$
\boxed{\text{Shared Secret}}
$$

without directly sending that secret.

### Traditional DH

Uses modular exponentiation:

$$
g^{ab}\mod p
$$

### Modern version

Modern protocols commonly use **elliptic-curve Diffie-Hellman**, especially:

$$
\boxed{\text{X25519}}
$$

---

# 13. Why not use ordinary Diffie-Hellman everywhere?

Traditional finite-field Diffie-Hellman requires relatively large parameters for modern security.

Elliptic-curve approaches can provide comparable security with much smaller keys.

So modern systems often prefer:

```text
Traditional DH
      ↓
Elliptic Curve DH
      ↓
X25519
```

---

# 14. What about ElGamal?

ElGamal is important academically because it demonstrates public-key encryption based on the discrete logarithm problem.

However, **raw textbook ElGamal is not generally the first choice for modern application encryption**.

Modern cryptographic protocols tend to use standardized constructions based on:

* ECC
* X25519
* authenticated encryption
* carefully designed protocols

---

# 15. Why not use Hill Cipher?

Hill Cipher is mathematically interesting because it uses:

$$
C=KP\mod26
$$

But it has weaknesses and is not designed for modern secure communication.

It is useful for understanding:

* Matrices
* Modular arithmetic
* Modular inverses
* Block transformations

### Nowadays?

❌ Not used for modern secure encryption.

---

# 16. Why not use Transposition Cipher?

Transposition only changes the positions of characters.

It doesn't provide sufficient modern cryptographic security.

### Nowadays?

❌ Not used as a standalone secure encryption method.

---

# 17. Why do we need GCD?

This is where your Labs 5 and 14 become important.

GCD itself is not an encryption algorithm.

It is a **mathematical building block**.

For example, RSA needs:

$$
GCD(e,\phi(n))=1
$$

And the Extended Euclidean Algorithm helps calculate modular inverses.

So:

```text
GCD
 ↓
Number theory
 ↓
Cryptographic algorithms
```

---

# 18. Why Extended Euclidean Algorithm?

Because we often need:

$$
a^{-1}\mod m
$$

That means finding \(x\) such that:

$$
ax\equiv1\pmod m
$$

The Extended Euclidean Algorithm can find this when:

$$
GCD(a,m)=1
$$

This is particularly important in your:

* RSA
* Hill Cipher
* ElGamal

labs.

---

# 19. Why Primitive Roots?

Primitive roots are important in the mathematical foundation of algorithms such as:

* Diffie-Hellman
* ElGamal

A primitive root can generate all non-zero elements of the multiplicative group modulo a prime.

For example:

$$
3^1,3^2,\ldots,3^6\mod7
$$

generates:

```text
3, 2, 6, 4, 5, 1
```

So every non-zero residue appears.

---

# 20. The most important comparison

Memorize this table:

| Algorithm          | Why studied                     | Used nowadays?                          |
| ------------------ | ------------------------------- | --------------------------------------- |
| Caesar             | Basic substitution              | ❌ No                                    |
| Playfair           | Pair substitution               | ❌ No                                    |
| Monoalphabetic     | Substitution/frequency analysis | ❌ No                                    |
| OTP                | Perfect secrecy concept         | ⚠️ Specialized                          |
| Euclidean          | GCD                             | ✅ Mathematical building block           |
| Caesar brute force | Cryptanalysis                   | ✅ Concept/attack technique              |
| Hill               | Matrix-based cryptography       | ❌ No                                    |
| Transposition      | Rearrangement concept           | ❌ No                                    |
| ElGamal            | Public-key cryptography concept | ⚠️ Raw form generally not preferred     |
| DES                | Historical symmetric cipher     | ❌ Obsolete                              |
| RSA                | Public-key crypto/signatures    | ✅ Still used                            |
| Diffie-Hellman     | Key agreement                   | ✅ Modern variants used                  |
| Primitive Root     | Number-theory foundation        | ✅ In some cryptographic constructions   |
| Extended Euclidean | Modular inverse                 | ✅ Important mathematical building block |

---

# 21. What is actually used today?

This is probably the **single most useful viva question**.

### Symmetric encryption

Common modern choices include:

$$
\boxed{\text{AES}}
$$

and:

$$
\boxed{\text{ChaCha20}}
$$

Often used in authenticated-encryption forms such as:

$$
\boxed{\text{AES-GCM}}
$$

and:

$$
\boxed{\text{ChaCha20-Poly1305}}
$$

---

### Key Exchange

Modern systems commonly use:

$$
\boxed{\text{X25519}}
$$

which is an elliptic-curve Diffie-Hellman construction.

---

### Digital Signatures

Common choices include:

$$
\boxed{\text{Ed25519}}
$$

and:

$$
\boxed{\text{ECDSA}}
$$

RSA signatures also remain important, particularly for compatibility and existing infrastructure.

---

### Hashing

For cryptographic hashing:

$$
\boxed{\text{SHA-256}}
$$

and:

$$
\boxed{\text{SHA-3}}
$$

are examples of modern cryptographic hash functions.

For **password storage**, you should not simply use SHA-256. Password hashing/KDFs such as:

$$
\boxed{\text{Argon2id}}
$$

are designed for that purpose.

---

# 22. What happens when you open an HTTPS website?

This is an excellent viva question.

Don't say:

> "HTTPS uses RSA."

That's an outdated oversimplification.

A modern TLS connection generally involves several cryptographic components.

Conceptually:

```text
          HTTPS
            │
            ▼
     Key Agreement
       X25519 etc.
            │
            ▼
     Shared Session Keys
            │
            ▼
 Authenticated Encryption
     AES-GCM / ChaCha20-Poly1305
            │
            ▼
       Encrypted Data
```

Certificates and digital signatures are also involved in authentication.

So the important principle is:

> **Asymmetric cryptography helps establish/authenticate the connection, while symmetric authenticated encryption handles the actual bulk data efficiently.**

---

# 23. Why don't we simply use RSA to encrypt the whole website data?

Because RSA is comparatively expensive and is not designed for efficient bulk encryption of arbitrary amounts of data.

Instead:

```text
Public-key cryptography
        ↓
Establish/protect session key
        ↓
Symmetric encryption
        ↓
Encrypt millions of bytes efficiently
```

This is called **hybrid cryptography**.

### Very important viva answer:

> "Asymmetric cryptography solves key establishment and authentication problems, while symmetric cryptography is much more efficient for bulk data encryption."

---

# 24. One final question: "If AES is so good, why do we study Caesar?"

Answer:

> "These laboratory algorithms teach the fundamental concepts of cryptography. Caesar demonstrates substitution and key-based transformation; Playfair demonstrates digraph substitution; Hill demonstrates matrix operations; DES demonstrates block ciphers and Feistel networks; RSA and ElGamal demonstrate public-key cryptography; and Diffie-Hellman demonstrates key exchange. Modern algorithms build on more advanced versions of these underlying concepts."

That's a **very strong viva answer** because it shows that you understand **why the university is teaching these algorithms even though many are no longer used in practice**.
