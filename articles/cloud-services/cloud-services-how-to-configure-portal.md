<properties 
	pageTitle="Como configurar um serviço de nuvem - Azure" 
	description="Saiba como configurar serviços de nuvem no Azure. Saiba como atualizar a configuração do serviço de nuvem e configurar acesso remoto às instâncias de função." 
	services="cloud-services" 
	documentationCenter="" 
	authors="Thraka" 
	manager="timlt" 
	editor=""/>

<tags 
	ms.service="cloud-services" 
	ms.workload="tbd" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="06/29/2015"
	ms.author="adegeo"/>




# Como configurar serviços de nuvem

> [AZURE.SELECTOR]
- [Azure Portal](cloud-services-how-to-configure.md)
- [Azure Preview Portal](cloud-services-how-to-configure-portal.md)

Você pode definir as configurações usadas mais frequentemente para um Serviço de Nuvem no Portal de Gerenciamento do Azure. Ou então, se desejar atualizar diretamente seus arquivos de configuração, baixe um arquivo de configuração de serviço para atualizar e carregue o arquivo atualizado e atualize o serviço de nuvem com as alterações de configuração. De qualquer maneira, as atualizações da configuração são enviadas por push a todas as instâncias de função.

Você também pode habilitar uma conexão de área de trabalho remota para uma ou todas as funções em execução no Serviço de Nuvem. A área de trabalho remota permite que você acesse área de trabalho do seu aplicativo durante a execução e solucione e diagnostique problemas. Você pode habilitar uma conexão de área de trabalho remota para sua função mesmo sem ter configurado um arquivo de definição de serviço (.csdef) para a área de trabalho remota durante o desenvolvimento do aplicativo. Não é necessário reimplantar seu aplicativo para habilitar uma conexão de área de trabalho remota.

O Azure pode garantir apenas 99,95 por cento de disponibilidade do serviço durante as atualizações de configuração se você tiver, pelo menos, duas instâncias de função para cada função. Isso permite que uma máquina virtual processe as solicitações do cliente enquanto a outra é atualizada. Para obter mais informações, consulte [Contratos de Nível de Serviço](http://azure.microsoft.com/support/legal/sla/).

## Alterar um serviço de nuvem

1. No [Portal de Visualização do Azure](http://portal.azure.com/), navegue até seu serviço de nuvem.

2. Clique no ícone **Configurações** ou no link **Essentials/Todas as configurações** para abrir a folha **Configurações**.

    ![Página de configurações](./media/cloud-services-how-to-configure-portal/cloud-service.png)
    
    Aqui você pode exibir as **Propriedades**, alterar as **Configurações**, gerenciar os **Certificados**e gerenciar os **Usuários** que têm acesso a esse serviço de nuvem.

2. Na seção **Monitoramento**, você pode clicar em qualquer bloco para configurar alertas.

    ![Monitoramento de Serviço de Nuvem](./media/cloud-services-how-to-configure-portal/cs-monitoring.png)
    
3. Na seção **Funções e instâncias**, você pode clicar em qualquer função de serviço de nuvem para gerenciar a instância.

    ![Instância de Serviço de Nuvem](./media/cloud-services-how-to-configure-portal/cs-instance.png)
    
    Remotamente, você pode se conectar, reinicializar ou refazer a imagem do serviço de nuvem aqui.
    
    ![Botões de instância de serviço de nuvem](./media/cloud-services-how-to-configure-portal/cs-instance-buttons.png)

>[AZURE.NOTE]O sistema operacional usado para o serviço de nuvem não pode ser alterado usando o **Portal de Visualização do Azure**, só é possível alterar essa configuração por meio do [portal que não seja de visualização](http://manage.windowsazure.com/). Isso é detalhado [aqui](cloud-services-how-to-configure.md#update-a-cloud-service-configuration-file).

## Atualizar um arquivo de configuração de serviço de nuvem

1. Primeiramente, baixe o arquivo de configuração de serviço de nuvem de existente (.cscfg).

    1. No [Portal de Visualização do Azure](http://portal.azure.com/), navegue até seu serviço de nuvem.

    2. Clique no ícone **Configurações** ou no link **Essentials/Todas as configurações** para abrir a folha **Configurações**.

        ![Página de configurações](./media/cloud-services-how-to-configure-portal/cloud-service.png)
    
    3. Clique no item **Configurações**.

        ![Folha de configuração](./media/cloud-services-how-to-configure-portal/cs-settings-config.png)
    
    4. Clique no botão **Baixar**.

        ![Baixar](./media/cloud-services-how-to-configure-portal/cs-settings-config-panel-download.png)

2. Após atualizar o arquivo de configuração de serviço, carregue e aplique as atualizações da configuração:

    1. Siga as três primeiras etapas acima para abrir a folha **Configurações** do serviço de nuvem.
    
    2. Clique no botão **Carregar**.

        ![Carregar](./media/cloud-services-how-to-configure-portal/cs-settings-config-panel-upload.png)
    
    3. Selecione o arquivo .cscfg e clique em **OK**.

## Configurar o acesso remoto para instâncias de função

O acesso remoto não pode ser configurado usando o **Portal de Visualização do Azure**, você só pode alterar essa configuração por meio de um [portal que não seja de visualização](http://manage.windowsazure.com/). Isso é detalhado [aqui](cloud-services-how-to-configure.md#configure-remote-access-to-role-instances).
			
 

<!---HONumber=July15_HO3-->