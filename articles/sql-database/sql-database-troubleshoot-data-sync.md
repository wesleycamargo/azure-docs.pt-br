---
title: "Solução de problemas da Sincronização de Dados SQL do Azure (versão prévia) | Microsoft Docs"
description: "Saiba como solucionar problemas comuns com a Sincronização de Dados SQL do Azure (versão prévia)."
services: sql-database
ms.date: 11/13/2017
ms.topic: article
ms.service: sql-database
author: douglaslMS
ms.author: douglasl
manager: craigg
ms.openlocfilehash: 50cabbaa584671e52c1ea7efbd2ad990b8438272
ms.sourcegitcommit: 1d8612a3c08dc633664ed4fb7c65807608a9ee20
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/20/2017
---
# <a name="troubleshoot-issues-with-sql-data-sync-preview"></a>Solucionar problemas com a Sincronização de Dados SQL do Azure (versão prévia)

Esse artigo descreve como solucionar problemas conhecidos com a Sincronização de Dados SQL do Azure (versão prévia). Caso haja uma resolução para um problema, ela será fornecida aqui.

Para obter uma visão geral da Sincronização de Dados SQL (versão prévia), consulte [Sincronizar dados entre vários bancos de dados locais e de nuvem com a Sincronização de Dados SQL do Azure (versão prévia)](sql-database-sync-data.md).

## <a name="sync-issues"></a>Problemas de sincronização

### <a name="sync-fails-in-the-portal-ui-for-on-premises-databases-that-are-associated-with-the-client-agent"></a>Falha de sincronização na interface do usuário do portal em bancos de dados locais associados ao agente cliente

#### <a name="description-and-symptoms"></a>Descrição e sintomas

Falha de sincronização na interface do usuário do portal da Sincronização de Dados SQL (versão prévia) em bancos de dados locais associados ao agente. No computador local que executa o agente, são exibidos erros System.IO.IOException no Log de Eventos. Os erros informam que o disco não tem espaço suficiente.

#### <a name="resolution"></a>Resolução

Crie mais espaço na unidade na qual o diretório %TEMP% está localizado.

### <a name="my-sync-group-is-stuck-in-the-processing-state"></a>Meu grupo de sincronização travou no estado de processamento

#### <a name="description-and-symptoms"></a>Descrição e sintomas

Um grupo de sincronização na Sincronização de Dados SQL (versão prévia) esteve no estado de processamento por muito tempo. Ele não responde ao comando de **parada** e os logs não mostram novas entradas.

#### <a name="cause"></a>Causa

Qualquer uma das condições a seguir pode fazer com que um grupo de sincronização trave no estado de processamento:

-   **O agente cliente está offline**. Verifique se o agente cliente está online e tente novamente.

-   **O agente cliente está desinstalado ou ausente**. Se o agente cliente estiver desinstalado ou de outra maneira ausente:

    1. Remova o arquivo XML do agente da pasta de instalação da Sincronização de Dados SQL (versão prévia) caso o arquivo exista.
    2. Instale o agente em um computador local (pode ser o mesmo ou em outro computador). Em seguida, envie a chave do agente que é gerada no portal para o agente que é mostrado como offline.

-   **O serviço Sincronização de Dados SQL foi interrompido**.

    1. No menu **Iniciar**, pesquise por **Serviços**.
    2. Nos resultados da pesquisa, selecione **Serviços**.
    3. Localize o serviço **Sincronização de Dados SQL (versão prévia)**.
    4. Se o status do serviço for **Parado**, clique com o botão direito do mouse no nome do serviço e selecione **Iniciar**.

#### <a name="resolution"></a>Resolução

Se as informações anteriores não retirarem seu grupo de sincronização do estado de processamento, o Suporte da Microsoft poderá redefinir o status de seu grupo de sincronização. Para ter o status do grupo de sincronização redefinido, no [Fórum do banco de dados SQL do Azure](https://social.msdn.microsoft.com/Forums/azure/home?forum=ssdsgetstarted), crie uma postagem. Na postagem, inclua sua ID de assinatura e a ID do grupo de sincronização para o grupo que precisa ser redefinido. Um engenheiro do Suporte da Microsoft responderá à sua postagem e o informará quando o status tiver sido redefinido.

### <a name="i-see-erroneous-data-in-my-tables"></a>Há dados incorretos em minhas tabelas

#### <a name="description-and-symptoms"></a>Descrição e sintomas

Se as tabelas que têm o mesmo nome, mas que são de esquemas de banco de dados diferente forem incluídas em uma sincronização, você verá dados incorretos nas tabelas após a sincronização.

#### <a name="cause"></a>Causa

O processo de provisionamento da Sincronização de Dados SQL (versão prévia) usa as mesmas tabelas de acompanhamento para tabelas com o mesmo nome, mas que estão em esquemas diferentes. Por isso, as alterações de ambas as tabelas são refletidas na mesma tabela de rastreamento. Isso causa alterações de dados incorretas durante a sincronização.

#### <a name="resolution"></a>Resolução

Garanta que os nomes das tabelas envolvidas na sincronização sejam diferentes, mesmo que pertençam a esquemas diferentes em um banco de dados.

### <a name="i-see-inconsistent-primary-key-data-after-a-successful-sync"></a>Há dados de chave primária inconsistentes após uma sincronização bem-sucedida

#### <a name="description-and-symptoms"></a>Descrição e sintomas

Uma sincronização é relatada como bem-sucedida e o log não mostra nenhuma linha com falha ou ignorada, mas você observa que os dados da chave primária são inconsistentes entre os bancos de dados no grupo de sincronização.

#### <a name="cause"></a>Causa

Esse resultado ocorre por design. As alterações em qualquer coluna de chave primária resultam em dados inconsistentes nas linhas nas quais a chave primária foi alterada.

#### <a name="resolution"></a>Resolução

Para evitar esse problema, garanta que nenhum dado em uma coluna de chave primária é alterado.

Para corrigir esse problema depois que ele tiver ocorrido, exclua a linha que contém dados inconsistentes de todos os pontos de extremidade no grupo de sincronização. Em seguida, reinsira a linha.

### <a name="i-see-a-significant-degradation-in-performance"></a>Há uma degradação significativa no desempenho

#### <a name="description-and-symptoms"></a>Descrição e sintomas

O desempenho é degradado de forma significativa, possivelmente até o ponto em que você não pode nem mesmo abrir a interface do usuário da Sincronização de Dados.

#### <a name="cause"></a>Causa

A causa mais provável é um loop de sincronização. Um loop de sincronização ocorre quando uma sincronização por grupo de sincronização A aciona uma sincronização por grupo de sincronização B, que dispara uma sincronização por grupo de sincronização A. A situação real pode ser mais complexa e pode envolver mais de dois grupos de sincronização no loop. O problema é que há um acionamento circular de sincronização que é causado por grupos de sincronização sobrepostos entre si.

#### <a name="resolution"></a>Resolução

A melhor correção é a prevenção. Verifique se você não tem referências circulares nos grupos de sincronização. Nenhuma linha sincronizada por um grupo de sincronização pode ser sincronizada por outro grupo de sincronização.

### <a name="i-see-this-message-cannot-insert-the-value-null-into-the-column-column-column-does-not-allow-nulls-what-does-this-mean-and-how-can-i-fix-it"></a>Recebi essa mensagem que diz “Não é possível inserir o valor NULL na coluna \<coluna\>. A coluna não permite valores nulos”. O que isso significa e como posso corrigir o erro? 
Essa mensagem de erro indica que um dos dois problemas a seguir ocorreu:
-  Uma tabela não tem uma chave primária. Para corrigir esse problema, adicione uma chave primária a todas as tabelas que você está sincronizando.
-  Há uma cláusula WHERE na instrução CREATE INDEX. A Sincronização de Dados (versão prévia) não lidar com essa condição. Para corrigir esse problema, remova a cláusula WHERE ou realize as alterações manualmente para todos os bancos de dados. 
 
### <a name="how-does-data-sync-preview-handle-circular-references-that-is-when-the-same-data-is-synced-in-multiple-sync-groups-and-keeps-changing-as-a-result"></a>Como a Sincronização de Dados (versão prévia) trata referências circulares? Ou seja, quando os mesmos dados são sincronizados em vários grupos de sincronização, fazendo com que sejam continuamente alterados?
A Sincronização de Dados (versão prévia) não trata referências circulares. Evite usá-las. 

## <a name="client-agent-issues"></a>Problemas de agente cliente

### <a name="the-client-agent-install-uninstall-or-repair-fails"></a>Falhas de instalação, desinstalação ou reparo de agente cliente

#### <a name="cause"></a>Causa

Muitos cenários podem causar a falha. Para determinar a causa específica dessa falha, você precisa examinar os logs.

#### <a name="resolution"></a>Resolução

Para encontrar a causa específica para a falha ocorrida, você precisa gerar e examinar os logs do Windows Installer. Ative o log no prompt de comando. Por exemplo, se o arquivo AgentServiceSetup.msi baixado for LocalAgentHost.msi, gere e examine os arquivos de log usando as seguintes linhas de comando:

-   Para instalações: `msiexec.exe /i SQLDataSyncAgent-Preview-ENU.msi /l\*v LocalAgentSetup.InstallLog`
-   Para desinstalações: `msiexec.exe /x SQLDataSyncAgent-se-ENU.msi /l\*v LocalAgentSetup.InstallLog`

Também habilite o log para todas as instalações executadas pelo Windows Installer. O artigo da Base de Dados de Conhecimento Microsoft [Como habilitar o log do Windows Installer](https://support.microsoft.com/help/223300/how-to-enable-windows-installer-logging) fornece uma solução com um clique para ativar o log do Windows Installer. Ele também fornece o local desses logs.

### <a name="my-client-agent-doesnt-work"></a>Meu agente cliente não funciona

#### <a name="description-and-symptoms"></a>Descrição e sintomas

Você obtém as mensagens de erro a seguir quando tenta usar o agente cliente:

“Falha na sincronização com a exceção Erro ao tentar desserializar o parâmetro www.microsoft.com/.../05:GetBatchInfoResult. Consulte InnerException para obter mais detalhes.”

“Mensagem de exceção interna: o tipo 'Microsoft.Synchronization.ChangeBatch' é um tipo de coleção inválido porque ele não tem um construtor padrão.”

#### <a name="cause"></a>Causa

Este é um problema conhecido com a instalação da Sincronização de Dados SQL (versão prévia). A causa mais provável dessa mensagem é uma das seguintes:

-   Você está executando o Windows 8 Developer Preview.
-   Você tem o .NET Framework 4.5 instalado.

#### <a name="resolution"></a>Resolução

Certifique-se de que instalou o agente cliente em um computador que não esteja executando o Windows 8 Developer Preview e que o .NET Framework 4.5 não esteja instalado.

### <a name="my-client-agent-doesnt-work-after-i-cancel-the-uninstall"></a>Meu agente cliente não funciona depois que eu cancelo a desinstalação

#### <a name="description-and-symptoms"></a>Descrição e sintomas

O agente cliente não funciona mesmo após você cancelar a desinstalação.

#### <a name="cause"></a>Causa

Esse problema ocorre porque o agente cliente da Sincronização de Dados SQL (versão prévia) não armazena credenciais.

#### <a name="resolution"></a>Resolução

Você pode tentar estas duas soluções:

-   Use services.msc para reinserir suas credenciais do agente cliente.
-   Desinstale esse agente cliente e instale um novo. Baixe e instale o último agente cliente no [Centro de Download](http://go.microsoft.com/fwlink/?linkid=221479).

### <a name="my-database-isnt-listed-in-the-agent-list"></a>Meu banco de dados não está listado na lista de agentes

#### <a name="description-and-symptoms"></a>Descrição e sintomas

Quando você tenta adicionar um banco de dados existente do SQL Server a um grupo de sincronização, o banco de dados não aparece na lista de agentes.

#### <a name="cause"></a>Causa

Estes cenários podem causar esse problema:

-   O agente cliente e grupo de sincronização estão em diferentes data centers.
-   A lista de bancos de dados do agente cliente não é atual.

#### <a name="resolution"></a>Resolução

A solução depende da causa.

- **O agente cliente e grupo de sincronização estão em diferentes data centers**

    O agente cliente e o grupo de sincronização devem estar no mesmo data center. Para configurar isso, você tem duas opções:

    -   Crie um novo agente no data center no qual o grupo de sincronização está localizado. Em seguida, registre o banco de dados nesse agente.
    -   Exclua o grupo de sincronização atual. Em seguida, crie novamente o grupo de sincronização no data center no qual o agente está localizado.

- **A lista de bancos de dados do agente cliente não é atual**

    Pare e, em seguida, reinicie o serviço do agente cliente.

    O agente local baixa a lista de bancos de dados associados somente no primeiro envio da chave do agente. Ele não baixa a lista de bancos de dados associados em envios de chave do agente subsequentes. Os bancos de dados registrados durante uma movimentação do agente não aparecem na instância do agente original.

### <a name="client-agent-doesnt-start-error-1069"></a>O agente cliente não é iniciado (erro 1069)

#### <a name="description-and-symptoms"></a>Descrição e sintomas

Você descobriu que o agente não está sendo executado em um computador que hospeda o SQL Server. Ao tentar iniciar o agente manualmente, você obtém uma caixa de diálogo de erro com a mensagem de erro “Erro 1069: o serviço não foi iniciado devido a uma falha de logon”.

![Caixa de diálogo do erro 1069 da Sincronização de Dados](media/sql-database-troubleshoot-data-sync/sync-error-1069.png)

#### <a name="cause"></a>Causa

Uma causa provável desse erro é que a senha no servidor local foi alterada, já que você criou o agente e a senha do agente.

#### <a name="resolution"></a>Resolução

Atualize a senha do agente para a senha atual do servidor:

1. Localize o serviço de versão prévia do agente cliente da Sincronização de Dados SQL (versão prévia).  
    a. Selecione **Iniciar**.  
    b. Insira **services.msc** na caixa de pesquisa.  
    c. Nos resultados da pesquisa, selecione **Serviços**.  
    d. Na janela **Serviços**, role até a entrada de **Versão prévia do Agente da Sincronização de Dados SQL (versão prévia)**.  
2. Clique com o botão direito do mouse em **Visualização do Agente da Sincronização de Dados SQL (versão prévia)** e, em seguida, selecione **Parar**.
3. Clique com o botão direito do mouse em **Visualização do Agente da Sincronização de Dados SQL (Versão Prévia)** e, em seguida, selecione **Propriedades**.
4. Em **Propriedades da Visualização do Agente da Sincronização de Dados SQL (Versão Prévia)**, clique na guia **Fazer Logon**.
5. Na caixa de **Senha**, insira sua senha.
6. Na caixa de **Confirmar Senha**, insira sua senha novamente.
7. Selecione **Aplicar** e, depois, **OK**.
8. Na janela **Serviços**, clique com o botão direito do mouse no serviço **Visualização do Agente de Sincronização de Dados SQL (Versão Prévia)** e, em seguida, clique em **Iniciar**.
9. Feche a janela **Serviços**.

### <a name="i-cant-submit-the-agent-key"></a>Não consigo enviar a chave do agente

#### <a name="description-and-symptoms"></a>Descrição e sintomas

Depois de criar ou recriar uma chave para um agente, você tenta enviar essa chave pelo aplicativo SqlAzureDataSyncAgent. O envio não é concluído.

![Caixa de diálogo Erro de Sincronização – não é possível enviar a chave do agente](media/sql-database-troubleshoot-data-sync/sync-error-cant-submit-agent-key.png)

Antes de prosseguir, verifique as seguintes condições:

-   O serviço Windows Sincronização de Dados SQL (versão prévia) está em execução.  
-   A conta de serviço do serviço Windows de versão prévia da Sincronização de Dados SQL (versão prévia) tem acesso à rede.    
-   O agente cliente pode contatar o Serviço de Localizador. Verifique se a chave do Registro a seguir tem o valor https://locator.sync.azure.com/LocatorServiceApi.svc:  
    -   Em um computador x86: `HKEY\_LOCAL\_MACHINE\\SOFTWARE\\Microsoft\\SQL Azure Data Sync\\LOCATORSVCURI`  
    -   Em um computador x64: `HKEY\_LOCAL\_MACHINE\\SOFTWARE\\Wow6432Node\\Microsoft\\SQL Azure Data Sync\\LOCATORSVCURI`

#### <a name="cause"></a>Causa

A chave do agente identifica exclusivamente cada agente local. A chave deve atender às duas condições:

-   A chave do agente cliente no servidor da Sincronização de Dados SQL (versão prévia) e o computador local devem ser idênticos.
-   A chave do agente cliente pode ser usada apenas uma vez.

#### <a name="resolution"></a>Resolução

Se o agente não estiver funcionando, isso indicará que uma ou ambas as condições não foram atendidas. Para fazer com que o agente funcione novamente:

1. Gere uma nova chave.
2. Aplique a nova chave ao agente.

Para aplicar a nova chave ao agente:

1. No Explorador de Arquivos, vá até o diretório de instalação do agente. O diretório de instalação padrão é C:\\Arquivos de Programa (x86)\\Sincronização de Dados SQL Microsoft.
2. Clique duas vezes no subdiretório.
3. Abra o aplicativo SqlAzureDataSyncAgent.
4. Selecione **Enviar Chave do Agente**.
5. Cole a chave da área de transferência no espaço fornecido.
6. Selecione **OK**.
7. Feche o programa.

### <a name="the-client-agent-cant-be-deleted-from-the-portal-if-its-associated-on-premises-database-is-unreachable"></a>O agente cliente não poderá ser excluído do portal se o banco de dados local associado estiver inacessível

#### <a name="description-and-symptoms"></a>Descrição e sintomas

Se um ponto de extremidade local (ou seja, um banco de dados) registrado em um agente cliente da Sincronização de Dados SQL (versão prévia) ficar inacessível, o agente cliente não poderá ser excluído.

#### <a name="cause"></a>Causa

O agente local não pode ser excluído porque o banco de dados inacessível ainda está registrado no agente. Quando você tenta excluir o agente, o processo de exclusão tenta acessar o banco de dados, o que falha.

#### <a name="resolution"></a>Resolução

Use uma “exclusão forçada” para excluir o banco de dados inacessível.

> [!NOTE]
> Se, após uma “exclusão forçada”, as tabelas de metadados de sincronização permanecerem, use deprovisioningutil.exe para limpá-las.

### <a name="local-sync-agent-app-cant-connect-to-the-local-sync-service"></a>O aplicativo Agente de Sincronização Local não pode se conectar ao serviço de sincronização local

#### <a name="resolution"></a>Resolução

Experimente as etapas a seguir:

1. Saia do aplicativo.  
2. Abra o Painel de Serviços de Componentes.  
    a. Na caixa de pesquisa da barra de tarefas, digite **services.msc**.  
    b. Clique duas vezes em **Serviços** nos resultados da pesquisa.  
3. Pare o serviço **Visualização da Sincronização de Dados SQL (versão prévia)**.
4. Reinicie o serviço **Visualização da Sincronização de Dados SQL (Versão Prévia)**.  
5. Reabra o aplicativo.

## <a name="setup-and-maintenance-issues"></a>Problemas de instalação e manutenção

### <a name="i-get-a-disk-out-of-space-message"></a>Recebi uma mensagem que indica “Disco sem espaço”

#### <a name="cause"></a>Causa

A mensagem "disco sem espaço" poderá aparecer se arquivos restantes precisarem ser excluídos. Isso pode ser causado pelo software antivírus ou porque arquivos estavam abertos quando de operações de exclusão estavam sendo realizadas.

#### <a name="resolution"></a>Resolução

Exclua manualmente os arquivos de sincronização que estão na pasta %temp% (`del \*sync\* /s`). Em seguida, exclua os subdiretórios na pasta %temp%.

> [!IMPORTANT]
> Não exclua nenhum arquivo enquanto a sincronização estiver em andamento.

### <a name="i-cant-delete-my-sync-group"></a>Não consigo excluir meu grupo de sincronização

#### <a name="description-and-symptoms"></a>Descrição e sintomas

Falha ao tentar excluir um grupo de sincronização.

#### <a name="causes"></a>Causas

Qualquer um dos cenários a seguir pode resultar em uma falha ao excluir um grupo de sincronização:

-   O agente cliente está offline.
-   O agente cliente está desinstalado ou ausente. 
-   Um banco de dados está offline. 
-   O grupo de sincronização está sendo provisionado ou sincronizado. 

#### <a name="resolution"></a>Resolução

Para resolver a falha ao excluir um grupo de sincronização:

-   Verifique se o agente cliente está online e tente novamente.
-   Se o agente cliente estiver desinstalado ou de outra maneira ausente:  
    a. Remova o arquivo XML do agente da pasta de instalação da Sincronização de Dados SQL (versão prévia) caso o arquivo exista.  
    b. Instale o agente em um computador local (pode ser o mesmo ou em outro computador). Em seguida, envie a chave do agente que é gerada no portal para o agente que é mostrado como offline.
-   Certifique-se de que o serviço de Sincronização de Dados SQL (versão prévia) está em execução:  
    a. No menu **Iniciar**, pesquise por **Serviços**.  
    b. Nos resultados da pesquisa, selecione **Serviços**.  
    c. Localize o serviço **Visualização da Sincronização de Dados SQL (versão prévia)**.  
    d. Se o status do serviço for **Parado**, clique com o botão direito do mouse no nome do serviço e selecione **Iniciar**.
-   Verifique se seus bancos de dados SQL e bancos de dados do SQL Server estão online.
-   Aguarde até que o processo de provisionamento ou sincronização seja concluído e tente excluir o grupo de sincronização novamente.

### <a name="i-cant-unregister-an-on-premises-sql-server-database"></a>Não consigo cancelar o registro de um banco de dados local do SQL Server

#### <a name="cause"></a>Causa

Muito provavelmente, você está tentando cancelar o registro de um banco de dados que já foi excluído.

#### <a name="resolution"></a>Resolução

Para cancelar o registro de um banco de dados local do SQL Server, selecione o banco de dados e selecione **Forçar Exclusão**.

Se essa operação não conseguir remover o banco de dados do grupo de sincronização:

1. Pare e, em seguida, reinicie o serviço de host do agente cliente:  
    a. Selecione o menu **Iniciar**.  
    b. Insira **services.msc** na caixa de pesquisa.  
    c. Na seção **Programas** do painel de resultados da pesquisa, clique duas vezes em **Serviços**.  
    d. Clique com o botão direito do mouse no serviço **Sincronização de Dados SQL (versão prévia)**.  
    e. Se o serviço estiver em execução, interrompa-o.  
    f. Clique com o botão direito do mouse no serviço e, em seguida, selecione **Iniciar**.  
    g. Verifique se o banco de dados ainda está registrado. Se ele não estiver mais registrado, você terminou. Caso contrário, continue com a próxima etapa.
2. Abra o aplicativo do agente cliente (SqlAzureDataSyncAgent).
3. Selecione **Editar Credenciais** e, em seguida, insira as credenciais para o banco de dados.
4. Continue com o cancelamento do registro.

### <a name="i-dont-have-sufficient-privileges-to-start-system-services"></a>Não tenho privilégios suficientes para iniciar serviços do sistema

#### <a name="cause"></a>Causa

Esse erro ocorre em duas situações:
-   O nome de usuário e/ou a senha estão incorretos.
-   A conta de usuário especificada não tem privilégios suficientes para fazer logon como serviço.

#### <a name="resolution"></a>Resolução

Conceda credenciais de logon como serviço à conta de usuário:

1. Vá para **iniciar** > **Painel de Controle** > **Ferramentas Administrativas** > **Política de Segurança Local** > **Diretiva Local** > **Gerenciamento de Direitos do Usuário**.
2. Selecione **Fazer logon como um serviço**.
3. Na caixa de diálogo **Propriedades**, adicione a conta de usuário.
4. Selecione **Aplicar** e, depois, **OK**.
5. Feche todas as janelas.

### <a name="a-database-has-an-out-of-date-status"></a>Um banco de dados tem o status “Desatualizado”

#### <a name="cause"></a>Causa

A Sincronização de Dados SQL (versão prévia) remove banco de dados que ficaram offline do serviço por 45 dias ou mais (contado a partir da hora em que o banco de dados ficou offline). Se um banco de dados estiver offline por 45 dias ou mais e, em seguida, voltar a ficar online, seu status será definido como **Desatualizado**.

#### <a name="resolution"></a>Resolução

Evite obter o status **Desatualizado** garantindo que nenhum dos bancos de dados fique offline por 45 dias ou mais.

Se o status de um banco de dados for **Desatualizado**:

1. Remova o banco de dados que tem um status de **Desatualizadas** do grupo de sincronização.
2. Adicione o banco de dados de volta ao grupo de sincronização.

> [!WARNING]
> Você perderá todas as alterações feitas nesse banco de dados enquanto ele estava offline.

### <a name="a-sync-group-has-an-out-of-date-status"></a>Um grupo de sincronização tem o status “Desatualizado”

#### <a name="cause"></a>Causa

Se uma ou mais alterações não puderem ser aplicadas ao período de retenção inteiro de 45 dias, um grupo de sincronização poderá ficar desatualizado.

#### <a name="resolution"></a>Resolução

Para evitar obter um status **Desatualizado**, examine os resultados dos trabalhos de sincronização no visualizador de histórico regularmente. Investigue e resolva todas as alterações que não puderem ser aplicadas.

Se o status de um grupo de sincronização for **Desatualizado**, exclua o grupo de sincronização e recrie-o.

### <a name="a-sync-group-cant-be-deleted-within-three-minutes-of-uninstalling-or-stopping-the-agent"></a>Um grupo de sincronização não pode ser excluído em até três minutos após a desinstalação ou interrupção do agente

#### <a name="description-and-symptoms"></a>Descrição e sintomas

Não é possível excluir um grupo de sincronização em até três minutos após a desinstalação ou interrupção do agente cliente da Sincronização de Dados SQL (versão prévia) associado.

#### <a name="resolution"></a>Resolução

1. Remova um grupo de sincronização enquanto os agentes de sincronização associados estiverem online (recomendado).
2. Se o agente estiver offline, mas estiver instalado, coloque-o online no computador local. Aguarde até que o status do agente seja exibido como **Online** no portal da Sincronização de Dados SQL (versão prévia). Em seguida, remova o grupo de sincronização.
3. Se o agente estiver offline porque foi desinstalado:  
    a.  Remova o arquivo XML do agente da pasta de instalação da Sincronização de Dados SQL (versão prévia) caso o arquivo exista.  
    b.  Instale o agente em um computador local (pode ser o mesmo ou em outro computador). Em seguida, envie a chave do agente que é gerada no portal para o agente que é mostrado como offline.  
    c. Tente excluir o grupo de sincronização.

### <a name="what-happens-when-i-restore-a-lost-or-corrupted-database"></a>O que acontece quando eu restaurar um banco de dados perdido ou corrompido?

Se você restaurar um banco de dados perdido ou corrompido de um backup, poderá haver não convergência de dados no grupo de sincronização ao qual pertence o banco de dados.

## <a name="next-steps"></a>Próximas etapas
Para obter mais informações sobre a Sincronização de Dados SQL (versão prévia), consulte:

-   [Sincronizar dados entre vários bancos de dados locais e de nuvem com a Sincronização de Dados SQL do Azure (versão prévia)](sql-database-sync-data.md)  
-   [Configurar a Sincronização de Dados SQL do Azure (versão prévia)](sql-database-get-started-sql-data-sync.md)  
-   [Práticas recomendadas para a Sincronização de Dados SQL do Azure (versão prévia)](sql-database-best-practices-data-sync.md)  
-   [Monitorar a Sincronização de Dados SQL do Azure (versão prévia) com o Log Analytics do OMS](sql-database-sync-monitor-oms.md)  
-   Conclua os exemplos do PowerShell que mostram como configurar a Sincronização de Dados SQL (versão prévia):  
    -   [Usar o PowerShell para sincronização entre vários banco de dados SQL do Azure](scripts/sql-database-sync-data-between-sql-databases.md)  
    -   [Usar o PowerShell para sincronizar entre um Banco de Dados SQL do Azure e um banco de dados local do SQL Server](scripts/sql-database-sync-data-between-azure-onprem.md)  
-   [Baixe a documentação da API REST de Sincronização de Dados SQL (versão prévia)](https://github.com/Microsoft/sql-server-samples/raw/master/samples/features/sql-data-sync/Data_Sync_Preview_REST_API.pdf?raw=true)

Para saber mais sobre Bancos de Dados SQL, confira:

-   [Visão geral do Banco de Dados SQL](sql-database-technical-overview.md)
-   [Gerenciamento de ciclo de vida do banco de dados](https://msdn.microsoft.com/library/jj907294.aspx)
