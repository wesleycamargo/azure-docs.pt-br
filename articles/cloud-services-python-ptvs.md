<properties
	pageTitle="Funções Web e de trabalho do Python com as Ferramentas Python 2.2 para Visual Studio | Microsoft Azure"
	description="Visão geral do uso do Python Tools para Visual Studio para criar serviços de nuvem do Azure, incluindo funções Web e de Trabalho."
	services="cloud-services"
	documentationCenter="python"
	authors="thraka"
	manager="wpickett"
	editor=""/>

<tags
	ms.service="cloud-services"
	ms.workload="tbd"
	ms.tgt_pltfrm="na"
	ms.devlang="python"
	ms.topic="hero-article"
	ms.date="08/30/2015"
	ms.author="adegeo"/>




# Funções Web e de trabalho do Python com Ferramentas Python 2.2 para Visual Studio

Este artigo oferece uma visão geral do uso das funções Web e de trabalho do Python por meio das [Ferramentas Python para Visual Studio][].

## Pré-requisitos

 - Visual Studio 2013 ou 2015
 - [Ferramentas Python 2.2 para Visual Studio][] (PTVS)
 - [Azure Ferramentas SDK para VS 2013][] ou [Azure Ferramentas SDK para VS 2015][]
 - [Python 2.7 de 32 bits][] ou [Python 3.4 de 32 bits][]

[AZURE.INCLUDE [create-account-and-websites-note](../includes/create-account-and-websites-note.md)]

## O que são funções Web e de Trabalho do Python?

O Azure fornece três modelos de computação para a execução de aplicativos: [recurso Aplicativos Web no Serviço de Aplicativo do Azure][execution model-web sites], [Máquinas Virtuais do Azure][execution model-vms] e [Serviços de Nuvem do Azure][execution model-cloud services]. Todos os três modelos oferecem suporte ao Python. Os Serviços de Nuvem, que incluem as funções Web e de trabalho, fornecem a *PaaS (plataforma como serviço)*. Dentro de um serviço de nuvem, uma função Web fornece um servidor web dos Serviços de Informações da Internet (IIS) dedicado para hospedar aplicativos web de front-end, enquanto uma função de trabalho pode executar tarefas assíncronas, de longa execução ou perpétuas independentes de interação com o usuário ou de entrada.

Para obter mais informações, consulte [O que é um Serviço de Nuvem?].

> [AZURE.NOTE] *Deseja criar um site simples?* Se o seu cenário envolver apenas um front-end de site simples, considere usar um recurso Aplicativos Web leve no Serviço de Aplicativo do Azure. Você pode atualizar facilmente para um serviço de nuvem conforme o site cresce e suas necessidades mudam. Consulte o <a href="/develop/python/">Centro de Desenvolvedores do Python</a> para obter artigos que abordem o desenvolvimento do recurso Aplicativos Web no Serviço de Aplicativo do Azure. <br />


## Criação do projeto

No Visual Studio, você pode escolher o **Serviço de Nuvem do Azure** na caixa de diálogo **Novo Projeto**, em **Python**.

![Caixa de diálogo Novo Projeto](./media/cloud-services-python-ptvs/new-project-cloud-service.png)

No assistente do Serviço de Nuvem do Azure, você pode criar novas funções Web e de trabalho.

![Caixa de diálogo de serviço de nuvem do Azure](./media/cloud-services-python-ptvs/new-service-wizard.png)

O modelo de função de trabalho vem com o código de texto clichê para se conectar a uma conta de armazenamento ou ao Barramento de Serviço do Azure.

![Solução de serviço de nuvem](./media/cloud-services-python-ptvs/worker.png)

Você pode adicionar funções Web ou de Trabalho a um serviço de nuvem existente a qualquer momento. Você pode escolher adicionar projetos existentes em sua solução ou criar projetos novos.

![Adicionar comando de função](./media/cloud-services-python-ptvs/add-new-or-existing-role.png)

Seu serviço de nuvem pode conter funções implementadas em diferentes idiomas. Por exemplo, uma função Web do Python pode ser implementada usando o Django, com funções de trabalho do Python ou do C#. Você pode se comunicar facilmente entre as funções usando filas do Barramento de Serviço ou filas de armazenamento.

## Executar localmente

Se você configurar seu projeto de serviço de nuvem como o projeto de inicialização e pressionar F5, o serviço de nuvem será executado no emulador do Azure local.

Embora o PTVS ofereça suporte à inicialização no emulador, a depuração (por exemplo, pontos de interrupção) não funcionará.

Para depurar suas funções Web e de Trabalho, você pode configurar o projeto de funções como o projeto de inicialização e depurá-lo. Você também pode definir vários projetos de inicialização. Clique com o botão direito do mouse na solução e selecione **Definir Projetos de Inicialização**.

![Propriedades do projeto de inicialização da solução](./media/cloud-services-python-ptvs/startup.png)

## Publicar no Azure

Para publicar, clique com o botão direito do mouse no projeto de serviço de nuvem na solução e selecione **Publicar**.

![Conexão de publicação no Microsoft Azure](./media/cloud-services-python-ptvs/publish-sign-in.png)

Na página de configurações, selecione o serviço de nuvem no qual deseja publicar.

![Configurações de publicação do Microsoft Azure](./media/cloud-services-python-ptvs/publish-settings.png)

Você pode criar um novo serviço de nuvem se ainda não tiver um disponível.

![Criar caixa de diálogo de serviço de nuvem](./media/cloud-services-python-ptvs/publish-create-cloud-service.png)

Também é útil habilitar as conexões à área de trabalho remota para a(s) máquina(s) para falhas de depuração.

![Caixa de diálogo de configuração da área de trabalho remota](./media/cloud-services-python-ptvs/publish-remote-desktop-configuration.png)

Quando tiver concluído os ajustes de configurações, clique em **Publicar**.

Algum progresso aparecerá na janela de saída e, então, você verá a janela Log de atividade do Microsoft Azure.

![Janela Log de atividade do Microsoft Azure](./media/cloud-services-python-ptvs/publish-activity-log.png)

A implantação levará alguns minutos para ser concluída; em seguida, suas funções Web e/ou de Trabalho serão executadas no Azure!

## Próximas etapas

Para obter informações mais detalhadas sobre como usar funções Web e de trabalho nas Ferramentas Python para Visual Studio, consulte a documentação do PTVS:

- [Projetos do serviço de nuvem][]

Para obter mais detalhes sobre o uso dos serviços do Azure por meio das funções Web e de trabalho, como o uso do Armazenamento ou Barramento de Serviço do Azure, consulte os artigos a seguir.

- [Serviço Blob][]
- [Serviço Tabela][]
- [Serviço Fila][]
- [Filas do Barramento de Serviço][]
- [Tópicos do Barramento de Serviço][]


<!--Link references-->

[O que é um Serviço de Nuvem?]: ./cloud-services/cloud-services-choose-me.md
[execution model-web sites]: ./app-service-web/app-service-web-overview.md
[execution model-vms]: ./virtual-machines/virtual-machines-windows-about.md
[execution model-cloud services]: ./cloud-services/cloud-services-choose-me.md
[Python Developer Center]: /develop/python/

[Serviço Blob]: ./storage/storage-python-how-to-use-blob-storage.md
[Serviço Fila]: ./storage/storage-python-how-to-use-queue-storage.md
[Serviço Tabela]: ./storage/storage-python-how-to-use-table-storage.md
[Filas do Barramento de Serviço]: ./service-bus/service-bus-python-how-to-use-queues.md
[Tópicos do Barramento de Serviço]: ./service-bus/service-bus-python-how-to-use-topics-subscriptions.md


<!--External Link references-->

[Ferramentas Python para Visual Studio]: http://aka.ms/ptvs
[Python Tools for Visual Studio Documentation]: http://aka.ms/ptvsdocs
[Projetos do serviço de nuvem]: http://go.microsoft.com/fwlink/?LinkId=624028
[Ferramentas Python 2.2 para Visual Studio]: http://go.microsoft.com/fwlink/?LinkID=624025
[Azure Ferramentas SDK para VS 2013]: http://go.microsoft.com/fwlink/?LinkId=323510
[Azure Ferramentas SDK para VS 2015]: http://go.microsoft.com/fwlink/?LinkId=518003
[Python 2.7 de 32 bits]: http://go.microsoft.com/fwlink/?LinkId=517190
[Python 3.4 de 32 bits]: http://go.microsoft.com/fwlink/?LinkId=517191

<!---HONumber=AcomDC_0420_2016-->