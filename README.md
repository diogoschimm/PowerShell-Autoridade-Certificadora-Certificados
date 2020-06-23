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

Criando o Certificado Digital para o Cliente, esse certificado será assinado pelo certificado digital da nossa Autoridade Certificadora, com isso a cadeia de certificação ficará "Diogoschimm-CA" >> "Nosso Certificado Cliente"  
Agora vamos criar outro diretório para separar os certificados de cliente  

```powershell
 mkdir C:\Certs\Clients
```

Vamos digitar o seguinte comando para criar o certificado digital de cliente

```powershell
$mycert = New-SelfSignedCertificate 
  -certstorelocation cert:\CurrentUser\my 
  -dnsname "Diogoschimm-Client" 
  -Signer $rootcert 
  -NotAfter (Get-Date).AddYears(5) 
  -FriendlyName "Diogoschimm-Client"
  
$mypwd = ConvertTo-SecureString 
  -String "1234567890" 
  -Force -AsPlainText
  
Export-PfxCertificate 
  -Cert $mycert 
  -FilePath "C:\Certs\Clients\Diogoschimm-Client.pfx" 
  -Password $mypwd

Export-Certificate 
  -Cert $mycert 
  -FilePath "C:\Certs\Clients\Diogoschimm-Clinet.crt"
```
Se o $rootCert não estiver disponível (caso a janela do PowerShell tenha sido fechada) utilize o seguinte comando para obter o certificado digital.
É necessário trocar {TumPrint} pela Impressão Digital do Certificado Digital da Autoridade Certificadora.

```powershell
$rootcert = ( Get-ChildItem -Path cert:\CurrentUser\My\{TumbPrint} )
```






