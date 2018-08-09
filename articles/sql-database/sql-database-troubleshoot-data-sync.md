---
title: Solução de problemas da Sincronização de Dados SQL do Azure | Microsoft Docs
description: Saiba como solucionar problemas comuns com a Sincronização de Dados SQL do Azure.
services: sql-database
ms.date: 07/16/2018
ms.topic: conceptual
ms.service: sql-database
author: allenwux
ms.author: xiwu
manager: craigg
ms.custom: data-sync
ms.openlocfilehash: 8ba4b32f45dd978439b08650e498c3030c618aab
ms.sourcegitcommit: 35ceadc616f09dd3c88377a7f6f4d068e23cceec
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 08/08/2018
ms.locfileid: "39618702"
---
# <a name="troubleshoot-issues-with-sql-data-sync"></a>Solucionar problemas com a Sincronização de Dados SQL do Azure

Esse artigo descreve como solucionar problemas conhecidos com a Sincronização de Dados SQL do Azure. Caso haja uma resolução para um problema, ela será fornecida aqui.

Para obter uma visão geral da Sincronização de Dados SQL, consulte [Sincronizar dados entre vários bancos de dados locais e de nuvem com a Sincronização de Dados SQL do Azure](sql-database-sync-data.md).

## <a name="sync-issues"></a>Problemas de sincronização

- [A sincronização falha na interface do usuário do portal para bancos de dados locais associados ao agente cliente](#sync-fails)

- [Meu grupo de sincronização está paralisado no estado de processamento](#sync-stuck)

- [Há dados errados em minhas tabelas](#sync-baddata)

- [Há dados de chave primária inconsistentes após uma sincronização com êxito](#sync-pkdata)

- [Há uma degradação significativa no desempenho](#sync-perf)

- [Esta mensagem é exibida: "Não é possível inserir o valor NULL na coluna <column>. A coluna não permite valores nulos”. O que isso significa e como é possível corrigi-lo?](#sync-nulls)

- [Como a Sincronização de Dados trata referências circulares? Ou seja, quando os mesmos dados são sincronizados em vários grupos de sincronização e continuam sendo alterados como resultado?](#sync-circ)

### <a name="sync-fails"></a> Falha de sincronização na interface do usuário do portal em bancos de dados locais associados ao agente cliente

A sincronização falha na interface do usuário do portal da Sincronização de Dados SQL para bancos de dados locais associados ao agente cliente. No computador local que executa o agente, são exibidos erros System.IO.IOException no Log de Eventos. Os erros informam que o disco não tem espaço suficiente.

- **Causa**. A unidade não tem espaço suficiente.

- **Resolução**. Crie mais espaço na unidade na qual o diretório %TEMP% está localizado.

### <a name="sync-stuck"></a> Meu grupo de sincronização paralisou no estado de processamento

Um grupo de sincronização na Sincronização de Dados SQL esteve no estado de processamento por muito tempo. Ele não responde ao comando de **parada** e os logs não mostram novas entradas.

Qualquer uma das condições a seguir pode fazer com que um grupo de sincronização trave no estado de processamento:

- **Causa**. O agente cliente está offline

- **Resolução**. Verifique se o agente cliente está online e tente novamente.

- **Causa**. O agente cliente está desinstalado ou ausente.

- **Resolução**. Se o agente cliente estiver desinstalado ou de outra maneira ausente:

    1. Remova o arquivo XML do agente da pasta de instalação da Sincronização de Dados SQL caso o arquivo exista.
    1. Instale o agente em um computador local (pode ser o mesmo ou em outro computador). Em seguida, envie a chave do agente que é gerada no portal para o agente que é mostrado como offline.

- **Causa**. O serviço Sincronização de Dados SQL está interrompido.

- **Resolução**. Reinicie o serviço de Sincronização de Dados SQL.

    1. No menu **Iniciar**, pesquise por **Serviços**.
    1. Nos resultados da pesquisa, selecione **Serviços**.
    1. Localize o serviço de **Sincronização de Dados SQL**.
    1. Se o status do serviço for **Parado**, clique com o botão direito do mouse no nome do serviço e selecione **Iniciar**.

> [!NOTE]
> Se as informações anteriores não retirarem seu grupo de sincronização do estado de processamento, o Suporte da Microsoft poderá redefinir o status de seu grupo de sincronização. Para ter o status do grupo de sincronização redefinido, no [Fórum do banco de dados SQL do Azure](https://social.msdn.microsoft.com/Forums/azure/home?forum=ssdsgetstarted), crie uma postagem. Na postagem, inclua sua ID de assinatura e a ID do grupo de sincronização para o grupo que precisa ser redefinido. Um engenheiro do Suporte da Microsoft responderá à sua postagem e o informará quando o status tiver sido redefinido.

### <a name="sync-baddata"></a> Há dados errados em minhas tabelas

Se as tabelas que têm o mesmo nome, mas que são de esquemas de banco de dados diferente forem incluídas em uma sincronização, você verá dados incorretos nas tabelas após a sincronização.

- **Causa**. O processo de provisionamento da Sincronização de Dados SQL usa as mesmas tabelas de acompanhamento para tabelas com o mesmo nome, mas que estão em esquemas diferentes. Por isso, as alterações de ambas as tabelas são refletidas na mesma tabela de rastreamento. Isso causa alterações de dados incorretas durante a sincronização.

- **Resolução**. Garanta que os nomes das tabelas envolvidas na sincronização sejam diferentes, mesmo que pertençam a esquemas diferentes em um banco de dados.

### <a name="sync-pkdata"></a> Há dados de chave primária inconsistentes após uma sincronização com êxito

Uma sincronização é relatada como bem-sucedida e o log não mostra nenhuma linha com falha ou ignorada, mas você observa que os dados da chave primária são inconsistentes entre os bancos de dados no grupo de sincronização.

- **Causa**. Esse resultado ocorre por design. As alterações em qualquer coluna de chave primária resultam em dados inconsistentes nas linhas nas quais a chave primária foi alterada.

- **Resolução**. Para evitar esse problema, garanta que nenhum dado em uma coluna de chave primária é alterado. Para corrigir esse problema depois que ele tiver ocorrido, exclua a linha que contém dados inconsistentes de todos os pontos de extremidade no grupo de sincronização. Em seguida, reinsira a linha.

### <a name="sync-perf"></a> Há uma degradação significativa no desempenho

O desempenho é degradado de forma significativa, possivelmente até o ponto em que você não pode nem mesmo abrir a interface do usuário da Sincronização de Dados.

- **Causa**. A causa mais provável é um loop de sincronização. Um loop de sincronização ocorre quando uma sincronização por grupo de sincronização A aciona uma sincronização por grupo de sincronização B, que dispara uma sincronização por grupo de sincronização A. A situação real pode ser mais complexa e pode envolver mais de dois grupos de sincronização no loop. O problema é que há um acionamento circular de sincronização que é causado por grupos de sincronização sobrepostos entre si.

- **Resolução**. A melhor correção é a prevenção. Verifique se você não tem referências circulares nos grupos de sincronização. Nenhuma linha sincronizada por um grupo de sincronização pode ser sincronizada por outro grupo de sincronização.

### <a name="sync-nulls"></a> Esta mensagem é exibida: "Não é possível inserir o valor NULL na coluna <column>. A coluna não permite valores nulos”. O que isso significa e como posso corrigir o erro? 
Essa mensagem de erro indica que um dos dois problemas a seguir ocorreu:
-  Uma tabela não tem uma chave primária. Para corrigir esse problema, adicione uma chave primária a todas as tabelas que você está sincronizando.
-  Há uma cláusula WHERE na instrução CREATE INDEX. A Sincronização de Dados não lidar com essa condição. Para corrigir esse problema, remova a cláusula WHERE ou realize as alterações manualmente para todos os bancos de dados. 
 
### <a name="sync-circ"></a> Como a Sincronização de Dados trata referências circulares? Ou seja, quando os mesmos dados são sincronizados em vários grupos de sincronização, fazendo com que sejam continuamente alterados?
A Sincronização de Dados não trata referências circulares. Evite usá-las. 

## <a name="client-agent-issues"></a>Problemas de agente cliente

- [O agente cliente instala, desinstala ou repara falha](#agent-install)

- [O agente cliente não funciona depois de cancelar a desinstalação](#agent-uninstall)

- [Meu banco de dados não está listado na lista de agentes](#agent-list)

- [O agente cliente não inicia (Erro 1069)](#agent-start)

- [Não é possível enviar a chave do agente](#agent-key)

- [O agente do cliente não poderá ser excluído do portal, se o banco de dados local associado estiver inacessível ](#agent-delete)

- [O aplicativo do Agente de Sincronização local não pode conectar o serviço de sincronização local](#agent-connect)

### <a name="agent-install"></a> O agente cliente instala, desinstala ou repara falha

- **Causa**. Muitos cenários podem causar a falha. Para determinar a causa específica dessa falha, você precisa examinar os logs.

- **Resolução**. Para encontrar a causa específica para a falha ocorrida, você precisa gerar e examinar os logs do Windows Installer. Ative o log no prompt de comando. Por exemplo, se o arquivo AgentServiceSetup.msi baixado for LocalAgentHost.msi, gere e examine os arquivos de log usando as seguintes linhas de comando:

    -   Para instalações: `msiexec.exe /i SQLDataSyncAgent-Preview-ENU.msi /l\*v LocalAgentSetup.InstallLog`
    -   Para desinstalações: `msiexec.exe /x SQLDataSyncAgent-se-ENU.msi /l\*v LocalAgentSetup.InstallLog`

    Também habilite o log para todas as instalações executadas pelo Windows Installer. O artigo da Base de Dados de Conhecimento Microsoft [Como habilitar o log do Windows Installer](https://support.microsoft.com/help/223300/how-to-enable-windows-installer-logging) fornece uma solução com um clique para ativar o log do Windows Installer. Ele também fornece o local desses logs.

### <a name="agent-uninstall"></a> O agente cliente não funciona depois de cancelar a desinstalação

O agente cliente não funciona mesmo após você cancelar a desinstalação.

- **Causa**. Esse problema ocorre porque o agente cliente da Sincronização de Dados SQL não armazena credenciais.

- **Resolução**. Você pode tentar estas duas soluções:

    -   Use services.msc para reinserir suas credenciais do agente cliente.
    -   Desinstale esse agente cliente e instale um novo. Baixe e instale o último agente cliente no [Centro de Download](http://go.microsoft.com/fwlink/?linkid=221479).

### <a name="agent-list"></a> Meu banco de dados não está listado na lista de agentes

Quando você tenta adicionar um banco de dados existente do SQL Server a um grupo de sincronização, o banco de dados não aparece na lista de agentes.

Estes cenários podem causar esse problema:

- **Causa**. O agente cliente e grupo de sincronização estão em diferentes data centers.

- **Resolução**. O agente cliente e o grupo de sincronização devem estar no mesmo data center. Para configurar isso, você tem duas opções:

    -   Crie um novo agente no data center no qual o grupo de sincronização está localizado. Em seguida, registre o banco de dados nesse agente.
    -   Exclua o grupo de sincronização atual. Em seguida, crie novamente o grupo de sincronização no data center no qual o agente está localizado.

- **Causa**. A lista de bancos de dados do agente cliente não é atual.

- **Resolução**. Pare e, em seguida, reinicie o serviço do agente cliente.

    O agente local baixa a lista de bancos de dados associados somente no primeiro envio da chave do agente. Ele não baixa a lista de bancos de dados associados em envios de chave do agente subsequentes. Os bancos de dados registrados durante uma movimentação do agente não aparecem na instância do agente original.

### <a name="agent-start"></a> O agente cliente não inicia (Erro 1069)

Você descobriu que o agente não está sendo executado em um computador que hospeda o SQL Server. Ao tentar iniciar o agente manualmente, você obtém uma caixa de diálogo de erro com a mensagem de erro “Erro 1069: o serviço não foi iniciado devido a uma falha de logon”.

![Caixa de diálogo do erro 1069 da Sincronização de Dados](media/sql-database-troubleshoot-data-sync/sync-error-1069.png)

- **Causa**. Uma causa provável desse erro é que a senha no servidor local foi alterada, já que você criou o agente e a senha do agente.

- **Resolução**. Atualize a senha do agente para a senha atual do servidor:

  1. Localize o serviço do agente do cliente da Sincronização de Dados SQL.  
    a. Selecione **Iniciar**.  
    b. Insira **services.msc** na caixa de pesquisa.  
    c. Nos resultados da pesquisa, selecione **Serviços**.  
    d. Na janela **Serviços**, role até a entrada de **Agente da Sincronização de Dados SQL**.  
  1. Clique com o botão direito do mouse em **Agente da Sincronização de Dados SQL** e, em seguida, selecione **Parar**.
  1. Clique com o botão direito do mouse em **Agente da Sincronização de Dados SQL** e, em seguida, selecione **Propriedades**.
  1. Em **Propriedades do Agente da Sincronização de Dados SQL**, selecione a guia **Fazer Logon**.
  1. Na caixa de **Senha**, insira sua senha.
  1. Na caixa de **Confirmar Senha**, insira sua senha novamente.
  1. Selecione **Aplicar** e, depois, **OK**.
  1. Na janela **Serviços**, clique com o botão direito do mouse no serviço **Agente da Sincronização de Dados SQL** e, em seguida, clique em **Iniciar**.
  1. Feche a janela **Serviços**.

### <a name="agent-key"></a> Não é possível enviar a chave do agente

Depois de criar ou recriar uma chave para um agente, você tenta enviar essa chave pelo aplicativo SqlAzureDataSyncAgent. O envio não é concluído.

![Caixa de diálogo Erro de Sincronização – não é possível enviar a chave do agente](media/sql-database-troubleshoot-data-sync/sync-error-cant-submit-agent-key.png)

- **Pré-requisitos**. Antes de prosseguir, verifique os pré-requisitos a seguir:

  - O serviço Windows Sincronização de Dados SQL está em execução.

  - A conta de serviço do serviço Windows de versão prévia da Sincronização de Dados SQL tem acesso à rede.

  - A porta de saída 1433 está aberta na regra de firewall local.

  - O ip local é adicionado à regra de firewall do servidor ou banco de dados para o banco de dados de metadados de sincronização.

- **Causa**. A chave do agente identifica exclusivamente cada agente local. A chave deve atender às duas condições:

  -   A chave do agente cliente no servidor da Sincronização de Dados SQL e o computador local devem ser idênticos.
  -   A chave do agente cliente pode ser usada apenas uma vez.

- **Resolução**. Se o agente não estiver funcionando, isso indicará que uma ou ambas as condições não foram atendidas. Para fazer com que o agente funcione novamente:

  1. Gere uma nova chave.
  1. Aplique a nova chave ao agente.

  Para aplicar a nova chave ao agente:

  1. No Explorador de Arquivos, vá até o diretório de instalação do agente. O diretório de instalação padrão é C:\\Arquivos de Programa (x86)\\Sincronização de Dados SQL Microsoft.
  1. Clique duas vezes no subdiretório.
  1. Abra o aplicativo SqlAzureDataSyncAgent.
  1. Selecione **Enviar Chave do Agente**.
  1. Cole a chave da área de transferência no espaço fornecido.
  1. Selecione **OK**.
  1. Feche o programa.

### <a name="agent-delete"></a> O agente cliente não poderá ser excluído do portal, se o banco de dados local associado estiver inacessível

Se um ponto de extremidade local (ou seja, um banco de dados) registrado em um agente cliente da Sincronização de Dados SQL ficar inacessível, o agente cliente não poderá ser excluído.

- **Causa**. O agente local não pode ser excluído porque o banco de dados inacessível ainda está registrado no agente. Quando você tenta excluir o agente, o processo de exclusão tenta acessar o banco de dados, o que falha.

- **Resolução**. Use uma “exclusão forçada” para excluir o banco de dados inacessível.

> [!NOTE]
> Se as tabelas de metadados de sincronização permanecerem após uma "exclusão forçada", use `deprovisioningutil.exe` para limpá-las.

### <a name="agent-connect"></a> O aplicativo do Agente de Sincronização local não pode conectar o serviço de sincronização local

- **Resolução**. Experimente as etapas a seguir:

  1. Saia do aplicativo.  
  1. Abra o Painel de Serviços de Componentes.  
    a. Na caixa de pesquisa da barra de tarefas, digite **services.msc**.  
    b. Clique duas vezes em **Serviços** nos resultados da pesquisa.  
  1. Pare o serviço de **Sincronização de Dados SQL**.
  1. Reinicie o serviço de **Sincronização de Dados SQL**.  
  1. Reabra o aplicativo.

## <a name="setup-and-maintenance-issues"></a>Problemas de instalação e manutenção

- [Recebi uma mensagem que indica "disco sem espaço"](#setup-space)

- [Não consigo excluir meu grupo de sincronização](#setup-delete)

- [Não consigo cancelar o registro de um banco de dados SQL Server local](#setup-unreg)

- [Não tenho privilégios suficientes para iniciar serviços do sistema](#setup-perms)

- [Um banco de dados tem o status "Desatualizado"](#setup-date)

- [Um grupo de sincronização tem o status "Desatualizado"](#setup-date2)

- [Um grupo de sincronização não pode ser excluído em até três minutos após a desinstalação ou interrupção do agente](#setup-delete2)

- [O que acontece quando eu restaurar um banco de dados perdido ou corrompido?](#setup-restore)

### <a name="setup-space"></a> Recebi uma mensagem que indica "disco sem espaço"

- **Causa**. A mensagem "disco sem espaço" poderá aparecer se arquivos restantes precisarem ser excluídos. Isso pode ser causado pelo software antivírus ou porque arquivos estavam abertos quando de operações de exclusão estavam sendo realizadas.

- **Resolução**. Exclua manualmente os arquivos de sincronização que estão na pasta %temp% (`del \*sync\* /s`). Em seguida, exclua os subdiretórios na pasta %temp%.

> [!IMPORTANT]
> Não exclua nenhum arquivo enquanto a sincronização estiver em andamento.

### <a name="setup-delete"></a> Não consigo excluir meu grupo de sincronização

Falha ao tentar excluir um grupo de sincronização. Qualquer um dos cenários a seguir pode resultar em uma falha ao excluir um grupo de sincronização:

- **Causa**. O agente cliente está offline.

- **Resolução**. Verifique se o agente cliente está online e tente novamente.

- **Causa**. O agente cliente está desinstalado ou ausente.

- **Resolução**. Se o agente cliente estiver desinstalado ou de outra maneira ausente:  
    a. Remova o arquivo XML do agente da pasta de instalação da Sincronização de Dados SQL caso o arquivo exista.  
    b. Instale o agente em um computador local (pode ser o mesmo ou em outro computador). Em seguida, envie a chave do agente que é gerada no portal para o agente que é mostrado como offline.

- **Causa**. Um banco de dados está offline.

- **Resolução**. Verifique se seus bancos de dados SQL e bancos de dados do SQL Server estão online.

- **Causa**. O grupo de sincronização está sendo provisionado ou sincronizado.

- **Resolução**. Aguarde até que o processo de provisionamento ou sincronização seja concluído e tente excluir o grupo de sincronização novamente.

### <a name="setup-unreg"></a> Não consigo cancelar o registro de um banco de dados SQL Server local

- **Causa**. Muito provavelmente, você está tentando cancelar o registro de um banco de dados que já foi excluído.

- **Resolução**. Para cancelar o registro de um banco de dados local do SQL Server, selecione o banco de dados e selecione **Forçar Exclusão**.

  Se essa operação não conseguir remover o banco de dados do grupo de sincronização:

  1. Pare e, em seguida, reinicie o serviço de host do agente cliente:  
    a. Selecione o menu **Iniciar**.  
    b. Insira **services.msc** na caixa de pesquisa.  
    c. Na seção **Programas** do painel de resultados da pesquisa, clique duas vezes em **Serviços**.  
    d. Clique com o botão direito do mouse no serviço **Sincronização de Dados SQL**.  
    e. Se o serviço estiver em execução, interrompa-o.  
    f. Clique com o botão direito do mouse no serviço e, em seguida, selecione **Iniciar**.  
    g. Verifique se o banco de dados ainda está registrado. Se ele não estiver mais registrado, você terminou. Caso contrário, continue com a próxima etapa.
  1. Abra o aplicativo do agente cliente (SqlAzureDataSyncAgent).
  1. Selecione **Editar Credenciais** e, em seguida, insira as credenciais para o banco de dados.
  1. Continue com o cancelamento do registro.

### <a name="setup-perms"></a> Não tenho privilégios suficientes para iniciar serviços do sistema

- **Causa**. Esse erro ocorre em duas situações:
  -   O nome de usuário e/ou a senha estão incorretos.
  -   A conta de usuário especificada não tem privilégios suficientes para fazer logon como serviço.

- **Resolução**. Conceda credenciais de logon como serviço à conta de usuário:

  1. Vá para **iniciar** > **Painel de Controle** > **Ferramentas Administrativas** > **Política de Segurança Local** > **Diretiva Local** > **Gerenciamento de Direitos do Usuário**.
  1. Selecione **Fazer logon como um serviço**.
  1. Na caixa de diálogo **Propriedades**, adicione a conta de usuário.
  1. Selecione **Aplicar** e, depois, **OK**.
  1. Feche todas as janelas.

### <a name="setup-date"></a> Um banco de dados tem o status "Desatualizado"

- **Causa**. A Sincronização de Dados SQL remove banco de dados que ficaram offline do serviço por 45 dias ou mais (contado a partir da hora em que o banco de dados ficou offline). Se um banco de dados estiver offline por 45 dias ou mais e, em seguida, voltar a ficar online, seu status será definido como **Desatualizado**.

- **Resolução**. Evite obter o status **Desatualizado** garantindo que nenhum dos bancos de dados fique offline por 45 dias ou mais.

  Se o status de um banco de dados for **Desatualizado**:

  1. Remova o banco de dados que tem um status de **Desatualizadas** do grupo de sincronização.
  1. Adicione o banco de dados de volta ao grupo de sincronização.

  > [!WARNING]
  > Você perderá todas as alterações feitas nesse banco de dados enquanto ele estava offline.

### <a name="setup-date2"></a> Um grupo de sincronização tem o status "Desatualizado"

- **Causa**. Se uma ou mais alterações não puderem ser aplicadas ao período de retenção inteiro de 45 dias, um grupo de sincronização poderá ficar desatualizado.

- **Resolução**. Para evitar obter um status **Desatualizado**, examine os resultados dos trabalhos de sincronização no visualizador de histórico regularmente. Investigue e resolva todas as alterações que não puderem ser aplicadas.

  Se o status de um grupo de sincronização for **Desatualizado**, exclua o grupo de sincronização e recrie-o.

### <a name="setup-delete2"></a> Um grupo de sincronização não pode ser excluído em até três minutos após a desinstalação ou interrupção do agente

Não é possível excluir um grupo de sincronização em até três minutos após a desinstalação ou interrupção do agente cliente da Sincronização de Dados SQL associado.

- **Resolução**.

  1. Remova um grupo de sincronização enquanto os agentes de sincronização associados estiverem online (recomendado).
  1. Se o agente estiver offline, mas estiver instalado, coloque-o online no computador local. Aguarde até que o status do agente seja exibido como **Online** no portal da Sincronização de Dados SQL. Em seguida, remova o grupo de sincronização.
  1. Se o agente estiver offline porque foi desinstalado:  
    a.  Remova o arquivo XML do agente da pasta de instalação da Sincronização de Dados SQL caso o arquivo exista.  
    b.  Instale o agente em um computador local (pode ser o mesmo ou em outro computador). Em seguida, envie a chave do agente que é gerada no portal para o agente que é mostrado como offline.  
    c. Tente excluir o grupo de sincronização.

### <a name="setup-restore"></a> O que acontece quando eu restaurar um banco de dados perdido ou corrompido?

Se você restaurar um banco de dados perdido ou corrompido de um backup, poderá haver uma não convergência de dados nos grupos de sincronização aos quais o banco de dados pertence.

## <a name="next-steps"></a>Próximas etapas
Para obter mais informações sobre a Sincronização de Dados SQL, consulte:

-   [Sincronizar dados entre vários bancos de dados locais e de nuvem com a Sincronização de Dados SQL do Azure](sql-database-sync-data.md)  
-   [Configurar a Sincronização de Dados SQL do Azure](sql-database-get-started-sql-data-sync.md)  
-   [Melhores práticas para a Sincronização de Dados SQL do Azure](sql-database-best-practices-data-sync.md)  
-   [Monitorar a Sincronização de Dados SQL do Azure com o Log Analytics](sql-database-sync-monitor-oms.md)  
-   Conclua os exemplos do PowerShell que mostram como configurar a Sincronização de Dados SQL:  
    -   [Usar o PowerShell para sincronização entre vários banco de dados SQL do Azure](scripts/sql-database-sync-data-between-sql-databases.md)  
    -   [Usar o PowerShell para sincronizar entre um Banco de Dados SQL do Azure e um banco de dados local do SQL Server](scripts/sql-database-sync-data-between-azure-onprem.md)  

Para saber mais sobre Bancos de Dados SQL, confira:

-   [Visão geral do Banco de Dados SQL](sql-database-technical-overview.md)
-   [Gerenciamento de ciclo de vida do banco de dados](https://msdn.microsoft.com/library/jj907294.aspx)
