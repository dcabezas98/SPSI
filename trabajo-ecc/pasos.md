#### Lista de curvas
openssl ecparam -list_curves

#### Mostrar parámetros de la curva
openssl ecparam -name secp256k1 -out secp256k1.pem
openssl ecparam -in secp256k1.pem -text -param_enc explicit -noout

#### Generar clave privada
openssl ecparam -in secp256k1.pem -genkey -noout -out userS-privkey.pem
##### Vemos la privada y la pública asociada
openssl ec -in userS-privkey.pem -text -noout

#### Generar la clave pública
openssl ec -in userS-privkey.pem -pubout -out userS-pubkey.pem
##### Vemos la pública
openssl ec -in userS-pubkey.pem -pubin -text -noout

#### Firma
openssl dgst -sign userS-privkey.pem -out msg.txt.sign msg.txt

#### Cifrado
##### Crear secreto compartido
openssl pkeyutl -derive -inkey userS-privkey.pem -peerkey userR-pubkey.pem -out secret.txt

openssl enc -aes-256-cbc -md sha512 -pbkdf2 -iter 100000 -salt -in msg.txt -out msg.txt.enc -pass file:secret.txt


# Receptor
##### Crear secreto compartido
openssl pkeyutl -derive -inkey userR-privkey.pem -peerkey userS-pubkey.pem -out secret.txt

#### Descifrar
openssl enc -aes-256-cbc -md sha512 -pbkdf2 -iter 100000 -salt -d -in msg.txt.enc -out msg.txt -pass file:secret.txt

#### Verificar
openssl dgst -verify userS-pubkey.pem -signature msg.txt.sign msg.txt
