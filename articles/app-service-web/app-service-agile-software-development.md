<properties
	pageTitle="Desenvolvimento de software Agile com o Serviço de Aplicativo do Azure"
	description="Aprenda a criar aplicativos complexos de grande escala com o Serviço de Aplicativo do Azure, de forma a oferecer suporte ao desenvolvimento de software Agile."
	services="app-service\web,app-service\api,app-service\mobile"
	documentationCenter=""
	authors="cephalin"
	manager="wpickett"
	editor=""/>

<tags
	ms.service="app-service-web"
	ms.workload="web"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="06/24/2015"
	ms.author="cephalin"/>


# Desenvolvimento de software Agile com o Serviço de Aplicativo do Azure #

Neste tutorial, você aprenderá como criar aplicativos complexos de grande escala com o [Serviço de Aplicativo do Azure](/services/app-service/), de forma a oferecer suporte ao [desenvolvimento de software Agile](https://en.wikipedia.org/wiki/Agile_software_development). Ele pressupõe que você já sabe como [implantar aplicativos complexos de modo previsível no Azure](app-service-deploy-complex-application-predictably.md).

Limitações em processos técnicos muitas vezes podem atrapalhar o êxito da implementação das metodologias Agile. O Serviço de Aplicativo do Azure, com recursos como [publicação contínua](web-sites-publish-source-control.md), [ambientes de preparo](web-sites-staged-publishing.md) (slots) e [monitoramento](web-sites-monitor.md), quando associado com sabedoria à coordenação e ao gerenciamento da implantação no[Gerenciador de Recursos do Azure](resource-group-overview.md), pode ser parte de uma solução excelente para desenvolvedores que adotam o desenvolvimento de software Agile.

A tabela a seguir é uma breve lista que mostra os requisitos associados ao desenvolvimento Agile e como os serviços do Azure habilitam cada um deles.

| Requisito | Como o Azure o habilita |
|---------------------------------------------------------------|--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| - Compilação com cada confirmação<br>- Compilação automática e rápida | Quando configurado com a implantação contínua, o Serviço de Aplicativo do Azure pode funcionar como compilações em execução em tempo real com base em uma ramificação de desenvolvimento. Sempre que o código é enviado por push para a ramificação, ele é compilado automaticamente e executado em tempo real no Azure.|
| - Realizar autoteste de compilações | Testes de carregamento, testes da web etc. podem ser implantados com o modelo do Gerenciador de Recursos do Azure.|
| - Executar testes em um clone do ambiente de produção | Os modelos do Gerenciador de Recursos do Azure podem ser usados para criar clones de um ambiente de produção do Azure (incluindo as configurações do aplicativo, modelos de cadeia de conexão, escala etc.) para o teste de modo rápido e previsível.|
| - Exibir o resultado da compilação mais recente com facilidade | Com a implantação contínua no Azure a partir de um repositório, você pode testar o novo código em um aplicativo em tempo real, imediatamente após confirmar suas alterações. |
| - Enviar confirmações diárias para a ramificação principal <br>- Automatizar a implantação | A integração contínua de um aplicativo de produção com a ramificação principal do repositório permite a implantação automática de cada confirmação/mesclagem na ramificação principal para a produção. |

[AZURE.INCLUDE [app-service-web-to-api-and-mobile](../../includes/app-service-web-to-api-and-mobile.md)]

## O que você fará ##

Você verá um fluxo de trabalho dev-test-stage-production típico para publicar novas alterações no aplicativo de exemplo [ToDoApp](https://github.com/azure-appservice-samples/ToDoApp), que consiste em dois [aplicativos Web](/services/app-service/web/), sendo um front-end (FE) e um back-end (BE) da API Web, e um [banco de dados SQL](/services/sql-database/). Você trabalhará com a arquitetura de implantação mostrada abaixo:

![](./media/app-service-agile-software-development/what-1-architecture.png)

Colocando em palavras:

-	A arquitetura de implantação é separada em três ambientes distintos (ou [grupos de recursos](resource-group-overview.md) no Azure), cada um com seu próprio [plano do Serviço de Aplicativo](azure-web-sites-web-hosting-plans-in-depth-overview.md), configurações de [escala](web-sites-scale.md) e banco de dados SQL. 
-	Cada ambiente pode ser gerenciado separadamente. Eles podem até mesmo existir em assinaturas diferentes.
-	O preparo e a produção são implementados como dois slots do mesmo aplicativo de Serviço de Aplicativo. A ramificação mestre está configurada para integração contínua com o slot de preparo.
-	Quando uma confirmação para a ramificação mestre é verificada no slot de preparo (com dados de produção), o aplicativo de preparo verificado é trocado no slot de produção [sem tempo de inatividade](web-sites-staged-publishing.md).

O ambiente de preparo e produção é definido pelo modelo em [*&lt;raiz\_do\_repositório>*/ARMTemplates/ProdandStage.json](https://github.com/azure-appservice-samples/ToDoApp/blob/master/ARMTemplates/ProdAndStage.json).

Os ambientes de desenvolvimento e teste são definidos pelo modelo em [*&lt;raiz\_do\_repositório>*/ARMTemplates/Dev.json](https://github.com/azure-appservice-samples/ToDoApp/blob/master/ARMTemplates/Dev.json).

Você também usará a estratégia de ramificação típica, com código movendo-se da ramificação de desenvolvimento até a ramificação de teste, e em seguida para a ramificação mestre (com melhora na qualidade, por assim dizer).

![](./media/app-service-agile-software-development/what-2-branches.png)

## O que será necessário ##

-	Uma conta do Azure
-	Uma conta do [GitHub](https://github.com/)
-	Git Shell (instalado com [GitHub para Windows](https://windows.github.com/)) — permite que você execute comandos Git e do PowerShell na mesma sessão 
-	Bits do [Azure PowerShell](https://github.com/Azure/azure-powershell/releases/download/0.9.4-June2015/azure-powershell.0.9.4.msi) mais recentes
-	Noções básicas sobre:
	-	Implantação do modelo do [Gerenciador de Recursos do Azure](resource-group-overview.md) (consulte também[Implantar um aplicativo complexo de modo previsível no Azure](app-service-deploy-complex-application-predictably.md))
	-	[Git](http://git-scm.com/documentation)
	-	[PowerShell](https://technet.microsoft.com/library/bb978526.aspx)

> [AZURE.NOTE]Você precisa de uma conta do Azure para concluir este tutorial: +, você pode [Abrir uma conta do Azure gratuitamente](/pricing/free-trial/?WT.mc_id=A261C142F) - Você recebe créditos que podem ser usados para experimentar os serviços do Azure pagos e, mesmo depois que tiverem se esgotado, você pode manter a conta e usar serviços do Azure gratuitos, como Web Apps. + Você pode [ativar os benefícios de assinante MSDN](/pricing/member-offers/msdn-benefits-details/?WT.mc_id=A261C142F) - Sua assinatura do MSDN fornece você créditos a cada mês que podem ser usados para serviços do Azure pagos.
>
> Se você deseja começar a usar o Serviço de Aplicativo do Azure antes de se inscrever em uma conta do Azure, vá até [Experimentar o Serviço de Aplicativo](http://go.microsoft.com/fwlink/?LinkId=523751), em que você pode criar imediatamente um aplicativo Web inicial de curta duração no Serviço de Aplicativo. Nenhum cartão de crédito é exigido, sem compromissos.

## Configurar seu ambiente de produção ##

>[AZURE.NOTE]O script usado neste tutorial configurará automaticamente a publicação contínua de seu repositório GitHub. Isso requer que as credenciais do GitHub já estejam armazenadas no Azure; caso contrário, a implantação de scripts falhará ao tentar definir configurações de controle de origem para aplicativos Web.
>
>Para armazenar suas credenciais do GitHub no Azure, crie um aplicativo Web no [Portal de Visualização do Azure](https://portal.azure.com) e [configure a implantação do GitHub](web-sites-publish-source-control.md#Step7). Você só precisa fazer isso uma vez.

Em um cenário típico de DevOps, você tem um aplicativo que está em execução em tempo real no Azure e deseja fazer alterações nele por meio de publicação contínua. Nesse cenário, você tem um modelo que desenvolveu, testou e usou para implantar o ambiente de produção. Você o configurará nesta seção.

1.	Crie sua própria bifurcação do repositório [ToDoApp](https://github.com/azure-appservice-samples/ToDoApp). Para obter informações sobre como criar a bifurcação, consulte [Bifurcar um repositório](https://help.github.com/articles/fork-a-repo/). Depois que a bifurcação é criada, você pode vê-la no navegador.
 
	![](./media/app-service-agile-software-development/production-1-private-repo.png)

2.	Abra uma sessão do Git Shell. Caso ainda não tenha o Git Shell, instale o [GitHub para Windows](https://windows.github.com/) agora.

3.	Crie um clone local de seu bifurcação executando o seguinte comando:

		git clone https://github.com/<your_fork>/ToDoApp.git 

4.	Depois que tiver o clone local, navegue até *&lt;raiz\_do\_repositório>*\\ARMTemplates e execute o script deploy.ps1 da seguinte maneira:

		.\deploy.ps1 –RepoUrl https://github.com/<your_fork>/todoapp.git

4.	Quando solicitado, digite o nome de usuário desejado e a senha para acesso ao banco de dados.

	Você deverá ver o progresso de provisionamento de vários recursos do Azure. Quando a implantação for concluída, o script iniciará o aplicativo no navegador e emitirá um bipe.

	![](./media/app-service-agile-software-development/production-2-app-in-browser.png)
 
	>[AZURE.TIP]Dê uma olhada em *&lt;raiz\_do\_repositório>*\\ARMTemplates\\Deploy.ps1 para ver como ele gera recursos com IDs exclusivas. Você pode usar a mesma abordagem para criar clones da mesma implantação sem se preocupar com conflitos entre os nomes de recursos.
 
6.	De volta à sessão do Git Shell, execute:

		.\swap –Name ToDoApp<unique_string>master

	![](./media/app-service-agile-software-development/production-4-swap.png)

7.	Quando o script for concluído, volte para navegar até endereço do front-end (http://ToDoApp*&lt;unique_string>*master.azurewebsites.net/) para ver o aplicativo em execução na produção.
 
5.	Faça logon no [Portal de Visualização do Azure](https://portal.azure.com) e veja o que foi criado.

	Você deverá ser capaz de ver dois aplicativos Web no mesmo grupo de recursos, um com o sufixo `Api` no nome. Se examinar o modo de exibição do grupo de recursos, você também verá o banco de dados SQL e o servidor, o plano do Serviço de Aplicativo e os slots de preparo dos aplicativos Web. Percorra os diferentes recursos e compare-os com *&lt;raiz\_do\_repositório>*\\ARMTemplates\\ProdAndStage.json para ver como eles são configurados no modelo.

	![](./media/app-service-agile-software-development/production-3-resource-group-view.png)

Agora você configurou o ambiente de produção. Em seguida, você iniciará uma nova atualização para o aplicativo.

## Criar o desenvolvimento e testar ramificações ##

Agora que há um aplicativo complexo em execução em produção no Azure, você fará uma atualização para o aplicativo de acordo com a metodologia Agile. Nesta seção, você criará as ramificações de desenvolvimento e teste das quais precisará para fazer as atualizações necessárias.

1.	Crie o ambiente de teste primeiro. Na sessão do Git Shell, execute os seguintes comandos para criar o ambiente para uma nova ramificação chamada **NewUpdate**. 

		git checkout -b NewUpdate
		git push origin NewUpdate 
		.\deploy.ps1 -TemplateFile .\Dev.json -RepoUrl https://github.com/<your_fork>/ToDoApp.git -Branch NewUpdate

1.	Quando solicitado, digite o nome de usuário desejado e a senha para acesso ao banco de dados.

	Quando a implantação for concluída, o script iniciará o aplicativo no navegador e emitirá um bipe. Desse modo, você terá uma nova ramificação com ambiente de teste próprio. Dedique um momento para examinar algumas coisas sobre esse ambiente de teste:

	-	Você pode criá-lo em qualquer assinatura do Azure. Isso significa que o ambiente de produção pode ser gerenciado separadamente do ambiente de teste.
	-	Seu ambiente de teste está em execução em tempo real no Azure.
	-	Seu ambiente de teste é idêntico ao ambiente de produção, exceto pelos slots de preparo e pelas configurações de escala. Isso pode ser observado porque essas são as únicas diferenças entre ProdandStage.json e Dev.json.
	-	Você pode gerenciar o ambiente de teste em seu próprio plano do Serviço de Aplicativo, com uma faixa de preço diferente (como **Gratuito**).
	-	Excluir esse ambiente de teste é tão simples quanto excluir o grupo de recursos. Você verá como fazer isso [posteriormente](#delete).

2.	Continue com a criação de uma ramificação de desenvolvimento. Para isso, execute os seguintes comandos:

		git checkout -b Dev
		git push origin Dev
		.\deploy.ps1 -TemplateFile .\Dev.json -RepoUrl https://github.com/<your_fork>/ToDoApp.git -Branch Dev

3.	Quando solicitado, digite o nome de usuário desejado e a senha para acesso ao banco de dados.

	Dedique um momento para examinar algumas coisas sobre esse ambiente de desenvolvimento:

	-	Seu ambiente de desenvolvimento tem uma configuração idêntica ao ambiente de teste porque foi implantado usando o mesmo modelo.
	-	Cada ambiente de desenvolvimento pode ser criado na assinatura do Azure do desenvolvedor, deixando o ambiente de teste para ser gerenciado separadamente.
	-	O ambiente de desenvolvimento está em execução em tempo real no Azure.
	-	Excluir o ambiente de desenvolvimento é tão simples quanto excluir o grupo de recursos. Você verá como fazer isso [posteriormente](#delete).

>[AZURE.NOTE]Quando houver vários desenvolvedores trabalhando na nova atualização, cada um deles poderá criar facilmente uma ramificação e um ambiente de desenvolvimento dedicado fazendo o seguinte:
>
>1.	Criar suas próprias bifurcações do repositório no GitHub (consulte [Bifurcar um repositório](https://help.github.com/articles/fork-a-repo/)).
>2.	Clonar a bifurcação na máquina local
>3.	Execute os mesmos comandos para criar ramificação e ambiente de desenvolvimento próprios.

Quando terminar, a bifurcação GitHub deverá ter três ramificações:

![](./media/app-service-agile-software-development/test-1-github-view.png)

E você deverá ter seis aplicativos Web (três conjuntos de dois) em três grupos de recursos separados:

![](./media/app-service-agile-software-development/test-2-all-webapps.png)
 
>[AZURE.NOTE]Observe que ProdandStage.json especifica o ambiente de produção para usar o tipo de preço **Standard**, que é adequada para a escalabilidade do aplicativo de produção.

## Compilar e testar cada confirmação ##

Os arquivos de modelo ProdAndStage.json e Dev.json já especificam os parâmetros de controle de origem, que, por padrão, configuram a publicação contínua para o aplicativo Web. Portanto, cada confirmação para a ramificação GitHub dispara uma implantação automática no Azure dessa ramificação. Vejamos como a configuração funciona.

1.	Certifique-se de que você está na ramificação de desenvolvimento do repositório local. Para fazer isso, execute o seguinte comando no Git Shell:

		git checkout Dev

2.	Faça uma alteração simples na camada de interface do usuário do aplicativo alterando o código para usar listas de [inicialização](http://getbootstrap.com/components/). Abra *&lt;raiz\_do\_repositório>*\\src\\MultiChannelToDo.Web\\app\\index.cshtml e faça a alteração realçada abaixo:

	![](./media/app-service-agile-software-development/commit-1-changes.png)

	>[AZURE.NOTE]Se você não puder ler a imagem acima:
	>
	>- Na linha 18, altere `check-list` para `list-group`.
	>- Na linha 19, altere `class="check-list-item"` para `class="list-group-item"`.

3.	Salve a alteração. De volta ao Git Shell, execute os seguintes comandos:

		cd <repository_root>
		git add .
		git commit -m "changed to bootstrap style"
		git push origin Dev
 
	Esses comandos git são semelhantes à "verificação em seu código" em outro sistema de controle de origem, como o TFS. Quando você executa `git push`, a nova confirmação dispara um envio de código por push automático ao Azure, que recompila o aplicativo para refletir a alteração no ambiente de desenvolvimento.

4.	Para verificar se esse código foi enviado por push ao ambiente de desenvolvimento, vá até a folha de aplicativo Web do ambiente de desenvolvimento e examine a parte **Implantação**. Você deverá ser capaz de ver a última mensagem de confirmação.

	![](./media/app-service-agile-software-development/commit-2-deployed.png)

5.	A partir dali, clique em **Procurar** para ver a nova alteração no aplicativo em tempo real no Azure.

	![](./media/app-service-agile-software-development/commit-3-webapp-in-browser.png)

	Essa é uma alteração secundária no aplicativo. No entanto, muitas vezes, novas alterações em um aplicativo Web complexo têm efeitos colaterais não intencionais e indesejáveis. A capacidade de testar facilmente cada confirmação em compilações em tempo real permite capturar esses problemas antes que os clientes os vejam.

Agora você já deve estar familiarizado com o conceito de que, como desenvolvedor do projeto **NewUpdate**, você poderá criar um ambiente de desenvolvimento para si mesmo com facilidade e, em seguida, compilar cada confirmação e testar cada compilação.

## Mesclar o código no ambiente de teste ##

Quando você estiver pronto para enviar por push o seu código da ramificação de desenvolvimento para a ramificação NewUpdate, use o processo git padrão:

1.	Mescle todas as novas confirmações de NewUpdate na ramificação de desenvolvimento no GitHub, como confirmações criadas por outros desenvolvedores. Qualquer nova confirmação no GitHub disparará um push de código e uma compilação no ambiente de desenvolvimento. Em seguida, você poderá verificar se o código na ramificação de desenvolvimento ainda funciona com os bits mais recentes da ramificação NewUpdate.

2.	Mescle todas as confirmações novas da ramificação de desenvolvimento na ramificação NewUpdate no GitHub. Essa ação aciona um push de código e uma compilação no ambiente de teste.

Mais uma vez, observe que, como a implantação contínua já está configurada com essas ramificações git, você não precisará realizar qualquer outra ação, como executar compilações de integração. Você só precisará executar as práticas de controle de origem padrão usando git, e o Azure executará todos os processos de compilação para você.

Agora, vamos enviar o código para a ramificação **NewUpdate**. No Git Shell, execute os seguintes comandos:

	git checkout NewUpdate
	git pull origin NewUpdate
	git merge Dev
	git push origin NewUpdate

É isso!

Vá para a folha de aplicativo Web do ambiente de teste para ver a nova confirmação (mesclada na ramificação NewUpdate) ser enviada por push para o ambiente de teste. Em seguida, clique em **Procurar** para observar a alteração de estilo em execução em tempo real no Azure.

## Implantar a atualização na produção ##

O envio de código por push para o ambiente de produção e preparo não deve ser diferente do envio de código por push para o ambiente de teste. É muito simples.

No Git Shell, execute os seguintes comandos:

	git checkout master
	git pull origin master
	git merge NewUpdate
	git push origin master

Lembre-se de que, com base na maneira como o ambiente de produção e preparo é configurado no ProdandStage.json, seu novo código é enviado por push para o slot **Preparo** e executado lá. Se navegar para a URL do slot de preparo, você verá o novo código em execução lá. Para fazer isso, execute o cmdlet `Show-AzureWebsite` no Git Shell.

	Show-AzureWebsite -Name ToDoApp<unique_string>master -Slot Staging
 
Depois de verificar a atualização no slot de preparo, a única coisa que resta a fazer é trocá-lo na produção. No Git Shell, execute os seguintes comandos:

	cd <repository_root>\ARMTemplates
	.\swap.ps1 -Name ToDoApp<unique_string>master

Parabéns! Você publicou com êxito uma nova atualização para o aplicativo Web de produção. E você já fez isso apenas criando facilmente os ambientes de teste/desenvolvimento e compilando/testando todas as confirmações. Esses são elementos fundamentais para o desenvolvimento de software Agile.

<a name="delete"></a>
## Excluir os ambientes de desenvolvimento e teste ##

Como você projetou intencionalmente os ambientes de teste e desenvolvimento para serem grupos de recursos independentes, é muito fácil exclui-los. Para excluir os que você criou neste tutorial (as ramificações do GitHub e os artefatos do Azure), basta executar os seguintes comandos no Git Shell:

	git branch -d Dev
	git push origin :Dev
	git branch -d NewUpdate
	git push origin :NewUpdate
	Switch-AzureMode AzureResourceManager
	Remove-AzureResourceGroup -Name ToDoApp<unique_string>dev-group -Force -Verbose
	Remove-AzureResourceGroup -Name ToDoApp<unique_string>newupdate-group -Force -Verbose

## Resumo ##

O desenvolvimento de software Agile é indispensável para muitas empresas que desejam adotar o Azure como sua plataforma de aplicativos. Neste tutorial, você aprendeu a criar e eliminar réplicas exatas ou réplicas muito semelhantes do ambiente de produção com facilidade, mesmo para aplicativos complexos. Você também aprendeu como aproveitar essa capacidade para criar um processo de desenvolvimento capaz de compilar e testar cada confirmação única no Azure. Este tutorial mostrou como você pode usar Serviço de Aplicativo do Azure e o Gerenciador de Recursos do Azure em conjunto de modo mais adequado, a fim de criar uma solução de DevOps útil para metodologias Agile.

## Mais recursos ##

-	[Implantar um aplicativo complexo de modo previsível no Azure](app-service-deploy-complex-application-predictably.md)
-	[Desenvolvimento Agile na prática: dicas e truques do ciclo de desenvolvimento modernizado](http://channel9.msdn.com/Events/Ignite/2015/BRK3707)
-	[Estratégias de implantação avançada para aplicativos Web do Azure usando modelos do Gerenciador de Recursos](http://channel9.msdn.com/Events/Build/2015/2-620)
-	[Criação de modelos do Gerenciador de Recursos do Azure](resource-group-authoring-templates.md)
-	[JSONLint - o validador JSON](http://jsonlint.com/)
-	[ARMClient – configurar publicação do GitHub no site](https://github.com/projectKudu/ARMClient/wiki/Setup-GitHub-publishing-to-Site)
-	[Ramificação Git – Conceitos básicos de ramificação e mesclagem](http://www.git-scm.com/book/en/v2/Git-Branching-Basic-Branching-and-Merging)
-	[Blog de David Ebbo](http://blog.davidebbo.com/)
-	[PowerShell do Azure](powershell-install-configure.md)
-	[Ferramentas de linha de comando de plataformas cruzadas do Azure](xplat-cli.md)
-	[Criar ou editar usuários no AD do Azure](https://msdn.microsoft.com/library/azure/hh967632.aspx#BKMK_1)
-	[Projeto Kudu Wiki](https://github.com/projectkudu/kudu/wiki)

<!---HONumber=Sept15_HO3-->