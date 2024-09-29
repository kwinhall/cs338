# Being Eve

By Kendra Winhall

## Diffie-Hellman

We are given the following information:
* We know that `g = 7`, `p = 97`. 
* Alice sent the number `A = 53` to Bob. We know that `A = g^X mod p`.
* Bob sent the number `B = 82` to Alice. We know that `B = g^Y mod p`.
* Furthermore, we know that the shared secret key is equal to `B^X mod p` and `A^Y mod p`.

Because these integers are small, we can use a brute-force approach to find the shared key. To compute the shared secret key, I wrote a function that uses the fact that `A = g^X mod p` and `B = g^Y mod p` to find values of `X` and `Y`. Then, using the fact that the shared secret key is equal to `B^X mod p` and `A^Y mod p`, I computed the shared secret key. (You don't need to know both `X` and `Y` to find the shared secret key, but since the given value of `p` is small in this particular case, it's very quick to find both values). To verify our results, we can check that both methods of calculating the shared secret key return the same result.

Here's the function I wrote:

```python
def solve_diffie(g, p, A, B):
    X = 1
    while X > 0:
        if A == (g**X)%p:
            shared_secret_1 = (B**X)%p
            break
        else:
            X += 1
    
    Y = 1
    while Y > 0:
        if B == (g**Y)%p:
            shared_secret_2 = (A**Y)%p
            break
        else:
            Y += 1
    print(f"X = {X}, shared secret key (calculated from X) = {shared_secret_1}\nY = {Y}, shared secret key (calculated from Y) = {shared_secret_2}")
```

Since we are given that `g = 7`, `p = 97`, `A = 53`, and `B = 82`, run `solve_diffie(7, 97, 53, 82)`. The output is as follows:
```
X = 22, shared secret key (calculated from X) = 65
Y = 41, shared secret key (calculated from Y) = 65
```

Since both methods of calculating the shared secret key return the same result, we know that these values of `X`, `Y`, and the shared secret key are correct. Therefore, `X = 22`, `Y = 41`, and the shared secret key is equal to 65.

If the integers involved were very large, then my brute-force approach would not have worked. Specifically, if `p` was a very large prime number (instead of a smaller prime number), it would have taken an extremely long time to find a value of `X` that satisfies the condition `A = g^X mod p`. (Alternatively, it would have taken an extremely long time to find a value of `Y` that satisfies the condition `B = g^Y mod p`.) This would make my approach infeasible.

## RSA

We are given Bob's public key: `(e_Bob, n_Bob) = (13, 162991)`

We are also given the encrypted data sent from Alice to Bob:
```
[17645, 100861, 96754, 160977, 120780, 90338, 130962, 74096, 128123, 25052, 119569, 39404, 6697, 82550, 126667, 151824, 80067, 75272, 72641, 43884, 5579, 29857, 33449, 46274, 59283, 109287, 22623, 84902, 6161, 109039, 75094, 56614, 13649, 120780, 133707, 66992, 128221]
```

Well, we know that `n_Bob = p_Bob * q_Bob`. Luckily for us, `n_Bob` (162991) is small, so we can quickly find its factors: 389 and 419 (I used [this calculator](https://www.calculatorsoup.com/calculators/math/factors.php)). It doesn't matter which one is which, so let's say `p_Bob = 389` and `q_Bob = 419`.

We know that `λ(n_Bob) = lcm(p_Bob - 1, q_Bob - 1)`. We plug our values into this equation to get `λ(162991) = lcm(389 - 1, 419 - 1) = lcm(388, 418) = 81092`.

We know that `e_Bob * d_Bob mod λ(n_Bob) = 1`. Since we know `e_Bob` and `λ(n_Bob)`, we can compute `d_Bob` with a brute force approach. See the function below:

```python
def compute_d(e, l):
    d = 1
    while d > 0:
        if (e*d)%l == 1:
            break
        else:
            d += 1
    print(d)
```

By running `compute_d(13, 81092)`, we get `d_Bob = 43665`.

To decrypt each integer `y`, we must compute `y^d_Bob mod n_Bob`, like so:

```python
def decrypt_rsa(encrypted_data, d, n):
    for y in encrypted_data:
        decrypted_y = (y**d)%n
        print(decrypted_y)
```

Using the given encrypted data (in its original format as a list of integers), the value of `d_Bob` that we computed (43665), and the given value of `n_Bob` (162991) as input, this function decrypts the message but returns numbers that don't seem like they represent any ASCII encodings (`17509 24946 8258 28514 11296` ...). So, I tried converting them to hexadecimal and removing the `0x` at the beginning:

```python
def decrypt_rsa(encrypted_data, d, n):
    for y in encrypted_data:
        decrypted_y = (y**d)%n
        hex_y = hex(decrypted_y)[2:]
        print(hex_y)
```

Using the same input as before, this returns some hexadecimal numbers (`4465 6172 2042 6f62 2c20`...). Looking at an ASCII chart for the first few hexadecimal numbers, they are valid ASCII encodings that seem to contain a message (`Dear Bob, `...). So we add one more line to our function to convert the entire hex string to ASCII:

```python
def decrypt_rsa(encrypted_data, d, n):
    message = ""
    for y in encrypted_data:
        decrypted_y = (y**d)%n
        hex_y = hex(decrypted_y)[2:]
        message += bytes.fromhex(hex_y).decode()
    print(message)
```

Using the same input as before, this returns the message: 

`Dear Bob, check this out. https://www.surveillancewatch.io/ See ya, Alice.`

Great message, Alice! We were correct--Alice encodes the characters in her message into hexadecimal numbers using the ASCII character encoding standard. Then, these hexadecimal numbers are encrypted using RSA.

If the integers involved were very large, then I would not have been able to decrypt Alice's message. Specifically, if `q_Bob` and `p_Bob` were two very large prime numbers (instead of smaller prime numbers), then their product, `n_Bob`, would be a very large semiprime number (instead of a smaller semiprime number). If `n_Bob` was a very large semiprime number, then it would take an extremely long time to factorize `n_Bob` into `q_Bob` and `p_Bob`. This would make my approach infeasible. (Furthermore, my brute force method of finding `d_Bob` probably would have taken a long time if the other integers involved were very large.)

Regardless of the size of Bob's integers or the security of RSA, Alice's message encoding system is still insecure. Alice converts two characters at a time into a hexadecimal number, then encrypts each hexadecimal number as its own separate block. This is insecure for a couple reasons:
* A malicious attacker could simply generate all blocks of two ASCII characters (in hexadecimal), encrypt them with Bob's public key, and use them as a reference to read the message. For instance, a malicious attacker could generate a comma followed by a space and then encode this into its hexadecimal ASCII encoding `0x2c20`. Then, they could encrypt this with Bob's public key. They could plug the public values `e_Bob = 13` and `n_Bob = 162991` into the equation `0x2c20^e_Bob mod n_Bob`. This would give them `0x2c20^13 mod 162991 = 120780`. Then, looking at the encrypted data (17645, 100861, 96754, 160977, 120780 ...), the malicious attacker could see that 120780 is the 5th number in the encrypted data, meaning that the 9th and 10th characters of the message are a comma and a space, respectively. The attacker could repeat this process for all common sets of two ASCII characters until they could read the entire message.
* More broadly, a malicious attacker could use repeated patterns in the encrypted data as a foothold. For instance, if a certain integer or set of integers appears frequently in the encrypted message, a malicious attacker could assume that it represents a common character (like a space or the letter e) and use that foothold to decrypt the message.