<properties 
	pageTitle="Implantar seu primeiro aplicativo Web no Azure em cinco minutos" 
	description="Saiba como é fácil executar aplicativos Web no Serviço de Aplicativo implantando um aplicativo de exemplo com apenas algumas etapas. Inicie o desenvolvimento real em cinco minutos e veja os resultados imediatamente." 
	services="app-service\web"
	documentationCenter=""
	authors="cephalin" 
	manager="wpickett" 
	editor="" 
/>

<tags 
	ms.service="app-service-web" 
	ms.workload="web" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="hero-article"
	ms.date="05/12/2016" 
	ms.author="cephalin"
/>
	
# Implantar seu primeiro aplicativo Web no Azure em cinco minutos

[AZURE.INCLUDE [guias](../../includes/app-service-web-get-started-nav-tabs.md)]

Esse tutorial o ajuda a implantar seu primeiro aplicativo Web para o [Serviço de Aplicativo do Azure](../app-service/app-service-value-prop-what-is.md). O Serviço de Aplicativo permite criar aplicativos Web, [back-ends de aplicativo móvel](/documentation/learning-paths/appservice-mobileapps/) e [aplicativos de API](../app-service-api/app-service-api-apps-why-best-platform.md).

Com pouca ação de sua parte, você vai:

- Implantar um aplicativo Web de exemplo (escolha entre ASP.NET, PHP, Node.js, Java ou Python).
- Ver o aplicativo em execução em segundos.
- Atualize o aplicativo Web da mesma maneira como faria com [confirmações do Git por push](https://git-scm.com/docs/git-push).

Você também vai conferir o [portal do Azure](https://portal.azure.com) e examinar os recursos disponíveis nele.

## Pré-requisitos

- [instalar o Git](http://www.git-scm.com/downloads). 
- [Instale a CLI do Azure.](../xplat-cli-install.md) 
- Obtenha uma conta do Microsoft Azure. Se não tiver uma conta, você poderá [inscrever-se para uma avaliação gratuita](/pricing/free-trial/?WT.mc_id=A261C142F) ou [ativar seus benefícios de assinante do Visual Studio](/pricing/member-offers/msdn-benefits-details/?WT.mc_id=A261C142F).

>[AZURE.NOTE] Veja um aplicativo Web em ação. [Experimente o Serviço de Aplicativo](http://go.microsoft.com/fwlink/?LinkId=523751) imediatamente e crie um aplicativo inicializador de curta duração, sem necessidade de cartão de crédito e sem compromisso.

## Implantar um aplicativo Web

Vamos implantar um aplicativo Web no Serviço de Aplicativo do Azure.

1. Abra um novo prompt de comando do Windows, janela do PowerShell, shell do Linux ou terminal do OS X. Execute `git --version` e `azure --version` para verificar se o Git e a CLI do Azure estão instalados em seu computador. 

    ![Testar a instalação das ferramentas da CLI para seu primeiro aplicativo Web no Azure](./media/app-service-web-get-started/1-test-tools.png)

    Se ainda não tiver instalado as ferramentas, confira [Pré-requisitos](#Prerequisites) para obter links de download.

1. `CD` em um diretório de trabalho e clone o aplicativo de exemplo da seguinte forma:

        git clone <github_sample_url>

    ![Clonar o código de exemplo de aplicativo para seu primeiro aplicativo Web no Azure](./media/app-service-web-get-started/2-clone-sample.png)

    Para *&lt;github\_sample\_url>*, use uma das seguintes URLs, dependendo da estrutura de sua preferência:

    - HTML+CSS+JS: [https://github.com/Azure-Samples/app-service-web-html-get-started.git](https://github.com/Azure-Samples/app-service-web-html-get-started.git)
    - ASP.NET: [https://github.com/Azure-Samples/app-service-web-dotnet-get-started.git](https://github.com/Azure-Samples/app-service-web-dotnet-get-started.git)
    - PHP (CodeIgniter): [https://github.com/Azure-Samples/app-service-web-php-get-started.git](https://github.com/Azure-Samples/app-service-web-php-get-started.git)
    - Node.js (Express): [https://github.com/Azure-Samples/app-service-web-nodejs-get-started.git](https://github.com/Azure-Samples/app-service-web-nodejs-get-started.git) 
    - Java: [https://github.com/Azure-Samples/app-service-web-java-get-started.git](https://github.com/Azure-Samples/app-service-web-java-get-started.git)
    - Python (Django): [https://github.com/Azure-Samples/app-service-web-python-get-started.git](https://github.com/Azure-Samples/app-service-web-python-get-started.git)

2. `CD` no diretório raiz do aplicativo de exemplo. Por exemplo,

        cd app-service-web-html-get-started

3. Faça logon no Azure da seguinte forma:

        azure login
    
    Siga a mensagem de ajuda para continuar o processo de logon.
    
    ![Fazer logon no Azure para criar seu primeiro aplicativo Web](./media/app-service-web-get-started/3-azure-login.png)

4. Crie o recurso de aplicativo do Serviço de Aplicativo no Azure com um nome de aplicativo exclusivo com o próximo comando. Quando solicitado, especifique o número da região desejada.

        azure site create --git <app_name>
    
    ![Criar o recurso do Azure para seu primeiro aplicativo Web no Azure](./media/app-service-web-get-started/4-create-site.png)
    
    >[AZURE.NOTE] Se nunca tiver configurado credenciais de implantação para sua assinatura do Azure, você será solicitado a criá-las. Essas credenciais, não as credenciais de sua conta do Azure, são usadas pelo Serviço de Aplicativo somente para implantações do Git e logons de FTP.
    
    Seu aplicativo está criado no Azure. Além disso, seu diretório atual é inicializado no Git e conectado ao novo aplicativo do Serviço de Aplicativo como um remoto do Git. Você pode navegar até a URL do aplicativo (http://&lt;app_name>.azurewebsites.net) para ver a bela página HTML padrão, mas agora vamos incluir seu próprio código lá.

4. Implante o código de exemplo no novo aplicativo do Serviço de Aplicativo, como você faria com qualquer código com o Git:

        git push azure master 

    ![Enviar código por push para seu primeiro aplicativo Web no Azure](./media/app-service-web-get-started/5-push-code.png)
    
    Se tiver usado uma das estruturas de linguagem, talvez você veja uma saída diferente daquela mostrada acima. Isso ocorre porque o `git push` não só coloca o código no Azure, mas também dispara tarefas de implantação no mecanismo de implantação. Se houver um arquivo package.json (Node.js) ou requirements.txt (Python) na raiz do projeto (repositório) ou um arquivo packages.config no projeto ASP.NET, os scripts de implantação restaurarão os pacotes necessários para você. Você também pode [habilitar a extensão do Composer](web-sites-php-mysql-deploy-use-git.md#composer) para processar automaticamente arquivos composer.json no aplicativo PHP.

Parabéns, você implantou seu aplicativo no Serviço de Aplicativo do Azure.

## Ver o aplicativo em execução

Para ver seu aplicativo em execução no Azure, execute este comando em qualquer pasta no repositório:

    azure site browse

## Fazer atualizações no aplicativo

Agora você pode usar o Git para enviar da raiz do projeto (repositório) a qualquer momento e fazer uma atualização no site ativo. Você fazê-lo da mesma forma que quando você implantou o aplicativo no Azure pela primeira vez. Por exemplo, sempre que você desejar enviar novas alterações que testou localmente, bastará executar os seguintes comandos da raiz do projeto (repositório):
    
    git add .
    git commit -m "<your_message>"
    git push azure master

## Ver o aplicativo no portal do Azure

Agora, vamos para o portal do Azure para ver o que você criou:

1. Faça logon no [portal do Azure](https://portal.azure.com) com uma conta da Microsoft que tenha sua assinatura do Azure.

2. Na barra à esquerda, clique em **Serviços de Aplicativos**.

3. Clique no aplicativo que você acabou de criar para abrir sua página no portal (chamada de [folha](../azure-portal-overview.md)). A folha **Configurações** também é aberta por padrão, para fins de conveniência.

    ![Exibição do portal de seu primeiro aplicativo Web no Azure](./media/app-service-web-get-started/portal-view.png)

A folha do portal do aplicativo do Serviço de Aplicativo expõe um conjunto avançado de configurações e ferramentas para que você configure, monitore, proteja e solucione problemas do aplicativo. Reserve algum tempo para se familiarizar com essa interface executando algumas tarefas simples (o número da tarefa corresponde ao número na captura de tela):

1. interromper o aplicativo
2. reiniciar o aplicativo
3. clique no link de **Grupo de Recursos** para ver todos os recursos implantados no grupo de recursos
4. clique em **Configurações** > **Propriedades** para ver outras informações sobre o aplicativo
5. clique em **Ferramentas** para acessar ferramentas úteis para monitoramento e solução de problemas  

## Próximas etapas

- Leve o aplicativo do Azure para o próximo patamar. Proteja-o com a autenticação. Dimensione-o com base na demanda. Configure alguns alertas de desempenho. Tudo isso com apenas alguns cliques. Confira [Adicionar funcionalidade a seu primeiro aplicativo Web](app-service-web-get-started-2.md).
- Além do Git e da CLI do Azure, há outras maneiras de implantar aplicativos Web no Azure (confira [Implantar o aplicativo no Serviço de Aplicativo do Azure](../app-service-web/web-sites-deploy.md)). Localize as etapas de implantação e desenvolvimento preferenciais para a estrutura de linguagem selecionando a estrutura no início do artigo.

<!---HONumber=AcomDC_0518_2016-->