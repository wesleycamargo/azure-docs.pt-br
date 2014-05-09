<properties linkid="dev-nodejs-enablesslworker" urlDisplayName="Habilitar a função de trabalho SSL" pageTitle="Configurar o SSL para uma função de trabalho de serviço de nuvem (Node.js)" metaKeywords="Node.js Azure SSL, Node.js do Azure, função de trabalho SSL" description="" metaCanonical="" services="cloud-services" documentationCenter="Node.js" title="Configurando o SSL para um aplicativo Node.js em uma função de trabalho do Azure" authors="" solutions="" manager="" editor="" />





# Configurando o SSL para um aplicativo Node.js em uma função de trabalho do Azure

Normalmente a criptografia SSL é o método mais usado para proteger dados enviados pela Internet. Essa tarefa comum aborda como especificar um ponto de extremidade HTTPS para um aplicativo Node.js hospedado como uma Serviço de Nuvem do Azure em uma função de trabalho.

<div class="dev-callout">
	<b>Observação</b>
	<p>As etapas deste artigo se aplicam somente a aplicativos de nó hospedados como um Serviço de Nuvem do Azure em uma função de trabalho.</p>
	</div>

Esta tarefa inclui as seguintes etapas:

-   [Etapa 1: Criar um serviço do Node.js e publicar o serviço na nuvem]
-   [Etapa 2: Obter um certificado SSL]
-   [Etapa 3: Modificar o aplicativo para usar o certificado SSL]
-   [Etapa 4: Modificar o arquivo de definição de serviço]
-   [Etapa 5: Conectar-se à instância da função usando HTTPS]

## <a name="step1"> </a>Etapa 1: Criar um serviço do Node.js e publicar o serviço na nuvem

Você pode criar um serviço simples 'hello world' do Node.js usando o PowerShell do Azure com estas etapas:

1. No **Menu Iniciar** ou na **Tela Iniciar**, pesquise por **PowerShell do Azure**. Finalmente, clique com o botão direito do mouse no **PowerShell do Azure** e selecione **Executar como Administrador**.

	![Ícone PowerShell do Azure][powershell-menu]

	

2.  Criar um novo serviço, usando o cmdlet **Novo AzureServiceProject**.

	![][1]

3.  Adicionar uma função de trabalho ao seu serviço usando o cmdlet **Adicionar no AzureNodeWorkerRole**:

    ![][2]

4.  Publicar seu serviço na nuvem usando o cmdlet **Publish-AzureServiceProject**:

    ![][3]

	<div class="dev-callout">
	<strong>Observação</strong>
	<p>Se não tiver importado anteriormente as configurações de publicação para sua assinatura do Azure, você receberá um erro ao tentar publicar. Para obter informações sobre como baixar e importar as configurações de publicação para sua assinatura, consulte <a href="https://www.windowsazure.com/pt-br/develop/nodejs/how-to-guides/powershell-cmdlets/#ImportPubSettings">Como usar o PowerShell do Azure para o Node.js (a página pode estar em inglês)</a></p>
	</div>

O valor da **URL do Site Criada** retornado pelo cmdlet **Publish-AzureServiceProject** contém o nome de domínio totalmente qualificado para o seu aplicativo hospedado. Você precisará obter um certificado SSL para esse nome de domínio específico totalmente qualificado e implantá-lo no Azure.

## <a name="step2"> </a>Etapa 2: Obter um Certificado SSL

Para configurar o SSL para um aplicativo, você deve primeiro obter um certificado SSL assinado por uma CA (Autoridade de Certificação), um terceiro confiável que emita certificados para essa finalidade. Se você ainda não tiver um certificado, precisará obter um de uma empresa que venda certificados SSL.

O certificado deve atender aos seguintes requisitos para certificados SSL no Azure:

-   O certificado deve conter uma chave privada.
-   O certificado deve ser criado para troca de chaves (arquivo **.pfx**).
-   O nome do assunto do certificado deve corresponder ao domínio usado para acessar o serviço de nuvem. Você não pode adquirir um certificado SSL para o domínio cloudapp.net, portanto, o nome de assunto do certificado deve corresponder ao nome do domínio personalizado usado para acessar o aplicativo. Por exemplo, __mysecuresite.cloudapp.net__.
-   O certificado deve usar, no mínimo, uma criptografia de 2048 bits.

O arquivo **.pfx** contendo o certificado será adicionado ao seu projeto de serviço e implantado no Azure nas etapas a seguir.

## <a name="step3"> </a>Etapa 3: Modificar o aplicativo para usar o certificado SSL

Quando um aplicativo do Node.js é implantado em uma função de trabalho, o certificado do servidor e a conexão SSL são gerenciados pelo Node.exe. Para tratar o tráfego do SSL, você deve usar o módulo 'https' em vez de 'http'. Execute as seguintes etapas para adicionar o certificado SSL ao seu projeto e, em seguida, modificar o aplicativo para usar o certificado.

1.   Salve o arquivo **.pfx** fornecido por sua CA (Autoridade de Certificação) no diretório que contém o seu aplicativo. Por exemplo, **c:\\node\\securesite\\workerrole1** é o diretório que contém o aplicativo usado neste artigo.

2.   Abra o arquivo **c:\\node\\securesite\\workerrole1\server.js** usando o Notepad.exe e substitua o conteúdo do arquivo pelo seguinte:

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
	<strong>Observação</strong>
	<p>Você deve substituir 'certificate.pfx' pelo nome do arquivo do certificado e "password" pela senha (se houver) do arquivo do certificado.</p>
	</div>

3.   Salve o arquivo **server.js**.

As modificações no resultado do arquivo **Server.js** no aplicativo que está escutando a comunicação na porta 443 (a porta padrão para comunicações SSL) quando ele é implantado no Azure. O arquivo **.pfx** será usado para implementar comunicações SSL por este transporte.

## <a name="step4"> </a>Etapa 4: Modificar o arquivo de definição de serviço

Como agora seu aplicativo está escutando na porta 443, você também deve modificar a definição do serviço para permitir a comunicação através dessa porta.

1.  No diretório de serviço, abra o arquivo de definição de serviço (**ServiceDefinition.csdef**), atualize o elemento http **InputEndpoint** na seção **Endpoints** para permitir a comunicação pela porta 443:

        <WorkerRole name="WorkerRole1" vmsize="Small">
        ...
            <Endpoints>
                <InputEndpoint name="HttpIn" protocol="tcp" 
                    port="443" />
            </Endpoints>
        ...
        </WorkerRole>

	Depois de fazer essa alteração, salve o arquivo **ServiceDefinition.csdef**.

4.  Atualize sua configuração atualizada na nuvem publicando seu serviço novamente. No prompt do PowerShell do Azure, digite **Publish-AzureServiceProject** no diretório de serviço.

## <a name="step5"> </a>Etapa 5: Conectar-se à instância da função usando HTTPS

Agora que sua implantação está em funcionamento no Azure, você pode conectar-se a ela usando HTTPS.

1.  No Portal de Gerenciamento, selecione seu serviço de nuvem, e clique em **Painel**.

2. Role para baixo e clique no link exibido como a **URL do Site**:

    ![a url do site][site-url]

	<div class="dev-callout">
	<strong>Observação</strong>
	<p>Se a URL do Site exibida no portal não especificar HTTPS, você deverá inserir manualmente a URL no navegador usando HTTPS em vez de HTTP.</p>
	</div>

3.  Um novo navegador será aberto e exibirá o seu site.

    Seu navegador exibirá um ícone de bloqueio para indicar que ele está usando uma conexão HTTPS. Isso também indica que o seu aplicativo foi configurado corretamente para o SSL.

    ![][8]

## Recursos adicionais

[Como associar um certificado a um serviço]

[Configurando o SSL para um aplicativo Node.js em uma função de trabalho do Azure]

[Como configurar um certificado SSL em um ponto de extremidade HTTPS]

  [Etapa 1: Criar um serviço do Node.js e publicar o serviço na nuvem]: #step1
  [Etapa 2: Obter um certificado SSL]: #step2
  [Etapa 3: Modificar o aplicativo para usar o certificado SSL]: #step3
  [Etapa 4: Modificar o arquivo de definição de serviço]: #step4
  [Etapa 5: Conectar-se à instância da função usando HTTPS]: #step5
  [**PowerShell do Azure**]: http://go.microsoft.com/?linkid=9790229&clcid=0x409
  
  
  
  
  [1]: ./media/cloud-services-nodejs-configure-ssl-certficate-worker-role/enable-ssl-01.png
  [2]: ./media/cloud-services-nodejs-configure-ssl-certficate-worker-role/enable-ssl-02-worker.png
  [3]: ./media/cloud-services-nodejs-configure-ssl-certficate-worker-role/enable-ssl-03-worker.png
  [Portal de Gerenciamento do Azure]: http://manage.windowsazure.com
  
  
  [Como associar um certificado a um serviço]: http://msdn.microsoft.com/pt-br/library/windowsazure/gg465718.aspx
  
  [site-url]: ./media/cloud-services-nodejs-configure-ssl-certficate-worker-role/site-url.png
  [8]: ./media/cloud-services-nodejs-configure-ssl-certficate-worker-role/enable-ssl-08.png
  [Como configurar um certificado SSL em um ponto de extremidade HTTPS]: http://msdn.microsoft.com/pt-br/library/windowsazure/ff795779.aspx
  [powershell-menu]: ./media/cloud-services-nodejs-configure-ssl-certficate-worker-role/azure-powershell-start.png
  
  
  [Configurando o SSL para um aplicativo Node.js em uma função de trabalho do Azure]: /pt-br/develop/nodejs/common-tasks/enable-ssl/
  

