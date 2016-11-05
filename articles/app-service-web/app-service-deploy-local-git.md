---
title: Implantação do Git local para o Serviço de Aplicativo do Azure
description: Saiba como habilitar a implantação do Git local no Serviço de Aplicativo do Azure.
services: app-service
documentationcenter: ''
author: dariagrigoriu
manager: wpickett
editor: mollybos

ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/13/2016
ms.author: dariagrigoriu

---
# Implantação do Git local para o Serviço de Aplicativo do Azure
Este tutorial mostra como implantar seu aplicativo para o [Serviço de Aplicativo do Azure] de um repositório do Git no computador local. O Serviço de Aplicativo dá suporte a essa abordagem com a opção de implantação do **Git Local** no [Portal do Azure]. Muitos dos comandos do Git descritos neste artigo são executados automaticamente ao criar um aplicativo do Serviço de Aplicativo usando a [interface de linha de comando do Azure] conforme descrito [aqui](app-service-web-get-started.md).

## Pré-requisitos
Para concluir este tutorial, você precisará:

* Git. Você pode baixar o binário de instalação [aqui](http://www.git-scm.com/downloads).
* Conhecimento básico do Git.
* Uma conta do Microsoft Azure. Se não tiver uma conta, você poderá [inscrever-se para uma avaliação gratuita](https://azure.microsoft.com/pricing/free-trial) ou [ativar seus benefícios de assinante do Visual Studio](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details).

> [!NOTE]
> Se você deseja começar com o Serviço de Aplicativo do Azure antes de se inscrever em uma conta do Azure, acesse [Experimentar o Serviço de Aplicativo](http://go.microsoft.com/fwlink/?LinkId=523751), em que você pode criar imediatamente um aplicativo inicial de curta duração no Serviço de Aplicativo. Nenhum cartão de crédito é exigido, sem compromissos.
> 
> 

## <a id="Step1"></a>Etapa 1: Criar um repositório local
Execute as seguintes tarefas para criar um novo repositório do Git.

1. Inicie uma ferramenta de linha de comando, como **GitBash** (Windows) ou **Bash** (shell do Unix). Nos sistemas OS X, você pode acessar a linha de comando por meio do aplicativo **Terminal**.
2. Navegue até o diretório em que o conteúdo para implantar estaria localizado.
3. Use o seguinte comando para inicializar um novo repositório do Git:
   
        git init

## <a id="Step2"></a>Etapa 2: Confirmar seu conteúdo
O Serviço de Aplicativo dá suporte a aplicativos criados em uma variedade de linguagens de programação.

1. Se seu repositório já inclui conteúdo, ignore esse ponto e passe para o ponto 2 abaixo. Se seu repositório ainda não inclui conteúdo, simplesmente popule-o com um arquivo .html estático da seguinte maneira:
   
   * Usando um editor de texto, crie um novo arquivo chamado **index.html** na raiz do repositório do Git
   * Adicione o seguinte como o conteúdo do arquivo index.html e salve-o: *Hello Git!*
2. Na linha de comando, verifique se você está na raiz do seu repositório do Git. Em seguida, use os comandos a seguir para adicionar arquivos ao repositório:
   
        git add -A 
3. Em seguida, confirme as alterações no repositório usando o comando a seguir:
   
        git commit -m "Hello Azure App Service"

## <a id="Step3"></a>Etapa 3: Habilitar o repositório de aplicativo do Serviço de Aplicativo
Execute as seguintes etapas para habilitar um repositório do Git para seu aplicativo do Serviço de Aplicativo.

1. Faça logon no [Portal do Azure].
2. Na folha do seu aplicativo do Serviço de Aplicativo, clique em **Configurações > Origem da Implantação**. Clique em **Escolher origem**, depois em **Repositório Local do GIT** e em **OK**.
   
    ![Repositório Git local](./media/app-service-deploy-local-git/local_git_selection.png)
3. Se for a primeira vez que você configura um repositório no Azure, você precisa criar as credenciais de logon para ele. Você as usará para fazer logon no repositório do Azure e enviar por push as alterações do seu repositório Git Local. Na folha de seu aplicativo, clique em **Configurações > Credenciais de implantação** e, em seguida, configure o nome de usuário e senha de implantação. Quando terminar, clique em **Salvar**.
   
    ![](./media/app-service-deploy-local-git/deployment_credentials.png)

## <a id="Step4"></a>Etapa 4: Implantar seu projeto
Use as etapas a seguir para publicar seu aplicativo no Serviço de Aplicativo usando o Git Local.

1. Na folha do aplicativo no Portal do Azure, clique em **Configurações > Propriedades** da **URL do Git**.
   
    ![](./media/app-service-deploy-local-git/git_url.png)
   
    A **URL do Git** é a referência remota para implantação a partir de seu repositório local. Você usará essa URL nas etapas a seguir.
2. Usando a linha de comando, verifique se você está na raiz do seu repositório do Git local.
3. Use `git remote` para adicionar a referência remota listada na **URL do Git** na etapa 1. O comando será semelhante ao seguinte:
   
        git remote add azure https://<username>@localgitdeployment.scm.azurewebsites.net:443/localgitdeployment.git         
   > [!NOTE]
   > O comando **remoto** adiciona uma referência com nome a um repositório remoto. Neste exemplo, ele cria uma referência chamada ‘azure’ para o repositório do aplicativo Web.
   > 
   > 
4. Envie por push seu conteúdo para o Serviço de Aplicativo usando o novo remoto **azure** que você acabou de criar.
   
        git push azure master
   
    Será solicitada a senha que você criou anteriormente ao redefinir suas credenciais de implantação no Portal do Azure. Digite a senha (o Gitbash não ecoa asteriscos no console à medida que você digita a senha).
5. Volte para seu aplicativo no Portal do Azure. Uma entrada de log do seu envio por push mais recente deve ser exibida na folha **Implantações**.
   
    ![](./media/app-service-deploy-local-git/deployment_history.png)
6. Clique no botão **Procurar** na parte superior da folha do aplicativo para verificar se o conteúdo foi implantado.

## <a id="Step5"></a>Solucionar problemas
Estes são erros ou problemas comumente encontrados ao usar o Git para publicar em um aplicativo do Serviço de Aplicativo no Azure:

- - -
**Sintoma**: não foi possível acessar ‘[siteURL]': falha ao conectar ao [scmAddress]

**Causa**: esse erro poderá ocorrer se o aplicativo não estiver em funcionamento.

**Resolução**: inicie o aplicativo no Portal do Azure. A implantação do Git não funcionará a menos que o aplicativo esteja em execução.

- - -
**Sintoma**: não foi possível resolver o nome de host 'host'

**Causa**: este erro pode ocorrer se as informações de endereço inseridas ao criar o azure remoto estiverem incorretas.

**Resolução**: use o comando `git remote -v` para listar todos os remotos, junto com a URL associada. Verifique se a URL do remote do 'azure' está correta. Se necessário, remova e recrie esse remoto usando a URL correta.

- - -
**Sintoma**: não há referências em comum e nenhuma especificada; nada é feito. Talvez você deva especificar uma ramificação como 'mestre'.

**Causa**: este erro pode ocorrer se você não especificar uma ramificação ao executar uma operação de envio de git e não tiver definido o valor de push.default usado pelo Git.

**Solução**: execute a operação de envio novamente, especificando a ramificação mestre. Por exemplo:

    git push azure master

- - -
**Sintoma**: src refspec [branchname] não corresponde a nada.

**Causa**: este erro pode ocorrer se você tentar enviar para uma ramificação que não seja a mestre no remote 'azure'.

**Solução**: execute a operação de envio novamente, especificando a ramificação mestre. Por exemplo:

    git push azure master

- - -
**Sintoma**: erro - alterações confirmadas no repositório remoto, mas o aplicativo Web não foi atualizado.

**Causa**: esse erro poderá ocorrer se você estiver implantando um aplicativo do Node. js que contém um arquivo package.json que especifica módulos adicionais necessários.

**Resolução**: mensagens adicionais contendo 'npm ERR!' devem ser registradas antes deste erro e podem fornecer contexto adicional sobre a falha. A seguir estão as causas conhecidas desse erro e a mensagem 'npm ERR!' correspondente:

* **Arquivo malformado package.json**: npm ERR! Não foi possível ler as dependências.
* **Um módulo nativo que não tenha uma distribuição binária para o Windows**:
  
  * npm ERR! `cmd "/c" "node-gyp rebuild"` falhou com 1
    
      OU
  * npm ERR! [modulename@version] preinstall: `make || gmake`

## Recursos adicionais
* [Documentação do Git](http://git-scm.com/documentation)
* [Documentação do projeto Kudu](https://github.com/projectkudu/kudu/wiki)
* [Implantação contínua no Serviço de Aplicativo do Azure](app-service-continuous-deployment.md)
* [Como usar o PowerShell para o Azure](../powershell-install-configure.md)
* [Como usar as ferramentas da interface de linha de comando do Azure](../xplat-cli-install.md)

[Serviço de Aplicativo do Azure]: https://azure.microsoft.com/documentation/articles/app-service-changes-existing-services/
[Azure Developer Center]: http://azure.microsoft.com/develop/overview/
[Portal do Azure]: https://portal.azure.com
[Git website]: http://git-scm.com
[Installing Git]: http://git-scm.com/book/en/Getting-Started-Installing-Git
[interface de linha de comando do Azure]: https://azure.microsoft.com/documentation/articles/xplat-cli-azure-resource-manager/

[Using Git with CodePlex]: http://codeplex.codeplex.com/wikipage?title=Using%20Git%20with%20CodePlex&referringTitle=Source%20control%20clients&ProjectName=codeplex
[Quick Start - Mercurial]: http://mercurial.selenic.com/wiki/QuickStart

<!---HONumber=AcomDC_0803_2016-->