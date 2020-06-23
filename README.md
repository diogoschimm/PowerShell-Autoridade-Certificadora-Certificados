# Certificados Digitais

Explicação de comandos e conceitos para criação de certificados digitais para teste em aplicações asp.net.

## Conceitos:

**Certificado de Autoridade Certificadora**:

**Certificado Digital**:

**Cadeia de Confiança**:

**Repositórios de Certificados**:


## Comandos

Vamos abrir o PowerShell e digitar os seguintes comandos 

Para criar uma pasta na unidade C chamada Certs com o seguinte comando.  
Será o local onde iremos colocar os certificados digitais

```powershell
mkdir C:\Certs
```

Criando o Certificado Digital para a Autoridade Certificadora

```powershell

$rootcert = New-SelfSignedCertificate 
  -CertStoreLocation Cert:\CurrentUser\My 
  -DnsName "Diogoschimm-CA" 
  -TextExtension @("2.5.29.19={text}CA=true") 
  -NotAfter (Get-Date).AddYears(10) 
  -KeyUsage CertSign,CrlSign,DigitalSignature

$rootcertPassword = ConvertTo-SecureString 
  -String "1234567890" 
  -Force -AsPlainText

Export-PfxCertificate 
  -Cert $rootcert 
  -FilePath 'C:\Certs\Diogoschimm-CA.pfx' 
  -Password $rootcertPassword
  
Export-Certificate 
  -Cert $rootcert 
  -FilePath 'C:\Certs\Diogoschimm-CA.crt'

```


