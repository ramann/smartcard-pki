# Create keys & certs for Strongswan VPN server and Android client

### The details:
Create dirs and db

  ```mkdir -p ca/signing-ca/db crl certs```

  ```touch ca/signing-ca/private```
  
  ```chmod 700 ca/signing-ca/private```
  
  ```cp /dev/null ca/signing-ca/db/signing-ca.db```
  
  ```cp /dev/null ca/signing-ca/db/signing-ca.db.attr```
  
  ```echo 01 > ca/signing-ca/db/signing-ca.crt.srl```
  
  ```echo 01 > ca/signing-ca/db/signing-ca.crl.srl```

Create keys and issue certs
  ```openssl genpkey -algorithm RSA -out ca_key.pem -pkeyopt rsa_keygen_bits:8192```

  ```openssl genpkey -algorithm RSA -out vpn_server_key.pem -pkeyopt rsa_keygen_bits:8192```

  ```openssl genpkey -algorithm RSA -out phone_key.pem -pkeyopt rsa_keygen_bits:8192```

  ```openssl req -new -config etc/signing-ca.conf -out ca/signing-ca.csr -key ca_key.pem```

  ```openssl ca -selfsign -config etc/signing-ca.conf -in ca/signing-ca.csr -out ca/signing-ca.crt -keyfile ca_key.pem -extensions signing_ca_ext```

  ```openssl req -new -config etc/vpn_server.conf -out certs/vpn_server.csr -key vpn_server_key.pem```

  ```openssl ca -config etc/signing-ca.conf -in certs/vpn_server.csr -out certs/vpn_server.crt -keyfile ca_key.pem```

  ```openssl req -new -config etc/phone.conf -out certs/phone.csr -key phone_key.pem```

  ```openssl ca -config etc/signing-ca.conf -in certs/phone.csr -out certs/phone.crt -keyfile ca_key.pem```

  ```openssl pkcs12 -export -out phone-and-ca.pfx -inkey phone_key.pem -in certs/phone.crt -certfile ca/signing-ca.crt```
