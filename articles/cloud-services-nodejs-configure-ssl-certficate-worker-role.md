<properties linkid="dev-nodejs-enablesslworker" urlDisplayName="Enable SSL worker role" pageTitle="Configure SSL for a cloud service (Node.js) worker role" metaKeywords="Node.js Azure SSL, Node.js Azure, SSL worker role" description="" metaCanonical="" services="cloud-services" documentationCenter="nodejs" title="Configuring SSL for a Node.js Application in an Azure Worker Role" authors="larryfr" solutions="" manager="" editor="" />

<tags ms.service="cloud-services" ms.workload="tbd" ms.tgt_pltfrm="na" ms.devlang="nodejs" ms.topic="article" ms.date="09/17/2014" ms.author="larryfr" />

# Configurando o SSL para um aplicativo Node.js em uma função de trabalho do Azure

A criptografia SSL (Secure Socket Layer) é o método mais usado para
proteger dados enviados pela Internet. Essa tarefa comum aborda como
especificar um ponto de extremidade HTTPS para um aplicativo Node.js hospedado como uma Serviço de Nuvem do Azure em uma função de trabalho.

<div class="dev-callout">
    <b>Observa&ccedil;&atilde;o</b>
    <p>As etapas deste artigo se aplicam somente a aplicativos de n&oacute; hospedados como um Servi&ccedil;o de Nuvem do Azure em uma fun&ccedil;&atilde;o de trabalho.</p>
    </div>

Esta tarefa inclui as seguintes etapas:

-   [Etapa 1: Criar um serviço do Node.js e publicar o serviço para a nuvem][Etapa 1: Criar um serviço do Node.js e publicar o serviço para a nuvem]
-   [Etapa 2: Obter um certificado SSL][Etapa 2: Obter um certificado SSL]
-   [Etapa 3: Modificar o aplicativo para usar o certificado SSL][Etapa 3: Modificar o aplicativo para usar o certificado SSL]
-   [Etapa 4: Modificar o arquivo de definição do serviço][Etapa 4: Modificar o arquivo de definição do serviço]
-   [Etapa 5: Conectar-se à Instância da Função usando HTTPS][Etapa 5: Conectar-se à Instância da Função usando HTTPS]

## <a name="step1"> </a>Etapa 1: Criar um serviço do Node.js e publicar o serviço para a nuvem

Você pode criar um serviço simples do Node.js 'hello
world' usando o PowerShell Azure utilizando estas etapas:

1.  No **Menu Iniciar** ou **Tela Iniciar**, pesquise por **PowerShell do Azure**. Finalmente, clique com o botão direito do mouse no **PowerShell do Azure** e selecione **Executar como Administrador**.

    ![Ícone PowerShell do Azure][Ícone PowerShell do Azure]

2.  Criar um novo serviço, usando o cmdlet **Novo AzureServiceProject**.

    ![][0]

3.  Adicionar uma função de trabalho ao seu serviço usando o cmdlet **Adicionar no AzureNodeWorkerRole**:

    ![][1]

4.  Publicar seu serviço na nuvem usando o cmdlet **Publicar no AzureServiceProject**:

    ![][2]

    <div class="dev-callout">
<strong>Observa&ccedil;&atilde;o</strong>
<p>Se n&atilde;o tiver importado anteriormente as configura&ccedil;&otilde;es de publica&ccedil;&atilde;o para sua assinatura do Azure, voc&ecirc; receber&aacute; um erro ao tentar publicar. Para obter informa&ccedil;&otilde;es sobre como baixar e importar as configura&ccedil;&otilde;es de publica&ccedil;&atilde;o para sua assinatura, consulte <a href="https://www.windowsazure.com/pt-BR/develop/nodejs/how-to-guides/powershell-cmdlets/#ImportPubSettings">Como usar o PowerShell do Azure para o Node.js (a p&aacute;gina pode estar em ingl&ecirc;s)</a></p>
</div>

O valor da **URL do Site Criada** retornado pelo cmdlet **Publish-AzureServiceProject** contém o nome de domínio totalmente qualificado para o seu aplicativo hospedado. Você precisará obter um certificado SSL para esse nome de domínio específico totalmente qualificado e implantá-lo no Azure.

## <a name="step2"> </a>Etapa 2: Obter um certificado SSL

Para configurar SSL de um aplicativo, você deve primeiro obter um
certificado SSL assinado por uma Autoridade de Certificação (CA), um
terceiro confiável que emita certificados com essa finalidade. Se ainda
não tiver um, precisará obter junto a uma empresa que
venda certificados SSL.

O certificado deve atender aos seguintes requisitos para
certificados SSL no Azure:

-   O certificado deve conter uma chave privada.
-   O certificado deve ser criado para troca de chaves (arquivo **.pfx**).
-   O nome de assunto do certificado deve corresponder ao domínio usado para acessar o
    serviço de nuvem. Você não pode adquirir um certificado SSL para o domínio
    cloudapp.net, portanto, o nome de assunto do certificado deve corresponder ao
    nome do domínio personalizado usado para acessar o aplicativo. Por exemplo, **mysecuresite.cloudapp.net**.
-   O certificado deve usar, no mínimo, uma criptografia de 2048 bits.

O arquivo **.pfx** contendo o certificado será adicionado ao seu projeto de serviço e implantado no Azure nas etapas a seguir.

## <a name="step3"> </a>Etapa 3: Modificar o aplicativo para usar o certificado SSL

Quando um aplicativo do Node.js é implantado em uma função de trabalho, o certificado do servidor e a conexão SSL são gerenciados pelo Node.exe. Para tratar o tráfego do SSL, você deve usar o módulo 'https' em vez de 'http'. Execute as seguintes etapas para adicionar o certificado SSL ao seu projeto e, em seguida, modificar o aplicativo para usar o certificado.

1.  Salve o arquivo **.pfx** fornecido por sua CA (Autoridade de Certificação) no diretório que contém o seu aplicativo. Por exemplo, **c:\\node\\securesite\\workerrole1** é o diretório que contém o aplicativo usado neste artigo.

2.  Abra o arquivo **c:\\node\\securesite\\workerrole1\\server.js** usando o Notepad.exe e substitua o conteúdo do arquivo pelo seguinte:

        var https = require('https');
        var fs = require('fs');

        var options = {
            pfx: fs.readFileSync('certificate.pfx'),
            passphrase: "password"
        };
        var port = process.env.PORT || 8000;
        https.createServer(options, function (req, res) {
            res.writeHead(200, { 'Content-Type': 'text/plain' });
            res.end('Hello World\n');
        }).listen(port);

    <div class="dev-callout">
<strong>Observa&ccedil;&atilde;o</strong>
<p>Voc&ecirc; deve substituir 'certificate.pfx' pelo nome do arquivo do certificado e &quot;password&quot; pela senha (se houver) do arquivo do certificado.</p>
</div>

3.  Salvar o arquivo **server.js**.

As modificações no resultado do arquivo **Server.js** no aplicativo que está escutando a comunicação na porta 443 (a porta padrão para comunicações SSL) quando ele é implantado no Azure. O arquivo **.pfx** será usado para implementar comunicações SSL por este transporte.

## <a name="step4"> </a>Etapa 4: Modificar o arquivo de definição do serviço

Como agora seu aplicativo está escutando na porta 443, você também deve modificar a definição do serviço para permitir a comunicação através dessa porta.

1.  No diretório de serviço, abra o arquivo de definição de serviço
    (**ServiceDefinition.csdef**), atualize o elemento http **InputEndpoint** na seção **Pontos de extremidade** para permitir a comunicação pela porta 443:

        <WorkerRole name="WorkerRole1" vmsize="Small">
        ...
            <Endpoints>
                <InputEndpoint name="HttpIn" protocol="tcp" 
                    port="443" />
            </Endpoints>
        ...
        </WorkerRole>

    Depois de fazer essa alteração, salve o arquivo **ServiceDefinition.csdef**.

2.  Atualize sua configuração atualizada na nuvem publicando
    seu serviço novamente. No
    prompt do PowerShell do Azure, digite **Publish-AzureServiceProject** no diretório de serviço.

## <a name="step5"> </a>Etapa 5: Conectar-se à Instância da Função usando HTTPS

Agora que sua implantação está em funcionamento no Azure, você pode
conectar-se a ela usando HTTPS.

1.  No Portal de Gerenciamento, selecione seu serviço de nuvem, e clique em **Painel**.

2.  Role para baixo e clique no link exibido como a **URL do Site**:

    ![a url do site][a url do site]

    <div class="dev-callout">
<strong>Observa&ccedil;&atilde;o</strong>
<p>Se a URL do Site exibida no portal n&atilde;o especificar HTTPS, voc&ecirc; dever&aacute; inserir manualmente a URL no navegador usando HTTPS em vez de HTTP.</p>
</div>

3.  Um novo navegador será aberto e exibirá o seu site.

    Seu navegador exibirá um ícone de bloqueio para indicar que ele está
    usando uma conexão HTTPS. Isso também indica que o seu aplicativo
    foi configurado corretamente para o SSL.

    ![][3]

## Recursos adicionais

[Como associar um certificado a um serviço][Como associar um certificado a um serviço]

[Configurando o SSL para um aplicativo Node.js em uma função de trabalho do Azure][Configurando o SSL para um aplicativo Node.js em uma função de trabalho do Azure]

[Como configurar um certificado SSL em um ponto de extremidade HTTPS][Como configurar um certificado SSL em um ponto de extremidade HTTPS]

  [Etapa 1: Criar um serviço do Node.js e publicar o serviço para a nuvem]: #step1
  [Etapa 2: Obter um certificado SSL]: #step2
  [Etapa 3: Modificar o aplicativo para usar o certificado SSL]: #step3
  [Etapa 4: Modificar o arquivo de definição do serviço]: #step4
  [Etapa 5: Conectar-se à Instância da Função usando HTTPS]: #step5
  [Ícone PowerShell do Azure]: ./media/cloud-services-nodejs-configure-ssl-certficate-worker-role/azure-powershell-start.png
  [0]: ./media/cloud-services-nodejs-configure-ssl-certficate-worker-role/enable-ssl-01.png
  [1]: ./media/cloud-services-nodejs-configure-ssl-certficate-worker-role/enable-ssl-02-worker.png
  [2]: ./media/cloud-services-nodejs-configure-ssl-certficate-worker-role/enable-ssl-03-worker.png
  [a url do site]: ./media/cloud-services-nodejs-configure-ssl-certficate-worker-role/site-url.png
  [3]: ./media/cloud-services-nodejs-configure-ssl-certficate-worker-role/enable-ssl-08.png
  [Como associar um certificado a um serviço]: http://msdn.microsoft.com/pt-BR/library/windowsazure/gg465718.aspx
  [Configurando o SSL para um aplicativo Node.js em uma função de trabalho do Azure]: /pt-BR/develop/nodejs/common-tasks/enable-ssl/
  [Como configurar um certificado SSL em um ponto de extremidade HTTPS]: http://msdn.microsoft.com/pt-BR/library/windowsazure/ff795779.aspx
