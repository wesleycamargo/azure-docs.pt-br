<properties
	pageTitle="Sincronizar o conteúdo de uma pasta de nuvem para o Serviço de Aplicativo do Azure"
	description="Saiba como implantar seu aplicativo no Serviço de Aplicativo do Azure por meio da sincronização de conteúdo de uma pasta de nuvem."
	services="app-service"
	documentationCenter=""
	authors="dariagrigoriu"
	manager="wpickett"
	editor="mollybos"/>

<tags
	ms.service="app-service"
	ms.workload="na"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="06/13/2016"
	ms.author="dariagrigoriu"/>
    
# Sincronizar o conteúdo de uma pasta de nuvem para o Serviço de Aplicativo do Azure

Este tutorial mostra como implantar o [Serviço de Aplicativo do Azure](http://go.microsoft.com/fwlink/?LinkId=529714) sincronizando o conteúdo de serviços populares de armazenamento em nuvem como o Dropbox e o OneDrive.

## <a name="overview"></a>Visão geral da implantação de sincronização de conteúdo

A implantação de sincronização de conteúdo sob demanda é proveniente da plataforma do [mecanismo de implantação do Kudu](https://github.com/projectkudu/kudu/wiki) integrado ao Serviço de Aplicativo. No [Portal do Azure](https://portal.azure.com), é possível designar uma pasta em seu armazenamento em nuvem, trabalhar com o código e o conteúdo do aplicativo nessa pasta e sincronizá-la com o Serviço de Aplicativo com apenas um clique. A sincronização de conteúdo utiliza o processo do Kudu para build e implantação.
    
## <a name="contentsync"></a>Como habilitar a implantação de sincronização de conteúdo
Para habilitar a sincronização de conteúdo por meio do [Portal do Azure](https://portal.azure.com), siga estas etapas:

1. Na folha do aplicativo no Portal do Azure, clique em **Configurações** > **Origem da Implantação**. Clique em **Escolher Origem** e selecione **OneDrive** ou **Dropbox** como a origem da implantação. 

    ![Sincronização de conteúdo](./media/app-service-deploy-content-sync/deployment_source.png)

2. Conclua o fluxo de trabalho de autorização para permitir que o Serviço de Aplicativo acesse um caminho designado predefinido específico para o OneDrive ou Dropbox no qual todo o conteúdo do Serviço de Aplicativo será armazenado. Após a autorização, a plataforma do Serviço de Aplicativo lhe dará a opção de criar uma pasta de conteúdo no caminho de conteúdo designado ou escolher uma pasta de conteúdo existente nesse caminho de conteúdo designado. Os caminhos de conteúdo designados em suas contas de armazenamento em nuvem usados para a sincronização do Serviço de Aplicativo são os seguintes:
    * **OneDrive**: `Apps\Azure Web Apps` 
    * **Dropbox**: `Dropbox\Apps\Azure`

3. Após a sincronização inicial de conteúdo, a sincronização de conteúdo poderá ser iniciada sob demanda no portal do Azure. O histórico de implantação está disponível na folha **Implantações**.

    ![Histórico de implantação](./media/app-service-deploy-content-sync/onedrive_sync.png)
 
Mais informações para a implantação do Dropbox estão disponíveis em [Implantar por meio do Dropbox](http://blogs.msdn.com/b/windowsazure/archive/2013/03/19/new-deploy-to-windows-azure-web-sites-from-dropbox.aspx).

<!---HONumber=AcomDC_0615_2016-->