---
title: TP Wazuh

---

# TP Wazuh

## Installation de Wazuh

```
git clone https://github.com/wazuh/wazuh-docker.git -b v4.10.1
```

```
cd wazuh-docker/single-node
```

```
docker-compose -f generate-indexer-certs.yml run --rm generator
```

```
docker-compose up -d
```

> https://localhost:443 pour accéder au tableau de bord de Wazuh


---------------

#### VM Agent

## Installation
```
curl -s https://packages.wazuh.com/key/GPG-KEY-WAZUH | gpg --no-default-keyring --keyring gnupg-ring:/usr/share/keyrings/wazuh.gpg --import && chmod 644 /usr/share/keyrings/wazuh.gpg
```

```
echo "deb [signed-by=/usr/share/keyrings/wazuh.gpg] https://packages.wazuh.com/4.x/apt/ stable main" | tee -a /etc/apt/sources.list.d/wazuh.list
```

```
apt-get update
```

```
WAZUH_MANAGER="IP_VM_MANAGER" apt-get install wazuh-agent
```

```
systemctl daemon-reload
systemctl enable wazuh-agent
systemctl start wazuh-agent
```

## Autorité de certification sur la VM manager

```
openssl req -x509 -new -nodes -newkey rsa:4096 -keyout rootCA.key -out rootCA.pem -batch -subj "/C=US/ST=CA/O=Wazuh"
```

```
[req]
distinguished_name = req_distinguished_name
req_extensions = req_ext
prompt = no
[req_distinguished_name]
C = US
CN = IP_VM_MANAGER
[req_ext]
subjectAltName = @alt_names
[alt_names]
DNS.1 = wazuh
DNS.2 = wazuh.com
```

```
openssl req -new -nodes -newkey rsa:4096 -keyout sslmanager.key -out sslmanager.csr -config req.conf
```

```
openssl x509 -req -days 365 -in sslmanager.csr -CA rootCA.pem -CAkey rootCA.key -out sslmanager.cert -CAcreateserial -extfile req.conf -extensions req_ext
```

```
cp sslmanager.cert sslmanager.key /var/ossec/etc
```

```
systemctl restart wazuh-manager
```

## Application Web Vulnérable

**Transfert de `app.log` vers Wazuh**

On ajoute une directive **localfile** dans /var/ossec/etc/ossec.conf :

```
<localfile>
    <log_format>syslog</log_format>
    <location>/appformation1_students_final/app.log</location>
</localfile>
```

Restart de l'agent :

```
systemctl restart wazuh-agent
```

Création d'un décodeur :

```
<decoder name="XSSApp">
    <program_name>XSSApp</program_name>
</decoder>

<decoder name="XSSApp">
    <parent>XSSApp</parent>
    <regex>(\w+)-(\w+) : (.+)</regex>
    <order>type,action,datas</order>
</decoder>
```

Détecttion de la faille XSS sur l'app.

```xml
<group name="xss"> 
	<rule id="100111" level="10"> 
		<decoded_as>XSSApp</decoded_as> 
		<regex>script\.+script</regex> 
		<description>XSS detected</description>
	</rule>
</group>
```

redémarrage de Wazuh :

```
systemctl restart wazuh-manager
```

Test de la règle.

```
<script>alert(1)</script>
```