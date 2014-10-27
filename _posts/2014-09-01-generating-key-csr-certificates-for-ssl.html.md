---
layout: post
title: Generating Key, CSR and Certificates for SSL
date: 2014-08-26
draft: false
tags: [security,java,ssl,tutorial]
---

If you want to run your application in HTTPS mode, you will need to get SSL certificates from a valid CA (Certificate Authority) like Verisign. 

In this post I will provide steps for generating private key, CSR (Certificate Signing Request) and importing signed certificate back to private key store using java [keytool](http://docs.oracle.com/javase/7/docs/technotes/tools/windows/keytool.html) utility


***1. Generating Private Key and CSR*** 
	
The following shell script can be used to generate Private Key and CSR. You should modify the variables according to your server domain and organization.

```sh
outputDir=out
keytoolCommand=$JAVA_HOME/bin/keytool

function genServerKeyAndCSR {
    CN="My website domain"
    OU="My organization unit"
    C="MY Country"
    ST="My State"
    L="My City"
    O="My Corp"
    passwd="My secure passwd"
    keystoreFile="${outputDir}/server.jks"
    csrFile="${outputDir}/server.csr"

    ${keytoolCommand} -genkeypair -v -alias "server" -validity 1095 -keystore "${keystoreFile}" -dname "CN=$CN, OU=$OU, O=$O, L=$L, S=$ST, C=$C" -keysize 2048 -keyalg "RSA" -storetype jks -storepass "${passwd}" -keypass "${passwd}"

    ${keytoolCommand} -certreq -alias "server" -file "${csrFile}" -keystore "${keystoreFile}" -storepass "${passwd}" -keypass "${passwd}"

    echo "Your keystore file and CSR has been generated. Keystore file is "${keystoreFile}". CSR file is "${csrFile}". Thank you."

    ${keytoolCommand} -list -v -keystore "${keystoreFile}" -storepass "${passwd}" -keypass "${passwd}"
}

genServerKeyAndCSR
```



This will create two files `server.jks` containing private key and `server.csr` containing certificate signing request that can be used to get signed certificates.


	

***2. Requesting Signed Certificates***
 
You can submit generated `server.csr` to request signed certificate from verisign or any CA. Then you will receive email with signed certificates as attachments or with link to download them.
You should also download the CA's root certificates along with your server's certificate in PEM encoded format. For eg: Using Verizon Cybertrust, I got following files

* Baltimore_CyberTrust_Root.PEM
* Cybertrust_Public_SureServer_SV_CA.PEM
* my-server-domain-name.PEM


Note: `my-server-domain-name` is the name of CN you used in previous step.



    
***3. Importing Signed certificates back to Java Keystore*** 

Now you need to import your signed certificate along with CA's root certificate in your private key Java Keystore you created in step 1. Here is sample script for doing this

```sh
outputDir=out
keytoolCommand=$JAVA_HOME/bin/keytool

function importCerts {
    
    serverCertFile="my-server-domain-name.PEM"
    keystoreFile="${outputDir}/server.jks"
    passwd="My secure passwd"
    cat Baltimore_CyberTrust_Root.PEM Cybertrust_Public_SureServer_SV_CA.PEM ${serverCertFile} > server.chained.tmp

    ##Optionaly Verify Chain
    openssl verify -CAfile server.chained.tmp ${serverCertFile}

    ${keytoolCommand} -import -trustcacerts -file server.chained.tmp -alias server -keystore "${keystoreFile}" -storetype jks -storepass "${passwd}" -keypass "${passwd}" -noprompt

    ##Optionaly Verify cert after import
    ${keytoolCommand} -list -v -keystore "${keystoreFile}" -storepass "${passwd}" -keypass "${passwd}"
}

importCerts

```

Above script concats CA's root certificates along with server's signed certificate and imports it back to java key store `server.jks`. Now `server.jks` can be used to configure application server like jetty to run in SSL mode.

    
