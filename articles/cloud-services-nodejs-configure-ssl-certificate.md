<properties 
	pageTitle="Configurar SSL para um serviço de nuvem (Node.js) - Azure" 
	description="Saiba como especificar um ponto de extremidade HTTPS para uma função Web do Node.js e como carregar um certificado SSL para proteger seu aplicativo." 
	services="cloud-services" 
	documentationCenter="nodejs" 
	authors="" 
	manager="wpickett" 
	editor=""/>

<tags 
	ms.service="cloud-services" 
	ms.workload="tbd" 
	ms.tgt_pltfrm="na" 
	ms.devlang="nodejs" 
	ms.topic="article" 
	ms.date="09/17/2014" 
	ms.author="wpickett"/>




# Configurando o SSL para um aplicativo Node.js em uma função Web do Azure

A criptografia Secure Socket Layer (SSL) é o método mais comumente usado
para proteger dados enviados pela Internet. Esta tarefa comum aborda como
especificar um ponto de extremidade HTTPS para um aplicativo do Node.js hospedado como um serviço de nuvem do Azure em uma função da Web e como carregar um
certificado SSL para proteger seu aplicativo.

> [AZURE.NOTE] As etapas deste artigo se aplicam somente aos aplicativos de nó hospedados como um Serviço de Nuvem do Azure em uma função web. Para Sites da Web, consulte [Configurando um certificado SSL para um site do Azure](../web-sites-configure-ssl-certificate/).

Esta tarefa inclui as seguintes etapas:

-   [Etapa 1: Criar um serviço do Node.js e publicar o serviço para a nuvem]
-   [Etapa 2: Obter um certificado SSL]
-   [Etapa 3: Importar o certificado SSL]
-   [Etapa 4: Modificar a definição do serviço e os arquivos de configuração]
-   [Etapa 5: Conectar-se à Instância da Função usando HTTPS]

## <a name="step1"> </a>Etapa 1: Criar um serviço do Node.js e publicar o serviço para a nuvem

Quando um aplicativo do Node.js é implantado em uma função Web do Azure, o
certificado do servidor e a conexão SSL são gerenciados pelo Internet
Information Services (IIS), para que o serviço do Node.js possa ser escrito
como se fosse um serviço http. Você pode criar um serviço Node.js simples 'hello
world'usando o PowerShell do Azure usando estas etapas:

1. No **Menu Iniciar** ou na **Tela Iniciar**, pesquise por **PowerShell do Azure**. Finalmente, clique com o botão direito do mouse no **PowerShell do Azure** e selecione **Executar como Administrador**.

	![Azure PowerShell icon][powershell-menu]

[AZURE.INCLUDE [install-dev-tools](../includes/install-dev-tools.md)]


2.  Crie um novo projeto de serviço usando o cmdlet **New-AzureServiceProject**. 

	![][1]

3.  Adicione uma função web a seu serviço usando o cmdlet **Add-AzureNodeWebRole**:

    ![][2]

4.  Publicar seu serviço na nuvem usando o cmdlet **Publicar no AzureServiceProject**:

    ![][3]

	> [AZURE.NOTE] Se não tiver importado anteriormente as configurações de publicação para sua assinatura do Azure, você receberá um erro ao tentar publicar. Para obter informações sobre como baixar e importar as configurações de publicação para sua assinatura, consulte [Como usar o PowerShell do Azure para o Node.js (a página pode estar em inglês)](https://www.windowsazure.com/pt-br/develop/nodejs/how-to-guides/powershell-cmdlets/#ImportPubSettings)

O valor da **URL do Site Criada** retornado pelo cmdlet **Publish-AzureServiceProject** contém o nome de domínio totalmente qualificado para o seu aplicativo hospedado. Você precisará obter um certificado SSL para esse nome de domínio específico totalmente qualificado e implantá-lo no Azure.

## <a name="step2"> </a>Etapa 2: Obter um certificado SSL

Para configurar o SSL para um aplicativo, primeiro você precisa obter um certificado SSL
que foi assinado por uma autoridade de certificação (CA), um
terceiro confiável que emite certificados para essa finalidade. Se você
ainda não tiver um, você precisará obtê-lo de uma empresa que
venda certificados SSL.

O certificado deve atender aos seguintes requisitos para certificados SSL
no Azure:

-   O certificado deve conter uma chave privada.
-   O certificado deve ser criado para troca de chaves (arquivo .pfx).
-   O nome da entidade do certificado deve corresponder ao domínio usado para acessar
    o serviço de nuvem. Não é possível adquirir um certificado SSL para o
    domínio cloudapp.net, então o nome da entidade do certificado deve corresponder
    ao nome de domínio personalizado usado para acessar seu aplicativo. Por exemplo, __mysecuresite.cloudapp.net__.
-   O certificado deve usar, no mínimo, uma criptografia de 2048 bits.

## <a name="step3"> </a>Etapa 3: Importar o certificado SSL

Depois que você tiver um certificado, instale-o no armazenamento de certificados no seu computador de desenvolvimento. Esse certificado será recuperado e carregado no Azure como parte do seu pacote de implantação do aplicativo com base nas alterações na configuração feitas em uma etapa posterior.

> [AZURE.NOTE] As etapas usadas nesta seção são baseadas na versão do Windows 8 do Assistente para Importação de Certificados. Se você estiver usando uma versão anterior do Windows, as etapas listadas aqui não poderão corresponder à ordem exibida no assistente. Nesse caso, leia totalmente esta seção antes de usar o Assistente para Importação de Certificados para compreender quais ações gerais devem ser executadas.

Para importar o certificado SSL, execute as seguintes etapas:

1.   Usando o Windows Explorer, navegue até o diretório onde o arquivo **.pfx** que contém o certificado está localizado e clique duas vezes no certificado. Isso exibirá o Assistente para Importação de Certificados.
	
	![certificate wizard][cert-wizard]

2.   Na seção **Local de Armazenamento**, selecione **Usuário Atual** e clique em **Avançar**. Isso instalará o certificado no armazenamento de certificados de sua conta de usuário.

3.   Continue com o assistente aceitando os padrões, até chegar na tela **Proteção de Chave Privada**. Aqui, você deve digitar a senha (se houver) do certificado. Você também deve selecionar **Marcar esta chave como exportável**. Finalmente, clique em **Próximo**.

	![private key protection][key-protection]

4. Prossiga com o assistente, aceitando os padrões, até que o certificado tenha sido instalado com êxito.

Agora você deve modificar a definição do serviço para fazer referência ao certificado que você
instalou.

## <a name="step4"> </a>Etapa 4: Modificar a definição do serviço e os arquivos de configuração

O aplicativo deve ser configurado para referenciar o certificado e um ponto de extremidade do HTTPS
deve ser adicionado. Como resultado, os arquivos de definição de serviço e de configuração do serviço
precisam ser atualizados.

1.  No diretório de serviço, abra o arquivo de definição de serviço
    (ServiceDefinition.csdef), adicione uma seção de **Certificados** dentro da seção **WebRole** e inclua as seguintes informações sobre o
    certificado:

        <WebRole name="WebRole1" vmsize="ExtraSmall">
        ...
            <Certificates>
                <Certificate name="SampleCertificate" 
                    storeLocation="LocalMachine" storeName="My" />
            </Certificates>
        ...
        </WebRole>

    A seção **Certificados** define o nome do certificado,
    seu local e o nome do repositório onde ele está localizado. Como instalamos o certificado no armazenamento de certificados do usuário, é usado um valor de "My". Outros locais de armazenamento de certificado também podem ser usados. Consulte [Como
    Associar um certificado com um serviço] para obter mais informações.

2.  No arquivo de definição do serviço, atualize o elemento **InputEndpoint** http dentro da seção **Pontos de Extremidade** para habilitar HTTPS:

        <WebRole name="WebRole1" vmsize="Small">
        ...
            <Endpoints>
                <InputEndpoint name="Endpoint1" protocol="https" 
                    port="443" certificate="SampleCertificate" />
            </Endpoints>
        ...
        </WebRole>

    Todas as alterações obrigatórias no arquivo de definição do serviço foram
    concluídas, mas você continua precisando adicionar as informações de certificado ao
    arquivo de configuração do serviço.

3.  Nos arquivos de configuração de serviço
    (**ServiceConfiguration.Cloud.cscfg** e
    **ServiceConfiguration.Local.cscfg**), adicione o certificado para seção
    vazia dos **Certificados** na seção de **Função**,
    substituindo o valor de impressão digital de exemplo abaixo com aquele do seu
    certificado:

        <Role name="WebRole1">
        ...
            <Certificates>
                <Certificate name="SampleCertificate" 
                    thumbprint="9427befa18ec6865a9ebdc79d4c38de50e6316ff" 
                    thumbprintAlgorithm="sha1" />
            </Certificates>
        ...
        </Role>

4.  Atualize a configuração do serviço na nuvem, publicando
    o serviço novamente. No prompt do PowerShell do Azure
    digite **Publish-AzureServiceProject** do diretório de serviço.

	Como parte do processo de publicação, o certificado referenciado será copiado do armazenamento de certificado local e incluído no pacote de implantação.

## <a name="step5"> </a>Etapa 5: Conectar-se à Instância da Função usando HTTPS

Agora que sua implantação está em funcionamento no Azure, você pode
conectar-se a ela usando HTTPS.

1.  No Portal de Gerenciamento, selecione seu serviço de nuvem, e clique em **Painel**.

2. Role para baixo e clique no link exibido como a **URL do Site**:

    ![the site url][site-url]

	> [AZURE.IMPORTANT]Se a URL do Site exibida no portal não especificar HTTPS, você deverá inserir manualmente a URL no navegador usando HTTPS em vez de HTTP.

3.  Um novo navegador será aberto e exibirá o seu site.

    O navegador exibirá um ícone de cadeado para indicar que ele está
    usando uma conexão HTTPS. Isso também indica que seu aplicativo
    foi configurado corretamente para SSL.

    ![][8]

## Recursos adicionais

[Como associar um certificado a um serviço]

[Configurando o SSL para um aplicativo Node.js em uma função de trabalho do Azure]

[Como configurar um certificado SSL em um ponto de extremidade HTTPS]

  [Etapa 1: Criar um serviço do Node.js e publicar o serviço para a nuvem]: #step1
  [Etapa 2: Obter um certificado SSL]: #step2
  [Etapa 3: Importar o certificado SSL]: #step3
  [Etapa 4: Modificar a definição do serviço e os arquivos de configuração]: #step4
  [Etapa 5: Conectar-se à Instância da Função usando HTTPS]: #step5
  [**PowerShell do Azure**]: http://go.microsoft.com/?linkid=9790229&clcid=0x409
  
  
  
  
  [1]: ./media/cloud-services-nodejs-configure-ssl-certificate/enable-ssl-01.png
  [2]: ./media/cloud-services-nodejs-configure-ssl-certificate/enable-ssl-02.png
  [3]: ./media/cloud-services-nodejs-configure-ssl-certificate/enable-ssl-03.png
  [Portal de Gerenciamento do Azure]: http://manage.windowsazure.com
  
  
  [Como associar um certificado a um serviço]: http://msdn.microsoft.com/library/windowsazure/gg465718.aspx
  [Como Associar um certificado com um serviço]: http://msdn.microsoft.com/library/windowsazure/gg465718.aspx
  [site-url]: ./media/cloud-services-nodejs-configure-ssl-certificate/site-url.png
  [8]: ./media/cloud-services-nodejs-configure-ssl-certificate/enable-ssl-08.png
  [Como configurar um certificado SSL em um ponto de extremidade HTTPS]: http://msdn.microsoft.com/library/windowsazure/ff795779.aspx
  [powershell-menu]: ./media/cloud-services-nodejs-configure-ssl-certificate/azure-powershell-start.png
  [cert-wizard]: ./media/cloud-services-nodejs-configure-ssl-certificate/certificateimport.png
  [key-protection]: ./media/cloud-services-nodejs-configure-ssl-certificate/exportable.png
  [Configurando o SSL para um aplicativo Node.js em uma função de trabalho do Azure]: /pt-br/develop/nodejs/common-tasks/enable-ssl-worker-role/

<!--HONumber=45--> 
