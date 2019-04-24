---
title: Introdução à Integração de Logs do Azure | Microsoft Docs
description: Saiba como instalar o serviço de Integração de Logs do Azure e como integrar logs do Armazenamento do Azure, dos Logs de auditoria do Azure e dos alertas da Central de Segurança do Azure.
services: security
documentationcenter: na
author: Barclayn
manager: barbkess
editor: TomShinder
ms.assetid: 53f67a7c-7e17-4c19-ac5c-a43fabff70e1
ms.service: security
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ums.workload: na
ms.date: 01/14/2019
ms.author: barclayn
ms.custom: azlog
ms.openlocfilehash: 244b2d1764f30f790c3e51e23cd2fa0af6375960
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60480147"
---
# <a name="azure-log-integration-with-azure-diagnostics-logging-and-windows-event-forwarding"></a>Integração de Logs do Azure com logs do Diagnóstico do Azure e encaminhamento de eventos do Windows


>[!IMPORTANT]
> O recurso Integração de log do Azure será preterido em 01/06/2019. Downloads de AzLog foram desabilitados em 27 de junho de 2018. Para obter diretrizes sobre o que fazer para prosseguir com a análise da postagem [Usar o Azure Monitor para a integração com ferramentas SIEM](https://azure.microsoft.com/blog/use-azure-monitor-to-integrate-with-siem-tools/) 

Você só deverá usar a integração de log do Azure se um conector do [Azure Monitor](../monitoring-and-diagnostics/monitoring-get-started.md) não estiver disponível de seu fornecedor de SIEM (Gerenciamento de Eventos e Informações de Segurança).

A Integração de Logs do Azure torna os logs do Azure disponíveis para o SIEM para que você crie um painel de segurança unificado para todos os seus ativos.
Para saber mais sobre o status de um conector do Azure Monitor, contate o fornecedor de SIEM.

> [!IMPORTANT]
> Se o seu interesse principal é coletar logs de máquina virtual, a maioria dos fornecedores de SIEM inclui essa opção em sua solução. O uso do conector do fornecedor SIEM sempre é a opção preferencial.

Este artigo ajuda você a começar a usar a Integração de Logs do Azure. Ele aborda a instalação do serviço de Integração de Logs do Azure e integra o serviço ao Diagnóstico do Azure. O serviço de Integração de Logs do Azure coleta informações de Log de Eventos do Windows do canal de Eventos de Segurança do Windows de máquinas virtuais implantadas em uma infraestrutura como serviço do Azure. Isso é semelhante ao *encaminhamento de eventos* que você pode usar em um sistema local.

> [!NOTE]
> A integração do resultado da Integração de Logs do Azure a um SIEM é feita pelo próprio SIEM. Para saber mais, confira [Integrar a Integração de Logs do Azure com o SIEM local](https://blogs.msdn.microsoft.com/azuresecurity/2016/08/23/azure-log-siem-configuration-steps/).

O serviço de Integração de Logs do Azure é executado em um computador físico ou virtual que execute o Windows Server 2008 R2 ou posterior (recomendamos o Windows Server 2016 ou o Windows Server 2012 R2).

Um computador físico pode ser executado localmente ou em um site de hospedagem. Se você optar por executar o serviço Integração de Logs do Azure em uma máquina virtual, ela poderá estar localizada localmente ou em uma nuvem pública, como o Microsoft Azure.

A máquina virtual ou o computador físico que executa o serviço Integração de Logs do Azure requer conectividade de rede com a nuvem pública do Azure. Este artigo fornece detalhes sobre a configuração necessária.

## <a name="prerequisites"></a>pré-requisitos

No mínimo, a instalação da Integração de Logs do Azure requer os seguintes itens:

* Uma **assinatura do Azure**. Se não tiver uma, você poderá se inscrever em uma [conta gratuita](https://azure.microsoft.com/free/).
* Uma **conta de armazenamento** que pode ser usada para logs de WAD (Diagnóstico do Azure para Windows). Você pode usar uma conta de armazenamento pré-configurada ou criar uma nova. Neste artigo, descreveremos como configurar a conta de armazenamento.

  > [!NOTE]
  > Dependendo do cenário, poderá não ser necessário ter uma conta de armazenamento. Para o cenário de Diagnóstico do Azure abordado neste artigo, uma conta de armazenamento é necessária.

* **Dois sistemas**: 
  * Um computador que executa o serviço de Integração de Logs do Azure. Este computador coleta todas as informações de log que são importadas para o SIEM mais tarde. Este sistema:
    * Pode ser local ou hospedado no Microsoft Azure.  
    * Deve estar executando uma versão x64 do Windows Server 2008 R2 SP1 ou posterior e ter o Microsoft .NET 4.5.1 instalado. Para determinar a versão do .NET instalada, confira [Determinar quais versões do .NET Framework estão instaladas](https://msdn.microsoft.com/library/hh925568).  
    * Deve ter conectividade com a conta de armazenamento do Azure que foi usada para o log do Diagnóstico do Azure. Neste artigo, descreveremos como confirmar a conectividade.
  * Um computador que você deseja monitorar. Trata-se de uma VM em execução como uma [máquina virtual do Azure](../virtual-machines/virtual-machines-windows-overview.md). As informações de log desse computador são enviadas à máquina de serviço de Integração de Logs do Azure.

Para ver uma demonstração rápida de como criar uma máquina virtual usando o portal do Azure, assista ao seguinte vídeo:<br /><br />


> [!VIDEO https://channel9.msdn.com/Blogs/Azure-Security-Videos/Azure-Log-Integration-Videos-Create-a-Virtual-Machine/player]

## <a name="deployment-considerations"></a>Considerações de implantação

Durante o teste, você pode usar qualquer sistema que atenda aos requisitos mínimos do sistema operacional. Para um ambiente de produção, a carga pode exigir que você planeje a expansão ou a redução.

Você pode executar várias instâncias do serviço de Integração de Logs do Azure. No entanto, você pode executar apenas uma instância do serviço por máquina virtual ou física. Além disso, você pode balancear a carga de contas de armazenamento de Diagnóstico do Azure para WAD. O número de assinaturas a serem fornecidas às instâncias baseia-se na sua capacidade.

> [!NOTE]
> No momento, não temos recomendações específicas sobre quando escalar horizontalmente as instâncias de computadores da Integração de Logs do Azure (ou seja, computadores que executam o serviço de Integração de Logs do Azure) ou para contas de armazenamento ou assinaturas. Tome decisões de dimensionamento com base em suas observações de desempenho em cada uma dessas áreas.

Para melhorar o desempenho, você também tem a opção de escalar verticalmente o serviço de Integração de Logs do Azure. As métricas de desempenho a seguir podem ajudar a dimensionar as máquinas que foram escolhidas para execução no serviço de Integração de Logs do Azure:

* Em uma máquina com oito processadores (núcleo), uma única instância de Integração de Logs do Azure pode processar cerca de 24 milhões de eventos por dia (cerca de um milhão de eventos por hora).
* Em um computador com quatro processadores (núcleo), uma única instância de Integração de Logs do Azure pode processar cerca de 1,5 milhão de eventos por dia (aproximadamente 62.500 eventos por hora).

## <a name="install-azure-log-integration"></a>Instalar a Integração de Logs do Azure

Executar a rotina de instalação. Escolha se as informações de telemetria devem ou não ser fornecidas à Microsoft.

O serviço de Integração de Logs do Azure coleta os dados telemétricos do computador no qual ele está instalado.  

Os dados telemétricos coletados incluem o seguinte:

* As exceções que ocorrem durante a execução da Integração de Logs do Azure.
* As métricas sobre o número de consultas e eventos processados.
* As estatísticas sobre quais opções de linha de comando do Azlog.exe são usadas. 

> [!NOTE]
> Recomendamos permitir que a Microsoft colete dados de telemetria. Você pode desativar a coleta de dados telemétricos desmarcando a caixa de seleção **Permitir que a Microsoft colete dados telemétricos**.
>

![Captura de tela do painel de instalação, com a caixa de seleção da telemetria marcada](./media/security-azure-log-integration-get-started/telemetry.png)


O processo de instalação é abordado no seguinte vídeo:<br /><br />

> [!VIDEO https://channel9.msdn.com/Blogs/Azure-Security-Videos/Azure-Log-Integration-Videos-Install-Azure-Log-Integration/player]

## <a name="post-installation-and-validation-steps"></a>Etapas pós-instalação e validação

Após concluir a configuração básica, você está pronto para executar as etapas de pós-instalação e validação:

1. Abra o PowerShell como administrador. Vá para C:\Arquivos de Programas\Integração de logs do Microsoft Azure.
2. Importe os cmdlets de Integração de Logs do Azure. Para importar os cmdlets, execute o script `LoadAzlogModule.ps1`. Insira `.\LoadAzlogModule.ps1` e pressione Enter (observe o uso de **.\\** nesse comando). Você verá algo parecido com o que é exibido na seguinte figura:

   ![Captura de tela da saída do comando LoadAzlogModule.ps1](./media/security-azure-log-integration-get-started/loaded-modules.png)
3. Em seguida, configure a Integração de Logs do Azure para usar um ambiente do Azure específico. Um *ambiente do Azure* é o tipo de datacenter na nuvem do Azure com o qual você deseja trabalhar. Embora existam vários ambientes do Azure, atualmente as opções relevantes são **AzureCloud** ou **AzureUSGovernment**. Executando o PowerShell como administrador, verifique se você está em C:\Arquivos de Programas\Integração de logs do Microsoft Azure\. Em seguida, execute este comando:

   `Set-AzlogAzureEnvironment -Name AzureCloud` (para **AzureCloud**)
  
   Se você quiser usar a nuvem do US Government Azure, use **AzureUSGovernment** para a variável **-Name**. No momento, não há suporte para outras nuvens do Azure.  

   > [!NOTE]
   > Você não recebe feedback quando o comando for executado com êxito. 

4. Para poder monitorar um sistema, você precisa do nome da conta de armazenamento usada no Diagnóstico do Azure. No portal do Azure, vá para **Máquinas virtuais**. Procure por uma máquina virtual do Windows que você irá monitorar. Na seção **Propriedades**, selecione **Configurações de Diagnóstico**.  Em seguida, selecione **Agente**. Anote o nome da conta de armazenamento especificada. Você precisa desse nome de conta para uma etapa posterior.

   ![Captura de tela do painel de Configurações de Diagnóstico do Azure](./media/security-azure-log-integration-get-started/storage-account-large.png) 

   ![Captura de tela do botão Habilitar o monitoramento em nível de convidado](./media/security-azure-log-integration-get-started/azure-monitoring-not-enabled-large.png)

   > [!NOTE]
   > Se o monitoramento não tiver sido habilitado quando a máquina virtual foi criada, você poderá habilitá-lo conforme mostrado na imagem anterior.

5. Agora, volte para a máquina de Integração de Logs do Azure. Verifique se você tem conectividade com a conta de armazenamento do sistema em que você instalou a Integração de Logs do Azure. O computador que executa o serviço Integração de Logs do Azure precisa acessar a conta de armazenamento para recuperar informações registradas pelo Diagnóstico do Azure em cada um dos sistemas monitorados. Para verificar a conectividade: 
   1. [Baixar o Gerenciador de Armazenamento do Microsoft Azure](https://storageexplorer.com/).
   2. Conclua a instalação.
   3. Quando a instalação for concluída, selecione **Avançar**. Deixe a caixa de seleção **Iniciar o Gerenciador de Armazenamento do Microsoft Azure** marcada.  
   4. Entre no Azure.
   5. Verifique se você pode ver a conta de armazenamento que configurou para o Diagnóstico do Azure: 

   ![Captura de tela de contas de armazenamento no Gerenciador de Armazenamento](./media/security-azure-log-integration-get-started/storage-explorer.png)

   1. Algumas opções são exibidas em contas de armazenamento. Em **Tabelas**, você deverá ver uma tabela chamada **WADWindowsEventLogsTable**.

   Se o monitoramento não foi habilitado quando a máquina virtual foi criada, você pode habilitá-lo conforme descrito anteriormente.


## <a name="integrate-windows-vm-logs"></a>Integrar os Logs de VM do Windows

Nesta etapa, você configura o computador que executa o serviço Integração de Logs do Azure para se conectar à conta de armazenamento que contém os arquivos de log.

Para concluir esta etapa, você precisa de alguns itens:  
* **FriendlyNameForSource**: um nome amigável que você pode aplicar à conta de armazenamento configurada na máquina virtual para armazenar informações de Diagnóstico do Azure.
* **StorageAccountName**: o nome da conta de armazenamento que você especificou ao configurar o Diagnóstico do Azure.  
* **StorageKey**: a chave de armazenamento da conta de armazenamento em que as informações do Diagnóstico do Azure são armazenadas para esta máquina virtual.  

Para obter a chave de armazenamento, conclua as seguintes etapas:
1. Vá para o [Portal do Azure](https://portal.azure.com).
2. No painel de navegação, selecione **Todos os serviços**.
3. Na caixa **Filtro**, digite **Armazenamento**. Em seguida, selecione **Contas de armazenamento**.

   ![Captura de tela que mostra as contas de armazenamento em Todos os serviços](./media/security-azure-log-integration-get-started/filter.png)

4. É exibida uma lista de contas de armazenamento. Clique duas vezes na conta que você atribuiu ao armazenamento de logs.

   ![Captura de tela que mostra uma lista de contas de armazenamento](./media/security-azure-log-integration-get-started/storage-accounts.png)

5. Em **Configurações**, selecione **Chaves de acesso**.

   ![Captura de tela que mostra a opção de Chaves de acesso no menu](./media/security-azure-log-integration-get-started/storage-account-access-keys.png)

6. Copie **key1** e salve-o em um local seguro que você possa acessar para a etapa seguinte.
7. No servidor em que você instalou a Integração de Logs do Azure, abra uma janela de Prompt de Comando como administrador. (Abra uma janela de Prompt de Comando como administrador e não PowerShell).
8. Vá para C:\Arquivos de Programas\Integração de logs do Microsoft Azure.
9. Execute este comando: `Azlog source add <FriendlyNameForTheSource> WAD <StorageAccountName> <StorageKey>`.
 
   Exemplo:
  
   `Azlog source add Azlogtest WAD Azlog9414 fxxxFxxxxxxxxywoEJK2xxxxxxxxxixxxJ+xVJx6m/X5SQDYc4Wpjpli9S9Mm+vXS2RVYtp1mes0t9H5cuqXEw==`

   Se você quiser que a ID da assinatura apareça no XML do evento, acrescente a ID da assinatura ao nome amigável:

   `Azlog source add <FriendlyNameForTheSource>.<SubscriptionID> WAD <StorageAccountName> <StorageKey>`
  
   Exemplo:
  
   `Azlog source add Azlogtest.YourSubscriptionID WAD Azlog9414 fxxxFxxxxxxxxywoEJK2xxxxxxxxxixxxJ+xVJx6m/X5SQDYc4Wpjpli9S9Mm+vXS2RVYtp1mes0t9H5cuqXEw==`

> [!NOTE]
> Aguarde até 60 minutos e exiba os eventos extraídos da conta de armazenamento. Para exibir os eventos, na integração de Logs do Azure, selecione **Visualizador de Eventos** > **Logs do Windows** > **Eventos Encaminhados**.

O seguinte vídeo aborda as etapas anteriores:<br /><br />

> [!VIDEO https://channel9.msdn.com/Blogs/Azure-Security-Videos/Azure-Log-Integration-Videos-Enable-Diagnostics-and-Storage/player]


## <a name="if-data-isnt-showing-up-in-the-forwarded-events-folder"></a>Se os dados não estão aparecendo na pasta Eventos Encaminhados
Se os dados não forem mostrados na pasta Eventos Encaminhados após uma hora, execute estas etapas:

1. Verifique o computador que está executando o serviço de Integração de Logs do Azure. Confirme se ele pode acessar o Azure. Para testar a conectividade, em um navegador, tente ir para o [portal do Azure](https://portal.azure.com).
2. Verifique se a conta de usuário Azlog tem permissão de gravação para a pasta usuários\Azlog.
   1. Abra o Explorador de Arquivos.
   2. Vá para C:\usuários.
   3. Clique com o botão direito do mouse em C:\usuários\Azlog.
   4. Selecione **Segurança**.
   5. Selecione **Service\Azlog NT**. Verifique as permissões da conta. Se a conta estiver ausente dessa guia ou se não mostrar as permissões apropriadas, você poderá conceder as permissões de conta nessa guia.
3. Quando você executar o comando `Azlog source list`, verifique se a conta de armazenamento que foi adicionada no comando `Azlog source add` está listada na saída.
4. Para ver se são relatados erros do serviço de Integração de Logs do Azure, vá para **Visualizador de Eventos** > **Logs do Windows** > **Aplicativo**.

Se tiver problemas durante a instalação e configuração, você poderá criar uma [solicitação de suporte](../azure-supportability/how-to-create-azure-support-request.md). Para o serviço, selecione **Integração de Logs**.

Outra opção de suporte é o [Fórum do MSDN de Integração de Logs do Azure](https://social.msdn.microsoft.com/Forums/home?forum=AzureLogIntegration). No Fórum do MSDN, a comunidade pode fornecer suporte respondendo a perguntas e compartilhando dicas e truques sobre como aproveitar ao máximo a Integração de Logs do Azure. A equipe de Integração de Logs do Azure também monitora esse fórum. Eles ajudam sempre que possível.

## <a name="integrate-azure-activity-logs"></a>Integrar os logs de atividades do Azure

O Log de Atividades do Azure é um log de assinatura que fornece informações sobre eventos no nível da assinatura que ocorreram no Azure. Isso inclui um intervalo de dados, de dados operacionais do Azure Resource Manager para atualizações em eventos de Integridade do Serviço. Os alertas da Central de Segurança do Azure também estão incluídos nesse Log.
> [!NOTE]
> Antes de experimentar as etapas neste artigo, você deve examinar o artigo [Introdução](security-azure-log-integration-get-started.md) e concluir as etapas dele.

### <a name="steps-to-integrate-azure-activity-logs"></a>Etapas para integrar os logs de atividades do Azure

1. Abra o prompt de comando e execute este comando: ```cd c:\Program Files\Microsoft Azure Log Integration```
2. Execute este comando: ```azlog createazureid```

    Esse comando solicitará o logon do Azure. O comando cria uma entidade de serviço do Azure Active Directory em locatários do Azure AD que hospedam as assinaturas do Azure nas quais o usuário conectado é um administrador, um coadministrador ou um proprietário. O comando falhará se o usuário conectado for apenas um usuário convidado no locatário do Azure AD. A autenticação do Azure é feita por meio do AD do Azure. A criação de uma entidade de serviço para a Integração de Logs do Azure criará a identidade do Azure AD que receberá o acesso de leitura das assinaturas do Azure.
3. Execute o seguinte comando para autorizar o acesso da entidade de serviço Integração de Logs do Azure criada na etapa anterior à leitura do Log de Atividades da assinatura. Você precisa ser um Proprietário na assinatura para executar o comando.

   ```Azlog.exe authorize subscriptionId``` Exemplo:

   ```AZLOG.exe authorize ba2c2367-d24b-4a32-17b5-4443234859```

4. Verifique as seguintes pastas para confirmar se os arquivos JSON do Log de Auditoria do Azure Active Directory são criados nelas:
   - C:\Usuários\azlog\AzureResourceManagerJson
   - C:\Usuários\azlog\AzureResourceManagerJsonLD

> [!NOTE]
> Para obter instruções específicas sobre como colocar as informações nos arquivos JSON em seu SIEM (sistema de gerenciamento de eventos e informações de segurança), contate o fornecedor do SIEM.

Ajuda da comunidade está disponível por meio de [Fórum do MSDN de integração de Log do Azure](https://social.msdn.microsoft.com/Forums/office/home?forum=AzureLogIntegration). Este fórum permite que as pessoas na comunidade de Integração de Log do Azure deem suporte umas às outras com perguntas, respostas, dicas e truques. Além disso, a equipe de Integração de Logs do Azure monitora esse fórum e ajuda sempre que possível.

Você também pode abrir uma [solicitação de suporte](../azure-supportability/how-to-create-azure-support-request.md). Selecione a Integração de Log como o serviço para o qual você está solicitando suporte.

## <a name="next-steps"></a>Próximos passos

Para saber mais sobre a Integração de Logs do Azure, consulte os seguintes artigos: Antes de experimentar as etapas neste artigo, você deve examinar o artigo Introdução e concluir as etapas dele.

* [Introdução à Integração de Logs do Azure](security-azure-log-integration-overview.md). Este documento apresenta a Integração de Logs do Azure, seus principais recursos e como ele funciona.
* [Etapas de configuração de parceiro](https://blogs.msdn.microsoft.com/azuresecurity/2016/08/23/azure-log-siem-configuration-steps/). Essa postagem de blog mostra a você como configurar a Integração de Logs do Azure para trabalhar com as soluções de parceiros Splunk, HP ArcSight e IBM QRadar. Descreve nossas diretrizes atuais sobre como configurar os componentes do SIEM. Entre em contato com seu fornecedor do SIEM para obter detalhes adicionais.
* [Perguntas frequentes sobre a Integração de Logs do Azure](security-azure-log-integration-faq.md). Este artigo de perguntas comuns responde às perguntas sobre a Integração de Logs do Azure.
* [Integração de alertas da Central de Segurança do Azure à Integração de Logs do Azure](../security-center/security-center-integrating-alerts-with-log-integration.md). Este artigo mostra como sincronizar alertas do Centro de Segurança e eventos de segurança de máquina virtual que são coletados pelo Diagnóstico do Azure e logs de atividade do Azure. Você pode sincronizar os logs usando o Azure Monitor logs ou solução SIEM.
* [Novos recursos para o Diagnóstico do Azure e os logs de auditoria do Azure](https://azure.microsoft.com/blog/new-features-for-azure-diagnostics-and-azure-audit-logs/). Esta postagem de blog apresenta os logs de auditoria do Azure e outros recursos que podem ajudá-lo a obter informações sobre as operações de recursos do Azure.
