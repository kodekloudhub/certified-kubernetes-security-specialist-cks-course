# One Way SSL vs Mutual SSL

  - Take me to the [Video Tutorial](https://kodekloud.com/topic/one-way-ssl-vs-mutual-ssl/)

In this section, we will take a look at `One Way SSL vs Mutual SSL`.

## Certificate
- A certificate is used to guarantee trust between 2 parties during a transaction.
- Example: when a user tries to access a web server, tls certificates ensure that the communication between them is encrypted.

  ![cert1](../../images/cert1.PNG)


## Symmetric Encryption
- It is a secure way of encryption, but it uses the same key to encrypt and decrypt the data and the key has to be exchanged between the sender and the receiver, there is a risk of a hacker gaining access to the key and decrypting the data.

  ![cert2](../../images/cert2.PNG)

## Asymmetric Encryption
- Instead of using a single key to encrypt and decrypt data, asymmetric encryption uses a pair of keys, a private key and a public key.

  ![cert3](../../images/cert3.PNG)

  ![cert4](../../images/cert4.PNG)

  ![cert5](../../images/cert5.PNG)

  ![cert6](../../images/cert6.PNG)


#### How do you look at a certificate and verify if it is legit?
- who signed and issued the certificate.
- If you generate the certificate then you will have to sign it by yourself; that is known as self-signed certificate.

  ![cert7](../../images/cert7.PNG)

#### How do you generate legitimate certificates? How do you get your certificates signed by someone with authority?
- That's where **`Certificate Authority (CA)`** comes in for you. Some of the popular ones are Symantec, DigiCert, Comodo, GlobalSign etc.

  ![cert8](../../images/cert8.PNG)

  ![cert9](../../images/cert9.PNG)

  ![cert10](../../images/cert10.PNG)

## Public Key Infrastructure

   ![pki](../../images/pki.PNG)

## Certificates naming convention

  ![cert11](../../images/cert11.PNG)
