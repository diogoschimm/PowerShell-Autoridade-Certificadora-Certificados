# Certificados Digitais

Explicação de comandos e conceitos para criação de certificados digitais para teste em aplicações asp.net.

## Conceitos:

**Certificado de Autoridade Certificadora**:
O Certificado Digital da Autoridade Certificadora é um arquivo contendo a Chave Publica e Chave Privada do Certificado que está do Topo da Cadeia de Certificação, ele deve estar instalado tanto no Servidor quanto no computador do Cliente, ele que vai garantir a cadeia de confiança entre a máquina Server e a máquina Client.
Não é necessário conter a chave privada na máquina de cliente e nem na máquina do servidor, a chave privada só é utilizada no momento da assinatura de outros certificados (então deve estar disponível somente na máquina que criar certificados filhos da autoridade certificadora).

**Certificado Digital**:
Arquivo contendo duas chaves criptograficas (chave publica e chave privada) é utilizado para garantir Autenticidade de Individuos ou Computadores.

![image](https://user-images.githubusercontent.com/30643035/85464343-84152200-b575-11ea-9470-8a282487f3d7.png)

**Cadeia de Confiança**:
Os sistemas operacionais utilizam o conceito de cadeia de confiança (caminho da certificação) para determinar se os certificados de cliente são válidos ou não, para isso o certificado digital da autoridade certificadora deve estar instalado no windows, se ele estiver instalado no sistema operacional todos os certificados digitais que estão abaixo daquela autoridade certificadora serão certificados válidos.

![image](https://user-images.githubusercontent.com/30643035/85464708-f38b1180-b575-11ea-91a0-f31ca2f46b52.png)

**Repositórios de Certificados**:
Windows possui diretórios especiais para armazenar os certificados digitais instalados, eles não são acessados pelo Windows Explorer conforme imagem abaixo.

![image](https://user-images.githubusercontent.com/30643035/85464199-5af49180-b575-11ea-9b4e-6c8b797d319d.png)

Para visualizar os certificados digitais instalados em seu computador digite na busca do windows ("Gerenciar Certificados de computador" e "Gerenciar Certificados de usuário")

![image](https://user-images.githubusercontent.com/30643035/85465407-c2f7a780-b576-11ea-8872-a366f2978784.png)


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

Se o $rootCert não estiver disponível (caso a janela do PowerShell tenha sido fechada) utilize o seguinte comando para obter o certificado digital.
É necessário trocar {TumbPrint} pela Impressão Digital do Certificado Digital da Autoridade Certificadora.

```powershell
$rootcert = ( Get-ChildItem -Path cert:\CurrentUser\My\{TumbPrint} )
```

A Impressão Digital pode ser obtida no seguinte caminho

![image](https://user-images.githubusercontent.com/30643035/85466488-09013b00-b578-11ea-9601-01e06ce1d88a.png)

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
  -FilePath "C:\Certs\Clients\Diogoschimm-Client.crt"
```

## Instalando os certificados digitais 

O Certificado Digital da Autoridade Certificadora deve ser instalado tanto na máquina Cliente quando no Servidor, em ambos os casos não é necessário instalar com a Chave Privada, pois ele não será utilizando para funções criptograficas, ele será utilizado somente na cadeia de confiança entre Cleinte Servidor.

O repositório para instalar a Autoridade Certificadora é (Autoridades de Certificação Raiz Confiáveis)

![image](https://user-images.githubusercontent.com/30643035/85466106-8f694d00-b577-11ea-91cd-44f0ec300783.png)


O Certificado Digital do Cliente deve ser instalado somente no computador do Cliente

![image](https://user-images.githubusercontent.com/30643035/85476183-b62e8000-b585-11ea-9674-04aa0c86d7f3.png)




