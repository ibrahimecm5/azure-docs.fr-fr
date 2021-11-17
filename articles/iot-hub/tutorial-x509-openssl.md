---
title: Tutoriel - Utiliser OpenSSL afin de créer des certificats de test X.509 pour Azure IoT Hub | Microsoft Docs
description: Tutoriel - Utiliser OpenSSL afin de créer des certificats d'AC et d'appareil pour Azure IoT Hub
author: eross-msft
ms.service: iot-hub
services: iot-hub
ms.topic: tutorial
ms.date: 02/26/2021
ms.author: lizross
ms.custom:
- mvc
- 'Role: Cloud Development'
- 'Role: Data Analytics'
ms.openlocfilehash: 5fd735420ad97934e45160f7d57fdc9029faee6a
ms.sourcegitcommit: 05c8e50a5df87707b6c687c6d4a2133dc1af6583
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/16/2021
ms.locfileid: "132547626"
---
# <a name="tutorial-using-openssl-to-create-test-certificates"></a>Tutoriel : Utiliser OpenSSL pour créer des certificats de test

Vous pouvez acheter des certificats X.509 auprès d'une autorité de certification de confiance, mais vous avez aussi la possibilité de créer votre propre hiérarchie de certificats de test ou d'utiliser des certificats auto-signés pour tester l'authentification des appareils IoT Hub. Dans l'exemple suivant, [OpenSSL](https://www.openssl.org/) et le livre [OpenSSL Cookbook](https://www.feistyduck.com/library/openssl-cookbook/online/ch-openssl.html) sont utilisés pour créer une autorité de certification (AC), une AC subordonnée et un certificat d'appareil. Puis l'AC subordonnée et le certificat d'appareil sont signés dans une hiérarchie de certificats. Ce scénario est présenté à titre d'exemple uniquement.

## <a name="step-1---create-the-root-ca-directory-structure"></a>Étape 1 : Créer la structure de répertoires AC racine

Créez une structure de répertoires pour l'autorité de certification.

* Le répertoire **certs** stocke les nouveaux certificats.
* Le répertoire **db** est utilisé pour la base de données de certificats.
* Le répertoire **private** stocke la clé privée de l'AC.

```bash
  mkdir rootca
  cd rootca
  mkdir certs db private
  touch db/index
  openssl rand -hex 16 > db/serial
  echo 1001 > db/crlnumber
```

## <a name="step-2---create-a-root-ca-configuration-file"></a>Étape 2 : Créer un fichier de configuration pour l'AC racine

Avant de créer une AC, créez un fichier de configuration et enregistrez-le sous le nom de `rootca.conf` dans le répertoire rootca.

```xml
[default]
name                     = rootca
domain_suffix            = example.com
aia_url                  = http://$name.$domain_suffix/$name.crt
crl_url                  = http://$name.$domain_suffix/$name.crl
default_ca               = ca_default
name_opt                 = utf8,esc_ctrl,multiline,lname,align

[ca_dn]
commonName               = "Test Root CA"

[ca_default]
home                     = ../rootca 
database                 = $home/db/index
serial                   = $home/db/serial
crlnumber                = $home/db/crlnumber
certificate              = $home/$name.crt
private_key              = $home/private/$name.key
RANDFILE                 = $home/private/random
new_certs_dir            = $home/certs
unique_subject           = no
copy_extensions          = none
default_days             = 3650
default_crl_days         = 365
default_md               = sha256
policy                   = policy_c_o_match

[policy_c_o_match]
countryName              = optional
stateOrProvinceName      = optional
organizationName         = optional
organizationalUnitName   = optional
commonName               = supplied
emailAddress             = optional

[req]
default_bits             = 2048
encrypt_key              = yes
default_md               = sha256
utf8                     = yes
string_mask              = utf8only
prompt                   = no
distinguished_name       = ca_dn
req_extensions           = ca_ext

[ca_ext]
basicConstraints         = critical,CA:true
keyUsage                 = critical,keyCertSign,cRLSign
subjectKeyIdentifier     = hash

[sub_ca_ext]
authorityKeyIdentifier   = keyid:always
basicConstraints         = critical,CA:true,pathlen:0
extendedKeyUsage         = clientAuth,serverAuth
keyUsage                 = critical,keyCertSign,cRLSign
subjectKeyIdentifier     = hash

[client_ext]
authorityKeyIdentifier   = keyid:always
basicConstraints         = critical,CA:false
extendedKeyUsage         = clientAuth
keyUsage                 = critical,digitalSignature
subjectKeyIdentifier     = hash

```

## <a name="step-3---create-a-root-ca"></a>Étape 3 : Créer une AC racine

Tout d'abord, générez la clé et la demande de signature de certificat dans le répertoire rootca.

```bash
  openssl req -new -config rootca.conf -out rootca.csr -keyout private/rootca.key
```

Puis créez un certificat d'AC auto-signé. Celui-ci est adapté à un test. Spécifiez les extensions de fichier de configuration ca_ext sur la ligne de commande. Celles-ci indiquent que le certificat est destiné à une AC racine et peut être utilisé pour signer des certificats et des listes de révocation de certificats. Signez le certificat et validez-le dans la base de données.

```bash
  openssl ca -selfsign -config rootca.conf -in rootca.csr -out rootca.crt -extensions ca_ext
```

## <a name="step-4---create-the-subordinate-ca-directory-structure"></a>Étape 4 : Créer la structure de répertoires de l'AC subordonnée

Créez une structure de répertoires pour l'AC subordonnée.

```bash
  mkdir subca
  cd subca
  mkdir certs db private
  touch db/index
  openssl rand -hex 16 > db/serial
  echo 1001 > db/crlnumber
```

## <a name="step-5---create-a-subordinate-ca-configuration-file"></a>Étape 5 : Créer un fichier de configuration d'AC subordonnée

Créez un fichier de configuration et enregistrez-le sous le nom de subca.conf dans le répertoire `subca`.

```bash
[default]
name                     = subca
domain_suffix            = example.com
aia_url                  = http://$name.$domain_suffix/$name.crt
crl_url                  = http://$name.$domain_suffix/$name.crl
default_ca               = ca_default
name_opt                 = utf8,esc_ctrl,multiline,lname,align

[ca_dn]
commonName               = "Test Subordinate CA"

[ca_default]
home                     = . 
database                 = $home/db/index
serial                   = $home/db/serial
crlnumber                = $home/db/crlnumber
certificate              = $home/$name.crt
private_key              = $home/private/$name.key
RANDFILE                 = $home/private/random
new_certs_dir            = $home/certs
unique_subject           = no
copy_extensions          = copy 
default_days             = 365
default_crl_days         = 90 
default_md               = sha256
policy                   = policy_c_o_match

[policy_c_o_match]
countryName              = optional
stateOrProvinceName      = optional
organizationName         = optional
organizationalUnitName   = optional
commonName               = supplied
emailAddress             = optional

[req]
default_bits             = 2048
encrypt_key              = yes
default_md               = sha256
utf8                     = yes
string_mask              = utf8only
prompt                   = no
distinguished_name       = ca_dn
req_extensions           = ca_ext

[ca_ext]
basicConstraints         = critical,CA:true
keyUsage                 = critical,keyCertSign,cRLSign
subjectKeyIdentifier     = hash

[sub_ca_ext]
authorityKeyIdentifier   = keyid:always
basicConstraints         = critical,CA:true,pathlen:0
extendedKeyUsage         = clientAuth,serverAuth
keyUsage                 = critical,keyCertSign,cRLSign
subjectKeyIdentifier     = hash

[client_ext]
authorityKeyIdentifier   = keyid:always
basicConstraints         = critical,CA:false
extendedKeyUsage         = clientAuth
keyUsage                 = critical,digitalSignature
subjectKeyIdentifier     = hash
```

## <a name="step-6---create-a-subordinate-ca"></a>Étape 6 : Créer une AC subordonnée

Créez un numéro de série dans le fichier `rootca/db/serial` pour le certificat d'AC subordonnée.

```bash
  openssl rand -hex 16 > ../rootca/db/serial
```

>[!IMPORTANT]
>Vous devez créer un numéro de série pour chaque certificat d'AC subordonnée et chaque certificat d'appareil que vous créez. Le même numéro de série ne peut pas être utilisé par différents certificats.

Cet exemple montre comment créer une AC subordonnée ou une AC d'inscription. Dans la mesure où vous pouvez utiliser l'AC racine pour signer les certificats, il n'est pas forcément nécessaire de créer une AC subordonnée. Une AC subordonnée imite toutefois les hiérarchies de certificats du monde réel dans lesquelles l'AC racine est conservée hors connexion et les AC subordonnées émettent des certificats clients.

Utilisez le fichier de configuration pour générer une clé et une demande de signature de certificat.

```bash
  openssl req -new -config subca.conf -out subca.csr -keyout private/subca.key
```

Envoyez la demande de signature de certificat à l'AC racine, et utilisez cette dernière pour émettre et signer le certificat d'AC subordonnée. Spécifiez sub_ca_ext pour le commutateur d'extensions sur la ligne de commande. Les extensions indiquent que le certificat est destiné à une AC qui peut signer des certificats et des listes de révocation de certificats. Lorsque vous y êtes invité, signez le certificat et validez-le dans la base de données.

```bash
  openssl ca -config ../rootca/rootca.conf -in subca.csr -out subca.crt -extensions sub_ca_ext
```

## <a name="step-7---demonstrate-proof-of-possession"></a>Étape 7 : Fournir une preuve de possession

Vous disposez maintenant d'un certificat d'AC et d'un certificat d'AC subordonnée. Vous pouvez utiliser l'un ou l'autre pour signer des certificats d'appareil. Celui que vous choisissez doit être chargé sur votre instance d'IoT Hub. Les étapes suivantes reposent sur l'utilisation du certificat d'AC subordonnée. Pour charger et inscrire votre certificat d'AC subordonnée dans votre instance d'IoT Hub :

1. Sur le portail Azure, accédez à votre instance d'IoT Hub et sélectionnez **Paramètres > Certificats**.

2. Sélectionnez **Ajouter** pour ajouter votre nouveau certificat d'AC subordonnée.

3. Entrez un nom d'affichage dans le champ **Nom du certificat**, puis sélectionnez le fichier de certificat PEM que vous avez précédemment créé.

> [!NOTE]
> Les certificats .crt créés ci-dessus sont similaires aux certificats .pem. Vous pouvez simplement modifier l’extension lors du chargement d’un certificat pour prouver sa possession ou vous pouvez utiliser la commande OpenSSL suivante :

```bash
openssl x509 -in mycert.crt -out mycert.pem -outform PEM
```

4. Sélectionnez **Enregistrer**. Votre certificat apparaît dans la liste des certificats avec l'état **Non vérifié**. Le processus de vérification prouvera que vous êtes propriétaire du certificat.

   
5. Sélectionnez le certificat pour afficher la boîte de dialogue **Détails du certificat**.

6. Sélectionnez **Générer un code de vérification**. Pour plus d'informations, consultez [Preuve de possession d'un certificat d'AC](tutorial-x509-prove-possession.md).

7. Copiez le code de vérification dans le Presse-papiers. Vous devez définir le code de vérification en tant qu'objet du certificat. Par exemple, si le code de vérification est BB0C656E69AF75E3FB3C8D922C1760C58C1DA5B05AAA9D0A, ajoutez-le en tant qu'objet de votre certificat, comme indiqué à l'étape 9.

8. Générez une clé privée.

  ```bash
    $ openssl genpkey -out pop.key -algorithm RSA -pkeyopt rsa_keygen_bits:2048
  ```

9. Générez une demande de signature de certificat à partir d'une clé privée existante. Ajoutez le code de vérification en tant qu'objet de votre certificat.

  ```bash
  openssl req -new -key pop.key -out pop.csr
  
    -----
    Country Name (2 letter code) [XX]:.
    State or Province Name (full name) []:.
    Locality Name (eg, city) [Default City]:.
    Organization Name (eg, company) [Default Company Ltd]:.
    Organizational Unit Name (eg, section) []:.
    Common Name (eg, your name or your server hostname) []:BB0C656E69AF75E3FB3C8D922C1760C58C1DA5B05AAA9D0A
    Email Address []:

    Please enter the following 'extra' attributes
    to be sent with your certificate request
    A challenge password []:
    An optional company name []:
 
  ```

10. Créez un certificat en utilisant le fichier de configuration de l’AC subordonnée et la demande de signature de certificat pour la preuve de possession.

  ```bash
    openssl ca -config subca.conf -in pop.csr -out pop.crt -extensions client_ext

  ```

11. Sélectionnez le nouveau certificat dans l'affichage **Détails du certificat**. Pour trouver le fichier PEM, accédez au dossier certs.

12. Une fois le certificat chargé, sélectionnez **Vérifier**. L'état du certificat d'AC doit passer à **Vérifié**.

## <a name="step-8---create-a-device-in-your-iot-hub"></a>Étape 8 : Créer un appareil dans votre instance d'IoT Hub

Accédez à votre instance d'IoT Hub sur le portail Azure et créez une nouvelle identité d'appareil IoT avec les valeurs suivantes :

1. Fournissez l’**ID d’appareil** qui correspond au nom du sujet de vos certificats d’appareil.

1. Sélectionnez le type d'authentification **X.509 signé par une AC**.

1. Sélectionnez **Enregistrer**.

## <a name="step-9---create-a-client-device-certificate"></a>Étape 9 : Créer un certificat d'appareil client

Pour générer un certificat client, vous devez d'abord générer une clé privée. La commande suivante montre comment utiliser OpenSSL pour créer une clé privée. Créez la clé dans le répertoire subca.

```bash
openssl genpkey -out device.key -algorithm RSA -pkeyopt rsa_keygen_bits:2048
```

Créez une demande de signature de certificat pour la clé. Vous n'êtes pas tenu d'entrer un mot de passe de stimulation ou un nom de société facultatif. Toutefois, vous devez entrer d'ID de l'appareil dans le champ de nom commun. Vous pouvez également entrer vos propres valeurs pour les autres paramètres, comme **Pays**, **Nom de l’organisation**, etc.

```bash
openssl req -new -key device.key -out device.csr

-----
Country Name (2 letter code) [XX]:.
State or Province Name (full name) []:.
Locality Name (eg, city) [Default City]:.
Organization Name (eg, company) [Default Company Ltd]:.
Organizational Unit Name (eg, section) []:
Common Name (eg, your name or your server hostname) []:`<your device ID>`
Email Address []:

Please enter the following 'extra' attributes
to be sent with your certificate request
A challenge password []:
An optional company name []:

```

Vérifiez que la demande de signature de certificat est conforme à vos attentes.

```bash
openssl req -text -in device.csr -noout
```

Envoyez la demande de signature de certificat à l'AC subordonnée pour la signature dans la hiérarchie de certificats. Spécifiez `client_ext` dans le commutateur `-extensions`. Notez que la présence de `Basic Constraints` dans le certificat émis indique qu'il n'est pas destiné à une AC. Si vous signez plusieurs certificats, veillez à mettre à jour le numéro de série avant de générer chaque certificat à l'aide de la commande openssl `rand -hex 16 > db/serial`.

```bash
openssl ca -config subca.conf -in device.csr -out device.crt -extensions client_ext
```

## <a name="next-steps"></a>Étapes suivantes

Accédez à [Test de l’authentification par certificat](tutorial-x509-test-certificate.md) pour déterminer si votre certificat peut authentifier votre appareil auprès de votre hub IoT. Le code dans cette page nécessite l’utilisation d’un certificat PFX. Utilisez la commande OpenSSL suivante pour convertir votre certificat .crt d’appareil au format .pfx.

```bash
openssl pkcs12 -export -in device.crt -inkey device.key -out device.pfx
```