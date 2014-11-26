<properties linkid="manage-linux-common-tasks-manage-certs" urlDisplayName="Gerenciar certificados" pageTitle="Gerenciar certificados para máquinas virtuais Linux no Azure" metaKeywords="certificados de gerenciamento do Azure, carregando certificados de gerenciamento, API de gerenciamento de serviços do Azure" description="Saiba como criar e carregar certificados de gerenciamento para Linux no Azure. O certificado será necessário se você usar a API do Gerenciamento de Serviços." metaCanonical="" services="virtual-machines" documentationCenter="" title="Criar certificados de gerenciamento para Linux no Azure" authors="kathydav" solutions="" manager="jeffreyg" editor="tysonn" />





#Criar certificados de gerenciamento para Linux no Azure

Um certificado de gerenciamento é necessário quando você deseja usar a API do Gerenciamento de Serviços para interagir com a plataforma de imagem do Azure. 

Já existe documentação sobre como criar e gerenciar esses certificados em [http://msdn.microsoft.com/pt-br/library/windowsazure/gg551721.aspx](http://msdn.microsoft.com/pt-br/library/windowsazure/gg551721.aspx). Você também pode usar o OpenSSL para criar o certificado de gerenciamento.  Para obter mais informações, consulte [OpenSSL](http://openssl.org/). No entanto, essa documentação é focalizada principalmente no uso do portal do Silverlight que pode não estar acessível a todos os usuários do Linux. Descreve como você pode obter acesso a esses certificados e integrá-los com nossas diferentes ferramentas e parceiros e usá-los por conta própria até que essa funcionalidade seja adicionada ao Portal de Gerenciamento do Azure. 


##Sumário##

* [Obter um certificado de gerenciamento do arquivo publishsettings](#createcert)
* [Instalar um certificado de gerenciamento usando o Portal de Gerenciamento do Azure](#management)

<h2><a id="publishsettings"></a>Como criar e carregar um certificado de gerenciamento</h2>


Criamos uma maneira fácil de você criar um certificado de gerenciamento para o Azure visitando: [https://windows.azure.com/download/publishprofile.aspx](https://windows.azure.com/download/publishprofile.aspx)

Esse site solicitará que você efetue logon usando suas credenciais do portal e, em seguida, gerará um certificado de gerenciamento que é empacotado junto com seu subscriptionID em um arquivo publishsettings do qual você deverá fazer download. 

![linuxcredentials](./media/linux-create-management-cert/linuxcredentials.png)

Certifique-se de salvar esse arquivo em local seguro, pois você não poderá recuperá-lo e será necessário gerar um novo certificado de gerenciamento. (Há um limite para o número total de certificados que você pode usar no sistema. Consulte a seção apropriada nesse site para confirmar isso.) Você pode usar esse certificado de várias maneiras:

###No Visual Studio###

![VSpublish](./media/linux-create-management-cert/VSpublish.png)


###Na linha de comando do Linux Azure###

Você pode importar o certificado para que possa usá-lo executando o comando de importação de conta do Azure:

![cmdlinepublish](./media/linux-create-management-cert/cmdlinepublish.png)

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
	
Você precisa copiar isso em um arquivo e, em seguida, decodificá-lo usando um decodificador de base 64, no Linux, você pode usar:

	Base64 -d [encodedfile] > [decodedfile].pfx

Isso fornecerá um arquivo pfx que você pode converter em outros formatos usando openssl para extrair a chave privada, se necessário:

 	openssl.exe pkcs12 -in publicAndprivate.pfx -nocerts -out privateKey.pem 

No Windows, você pode decodificar e extrair o arquivo PFX usando o PowerShell ou um decodificador de base 64 gratuito do Windows, como [http://www.fourmilab.ch/webtools/base64/base64.zip](http://www.fourmilab.ch/webtools/base64/base64.zip) executando o comando 

	base64 -d key.txt ->key.pfx

####Limite de geração de certificado####

Há um limite de até 10 certificados que podem ser criados na plataforma com essa ferramenta.
Infelizmente, não existe uma maneira de recuperar as chaves que você gerou depois de terem sido geradas, uma vez que essas chaves privadas não são salvas em nenhum lugar no sistema.
Se você atingir o limite de certificados do sistema, será necessário entrar em contato com o suporte por meio dos fóruns do Azure para ter seus certificados apagados ou usar um navegador que possa renderizar o antigo portal do Silverlight para executar essas tarefas.

####Se sua chave privada estiver comprometida ####

Se sua chave privada for comprometida a qualquer momento, você precisará usar um navegador que ofereça suporte ao Silverlight para acessar o antigo portal e excluir os certificados de gerenciamento correspondentes no arquivo ou entrar em contato com o suporte por meio dos fóruns.  
A geração de novos certificados não é suficiente uma vez que todos os 10 certificados são válidos e sua chave antiga comprometida ainda poderá acessar o site.

<h2><a id="management"></a>Instalar um certificado de gerenciamento usando o Portal de Gerenciamento do Azure</h2>

É possível criar um certificado de gerenciamento de várias maneiras.  Para obter mais informações sobre como criar certificados, consulte [Criar um certificado de gerenciamento para o Azure (a página pode estar em inglês)](http://msdn.microsoft.com/pt-br/library/windowsazure/gg551722.aspx).  Depois de criar o certificado, adicione-o à sua assinatura no Azure. 

1. Entre no Portal de Gerenciamento do Azure.

2. No painel de navegação, clique em **Configurações**.

3. Em **Certificados de Gerenciamento**, clique em **Carregar um certificado de gerenciamento**.

4. Em **Carregar um certificado de gerenciamento**, navegue até o arquivo de certificado e, em seguida, clique em **OK**.

### Obter a impressão digital do certificado e a ID da assinatura ###

Você precisa da impressão digital do certificado de gerenciamento que você adicionou e da ID da assinatura para poder carregar o arquivo .vhd no Azure.

1. No Portal de Gerenciamento, clique em **Configurações**.

2. Em **Certificados de Gerenciamento**, clique no certificado e, em seguida, registre a impressão digital no painel **Propriedades** copiando e colando-a em um local onde seja possível recuperá-la posteriormente.

Você também precisa da ID de sua assinatura para carregar o arquivo .vhd.

1. No Portal de Gerenciamento, clique em **Todos os Itens**.

2. No painel central, em **Assinatura**, copie e cole a assinatura em um local onde seja possível recuperá-la posteriormente.

###Fornecendo essas informações a ferramentas se você tiver gerado sua própria chave###

####Para CSUPLOAD

1.	Abra uma janela do prompt de comando do SDK do Azure como administrador.
2.	Defina a cadeia de conexão usando o seguinte comando e substituindo **Subscriptionid** e **CertThumbprint** pelos valores obtidos anteriormente:


		csupload Set-Connection "SubscriptionID=<Subscriptionid>;CertificateThumbprint=<Thumbprint>;ServiceManagementEndpoint=https://management.core.windows.net"

####Para ferramentas de linha de comando do Linux Azure

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
		
Onde xxxxx é o conteúdo do [arquivo codificado] que você usará para fornecer os detalhes para as ferramentas de linha de comando do Linux Azure com os comandos:
Importação de conta do Azure (arquivo)

