# Safe RSA
Crypto, 250 points

## Description:
> Now that you know about RSA can you help us decrypt this ciphertext? We don't have the decryption key but something about those values looks funky.. 
```
N: 374159235470172130988938196520880526947952521620932362050308663243595788308583992120881359365258949723819911758198013202644666489247987314025169670926273213367237020188587742716017314320191350666762541039238241984934473188656610615918474673963331992408750047451253205158436452814354564283003696666945950908549197175404580533132142111356931324330631843602412540295482841975783884766801266552337129105407869020730226041538750535628619717708838029286366761470986056335230171148734027536820544543251801093230809186222940806718221638845816521738601843083746103374974120575519418797642878012234163709518203946599836959811
e: 3

ciphertext (c): 2205316413931134031046440767620541984801091216351222789180582564557328762455422721368029531360076729972211412236072921577317264715424950823091382203435489460522094689149595951010342662368347987862878338851038892082799389023900415351164773 
```

## Solution:

Since [RsaCtfTool](https://github.com/Ganapati/RsaCtfTool) wasn't able to recover the private key, I searched for attacks against small exponents and found [this](https://l34rn-p14y.blogspot.com/2014/03/inctf-2014-crypto-200.html) and [this](https://eindbazen.net/2012/05/plaidctf-2012-rsa/).

I was able to reduce their code to the following, given the values above:
```python
import gmpy2

n = 374159235470172130988938196520880526947952521620932362050308663243595788308583992120881359365258949723819911758198013202644666489247987314025169670926273213367237020188587742716017314320191350666762541039238241984934473188656610615918474673963331992408750047451253205158436452814354564283003696666945950908549197175404580533132142111356931324330631843602412540295482841975783884766801266552337129105407869020730226041538750535628619717708838029286366761470986056335230171148734027536820544543251801093230809186222940806718221638845816521738601843083746103374974120575519418797642878012234163709518203946599836959811
e = 3
cipher_str = 2205316413931134031046440767620541984801091216351222789180582564557328762455422721368029531360076729972211412236072921577317264715424950823091382203435489460522094689149595951010342662368347987862878338851038892082799389023900415351164773

gs = gmpy2.mpz(cipher_str)
gm = gmpy2.mpz(n)
ge = gmpy2.mpz(e)

root, exact = gmpy2.iroot(gs, ge)
print format(root, 'x').decode('hex')
```

Explanation: 

RSA encryption is performed by calculating C=M^e(mod n).

However, if n is much larger than e (as is the case here), and if the message is not too long (i.e. small M), then M^e(mod n) == M^e and therefore M can be found by calculating the e-th root of C.

The result:
```console
root@kali:/media/sf_CTFs/pico/Safe_RSA# python decode.py
picoCTF{e_w4y_t00_sm411_81b6559f}
```

The flag: picoCTF{e_w4y_t00_sm411_81b6559f}