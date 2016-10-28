<properties 
	pageTitle="Como configurar um serviço de nuvem (portal) | Microsoft Azure" 
	description="Saiba como configurar serviços de nuvem no Azure. Saiba como atualizar a configuração do serviço de nuvem e configurar acesso remoto às instâncias de função. Esses exemplos usam o portal do Azure." 
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
	ms.date="07/27/2016"
	ms.author="adegeo"/>

# Como configurar serviços de nuvem

> [AZURE.SELECTOR]
- [Portal do Azure](cloud-services-how-to-configure-portal.md)
- [Portal clássico do Azure](cloud-services-how-to-configure.md)

Você pode definir as configurações usadas mais frequentemente para um Serviço de Nuvem no portal do Azure. Ou então, se desejar atualizar diretamente seus arquivos de configuração, baixe um arquivo de configuração de serviço para atualizar e carregue o arquivo atualizado e atualize o serviço de nuvem com as alterações de configuração. De qualquer maneira, as atualizações da configuração são enviadas por push a todas as instâncias de função.

Você também pode gerenciar as instâncias de suas funções de serviço de nuvem ou da área de trabalho remota para elas.

O Azure pode garantir apenas 99,95 por cento de disponibilidade do serviço durante as atualizações de configuração se você tiver, pelo menos, duas instâncias de função para cada função. Isso permite que uma máquina virtual processe as solicitações do cliente enquanto a outra é atualizada. Para obter mais informações, consulte [Contratos de Nível de Serviço](https://azure.microsoft.com/support/legal/sla/).

## Alterar um serviço de nuvem

Após abrir o [Portal do Azure](https://portal.azure.com/), navegue até seu serviço de nuvem. Daqui, você gerencia muitos aspectos dele.

![Página de configurações](./media/cloud-services-how-to-configure-portal/cloud-service.png)

Os links **Configurações** ou **Todas as configurações** abrirão a folha **Configurações**, na qual você pode alterar as **Propriedades** e a **Configuração**, gerenciar os **Certificados**, instalar **Regras de alerta** e gerenciar os **Usuários** que têm acesso a esse serviço de nuvem.

![Folha de configurações do serviço de nuvem do Azure](./media/cloud-services-how-to-configure-portal/cs-settings-blade.png)

>[AZURE.NOTE]
O sistema operacional usado para o serviço de nuvem não pode ser alterado usando o **portal do Azure**, só é possível alterar essa configuração por meio do [portal clássico do Azure](http://manage.windowsazure.com/). Isso é detalhado [aqui](cloud-services-how-to-configure.md#update-a-cloud-service-configuration-file).

## Monitoramento

Você pode adicionar alertas para o seu serviço de nuvem. Clique em **Configurações** > **Regras de alerta** > **Adicionar alerta**.

![](./media/cloud-services-how-to-configure-portal/cs-alerts.png)

Daqui, você pode configurar um alerta. Com a caixa suspensa **Métrica**, você pode configurar um alerta para os seguintes tipos de dados.

- Leitura de disco
- Gravação de disco
- Rede no
- Limite de rede
- Percentual de CPU

![](./media/cloud-services-how-to-configure-portal/cs-alert-item.png)

### Configurar o monitoramento de um bloco de métrica

Em vez de usar **Configurações** > **Regras de alerta**, você pode clicar em um dos blocos de métrica na seção **Monitoramento** da folha **Serviço de nuvem**.

![Monitoramento de Serviço de Nuvem](./media/cloud-services-how-to-configure-portal/cs-monitoring.png)

Daqui, você pode personalizar o gráfico usado com o bloco ou adicionar uma regra de alerta.


## Reinicializar, refazer imagem ou a área de trabalho remota

Neste momento, você não pode configurar a área de trabalho remota usando o **Portal do Azure**. No entanto, você pode defini-la por meio do [Portal clássico do Azure](cloud-services-role-enable-remote-desktop.md), do [PowerShell](cloud-services-role-enable-remote-desktop-powershell.md) ou do [Visual Studio](../vs-azure-tools-remote-desktop-roles.md).

Primeiro, clique na instância do serviço de nuvem.

![Instância de Serviço de Nuvem](./media/cloud-services-how-to-configure-portal/cs-instance.png)

Na folha que é aberta, você pode iniciar uma conexão de área de trabalho remota, reinicializar a instância ou refazer a imagem da instância remotamente (inicia com uma imagem atualizada).

![Botões de instância de serviço de nuvem](./media/cloud-services-how-to-configure-portal/cs-instance-buttons.png)



## Reconfigurar seu .cscfg

Talvez seja necessário reconfigurar o serviço de nuvem por meio do arquivo de [configuração de serviço (cscfg)](cloud-services-model-and-package.md#cscfg). Primeiro, você precisa baixar o arquivo .cscfg, modificá-lo e carregá-lo.

1. Clique no ícone **Configurações** ou no link **Todas as configurações** para abrir a folha **Configurações**.

    ![Página de configurações](./media/cloud-services-how-to-configure-portal/cloud-service.png)

2. Clique no item **Configurações**.

    ![Folha de configuração](./media/cloud-services-how-to-configure-portal/cs-settings-config.png)

3. Clique no botão **Baixar**.

    ![Baixar](./media/cloud-services-how-to-configure-portal/cs-settings-config-panel-download.png)

4. Após atualizar o arquivo de configuração de serviço, carregue e aplique as atualizações da configuração:

    ![Carregar](./media/cloud-services-how-to-configure-portal/cs-settings-config-panel-upload.png)
    
5. Selecione o arquivo .cscfg e clique em **OK**.

			
## Próximas etapas

* Saiba como [implantar um serviço de nuvem](cloud-services-how-to-create-deploy-portal.md).
* Configurar um [nome de domínio personalizado](cloud-services-custom-domain-name-portal.md).
* [Gerenciar seu serviço de nuvem](cloud-services-how-to-manage-portal.md).
* Configurar [certificados SSL](cloud-services-configure-ssl-certificate-portal.md).

<!---HONumber=AcomDC_0803_2016-->