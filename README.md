# Smartcard PKI
## Instructions to use two smartcards to create a CA and client cert. As the private keys are stored on the cards, you don't have to be (as) paranoid about their security. 

### Source: http://pki-tutorial.readthedocs.org/en/latest/simple/ for a very basic PKI

### The details:
1. On the Client card and the Signing CA card

  ```pkcs15-init -E```
  
  ```pkcs15-init --create-pkcs15 -p pkcs15+onepin --pin 1234 --puk 4321```

  ```pkcs15-init -G rsa/2048 -i 01 -a 01 -u sign --pin 1234```

2. create dirs and db

  ```mkdir -p ca/signing-ca/db crl certs```
  
  ```chmod 700 ca/signing-ca/private```
  
  ```cp /dev/null ca/signing-ca/db/signing-ca.db```
  
  ```cp /dev/null ca/signing-ca/db/signing-ca.db.attr```
  
  ```echo 01 > ca/signing-ca/db/signing-ca.crt.srl```
  
  ```echo 01 > ca/signing-ca/db/signing-ca.crl.srl```

3. with the Signing CA card in the computer...

  ```openssl req -new -config etc/signing-ca.conf -out ca/signing-ca.csr -key slot_1-id_01 -keyform engine -engine pkcs11```

  ```openssl ca -selfsign -config etc/signing-ca.conf -in ca/signing-ca.csr -out ca/signing-ca.crt -keyform engine -engine pkcs11 -keyfile slot_1-id_01 -extensions signing_ca_ext```

4. with the Client card in the computer

  ```openssl req -new -config etc/email.conf -out certs/me.csr -key slot_1-id_01 -keyform engine -engine pkcs11```

5. with the Signing CA card back in the computer, issue the client cert

  ```openssl ca -config etc/signing-ca.conf -in certs/me.csr -out certs/me.crt -keyfile slot_1-id_01 -keyform engine -engine pkcs11```

6. also, why not store the CA cert on the CA card

  ```pkcs15-init -X ca/signing-ca.crt -i 01 -a 01 ```

7. with the Client card in the reader, store the cert on the card

  ```pkcs15-init -X certs/me.crt -i 01 -a 01```

8. Take a look at the pam_pkcs11 setup instructions and use this instead of a self-signed cert
