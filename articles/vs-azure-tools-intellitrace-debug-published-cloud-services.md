<properties 
   pageTitle="Depurando um serviço de nuvem publicado com o IntelliTrace e o Visual Studio | Microsoft Azure"
   description="Depurando um serviço de nuvem publicado com o IntelliTrace e o Visual Studio"
   services="visual-studio-online"
   documentationCenter="n/a"
   authors="TomArcher"
   manager="douge"
   editor="" />
<tags 
   ms.service="visual-studio-online"
   ms.devlang="multiple"
   ms.topic="article"
   ms.tgt_pltfrm="multiple"
   ms.workload="na"
   ms.date="08/15/2016"
   ms.author="tarcher" />

# Depurando um serviço de nuvem publicado com o IntelliTrace e o Visual Studio

##Visão geral

Com o IntelliTrace, você pode registrar em log informações extensas de depuração sobre uma instância de função quando ela é executada no Azure. Se precisa encontrar a causa de um problema, você pode usar os logs do IntelliTrace para examinar seu código no Visual Studio como se ele estivesse em execução no Azure. Na verdade, o IntelliTrace registra dados de ambiente e a execução do código de chave quando seu aplicativo Azure está em execução como um serviço de nuvem no Azure, além de permitir que você reproduza os dados gravados usando o Visual Studio. Como alternativa, é possível usar a depuração remota para anexação direta a um serviço de nuvem em execução no Azure. Consulte [Depurando serviços de nuvem](http://go.microsoft.com/fwlink/p/?LinkId=623041).

>[AZURE.IMPORTANT] O IntelliTrace destina-se apenas aos cenários de depuração e não deve ser usado para uma implantação de produção.

>[AZURE.NOTE] Você poderá usar o IntelliTrace se tiver o Visual Studio Enterprise instalado e seu aplicativo Azure se destinar ao .NET Framework 4 ou uma versão posterior. O IntelliTrace coleta informações sobre suas funções no Azure. As máquinas virtuais para essas funções sempre executam sistemas operacionais de 64 bits.

## Para configurar um aplicativo Azure para o IntelliTrace

Para habilitar o IntelliTrace para um aplicativo Azure, você deve criar e publicar o aplicativo de um projeto Azure do Visual Studio. É preciso configurar o IntelliTrace para o aplicativo Azure antes de publicá-lo no Azure. Se você publicar o aplicativo sem configurar o IntelliTrace, mas decidir que deseja fazer isso, será preciso publicar o aplicativo novamente usando o Visual Studio. Para saber mais, consulte [Publicando um serviço de nuvem usando as Ferramentas do Azure](http://go.microsoft.com/fwlink/p/?LinkId=623012).

1. Quando estiver pronto para implantar o aplicativo Azure, verifique se os destinos de compilação do projeto estão definidos como **Depurar**.

1. Abra o menu de atalho do projeto do Azure no Gerenciador de Soluções e escolha **Publicar**.
 
    O assistente Publicar Aplicativo do Azure é exibido.

1. Para coletar os logs do IntelliTrace do seu aplicativo quando ele é publicado na nuvem, marque a caixa de seleção **Habilitar IntelliTrace**.

    >[AZURE.NOTE] Você pode habilitar o IntelliTrace ou a criação de perfis quando publica o aplicativo Azure. Não é possível habilitar ambos.

1. Para personalizar a configuração básica do IntelliTrace, escolha o hiperlink **Configurações**.

    A caixa de diálogo Configurações do IntelliTrace é exibida, conforme mostrado na figura a seguir. Você pode especificar quais eventos registrar no log, se deseja coletar informações de chamada, de quais módulos e processos coletar logs e quanto espaço alocar para o registro. Para saber mais sobre o IntelliTrace, consulte [Depurando com o IntelliTrace](http://go.microsoft.com/fwlink/?LinkId=214468).

    ![VST\_IntelliTraceSettings](./media/vs-azure-tools-intellitrace-debug-published-cloud-services/IC519063.png)

O log do IntelliTrace é um arquivo de log circular do tamanho máximo especificado nas configurações do IntelliTrace (o tamanho padrão é 250 MB). Os logs do IntelliTrace são coletados em um arquivo no sistema de arquivos da máquina virtual. Quando você solicita os logs, um instantâneo é feito nesse momento e baixado no computador local.

Depois que o aplicativo Azure tiver sido publicado no Azure, você poderá determinar se o IntelliTrace foi habilitado no nó Computação do Azure no Gerenciador de Servidores, conforme mostrado na seguinte imagem:

![VST\_DeployComputeNode](./media/vs-azure-tools-intellitrace-debug-published-cloud-services/IC744134.png)

## Baixando logs do IntelliTrace para uma instância de função

Você pode baixar logs do IntelliTrace para uma instância de função do nó **Serviços de Nuvem** no **Gerenciador de Servidores**. Expanda o nó **Serviços de Nuvem** até localizar a instância em que está interessado, abra o menu de atalho dessa instância e escolha **Exibir Logs do IntelliTrace**. Os logs do IntelliTrace são baixados em um arquivo em um diretório no computador local. Toda vez que você solicita os logs do IntelliTrace, um novo instantâneo é criado.

Quando os logs são baixados, o Visual Studio exibe o andamento da operação na janela Log de Atividades do Azure. Como mostrado na figura a seguir, você pode expandir o item de linha da operação para ver mais detalhes.

![VST\_IntelliTraceDownloadProgress](./media/vs-azure-tools-intellitrace-debug-published-cloud-services/IC745551.png)

É possível continuar trabalhando no Visual Studio enquanto os logs do IntelliTrace estão sendo baixados. Quando o download do log tiver acabado, o log será aberto automaticamente no Visual Studio.

>[AZURE.NOTE] Os logs do IntelliTrace podem conter exceções que a estrutura gera e subsequentemente trata. O código interno da estrutura gera essas exceções como uma parte normal de iniciar uma função, de modo que você pode ignorá-las com segurança.

## Consulte também

[Depurando serviços de nuvem](https://msdn.microsoft.com/library/ee405479.aspx)

<!---HONumber=AcomDC_0817_2016-->