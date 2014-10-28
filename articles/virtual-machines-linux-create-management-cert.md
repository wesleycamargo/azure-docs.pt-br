<properties linkid="manage-linux-common-tasks-manage-certs" urlDisplayName="Manage certificates" pageTitle="Manage certificates for Linux virtual machines in Azure" metaKeywords="Azure management certs, uploading management certs, Azure Service Management API" description="Learn how to create and upload management certificates for Linux in Azure. The certificate is required if you use the Service Management API." metaCanonical="" services="virtual-machines" documentationCenter="" title="Create management certificates for Linux in Azure" authors="kathydav" solutions="" manager="jeffreyg" editor="tysonn" />

<tags ms.service="virtual-machines" ms.workload="infrastructure-services" ms.tgt_pltfrm="vm-linux" ms.devlang="na" ms.topic="article" ms.date="01/01/1900" ms.author="kathydav"></tags>

# Criar certificados de gerenciamento para Linux no Azure

Um certificado de gerenciamento é necessário quando você deseja usar a API do Gerenciamento de Serviços para interagir com a plataforma de imagem do Azure.

Já existe documentação sobre como criar e gerenciar esses certificados em [][]<http://msdn.microsoft.com/pt-br/library/azure/gg981929.aspx></a>. Você também pode usar o OpenSSL para criar o certificado de gerenciamento. Para obter mais informações, consulte [OpenSSL][OpenSSL]. No entanto, essa documentação é focalizada principalmente no uso do portal do Silverlight que pode não estar acessível a todos os usuários do Linux. Descreve como você pode obter acesso a esses certificados e integrá-los com nossas diferentes ferramentas e parceiros e usá-los por conta própria até que essa funcionalidade seja adicionada ao Portal de Gerenciamento do Azure.

## Sumário

-   [Obter um certificado de gerenciamento do arquivo publishsettings][Obter um certificado de gerenciamento do arquivo publishsettings]
-   [Instalar um certificado de gerenciamento usando o Portal de Gerenciamento do Azure][Instalar um certificado de gerenciamento usando o Portal de Gerenciamento do Azure]

## <span id="publishsettings"></span></a>Como: Criar e carregar um certificado de gerenciamento

Elaboramos uma maneira fácil de você criar um certificado de gerenciamento para o Azure visitando: [][1]<https://windows.azure.com/download/publishprofile.aspx></a>

Esse site solicitará que você faça logon usando suas credenciais de portal e gerar um certificado de gerenciamento empacotado com sua subscriptionID em um arquivo publishsettings que deverá baixar.

![linuxcredentials][linuxcredentials]

Certifique-se de salvar esse arquivo em local seguro, pois você não poderá recuperá-lo e será necessário gerar um novo certificado de gerenciamento. (Há um limite para o número total de certificados que você pode usar no sistema. Consulte a seção apropriada desse site para confirmar isso.) Você pode usar esse certificado de várias maneiras:

### No Visual Studio

![VSpublish][VSpublish]

### Na linha de comando do Linux Azure

Você pode importar o certificado para que possa usá-lo executando o comando de importação de conta do Azure:

![cmdlinepublish][cmdlinepublish]

Em qualquer outro parceiro ou software onde precise da ferramenta, você precisará extrair o certificado de gerenciamento a partir do próprio arquivo e decodificá-lo em Base 64. Alguns parceiros, como ScaleXtreme e SUSE Studio consumirão o arquivo diretamente em sua forma atual.

Para extrair o certificado de gerenciamento, você precisa seguir este procedimento.

Você precisará extrair desse arquivo o conteúdo codificado em base 64 entre as aspas depois de ManagementCertificate.

    ?xml version="1.0" encoding="utf-8"?>
    <PublishData>
      <PublishProfile
        PublishMethod="AzureServiceManagementAPI"
        Url="https://management.core.windows.net/"
        ManagementCertificate="xxxxx">
        <Subscription
          Id="8a4a0a51-728e-482e-8daa-c477f03c541d"
          Name="hjereztest" />
      </PublishProfile>
    </PublishData>

Você precisará copiá-lo para um arquivo (por exemplo, encodedCert.txt) e decodificá-lo usando um decodificador base64:

    base64 -d [encodedCert.txt] > [decodedCert.pfx]

Isso fornecerá um arquivo pfx que você pode converter em outros formatos usando openssl para extrair a chave privada, se necessário:

    openssl pkcs12 -in [decodedCert.pfx] -out [cert.pem] -nodes

No Windows, você pode decodificar e extrair o arquivo PFX usando o PowerShell ou um decodificador base 64 gratuito do Windows, como [http://www.fourmilab.ch/webtools/base64/base64.zip](http://www.fourmilab.ch/webtools/base64/base64.zip) executando o comando

    base64 -d key.txt ->key.pfx

#### Limite de geração de certificado

Existe um limite de até 10 certificados que é possível criar na plataforma com essa ferramenta.
Infelizmente, não existe uma maneira de recuperar as chaves que você gerou depois de terem sido geradas, porque essas chaves privadas não são salvas em nenhum lugar no sistema.
Se atingir o limite de certificados do sistema, você precisará entrar em contato com o suporte por meio dos fóruns do Azure para apagar seus certificados ou usar um navegador que possa renderizar o antigo portal do Silverlight para executar essas tarefas.

#### Se sua chave privada estiver comprometida

Se a chave privada for comprometida a qualquer momento, você precisará usar um navegador que compatível com o Silverlight para acessar o antigo portal e excluir os certificados de gerenciamento correspondentes no arquivo ou entrar em contato com o suporte por meio dos fóruns.

A geração de novos certificados não basta, porque todos os 10 certificados são válidos e a chave comprometida anterior continuará sendo capaz de acessar o site.

## <span id="management"></span></a>Instalar um certificado de gerenciamento usando o Portal de Gerenciamento do Azure

É possível criar um certificado de gerenciamento de várias maneiras. Para obter mais informações sobre como criar certificados, consulte [Criar um certificado de gerenciamento para o Azure (a página pode estar em inglês)][Criar um certificado de gerenciamento para o Azure (a página pode estar em inglês)]. Depois de criar o certificado, adicione-o à sua assinatura no Azure.

1.  Entre no Portal de Gerenciamento do Azure.

2.  No painel de navegação, clique em **Configurações**.

3.  Em **Certificados de gerenciamento**, clique em **Atualizar um certificado de gerenciamento**.

4.  Em **Carregar um certificado de gerenciamento**, procure o arquivo do certificado e clique em **OK**.

### Obter a impressão digital do certificado e a ID da assinatura

Você precisa da impressão digital do certificado de gerenciamento que você adicionou e da ID da assinatura para poder carregar o arquivo .vhd no Azure.

1.  No Portal de Gerenciamento, clique em **Configurações**.

2.  Em **Certificados de Gerenciamento**, clique no certificado e registre a impressão digital no painel **Propriedades**, copiando e colando-a em um local onde seja possível recuperá-la depois.

Você também precisa da ID de sua assinatura para carregar o arquivo .vhd.

1.  No Portal de Gerenciamento, clique em **Todos os Itens**.

2.  No painel central, em **Assinatura**, copie e cole a assinatura em um local onde seja possível recuperá-la depois.

### Fornecendo essas informações a ferramentas se você tiver gerado sua própria chave

#### Para CSUPLOAD

1.  Abra uma janela do prompt de comando do SDK do Azure como administrador.
2.  Defina a cadeia de conexão usando o seguinte comando e substituindo **Subscriptionid** e **CertThumbprint** pelos valores obtidos anteriormente:

        csupload Set-Connection "SubscriptionID=<Subscriptionid>;CertificateThumbprint=<Thumbprint>;ServiceManagementEndpoint=https://management.core.windows.net"

#### Para ferramentas de linha de comando do Linux Azure

Você precisará codificar em base 64 o arquivo PFX criado usando openssl com o comando:

        Base64 [file] > [econded file]

Em seguida, será necessário mesclar sua ID de assinatura e o pfx codificado em base64 em um único arquivo que tenha a estrutura:

        ?xml version="1.0" encoding="utf-8"?>
        <PublishData>
          <PublishProfile
            PublishMethod="AzureServiceManagementAPI"
            Url="https://management.core.windows.net/"
            ManagementCertificate="xxxxx">
            <Subscription
              Id="8a4a0a51-728e-482e-8daa-c477f03c541d"
              Name="hjereztest" />
          </PublishProfile>
        </PublishData>
        

Em que xxxxx é o conteúdo do [arquivo codificado] que você usará para dar os detalhes para as ferramentas de linha de comando do Linux Azure com os comandos:
importação de conta do Azure (Arquivo)

  []: http://msdn.microsoft.com/pt-br/library/azure/gg981929.aspx
  [OpenSSL]: http://openssl.org/
  [Obter um certificado de gerenciamento do arquivo publishsettings]: #createcert
  [Instalar um certificado de gerenciamento usando o Portal de Gerenciamento do Azure]: #management
  [1]: https://windows.azure.com/download/publishprofile.aspx
  [linuxcredentials]: ./media/virtual-machines-linux-create-management-cert/linuxcredentials.png
  [VSpublish]: ./media/virtual-machines-linux-create-management-cert/VSpublish.png
  [cmdlinepublish]: ./media/virtual-machines-linux-create-management-cert/cmdlinepublish.png
  [Criar um certificado de gerenciamento para o Azure (a página pode estar em inglês)]: http://msdn.microsoft.com/library/azure/gg551722.aspx
