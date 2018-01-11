---
title: "Solucionar problemas de alterações em uma máquina virtual do Azure | Microsoft Docs"
description: "Use o Controle de Alterações para solucionar problemas de alterações em uma máquina virtual do Azure."
services: automation
keywords: "alteração, controle, automação"
author: jennyhunter-msft
ms.author: jehunte
ms.date: 12/14/2017
ms.topic: hero-article
ms.custom: mvc
manager: carmonm
ms.openlocfilehash: ae9ac6baaaeca418fcd3478145c50d1fa7917d7e
ms.sourcegitcommit: a648f9d7a502bfbab4cd89c9e25aa03d1a0c412b
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/22/2017
---
# <a name="troubleshoot-changes-in-your-environment"></a>Solucionar problemas de alterações em seu ambiente

Neste tutorial, você aprenderá como solucionar problemas de alterações em uma máquina virtual do Azure. Habilitando o Controle de alterações, é possível controlar alterações em software, arquivos, daemons do Linux, serviços do Windows e chaves de Registro do Windows em seus computadores.
Identificar essas alterações de configuração pode ajudar a detectar problemas operacionais em seu ambiente.

Neste tutorial, você aprenderá a:

> [!div class="checklist"]
> * Carregar uma VM para Controle de alterações e Inventário
> * Pesquisar serviços interrompidos nos logs de alterações
> * Configurar o controle de alterações
> * Habilitar conexão do log de atividades
> * Disparar um evento
> * Exibir alterações

## <a name="prerequisites"></a>pré-requisitos

Para concluir este tutorial, você precisará:

* Uma assinatura do Azure. Se você ainda não tiver uma, poderá [ativar os benefícios de assinante do MSDN](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/) ou inscrever-se em uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
* Uma [conta de Automação](automation-offering-get-started.md) para manter os runbooks inspetor e de ação e a tarefa do observador.
* Uma [máquina virtual](../virtual-machines/windows/quick-create-portal.md) a ser carregada.

## <a name="log-in-to-azure"></a>Fazer logon no Azure

Faça logon no Portal do Azure em http://portal.azure.com.

## <a name="enable-change-tracking-and-inventory"></a>Habilitar Controle de alterações e Inventário

Primeiro, é necessário habilitar o Controle de alterações e Inventário para sua VM neste tutorial. Se você já habilitou outra solução de automação para uma VM, esta etapa não é necessária.

1. No menu à esquerda, selecione **Máquinas virtuais** e selecione uma VM na lista
1. No menu à esquerda, na seção **Operações**, clique em **Inventário**. A página **Habilitar Controle de alterações e Inventário** é aberta.

É feita uma validação para determinar se o Controle de alterações e Inventário está habilitado para esta VM.
A validação inclui verificar se há um espaço de trabalho do Log Analytics e uma conta de automação vinculada e se a solução está no espaço de trabalho.

Um espaço de trabalho do [Log Analytics](../log-analytics/log-analytics-overview.md?toc=%2fazure%2fautomation%2ftoc.json) é usado para coletar dados gerados por recursos e serviços como Inventário.
O espaço de trabalho fornece um único local para examinar e analisar dados de várias fontes.

O processo de validação também verifica se a VM é provisionada com o MMA (Microsoft Monitoring Agent) e o Hybrid Worker.
Esse agente é usado para comunicar-se com a VM e obter informações sobre o software instalado.
O processo de validação também verifica se a VM é provisionada com o MMA (Microsoft Monitoring Agent) e o Hybrid Runbook Worker da Automação.

Se esses pré-requisitos não forem atendidos, será exibida uma faixa oferecendo a opção para habilitar a solução.

![Faixa de configuração integrada do Controle de alterações e Inventário](./media/automation-tutorial-troubleshoot-changes/enableinventory.png)

Para habilitar a solução, clique na faixa.
Se algum dos seguintes pré-requisitos estiver ausente após a validação, ele será adicionado automaticamente:

* Espaço de trabalho do [Log Analytics](../log-analytics/log-analytics-overview.md?toc=%2fazure%2fautomation%2ftoc.json)
* [Automação](./automation-offering-get-started.md)
* Uma [Hybrid Runbook Worker](./automation-hybrid-runbook-worker.md) está habilitada na VM

A tela **Controle de Alterações e Inventário** é aberta. Configure o local, o espaço de trabalho do Log Analytics e a conta de Automação a serem usados e clique em **Habilitar**. Caso os campos estejam esmaecidos, isso significa que outra solução de automação está habilitada para a VM e o mesmo espaço de trabalho e conta de Automação devem ser usados.

![Habilitar janela de solução de controle de alterações](./media/automation-tutorial-troubleshoot-changes/installed-software-enable.png)

A habilitação da solução pode levar até 15 minutos. Durante esse tempo, não feche a janela do navegador.
Depois que a solução for habilitada, as informações sobre o software instalado e as alterações na VM fluirão para o Log Analytics.
Pode levar entre 30 minutos e 6 horas para que os dados fiquem disponíveis para análise.


## <a name="using-change-tracking-in-log-analytics"></a>Usando o Controle de alterações no Log Analytics

O controle de alterações gera dados de log que são enviados para o Log Analytics. Para pesquisar os logs executando consultas, selecione **Log Analytics** na parte superior da janela **Controle de alterações**.
Os dados do Controle de alterações são armazenados com o tipo **ConfigurationChange**. A consulta de exemplo do Log Analytics retorna todos os serviços do Windows que foram interrompidos.

```
ConfigurationChange
| where ConfigChangeType == "WindowsServices" and SvcState == "Stopped"
```

Para saber mais sobre como executar e pesquisar arquivos de log no Log Analytics, consulte [Azure Log Analytics](https://docs.loganalytics.io/index).

## <a name="configure-change-tracking"></a>Configurar o Controle de alterações

O Controle de alterações oferece a capacidade de controlar alterações de configuração em sua VM. As etapas a seguir mostrarão como configurar o controle de arquivos e de chaves do Registro.
 
Para escolher quais arquivos e chaves do Registro coletar e rastrear, selecione **Editar configurações** na parte superior da página **Controle de alterações**.

> [!NOTE]
> O Inventário e o Controle de alterações usam as mesmas configurações de coleta, e as definições são configuradas no nível do espaço de trabalho.

Na janela **Configuração do Espaço de Trabalho**, adicione as chaves do Registro do Windows, os arquivos do Windows ou Linux a serem controlados, conforme descrito nas próximas três seções.

### <a name="add-a-windows-registry-key"></a>Adicionar uma chave do Registro do Windows

1. Na guia **Registro do Windows**, selecione **Adicionar**.
    A janela **Adicionar Registro do Windows para Controle de Alterações** se abre.

   ![Adicionar Registro do Controle de Alterações](./media/automation-vm-change-tracking/change-add-registry.png)

2. Em **Habilitado**, selecione **True**.
3. Na caixa **Nome do Item**, insira um nome amigável.
4. (Opcional) Na caixa **Grupo**, digite um nome de grupo.
5. Na caixa **Chave do Registro do Windows**, insira o nome da chave do registro que você deseja controlar.
6. Selecione **Salvar**.

### <a name="add-a-windows-file"></a>Adicionar um arquivo do Windows

1. Na guia **Arquivos do Windows**, selecione **Adicionar**. A janela **Adicionar Arquivo do Windows para Controle de Alterações** se abre.

   ![Adicionar arquivo do Windows do Controle de Alterações](./media/automation-vm-change-tracking/change-add-win-file.png)

2. Em **Habilitado**, selecione **True**.
3. Na caixa **Nome do Item**, insira um nome amigável.
4. (Opcional) Na caixa **Grupo**, digite um nome de grupo.
5. Na caixa **Inserir Caminho**, insira o caminho completo e o nome do arquivo que você deseja rastrear.
6. Selecione **Salvar**.

### <a name="add-a-linux-file"></a>Adicionar um arquivo Linux

1. Na guia **Arquivos Linux**, selecione **Adicionar**. A janela **Adicionar Arquivo Linux para Controle de Alterações** se abre.

   ![Adicionar arquivo do Linux do Controle de Alterações](./media/automation-vm-change-tracking/change-add-linux-file.png)

2. Em **Habilitado**, selecione **True**.
3. Na caixa **Nome do Item**, insira um nome amigável.
4. (Opcional) Na caixa **Grupo**, digite um nome de grupo.
5. Na caixa **Inserir Caminho**, insira o caminho completo e o nome do arquivo que você deseja rastrear.
6. Na caixa **Tipo de Caminho**, selecione **Arquivo** ou **Diretório**.
7. Em **Recursão**, selecione **Ativado** para controlar alterações para o caminho especificado e todos os arquivos e caminhos sob ele. Para controlar somente o caminho ou o arquivo selecionado, selecione **Desligado**.
8. Em **Use o Sudo**, para acompanhar arquivos que precisam do comando `sudo` para serem acessados, selecione **Ativado**. Caso contrário, selecione **Desligado**.
9. Selecione **Salvar**.

## <a name="enable-activity-log-connection"></a>Habilitar conexão do log de atividades

Na página **Controle de alterações** em sua VM, selecione **Gerenciar Conexão do Log de Atividades**. Essa tarefa abre a página **Log de Atividades do Azure**. Selecione **Conectar** para conectar o Controle de alterações ao log de atividades do Azure para sua VM.

Com essa configuração habilitada, navegue até a página **Visão geral** para a VM e selecione **Parar** para interromper a VM. Quando solicitado, selecione **Sim** para interromper a VM. Quando ele for desalocado, selecione **Iniciar** para reiniciar a VM.

Interromper e iniciar uma VM registra um evento em seu log de atividades. Navegue de volta para a página **Controle de alterações**. Selecione a guia **Eventos** na parte inferior da página. Após algum tempo, os eventos são exibidos no gráfico e na tabela. Como na etapa anterior, cada evento pode ser selecionado para exibir informações detalhadas sobre ele.

![Exibindo detalhes de alteração no portal](./media/automation-tutorial-troubleshoot-changes/viewevents.png)

## <a name="view-changes"></a>Exibir alterações

Depois que a solução Controle de alterações e Inventário estiver habilitada, será possível exibir os resultados na página **Controle de alterações**.

Na sua VM, selecione **Controle de alterações** em **OPERAÇÕES**.

![Criando um alerta no portal clássico do OMS](./media/automation-tutorial-troubleshoot-changes/change-tracking-list.png)

O gráfico mostra as alterações que ocorreram ao longo do tempo.
Depois de adicionar uma conexão do Log de Atividades, o grafo da linha na parte superior exibe eventos do Log de Atividades do Azure.
Cada linha de gráficos de barras representa um tipo de alteração controlável diferente.
Esses tipos são daemons do Linux, arquivos, chaves do Registro do Windows, software e serviços do Windows.
A guia de alteração mostra os detalhes para as alterações mostradas na visualização em ordem decrescente de hora em que a alteração ocorreu (mais recentes primeiro).
A guia **Eventos**, a tabela exibe os eventos do Log de Atividades conectados e os detalhes correspondentes com os mais recentes primeiro.

É possível ver nos resultados, que houve várias alterações no sistema, incluindo alterações em serviços e software. É possível usar os filtros na parte superior da página para filtrar os resultados por **Tipo de alteração** ou por um intervalo de tempo.

Selecione uma alteração **WindowsServices**. Isso abre a janela **Detalhes da alteração**. A janela detalhes da alteração mostra detalhes sobre a alteração e os valores antes e após a alteração. Nesse caso, o serviço de Proteção de Software foi interrompido.

![Exibindo detalhes de alteração no portal](./media/automation-tutorial-troubleshoot-changes/change-details.png)

## <a name="next-steps"></a>Próximas etapas

Neste tutorial, você aprendeu a:

> [!div class="checklist"]
> * Carregar uma VM para Controle de alterações e Inventário
> * Pesquisar serviços interrompidos nos logs de alterações
> * Configurar o controle de alterações
> * Habilitar conexão do log de atividades
> * Disparar um evento
> * Exibir alterações

Passe para a visão geral da solução de Controle de alterações e Inventário para saber mais sobre ela.

> [!div class="nextstepaction"]
> [Solução de gerenciamento de alterações e inventário](../log-analytics/log-analytics-change-tracking.md?toc=%2fazure%2fautomation%2ftoc.json)
