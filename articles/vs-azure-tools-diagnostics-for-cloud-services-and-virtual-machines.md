---
title: "Configurar diagnóstico para máquinas virtuais e Serviços de Nuvem do Azure | Microsoft Docs"
description: "Saiba como configurar o diagnóstico para depurar os serviços de nuvem do Azure e VMs (máquinas virtuais) no Visual Studio."
services: visual-studio-online
documentationcenter: na
author: mikejo
manager: ghogen
editor: 
ms.assetid: e70cd7b4-6298-43aa-adea-6fd618414c26
ms.service: multiple
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: multiple
ms.date: 11/11/2016
ms.author: mikejo
ms.openlocfilehash: f00771d89749e7507d7f303f366fe63f537900ff
ms.sourcegitcommit: b83781292640e82b5c172210c7190cf97fabb704
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/27/2017
---
# <a name="set-up-diagnostics-for-azure-cloud-services-and-virtual-machines"></a>Configurar diagnóstico para máquinas virtuais e Serviços de Nuvem do Azure
Quando você precisa solucionar problemas de uma máquina virtual ou serviço de nuvem do Azure, pode usar o Visual Studio para configurar mais facilmente o Diagnóstico do Azure. O diagnóstico captura dados do sistema e dados de log em máquinas virtuais e instâncias de máquinas virtuais que executam o serviço de nuvem. Dados de diagnóstico são transferidos para uma conta de armazenamento que você escolhe. Para obter mais informações sobre o registro em log de diagnóstico no Azure, consulte [Habilitar o registro em log de diagnóstico para aplicativos Web no Serviço de Aplicativo do Azure](app-service/web-sites-enable-diagnostic-log.md).

Neste artigo, mostramos como usar o Visual Studio para ativar e configurar o Diagnóstico do Azure, tanto antes quanto depois da implantação. Saiba como configurar o Diagnóstico em máquinas virtuais do Azure, como selecionar os tipos de informações de diagnóstico a coletar e como exibir as informações depois de coletadas.

Você pode usar uma das opções a seguir para configurar o Diagnóstico do Azure:

* Alterar as configurações de diagnóstico na caixa de diálogo **Configuração de Diagnóstico** no Visual Studio. As configurações são salvas em um arquivo chamado diagnostics.wadcfgx (no SDK do Azure 2.4 e anteriores, o arquivo é chamado diagnostics.wadcfg). Você também pode modificar diretamente o arquivo de configuração. Se você atualizar manualmente o arquivo, as alterações de configuração entrarão em vigor na próxima vez que você implantar o serviço de nuvem no Azure ou executar o serviço no emulador.
* Use o Cloud Explorer ou o Gerenciador de Servidores no Visual Studio para alterar as configurações de diagnóstico para um serviço de nuvem ou máquina virtual em execução.

## <a name="azure-sdk-26-diagnostics-changes"></a>Alterações de diagnóstico do SDK do Azure 2.6
As alterações a seguir se aplicam a projetos do SDK do Azure 2.6 e mais recentes no Visual Studio:

* O emulador local agora dá suporte a diagnósticos. Isso significa que você pode coletar dados de diagnóstico e garantir que seu aplicativo crie os rastreamentos certos enquanto você desenvolve e testa no Visual Studio. A cadeia de conexão `UseDevelopmentStorage=true` habilita a coleta de dados de diagnóstico durante a execução do seu projeto de serviço de nuvem no Visual Studio usando o emulador de armazenamento do Azure. Todos os dados de diagnóstico são coletados na conta de armazenamento do Armazenamento de Desenvolvimento.
* A cadeia de conexão da conta de armazenamento de diagnóstico `Microsoft.WindowsAzure.Plugins.Diagnostics.ConnectionString` é armazenada no arquivo de configuração (.cscfg) do serviço. No SDK do Azure 2.5, a conta de armazenamento de diagnóstico foi especificada no arquivo diagnostics.wadcfgx.

A cadeia de conexão funciona de maneira diferente de algumas maneiras principais no SDK do Azure 2.6 e posteriores versus Azure SDK 2.4 e anteriores:

* No SDK do Azure 2.4 e anteriores, a cadeia de conexão é usada como um tempo de execução pelo plug-in de diagnóstico para obter as informações de conta de armazenamento para transferir os logs de diagnóstico.
* No SDK do Azure 2.6 e posteriores, o Visual Studio usa a cadeia de conexão de diagnóstico para configurar a Extensão de Diagnóstico do Azure com as informações da conta de armazenamento apropriadas durante a publicação. Você pode usar a cadeia de conexão para definir diferentes contas de armazenamento para diferentes configurações de serviço que o Visual Studio usa durante a publicação. No entanto, como o plug-in de diagnóstico não está disponível (após o SDK do Azure 2.5), o arquivo .cscfg sozinho por si só não é capaz de configurar a extensão de diagnóstico. Você deve configurar a extensão separadamente usando ferramentas como Visual Studio ou PowerShell.
* Para simplificar o processo de configuração da extensão de diagnóstico usando o PowerShell, a saída do pacote do Visual Studio inclui o XML de configuração pública para a extensão de diagnóstico para cada função. O Visual Studio usa a cadeia de conexão de diagnóstico para preencher as informações da conta de armazenamento na configuração pública. Os arquivos de configuração pública são criados na pasta Extensões. Os arquivos de configuração pública usam o padrão de nomenclatura PaaSDiagnostics.&lt;nome da função\>.PubConfig.xml. Quaisquer implantações baseadas em PowerShell podem usar esse padrão para mapear cada configuração para uma função.
* O [portal do Azure](http://go.microsoft.com/fwlink/p/?LinkID=525040) usa a cadeia de conexão no arquivo .cscfg para acessar os dados de diagnóstico. Os dados aparecem na guia **Monitoramento**. A cadeia de conexão é necessária para configurar o serviço para mostrar dados do monitoramento detalhado no portal.

## <a name="migrate-projects-to-azure-sdk-26-and-later"></a>Migre projetos para o SDK do Azure 2.6 e posteriores
Ao migrar do SDK do Azure 2.5 para o SDK do Azure 2.6 ou posterior, se você tiver uma conta de armazenamento de diagnóstico especificada no arquivo .wadcfgx, a conta de armazenamento permanecerá naquele arquivo. Para aproveitar a flexibilidade de usar diferentes contas de armazenamento para diferentes configurações de armazenamento, adicione manualmente a cadeia de conexão ao seu projeto. Se estiver migrando um projeto do SDK do Azure 2.4 ou anterior para o SDK do Azure 2.6, as cadeias de conexão de diagnóstico preservadas. No entanto, observe as alterações em como cadeias de caracteres de conexão são tratadas no SDK do Azure 2.6, descritas na seção anterior.

### <a name="how-visual-studio-determines-the-diagnostics-storage-account"></a>Como o Visual Studio determina a conta de armazenamento de diagnóstico
* Se uma cadeia de conexão de diagnóstico for especificada no arquivo .cscfg, o Visual Studio a usará para configurar a extensão de diagnóstico ao publicar e gerar os arquivos XML de configuração pública durante o empacotamento.
* Se uma cadeia de conexão de diagnóstico não for especificada no arquivo .cscfg, o Visual Studio voltará a usar a conta de armazenamento especificada no arquivo .wadcfgx para configurar a extensão de diagnóstico durante a publicação e para o gerar os arquivos XML de configuração pública durante o empacotamento.
* A cadeia de conexão de diagnóstico no arquivo .cscfg tem precedência sobre a conta de armazenamento no arquivo .wadcfgx. Se uma cadeia de conexão de diagnóstico for especificada no arquivo .cscfg, o Visual Studio usará essa cadeia de conexão e ignorará a conta de armazenamento em .wadcfgx.

### <a name="what-does-the-update-development-storage-connection-strings-check-box-do"></a>O que faz a caixa de seleção “Atualizar cadeias de conexão do armazenamento de desenvolvimento…”?
A caixa de seleção **Atualizar cadeias de conexão do armazenamento de desenvolvimento para Diagnóstico e Cache com credenciais de conta de armazenamento do Microsoft Azure ao publicar no Microsoft Azure** oferece uma maneira conveniente de atualizar qualquer cadeia de conexão de conta de armazenamento de desenvolvimento com a conta de armazenamento do Azure especificada durante a publicação.

Por exemplo, se você marcar essa caixa de seleção e a cadeia de conexão de diagnóstico especificar `UseDevelopmentStorage=true`, quando você publicar o projeto no Azure, o Visual Studio automaticamente atualizará a cadeia de conexão de diagnóstico com a conta de armazenamento especificada no assistente de Publicação. No entanto, se uma conta de armazenamento real tiver sido especificada como a cadeia de conexão de diagnóstico, essa conta será usada.

## <a name="diagnostics-functionality-differences-in-azure-sdk-24-and-earlier-vs-azure-sdk-25-and-later"></a>Diferenças de funcionalidade de diagnóstico no SDK do Azure 2.4 e anteriores vs. SDK do Azure 2.5 e posteriores
Se você estiver atualizando seu projeto do SDK do Azure 2.4 e anteriores para o SDK do Azure 2.5 ou posterior, lembre-se destas diferenças de funcionalidade de diagnóstico:

* **As APIs de configuração são preteridas**. Configuração programática de diagnóstico está disponível no SDK do Azure 2.4 e anteriores, mas foi preterida no SDK do Azure 2.5 e posteriores. Se sua configuração de diagnóstico estiver definida atualmente no código, você precisará reconfigurá-las do zero no projeto migrado para que o diagnóstico continue a funcionar. O arquivo de configuração de diagnóstico para o SDK do Azure 2.4 é diagnostics.wadcfg. O arquivo de configuração de diagnóstico para o SDK do Azure 2.5 e posteriores é diagnostics.wadcfgx.
* **O diagnóstico para aplicativos de serviço de nuvem pode ser configurado somente no nível de função**. No SDK do Azure 2.5 e posteriores, você não pode configurar o diagnóstico para aplicativos de serviço de nuvem no nível de instância.
* **Toda vez que você implanta seu aplicativo, a configuração de diagnóstico é atualizada**. Isso poderá causar problemas de paridade se você alterar as configurações de diagnóstico no Gerenciador de Servidores do Visual Studio e, em seguida, reimplantá-las no seu aplicativo.
* **No SDK do Azure 2.5 e posteriores, despejos são configurados no arquivo de configuração de diagnóstico e não no código**. Se você tiver despejos configurados no código, deverá transferir manualmente a configuração do código para o arquivo de configuração. Despejos não são transferidos durante a migração para o SDK do Azure 2.6.

## <a name="turn-on-diagnostics-in-cloud-service-projects-before-you-deploy-them"></a>Ativar o diagnóstico em projetos de serviço de nuvem antes de implantá-los
No Visual Studio, você pode coletar dados de diagnóstico para funções executadas no Azure quando executa o serviço no emulador antes da implantação. Todas as alterações às configurações de diagnóstico no Visual Studio são salvas no arquivo de configuração diagnostics.wadcfgx. Essas configurações especificam a conta de armazenamento na qual os dados de diagnóstico são salvos quando você implanta seu serviço de nuvem.

[!INCLUDE [cloud-services-wad-warning](../includes/cloud-services-wad-warning.md)]

### <a name="to-turn-on-diagnostics-in-visual-studio-before-deployment"></a>Para habilitar o diagnóstico no Visual Studio antes da implantação

1. No menu de atalho para a função, selecione **Propriedades**. Na caixa de diálogo **Propriedades** da função, selecione a guia **Configuração**.
2. Na seção **Diagnóstico**, verifique se a caixa de seleção **Habilitar Diagnóstico** está marcada.
   
    ![Acesse a opção Habilitar Diagnóstico](./media/vs-azure-tools-diagnostics-for-cloud-services-and-virtual-machines/IC796660.png)
3. Para especificar a conta de armazenamento para os dados de diagnóstico, selecione o botão de reticências (…).
   
    ![Especifique a conta de armazenamento a ser usada](./media/vs-azure-tools-diagnostics-for-cloud-services-and-virtual-machines/IC796661.png)
4. Na caixa de diálogo **Criar Cadeia de Conexão de Armazenamento**, especifique se você deseja se conectar usando o emulador de armazenamento do Azure, uma assinatura do Azure ou credenciais inseridas manualmente.
   
    ![Caixa de diálogo Conta de armazenamento](./media/vs-azure-tools-diagnostics-for-cloud-services-and-virtual-machines/IC796662.png)
   
   * Se você selecionar **Emulador de armazenamento do Microsoft Azure**, a cadeia de conexão será definida como `UseDevelopmentStorage=true`.
   * Se você selecionar **Sua assinatura**, você poderá selecionar a assinatura do Azure que você deseja usar e digitar um nome de conta. Para gerenciar suas assinaturas do Azure, selecione **Gerenciar Contas**.
   * Se você selecionar **Credenciais inseridas manualmente**, digite o nome e a chave da conta do Azure que você deseja usar.
5. Para exibir a caixa de diálogo **Configuração de diagnóstico**, selecione **Configurar**. Exceto por **Geral** e **Diretórios de log**, cada guia representa uma fonte de dados de diagnóstico que você pode coletar. A guia padrão **Geral** oferece as seguintes opções de coleta de dados de diagnóstico: **Somente erros**, **Todas as informações** e **Plano personalizado**. A opção padrão **Somente erros** usa a menor quantidade de armazenamento porque não transfere avisos nem mensagens de rastreamento. A opção **Todas as informações** transfere a maioria das informações, usa o máximo de armazenamento e, portanto, é a opção mais cara.
   
    ![Habilitar a configuração e diagnóstico do Azure](./media/vs-azure-tools-diagnostics-for-cloud-services-and-virtual-machines/IC758144.png)
6. Para este exemplo, selecione a opção **Plano personalizado** para que você possa personalizar os dados coletados.
7. Na caixa **Cota de disco em MB**, você pode definir quanto espaço alocar na sua conta de armazenamento para dados de diagnóstico. Você pode alterar ou aceitar o valor padrão.
8. Em cada guia de dados de diagnóstico que você deseja coletar, marque a caixa de seleção **Habilitar Transferência do \<tipo de log\>**. Por exemplo, se você quiser coletar logs de aplicativo, na guia **Logs de aplicativo**, marque a caixa de seleção **Habilitar transferência de Logs de Aplicativos**. Além disso, especifique todas as outras informações necessárias para cada tipo de dados de diagnóstico. Para obter informações de configuração para cada guia, consulte a seção **Configurar fontes de dados de diagnóstico** posteriormente neste artigo. 
9. Depois de habilitar a coleção de todos os dados de diagnóstico que você desejar, escolha o botão **OK**.
10. Execute seu projeto de serviço de nuvem do Azure no Visual Studio, como de costume. Conforme você usa seu aplicativo, as informações de log habilitadas são salvas na conta de armazenamento do Azure especificada.

## <a name="turn-on-diagnostics-on-azure-virtual-machines"></a>Ativar o diagnóstico em máquinas virtuais do Azure
No Visual Studio, você pode coletar dados de diagnóstico para máquinas virtuais do Azure.

### <a name="to-turn-on-diagnostics-on-azure-virtual-machines"></a>Para ativar o diagnóstico em máquinas virtuais do Azure

1. No Gerenciador de Servidores, selecione o nó do Azure e conecte-se à sua assinatura do Azure, se você ainda não estiver conectado.
2. Expanda o nó **Máquinas virtuais** . Você pode criar uma nova máquina virtual ou selecionar um nó existente.
3. No menu de atalho para a máquina virtual que você deseja, selecione **Configurar**. A caixa de diálogo de configuração da máquina virtual será exibida.
   
    ![Configure uma máquina virtual do Azure](./media/vs-azure-tools-diagnostics-for-cloud-services-and-virtual-machines/IC796663.png)
4. Se ainda não estiver instalado, adicione a extensão do diagnóstico do Microsoft Monitoring Agent. Com essa extensão, você pode coletar dados de diagnóstico para a máquina virtual do Azure. Em **Extensões instaladas**, na caixa de listagem suspensa **Selecionar uma extensão disponível**, selecione **Diagnóstico do Microsoft Monitoring Agent**.
   
    ![Instalar uma extensão de máquina virtual do Azure](./media/vs-azure-tools-diagnostics-for-cloud-services-and-virtual-machines/IC766024.png)
   
    > [!NOTE]
   > Outras extensões de diagnóstico estão disponíveis para as máquinas virtuais. Para obter mais informações, consulte [Recursos e extensões da máquina virtual para Windows](https://docs.microsoft.com/azure/virtual-machines/windows/extensions-features).
   > 
   > 
5. Para adicionar a extensão e exibir sua caixa de diálogo **Configuração de diagnóstico**, selecione **Adicionar**.
6. Para especificar uma conta de armazenamento, selecione **Configurar** e, em seguida, selecione **OK**.
   
    Cada guia (exceto **Geral** e **Diretórios de Log**) representa uma fonte de dados de diagnóstico que você pode coletar.
   
    ![Habilitar a configuração e diagnóstico do Azure](./media/vs-azure-tools-diagnostics-for-cloud-services-and-virtual-machines/IC758144.png)
   
    A guia padrão, **Geral**, oferece as seguintes opções de coleta de dados de diagnóstico: **Somente erros**, **Todas as informações** e **Plano personalizado**. A opção padrão, **Somente erros**, traz a menor quantidade de armazenamento porque não transfere avisos ou mensagens de rastreamento. A opção **Todas as informações** transfere a maioria das informações e é, portanto, a opção mais cara em termos de armazenamento.
7. Neste exemplo, selecione a opção **Plano personalizado** , assim você pode personalizar os dados coletados.
8. A caixa **Cota de disco em MB** especifica a quantidade de espaço que você deseja alocar na sua conta de armazenamento para dados de diagnóstico. Você pode alterar o valor padrão se desejar.
9. Em cada guia de dados de diagnóstico que você deseja coletar, selecione a caixa de seleção **Habilitar Transferência de \<tipo de log\>**.
   
    Por exemplo, se você quiser coletar logs de aplicativo, selecione a caixa de seleção **Habilitar transferência dos Logs de aplicativo** da guia **Logs de aplicativos**. Além disso, especifique todas as outras informações necessárias para cada tipo de dados de diagnóstico. Para obter informações de configuração para cada guia, consulte a seção **Configurar fontes de dados de diagnóstico** posteriormente neste artigo.
10. Depois de habilitar a coleção de todos os dados de diagnóstico que você desejar, escolha o botão **OK**.
11. Salve o projeto atualizado.
    
    Uma mensagem na janela **Log de atividades do Microsoft Azure** indica que a máquina virtual foi atualizada.

## <a name="set-up-diagnostics-data-sources"></a>Configurar fontes de dados de diagnóstico
Após habilitar a coleção de dados de diagnóstico, você pode escolher exatamente quais fontes de dados que deseja coletar e quais informações são coletadas. As próximas seções descrevem as guias na caixa de diálogo **Configuração de Diagnóstico** e o que significa cada opção de configuração significa.

### <a name="application-logs"></a>Logs de aplicativo
Logs de aplicativo têm informações de diagnóstico que são geradas por um aplicativo Web. Por exemplo, se você quiser coletar logs de aplicativo, selecione a caixa de seleção **Habilitar transferência dos logs de aplicativo** . Para aumentar ou diminuir o intervalo entre a transferência de logs de aplicativo para sua conta de armazenamento, altere o valor do **Período de Transferência (min)**. Você pode também alterar a quantidade de informações capturadas no log definindo o valor de **Nível de log**. Por exemplo, selecione **Detalhado** para obter mais informações ou selecione **Crítico** para capturar somente erros críticos. Se você tiver um provedor de diagnósticos específico que emita os logs de aplicativo, poderá capturá-los adicionando o GUID do provedor na caixa **GUID do Provedor**.

  ![Logs de aplicativo](./media/vs-azure-tools-diagnostics-for-cloud-services-and-virtual-machines/IC758145.png)

Para obter mais informações sobre logs de aplicativo, consulte [Habilitar o registro em log de diagnóstico para aplicativos Web no Serviço de Aplicativo do Azure](app-service/web-sites-enable-diagnostic-log.md).

### <a name="windows-event-logs"></a>Logs de eventos do Windows
Para capturar logs de evento do Windows, marque a caixa de seleção **Habilitar transferência dos logs de evento do Windows**. Para aumentar ou diminuir o intervalo entre a transferência de logs de evento para sua conta de armazenamento, altere o valor do **Período de Transferência (min)**. Selecione as caixas de seleção para os tipos de eventos que você deseja controlar.

![Logs de eventos](./media/vs-azure-tools-diagnostics-for-cloud-services-and-virtual-machines/IC796664.png)

Se você estiver usando o SDK do Azure 2.6 ou posterior e quiser especificar uma fonte de dados personalizada, insira-a na caixa de texto **\<Nome da fonte de dados\>** e selecione **Adicionar**. A fonte de dados é adicionada ao arquivo diagnostics.cfcfg.

Se você estiver usando o SDK do Azure 2.5 e quiser especificar uma fonte de dados personalizada, poderá adicioná-la na seção `WindowsEventLog` do arquivo diagnostics.wadcfgx, como no exemplo a seguir:

```
<WindowsEventLog scheduledTransferPeriod="PT1M">
   <DataSource name="Application!*" />
   <DataSource name="CustomDataSource!*" />
</WindowsEventLog>
```
### <a name="performance-counters"></a>Contadores de desempenho
As informações do contador de desempenho podem ajudá-lo a localizar afunilamentos do sistema e ajustar o sistema e desempenho do aplicativo. Para obter mais informações, consulte [Criar e usar contadores de desempenho em um aplicativo do Azure](https://msdn.microsoft.com/library/azure/hh411542.aspx). Para capturar contadores de desempenho, marque a caixa de seleção **Habilitar a transferência de contadores de desempenho**. Para aumentar ou diminuir o intervalo entre a transferência de logs de evento para sua conta de armazenamento, altere o valor do **Período de Transferência (min)**. Selecione as caixas de seleção para os contadores de desempenho que você deseja controlar.

![Contadores de desempenho](./media/vs-azure-tools-diagnostics-for-cloud-services-and-virtual-machines/IC758147.png)

Para rastrear um contador de desempenho que não esteja listado, insira o contador de desempenho usando a sintaxe sugerida. Então selecione **Adicionar**. O sistema operacional na máquina virtual determina quais contadores de desempenho que você pode controlar. Para obter mais informações sobre a sintaxe, consulte [Especificar um caminho de contador](https://msdn.microsoft.com/library/windows/desktop/aa373193.aspx).

### <a name="infrastructure-logs"></a>Logs de infraestrutura
Logs de infraestrutura têm informações sobre a infraestrutura de diagnóstico do Azure, o módulo RemoteAccess e o módulo RemoteForwarder. Para coletar informações sobre logs de infraestrutura, marque a caixa de seleção **Habilitar transferência de Logs de Infraestrutura**. Para aumentar ou diminuir o intervalo entre a transferência de logs de infraestrutura para sua conta de armazenamento, altere o valor do **Período de Transferência (min)**.

![Logs de infraestrutura de diagnóstico](./media/vs-azure-tools-diagnostics-for-cloud-services-and-virtual-machines/IC758148.png)

Para saber mais, consulte [Coletar dados do log usando o Diagnóstico do Azure](https://msdn.microsoft.com/library/azure/gg433048.aspx).

### <a name="log-directories"></a>Diretórios de log
Diretórios de log têm dados coletados de diretórios de log para solicitações de IIS (Serviços de Informações da Internet), solicitações com falha ou pastas que você escolher. Para capturar os diretórios de log, marque a caixa de seleção **Habilitar transferência de Diretórios de Log**. Para aumentar ou diminuir o intervalo entre a transferência de logs para sua conta de armazenamento, altere o valor do **Período de Transferência (min)**.

Marque as caixas de seleção dos logs que deseja coletar, como **Logs do IIS** e logs de **Solicitação com Falha**. Os nomes de contêiner de armazenamento padrão são fornecidos, mas você pode alterá-los.

Você pode capturar logs de qualquer pasta. Especifique o caminho na seção **Log do Diretório Absoluto** e, em seguida, selecione **Adicionar Diretório**. Os logs são capturados nos contêineres especificados.

![Diretórios de log](./media/vs-azure-tools-diagnostics-for-cloud-services-and-virtual-machines/IC796665.png)

### <a name="etw-logs"></a>Logs do ETW
Se você usar [ETW](https://msdn.microsoft.com/library/windows/desktop/bb968803\(v=vs.85\).aspx) (Rastreamento de Eventos para Windows) e desejar capturar logs de ETW, selecione a caixa de seleção **Habilitar transferência de logs de ETW**. Para aumentar ou diminuir o intervalo entre a transferência de logs para sua conta de armazenamento, altere o valor do **Período de Transferência (min)**.

Os eventos são capturados das origens de eventos e manifestos de evento que você especificar. Para especificar uma origem de evento, na seção **Fontes de Evento**, insira um nome e, em seguida, selecione **Adicionar Origem de Evento**. Da mesma forma, você pode especificar um manifesto de evento na seção **Manifestos de Evento** e, em seguida, escolher o botão **Adicionar Manifesto de Evento**.

![Logs do ETW](./media/vs-azure-tools-diagnostics-for-cloud-services-and-virtual-machines/IC766025.png)

O framework do ETW tem suporte no ASP.NET por meio de classes no namespace [System.Diagnostics.aspx](https://msdn.microsoft.com/library/system.diagnostics(v=vs.110)). O namespace Microsoft.WindowsAzure.Diagnostics, que herda e estende classes [System.Diagnostics.aspx](https://msdn.microsoft.com/library/system.diagnostics(v=vs.110)) padrão, permite o uso de [System.Diagnostics.aspx](https://msdn.microsoft.com/library/system.diagnostics(v=vs.110)) como um registro em log estrutura no ambiente do Azure. Para obter mais informações, consulte [Assumir controle do registro em log e do rastreamento no Microsoft Azure](https://msdn.microsoft.com/magazine/ff714589.aspx) e [Habilitar o diagnóstico em máquinas virtuais e Serviços de Nuvem do Azure](cloud-services/cloud-services-dotnet-diagnostics.md).

### <a name="crash-dumps"></a>Despejos de falhas
Para capturar informações sobre quando uma instância de função falha, marque a caixa de seleção **Habilitar transferência de despejos**. (Pelo fato do ASP.NET manipular mais exceções, isso geralmente é útil somente para uma função de trabalho.) Para aumentar ou diminuir o percentual de espaço de armazenamento dedicado aos despejos, altere o valor **Cota de Diretório (%)**. Você pode alterar o contêiner de armazenamento no qual os despejos de memória são armazenados e selecionar se deseja capturar um despejo **Completo** ou **Mini**.

Os processos que estão sendo rastreados no momento são listados na seguinte captura de tela. Selecione as caixas de seleção para os processos que você deseja capturar. Para adicionar outro processo à lista, insira o nome do processo e, em seguida, selecione **Adicionar Processo**.

![Despejos de falhas](./media/vs-azure-tools-diagnostics-for-cloud-services-and-virtual-machines/IC766026.png)

Para obter mais informações, consulte [Tomar controle do registro em log e rastreamento no Microsoft Azure](https://msdn.microsoft.com/magazine/ff714589.aspx) e [Diagnóstico do Microsoft Azure parte 4: componentes de registro de log personalizados e alterações do Diagnóstico do Azure 1.3](http://justazure.com/microsoft-azure-diagnostics-part-4-custom-logging-components-azure-diagnostics-1-3-changes/).

## <a name="view-the-diagnostics-data"></a>Exibir os dados de diagnóstico
Depois de coletar os dados de diagnóstico para um serviço de nuvem ou uma máquina virtual, você poderá exibi-los.

### <a name="to-view-cloud-service-diagnostics-data"></a>Para exibir dados de diagnóstico do serviço de nuvem
1. Implante seu serviço de nuvem como de costume e, em seguida, execute-o.
2. Você pode exibir os dados de diagnóstico em um relatório que o Visual Studio gere ou em tabelas na sua conta de armazenamento. Para exibir os dados em um relatório, abra Cloud Explorer ou Gerenciador de Servidores, abra o menu de atalho do nó para a função desejada e selecione **Exibir Dados de Diagnóstico**.
   
    ![Exibir dados de diagnóstico](./media/vs-azure-tools-diagnostics-for-cloud-services-and-virtual-machines/IC748912.png)
   
    Um relatório que mostra os dados disponíveis é exibido.
   
    ![Relatório do Diagnóstico do Microsoft Azure no Visual Studio](./media/vs-azure-tools-diagnostics-for-cloud-services-and-virtual-machines/IC796666.png)
   
    Se os dados mais recentes não forem mostrados, você terá que esperar o período de transferência se passar.
   
    Para atualizar imediatamente os dados, selecione o link **Atualização**. Para que os dados sejam atualizados automaticamente, selecione um intervalo na caixa de listagem suspensa **Atualização Automática**. Para exportar os dados de erro, selecione o botão **Exportar para CSV** para criar um arquivo de valores separados por vírgulas que possa ser aberto em uma planilha do Excel.
   
    No Cloud Explorer ou no Gerenciador de Servidores, abra a conta de armazenamento associada à implantação.
3. Abra as tabelas de diagnóstico no Visualizador de tabela e, em seguida, analise os dados coletados. Para logs do IIS e os logs personalizados, você pode abrir um contêiner de blob. A tabela a seguir lista as tabelas ou contêineres de blob com os dados para os diferentes arquivos de log. Além dos dados para esse arquivo de log, as entradas de tabela contêm **EventTickCount**, **DeploymentId**, **Role** e **RoleInstance** para ajudá-lo a identificar qual máquina virtual e a função gerou os dados e quando. 
   
   | Dados de diagnóstico | Descrição | Local |
   | --- | --- | --- |
   | Logs de aplicativo |Logs que seu código gera chamando métodos da classe **System.Diagnostics.Trace**. |WADLogsTable |
   | Logs de eventos |Dados dos logs de eventos do Windows nas máquinas virtuais. O Windows armazena informações nesses logs, mas os aplicativos e os serviços também usam os logs para relatar erros ou registrar em log informações. |WADWindowsEventLogsTable |
   | Contadores de desempenho |Você pode coletar dados de qualquer contador de desempenho disponível na máquina virtual. O sistema operacional fornece contadores de desempenho, que inclui muitas estatísticas, como tempo de processador e uso de memória. |WADPerformanceCountersTable |
   | Logs de infraestrutura |Logs que são gerados por meio da própria infraestrutura de diagnóstico. |WADDiagnosticInfrastructureLogsTable |
   | Logs IIS |Logs que registram solicitações da Web. Se o seu serviço de nuvem obtiver uma quantidade significativa de tráfego, esses logs poderão ser demorados. É uma boa ideia coletar e armazenar esses dados somente quando você precisar dele. |Você pode encontrar os logs de solicitação com falha no contêiner de blob em wad-iis-failedreqlogs em um caminho para essa implantação, função e instância. Você pode encontrar logs completos em wad-iis-logfiles. As entradas para cada arquivo são feitas na tabela WADDirectories. |
   | Despejos de falhas |Fornecem imagens binárias do processo do serviço de nuvem (normalmente uma função de trabalho). |contêiner de blob wad-crush-dumps |
   | Arquivos de log personalizados |Logs de dados que você predefiniu. |Você pode especificar no código o local dos arquivos de log personalizados na sua conta de armazenamento. Por exemplo, você pode especificar um contêiner de blob personalizado. |
4. Se os dados de qualquer tipo estão truncados, você pode tentar aumentar o buffer para este tipo de dados ou encurtar o intervalo entre as transferências de dados da máquina virtual para sua conta de armazenamento.
5. (Opcional) Limpe os dados da conta de armazenamento, ocasionalmente, para reduzir os custos gerais de armazenamento.
6. Quando você faz uma implantação completa, o arquivo diagnostics.cscfg (.wadcfgx do SDK do Azure 2.5) é atualizado no Azure e seu serviço de nuvem seleciona as alterações para a configuração do diagnóstico. Se, em vez disso, você atualizar uma implantação existente, o arquivo .cscfg não será atualizado no Azure. Você ainda pode alterar as configurações de diagnóstico, porém, seguindo as etapas da próxima seção. Para obter mais informações sobre como executar uma implantação completa e atualizar uma implantação existente, consulte [Assistente de publicação de aplicativo no Azure](vs-azure-tools-publish-azure-application-wizard.md).

### <a name="to-view-virtual-machine-diagnostics-data"></a>Para exibir dados de diagnóstico de máquina virtual
1. No menu de atalho para a máquina virtual, selecione **Exibir dados de diagnóstico**.
   
    ![Exibir dados de diagnóstico em uma máquina virtual do Azure](./media/vs-azure-tools-diagnostics-for-cloud-services-and-virtual-machines/IC766027.png)
   
    A caixa de diálogo **Resumo de diagnóstico** é exibida.
   
    ![Resumo de diagnóstico de máquina virtual do Azure](./media/vs-azure-tools-diagnostics-for-cloud-services-and-virtual-machines/IC796667.png)  
   
    Se os dados mais recentes não forem mostrados, você terá que esperar o período de transferência se passar.
   
    Para atualizar imediatamente os dados, selecione o link **Atualização**. Para que os dados sejam atualizados automaticamente, selecione um intervalo na caixa de listagem suspensa **Atualização Automática**. Para exportar os dados de erro, selecione o botão **Exportar para CSV** para criar um arquivo de valores separados por vírgulas que possa ser aberto em uma planilha do Excel.

## <a name="set-up-cloud-service-diagnostics-after-deployment"></a>Configurar o diagnóstico de serviço de nuvem após a implantação
Se você estiver investigando um problema com um serviço de nuvem que já esteja em execução, talvez deseje coletar os dados que não especificou antes de implantar originalmente a função. Nesse caso, você pode começar a coletar dados alterando as configurações no Gerenciador de Servidores. Você pode configurar o diagnóstico para uma única instância ou para todas as instâncias de uma função, dependendo de se você abra a caixa de diálogo **Configuração de Diagnóstico** no menu de atalho para a instância ou a função. Se você configurar o nó da função, todas as alterações que você fizer serão aplicadas a todas as instâncias. Se você configurar o nó da instância, as alterações que você fizer se aplicarão somente a essa instância.

### <a name="to-set-up-diagnostics-for-a-running-cloud-service"></a>Para configurar o diagnóstico para um serviço de nuvem em execução
1. No Gerenciador de Servidores, expanda o nó **Serviços de Nuvem** e em seguida, expanda a lista de nós para localizar a função ou a instância (ou ambas) que você deseja investigar.
   
    ![Configurar o diagnóstico](./media/vs-azure-tools-diagnostics-for-cloud-services-and-virtual-machines/IC748913.png)
2. No menu de atalho para um nó da instância ou um nó de função, selecione **Atualizar configurações de diagnóstico** e, em seguida, selecione as configurações de diagnóstico que você deseja coletar.
   
    Para obter informações sobre as definições de configuração, consulte a seção **Configurar fontes de dados de diagnóstico** neste artigo. Para obter informações sobre como exibir os dados de diagnóstico, consulte a seção **Exibir os dados de diagnóstico** neste artigo.
   
    Se você alterar a coleta de dados no Gerenciador de Servidores, essas alterações permanecerão em vigor até que você reimplante seu serviço de nuvem. Se você usar as configurações de publicação padrão, as alterações não serão substituídas. A configuração de publicação padrão é atualizar a implantação existente, em vez de fazer uma reimplantação completa. Para garantir que as configurações estão limpas no momento da implantação, vá para a guia **Configurações avançadas** no Assistente de publicação e limpe a caixa de seleção **Atualização de implantação**. Quando você reimplanta com essa caixa de seleção desmarcada, as configurações revertem aquelas no arquivo .wadcfgx (ou wadcfg) conforme definido no editor de **Propriedades** da função. Se você atualizar a implantação, o Azure manterá as configurações mais antigas.

## <a name="troubleshoot-azure-cloud-service-issues"></a>Solucionar problemas do serviço de nuvem do Azure
Se você tiver problemas com seus projetos de serviço de nuvem, como uma função travar no status "ocupado", repetidamente reciclar ou lançar um erro interno do servidor, existem ferramentas e técnicas que você poderá usar para diagnosticar e corrigir esses problemas. Para obter exemplos específicos de problemas e soluções comuns e para uma visão geral dos conceitos e ferramentas que você pode usar para diagnosticar e corrigir esses erros, consulte [Dados de diagnóstico de computação de PaaS do Azure](http://blogs.msdn.com/b/kwill/archive/2013/08/09/windows-azure-paas-compute-diagnostics-data.aspx).

## <a name="q--a"></a>Perguntas e respostas
**Qual é o tamanho do buffer e que tamanho o buffer deve ter?**

Em cada instância de máquina virtual, as cotas limitam quantos dados de diagnóstico podem ser armazenados no sistema de arquivos local. Além disso, você pode especificar um tamanho do buffer para cada tipo de dados de diagnóstico disponível. Esse tamanho do buffer age como uma cota individual para esse tipo de dados. Para determinar a cota geral e a quantidade de memória que permanece, consulte a parte inferior da caixa de diálogo para o tipo de dados de diagnóstico. Se você especificar buffers maiores ou mais tipos de dados, abordará a cota geral. Você pode alterar a cota geral modificando o arquivo de configuração diagnostics.wadcfg ou .wadcfgx. Os dados de diagnóstico são armazenados no sistema de arquivos como seus dados do aplicativo. Se seu aplicativo usar uma grande quantidade de espaço em disco, você não deverá aumentar a cota de diagnóstico geral.

**Qual é o período de transferência e por qual tempo deve ser?**

O período de transferência é a quantidade de tempo que decorreu as capturas de dados. Depois de cada período de transferência, os dados são movidos do sistema de arquivos local de uma máquina virtual para tabelas em sua conta de armazenamento. Se a quantidade de dados que são coletados excede a cota antes do final de um período de transferência, dados antigos serão descartados. Você talvez queira diminuir o período de transferência se estiver perdendo dados porque seus dados excedem o tamanho do buffer ou a cota geral.

**Os carimbos de data/hora estão inseridos em que fuso horário?**

Os carimbos de data/hora estão no fuso horário local do data center que hospeda o serviço de nuvem. As três colunas de carimbo de data/hora a seguir nas tabelas de log são usadas:

* **PreciseTimeStamp**: o carimbo de data/hora de ETW do evento. Ou seja, a hora em que o evento é registrado no cliente.
* **TIMESTAMP**: o valor para **PreciseTimeStamp** arredondado para baixo até o limite de frequência de upload. Portanto, se a frequência de upload for de 5 minutos e a hora do evento for 00:17:12, TIMESTAMP será 00:15:00.
* **Timestamp**: o carimbo de data/hora em que a entidade foi criada na tabela do Azure.

**Como gerenciar custos ao coletar informações de diagnóstico?**

As configurações padrão (**Nível de log** definido como **Erro** e **Período de transferência** definido como **1 minuto**) são projetadas para minimizar os custos. Os custos de computação aumentam quando você coleta mais dados de diagnóstico ou se você diminui o período de transferência. Não colete mais dados do que você precisa e não se esqueça de desabilitar a coleta de dados quando não precisa mais dela. Você sempre poderá habilitá-lo novamente, mesmo no tempo de execução, conforme descrito anteriormente neste artigo.

**Como posso coletar logs de solicitação com falha do IIS?**

Por padrão, o IIS não coleta logs de solicitação com falha. Você pode configurar o IIS para coletar logs de solicitação com falha editando o arquivo web.config para sua função web.

**Não estou obtendo informações de rastreamento de métodos RoleEntryPoint como OnStart. O que está errado?**

Os métodos de **RoleEntryPoint** são chamados no contexto de WAIISHost.exe, não em IIS. As informações de configuração no web.config que normalmente habilitam o rastreamento não se aplicam. Para resolver esse problema, adicione um arquivo .config ao seu projeto de função web e nomeie o arquivo para corresponder ao assembly de saída que contém o código **RoleEntryPoint**. No projeto de função web padrão, o nome do arquivo .config deve ser WAIISHost.exe.config. Adicione as seguintes linhas a esse arquivo:

```
<system.diagnostics>
  <trace>
      <listeners>
          <add name “AzureDiagnostics” type=”Microsoft.WindowsAzure.Diagnostics.DiagnosticMonitorTraceListener”>
              <filter type=”” />
          </add>
      </listeners>
  </trace>
</system.diagnostics>
```

Na janela **Propriedades**, defina a propriedade **Copiar para Diretório de Saída** como **Copiar sempre**.

## <a name="next-steps"></a>Próximas etapas
Para saber mais sobre o registro em log de diagnósticos no Azure, consulte [Habilitar o diagnóstico em máquinas virtuais e Serviços de Nuvem do Azure](cloud-services/cloud-services-dotnet-diagnostics.md) e [Habilitar o registro em log de diagnóstico para aplicativos Web no Serviço de Aplicativo do Azure](app-service/web-sites-enable-diagnostic-log.md).

