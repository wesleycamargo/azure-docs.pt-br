---
title: "Solução de problemas da Sincronização de Dados SQL do Azure | Microsoft Docs"
description: "Saiba como solucionar problemas comuns com a Sincronização de Dados SQL do Azure"
services: sql-database
ms.date: 11/2/2017
ms.topic: article
ms.service: sql-database
author: douglaslMS
ms.author: douglasl
manager: craigg
ms.openlocfilehash: bbfcac5a54f04f20dbdeeecef7c06b91128b8c6a
ms.sourcegitcommit: ce934aca02072bdd2ec8d01dcbdca39134436359
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/08/2017
---
# <a name="troubleshoot-issues-with-azure-sql-data-sync-preview"></a>Solucionar problemas com a Sincronização de Dados SQL do Azure (versão prévia)

Este artigo descreve como solucionar problemas atuais conhecidos pela equipe da Sincronização de Dados SQL (versão prévia). Caso haja uma solução alternativa para um problema, ela será fornecida aqui.

Para obter uma visão geral da Sincronização de Dados SQL, consulte [Sincronizar dados entre vários bancos de dados locais e de nuvem com a Sincronização de Dados SQL do Azure (versão prévia)](sql-database-sync-data.md).
                                                           
## <a name="my-client-agent-doesnt-work"></a>Meu agente cliente não funciona

### <a name="description-and-symptoms"></a>Descrição e sintomas

Você obtém as mensagens de erro a seguir quando tenta usar o agente cliente.

“Falha na sincronização com a exceção Erro ao tentar desserializar o parâmetro www.microsoft.com/.../05:GetBatchInfoResult. Consulte InnerException para obter mais detalhes.

“Mensagem de exceção interna: o tipo 'Microsoft.Synchronization.ChangeBatch' é um tipo de coleção inválido porque ele não tem um construtor padrão.”


### <a name="cause"></a>Causa

Esse erro é um problema na Sincronização de Dados SQL (versão prévia).

A causa mais provável desse problema é:

-   Você está executando o Windows 8 Developer Preview ou

-   Você tem o .NET 4.5 instalado.

### <a name="solution-or-workaround"></a>Solução ou solução alternativa

Instale o agente cliente em um computador que não está executando o Windows 8 Developer Preview e verifique se o .NET Framework 4.5 não está instalado.

## <a name="my-client-agent-doesnt-work-after-i-cancel-the-uninstall"></a>Meu agente cliente não funciona depois que eu cancelo a desinstalação

### <a name="description-and-symptoms"></a>Descrição e sintomas

O agente cliente não funciona embora a desinstalação tenha sido cancelada.

### <a name="cause"></a>Causa

Esse problema ocorre porque o agente cliente da Sincronização de Dados SQL (versão prévia) não armazena credenciais.

### <a name="solution-or-workaround"></a>Solução ou solução alternativa

Existem duas soluções que você pode tentar:

-   Primeiro, use services.msc para reinserir suas credenciais do agente cliente.

-   Em seguida, desinstale esse agente cliente e instale um novo. Baixe e instale o último agente cliente no [Centro de Download](http://go.microsoft.com/fwlink/?linkid=221479).

## <a name="my-database-isnt-listed-in-the-agent-dropdown"></a>Meu banco de dados não está listado no menu suspenso do agente

### <a name="description-and-symptoms"></a>Descrição e sintomas

Quando você tenta adicionar um banco de dados existente do SQL Server a um grupo de sincronização, o banco de dados não é listado na lista suspensa.

### <a name="cause"></a>Causa

Há várias causas possíveis para esse problema:

-   O agente cliente e o grupo de sincronização estão em data centers diferentes.

-   A lista de bancos de dados do agente cliente não é atual.

### <a name="solution"></a>Solução

A solução depende da causa.

#### <a name="the-client-agent-and-sync-group-are-in-different-data-centers"></a>O agente cliente e o grupo de sincronização estão em data centers diferentes

Você deve ter o agente cliente e o grupo de sincronização no mesmo data center. Defina essa configuração seguindo um destes procedimentos:

-   Crie um novo agente no mesmo data center do grupo de sincronização. Em seguida, registre o banco de dados nesse agente. Consulte [Como instalar um agente cliente da Sincronização de Dados SQL (versão prévia)](#install-a-sql-data-sync-client-agent) para obter mais informações.

-   Exclua o grupo de sincronização atual. Em seguida, recrie-o no mesmo data center do agente.

#### <a name="the-client-agents-list-of-databases-is-not-current"></a>A lista de bancos de dados do agente cliente não é atual

Pare e, em seguida, reinicie o serviço do agente cliente.
O agente local baixa a lista de bancos de dados associados somente no primeiro envio da chave do agente, não em envios posteriores de chave do agente. Assim, os bancos de dados registrados durante uma movimentação do agente não aparecem na instância do agente original.

## <a name="client-agent-doesnt-start-error-1069"></a>O agente cliente não é iniciado (erro 1069)

### <a name="description-and-symptoms"></a>Descrição e sintomas

Você descobriu que o agente não está sendo executado em um computador que hospeda o SQL Server. Ao tentar iniciar o agente manualmente, você obtém uma caixa de diálogo de erro com a mensagem de erro “Erro 1069: o serviço não foi iniciado devido a uma falha de logon”.

![Caixa de diálogo do erro 1069 da Sincronização de Dados](media/sql-database-troubleshoot-data-sync/sync-error-1069.png)

### <a name="cause"></a>Causa

Uma causa provável desse erro é que a senha no servidor local foi alterada, já que você criou o agente e forneceu a ele uma senha de logon.

### <a name="solution-or-workaround"></a>Solução ou solução alternativa

Atualize a senha do agente para a senha atual do servidor.

1. Localize o serviço de versão prévia do agente cliente da Sincronização de Dados SQL (versão prévia).

    a. Clique em **Iniciar**.

    b. Digite “services.msc” na caixa de pesquisa.

    c. Nos resultados da pesquisa, clique em “Serviços”.

    d. Na janela **Serviços**, role até a entrada de **Versão prévia do Agente da Sincronização de Dados SQL (versão prévia)**.

2. Clique com o botão direito do mouse na entrada e selecione **Parar**.

3. Clique com o botão direito do mouse na entrada e, em seguida, clique em **Propriedades**.

4. Na janela **Propriedades da versão prévia do Agente da Sincronização de Dados SQL (versão prévia)**, clique na guia **Fazer Logon**.

5. Insira a senha na caixa de texto Senha.

6. Confirme a senha na caixa de texto Confirmar Senha.

7. Clique em **Aplicar** e, em seguida, clique em **OK**.

8. Na janela **Serviços**, clique com o botão direito do mouse no serviço **Versão prévia do Agente de Sincronização de Dados SQL (versão prévia)** e, em seguida, clique em **Iniciar**.

9. Feche a janela **Serviços**.

## <a name="i-get-a-disk-out-of-space-message"></a>Recebi uma mensagem que indica “Disco sem espaço”

### <a name="cause"></a>Causa

A mensagem “Disco sem espaço” pode ser exibida quando os arquivos que devem ser excluídos permanecem no local. Essa condição pode ocorrer devido a um software antivírus ou devido aos arquivos estarem abertos quando há a tentativa de executar operações de exclusão.

### <a name="solution"></a>Solução

A solução é excluir manualmente os arquivos de sincronização em `%temp%` (`del \*sync\* /s`) e, em seguida, remover os subdiretórios também.

> [!IMPORTANT]
> Aguarde até que a sincronização seja concluída antes de excluir os arquivos.

## <a name="i-cannot-delete-my-sync-group"></a>Não consigo excluir meu grupo de sincronização

### <a name="description-and-symptoms"></a>Descrição e sintomas

Há uma falha ao tentar excluir um grupo de sincronização.

### <a name="causes"></a>Causas

Qualquer uma das ações a seguir pode resultar em uma falha ao excluir um grupo de sincronização.

-   O agente cliente está offline.

-   O agente cliente está desinstalado ou ausente. 

-   Um banco de dados está offline. 

-   O grupo de sincronização está sendo provisionado ou sincronizado. 

### <a name="solutions"></a>Soluções

Para resolver a falha ao excluir um grupo de sincronização, verifique o seguinte:

-   Verifique se o agente cliente está online e tente novamente.

-   Se o agente cliente estiver desinstalado ou de outra maneira ausente:

    a. Remova o arquivo XML do agente da pasta de instalação da Sincronização de Dados SQL (versão prévia) caso o arquivo exista.

    b. Instale o agente no mesmo computador local ou em outro computador, envie a chave do agente por meio do portal gerada para o agente que é exibido offline.

-   **O serviço Sincronização de Dados SQL (versão prévia) foi interrompido.**

    a. No menu **Iniciar**, pesquise Serviços.

    b. Clique em Serviços nos resultados da pesquisa.

    c. Localize o serviço **Visualização da Sincronização de Dados SQL (versão prévia)**.

    d. Se o status do serviço for **Parado**, clique com o botão direito do mouse no nome do serviço e selecione **Iniciar** no menu suspenso.

-   Verifique seus Bancos de Dados SQL e bancos de dados do SQL Server para garantir que eles estão online.

-   Aguarde até que o processo de provisionamento ou sincronização seja concluído. Em seguida, tente novamente excluir o grupo de sincronização.

## <a name="sync-fails-in-the-portal-ui-for-on-premises-databases-associated-with-the-client-agent"></a>Falha de sincronização na interface do usuário do portal em bancos de dados locais associados ao agente cliente

### <a name="description-and-symptoms"></a>Descrição e sintomas

Falha de sincronização na interface do usuário do portal da Sincronização de Dados SQL (versão prévia) em bancos de dados locais associados ao agente. No computador local que executa o agente, são exibidos erros System.IO.IOException no Log de Eventos, informando que o disco não tem espaço suficiente.

### <a name="solution-or-workaround"></a>Solução ou solução alternativa

Crie mais espaço na unidade na qual reside o diretório %TEMP%.

## <a name="i-cant-unregister-an-on-premises-sql-server-database"></a>Não consigo cancelar o registro de um banco de dados local do SQL Server

### <a name="cause"></a>Causa

Muito provavelmente, você está tentando cancelar o registro de um banco de dados que já foi excluído.

### <a name="solution-or-workaround"></a>Solução ou solução alternativa

Para cancelar o registro de um banco de dados local do SQL Server, selecione o banco de dados e clique em **Forçar Exclusão**.

Se essa operação falhar em remover o banco de dados do grupo de sincronização, faça o seguinte:

1. Pare e, em seguida, reinicie o serviço de host do agente cliente.

    a. Clique no menu Iniciar.

    b. Insira *services.msc* na caixa de pesquisa.

    c. Na seção Programas do painel de resultados, clique duas vezes em **Serviços**.

    d. Localize e clique com o botão direito do mouse no serviço **Sincronização de Dados SQL (versão prévia)**.

    e. Se o serviço estiver em execução, interrompa-o.

    f. Clique com o botão direito do mouse e selecione **Iniciar**.

    g. Verifique se o banco de dados não está mais registrado. Se ele não estiver mais registrado, você terminou. Caso contrário, continue com a próxima etapa.

2. Abra o aplicativo do agente cliente (SqlAzureDataSyncAgent).

3. Clique em **Editar Credenciais** e forneça as credenciais do banco de dados, de modo que ele fique acessível.

4. Continue com o cancelamento do registro.

## <a name="i-cannot-submit-the-agent-key"></a>Não consigo enviar a Chave do Agente

### <a name="description-and-symptoms"></a>Descrição e sintomas

Depois de criar ou recriar uma chave para um agente, você tenta enviar essa chave pelo aplicativo SqlAzureDataSyncAgent, mas o envio não é concluído.

![Caixa de diálogo Erro de Sincronização – não é possível enviar a chave do agente](media/sql-database-troubleshoot-data-sync/sync-error-cant-submit-agent-key.png)

Antes de continuar, verifique se uma falha de uma das condições a seguir não é a causa do problema.

-   O serviço Windows Sincronização de Dados SQL (versão prévia) está em execução.

-   A conta de serviço do serviço Windows de versão prévia da Sincronização de Dados SQL (versão prévia) tem acesso à rede.

-   O agente cliente pode contatar o Serviço de Localizador. Verifique se a chave do Registro a seguir tem o valor “https://locator.sync.azure.com/LocatorServiceApi.svc”

    -   Em um computador x86: `HKEY\_LOCAL\_MACHINE\\SOFTWARE\\Microsoft\\SQL Azure Data Sync\\LOCATORSVCURI`

    -   Em um computador x64: `HKEY\_LOCAL\_MACHINE\\SOFTWARE\\Wow6432Node\\Microsoft\\SQL Azure Data Sync\\LOCATORSVCURI`

### <a name="cause"></a>Causa

A chave do agente identifica exclusivamente cada agente local. A chave deve atender às duas condições para que ela funcione:

-   A chave do agente cliente no servidor da Sincronização de Dados SQL (versão prévia) e o computador local devem ser idênticos.

-   A chave do agente cliente pode ser usada apenas uma vez.

### <a name="solution-or-workaround"></a>Solução ou solução alternativa

Se o agente não estiver funcionando, isso indicará que uma ou ambas dessas condições não foram atendidas. Para fazer com que o agente funcione novamente:

1. Gere uma nova chave.

2. Aplique a nova chave ao agente.

Para aplicar a nova chave ao agente, faça o seguinte:

1. Use o Explorador de Arquivos para navegar para o diretório de instalação do agente. O diretório de instalação padrão é `c:\\program files (x86)\\microsoft sql data sync`.

2. Clique duas vezes no subdiretório `bin`.

3. Inicie o aplicativo `SqlAzureDataSyncAgent`.

4. Clique em **Enviar Chave do Agente**.

5. Cole a chave na área de transferência no espaço fornecido.

6. Clique em **OK**.

7. Feche o programa.

## <a name="i-do-not-have-sufficient-privileges-to-start-system-services"></a>Não tenho privilégios suficientes para iniciar serviços do sistema

### <a name="cause"></a>Causa

Esse erro ocorre em duas situações:

-   O nome de usuário e/ou a senha estão incorretos.

-   A conta de usuário especificada não tem privilégios suficientes para fazer logon como serviço.

### <a name="solution-or-workaround"></a>Solução ou solução alternativa

Conceda credenciais de logon como serviço à conta de usuário.

1. Navegue para **Iniciar | Painel de Controle | Ferramentas Administrativas | Política de Segurança Local | Política Local | Gerenciamento de Direitos do Usuário**.

2. Localize e clique na entrada **Fazer logon como serviço**.

3. Adicione a conta de usuário à caixa de diálogo de **Propriedades Fazer logon como serviço**.

4. Clique em **Aplicar** e, em seguida, em **OK**.

5. Feche as janelas.

## <a name="local-sync-agent-app-is-unable-to-connect-to-the-local-sync-service"></a>O aplicativo Agente de Sincronização Local não pode se conectar ao serviço de sincronização local

### <a name="solution-or-workaround"></a>Solução ou solução alternativa

Experimente as etapas a seguir:

1. Saia do aplicativo.

2. Abra o Painel de Serviços de Componentes.

    a. Na caixa de pesquisa da barra de tarefas, digite “services.msc”.

    b. Clique duas vezes em “Serviços” nos resultados da pesquisa.

3. Pare e, em seguida, reinicie o serviço “Versão prévia da Sincronização de Dados SQL (versão prévia)”.

4. Reinicie o aplicativo.

## <a name="install-uninstall-or-repair-fails"></a>Falhas de instalação, desinstalação ou reparo

### <a name="cause"></a>Causa

Há muitas causas possíveis para a falha. Para determinar a causa específica dessa falha, você precisa examinar os logs.

### <a name="solution-or-workaround"></a>Solução ou solução alternativa

Para encontrar a causa específica para a falha ocorrida, você precisa gerar e examinar os logs do Windows Installer. Ative o log na linha de comando. Por exemplo, suponha que o arquivo AgentServiceSetup.msi baixado seja LocalAgentHost.msi. Gere e examine os arquivos de log usando as seguintes linhas de comando:

-   Para instalações: `msiexec.exe /i SQLDataSyncAgent-Preview-ENU.msi /l\*v LocalAgentSetup.InstallLog`

-   Para desinstalações: `msiexec.exe /x SQLDataSyncAgent-se-ENU.msi /l\*v LocalAgentSetup.InstallLog`

Também habilite o log para todas as instalações executadas pelo Windows Installer. O artigo da Base de Dados de Conhecimento Microsoft [Como habilitar o log do Windows Installer](https://support.microsoft.com/help/223300/how-to-enable-windows-installer-logging) fornece uma solução com um clique para ativar o log do Windows Installer. Ele também fornece o local desses logs.

## <a name="a-database-has-an-out-of-date-status"></a>Um banco de dados tem o status “Desatualizado”

### <a name="cause"></a>Causa

A Sincronização de Dados SQL (versão prévia) remove banco de dados que ficaram offline por 45 dias ou mais do serviço (contado a partir da hora em que o banco de dados ficou offline). Se um banco de dados estiver offline por 45 dias ou mais e, em seguida, voltar a ficar online, seu status será definido como “Desatualizado”.

### <a name="solution-or-workaround"></a>Solução ou solução alternativa

Evite obter o status “Desatualizado” garantindo que nenhum dos bancos de dados fica offline por 45 dias ou mais.

Se o status de um banco de dados for “Desatualizado”, você precisará fazer o seguinte:

1. Remova o banco de dados “Desatualizado” do grupo de sincronização.

2. Adicione o banco de dados de volta ao grupo de sincronização.

> [!WARNING]
> Você perderá todas as alterações feitas nesse banco de dados enquanto ele estava offline.

## <a name="a-sync-group-has-an-out-of-date-status"></a>Um grupo de sincronização tem o status “Desatualizado”

### <a name="cause"></a>Causa

Se uma ou mais alterações não puderem ser aplicadas ao período de retenção inteiro de 45 dias, um grupo de sincronização poderá ficar desatualizado.

### <a name="solution-or-workaround"></a>Solução ou solução alternativa

Para evitar obter o status “Desatualizado”, examine os resultados dos trabalhos de sincronização no visualizador de histórico regularmente e investigue e resolva as alterações que não puderam ser aplicadas.

Se o status de um grupo de sincronização for “Desatualizado”, você precisará excluir o grupo de sincronização e recriá-lo.

## <a name="i-see-erroneous-data-in-my-tables"></a>Há dados incorretos em minhas tabelas

### <a name="description-and-symptoms"></a>Descrição e sintomas

Se tabelas com o mesmo nome mas de esquemas diferentes em um banco de dados estiverem envolvidas na sincronização, você verá dados incorretos nessas tabelas após a sincronização.

### <a name="cause-and-fix"></a>Causa e correção

O processo de provisionamento da Sincronização de Dados SQL (versão prévia) usa as mesmas tabelas de acompanhamento para tabelas com o mesmo nome, mas em esquemas diferentes. Como resultado, as alterações de ambas as tabelas são refletidas na mesma tabela de acompanhamento e esse comportamento que causa dados incorretos é alterado durante a sincronização.

### <a name="resolution-or-workaround"></a>Resolução ou solução alternativa

Garanta que os nomes das tabelas envolvidas na sincronização são diferentes, mesmo que pertençam a esquemas diferentes.

## <a name="i-see-inconsistent-primary-key-data-after-a-successful-synchronization"></a>Há dados de chave primária inconsistentes após uma sincronização bem-sucedida

### <a name="description-and-symptoms"></a>Descrição e sintomas

Depois que uma sincronização é relatada como bem-sucedida e o log não mostra nenhuma linha com falha ou ignorada, você observa que os dados da chave primária são inconsistentes entre os bancos de dados no grupo de sincronização.

### <a name="cause"></a>Causa

Este comportamento ocorre por design. As alterações em qualquer coluna de chave primária resultam em dados inconsistentes nas linhas nas quais a chave primária foi alterada.

### <a name="resolution-or-workaround"></a>Resolução ou solução alternativa

Para evitar esse problema, garanta que nenhum dado em uma coluna de chave primária é alterado.

Para corrigir esse problema, depois que ele tiver ocorrido, remova a linha afetada de todos os pontos de extremidade no grupo de sincronização e, em seguida, insira a linha novamente.

## <a name="i-see-a-significant-degradation-in-performance"></a>Há uma degradação significativa no desempenho

### <a name="description-and-symptoms"></a>Descrição e sintomas

O desempenho é degradado de forma significativa, possivelmente até o ponto em que você não pode nem mesmo iniciar a interface do usuário da Sincronização de Dados.

### <a name="cause"></a>Causa

A causa mais provável é um loop de sincronização. Um loop de sincronização ocorre quando uma sincronização pelo grupo de sincronização A dispara uma sincronização pelo grupo de sincronização B, que, por sua vez, dispara uma sincronização pelo grupo de sincronização A. A situação real pode ser mais complexa, envolvendo mais de dois grupos de sincronização no loop, mas o fator significativo é que há um gatilho circular de sincronizações causado pela sobreposição dos grupos de sincronização.

### <a name="resolution-or-workaround"></a>Resolução ou solução alternativa

A melhor correção é a prevenção. Verifique se você não tem referências circulares nos grupos de sincronização. Qualquer linha sincronizada por um grupo de sincronização não pode ser sincronizada por outro grupo de sincronização.

## <a name="client-agent-cannot-be-deleted-from-the-portal-if-its-associated-on-premises-database-is-unreachable"></a>O agente cliente não pode ser excluído do portal se o banco de dados local associado está inacessível

### <a name="description-and-symptoms"></a>Descrição e sintomas

Se um ponto de extremidade local (ou seja, um banco de dados) registrado em um agente cliente da Sincronização de Dados SQL (versão prévia) ficar inacessível, o agente cliente não poderá ser excluído.

### <a name="cause"></a>Causa

O agente local não pode ser excluído porque o banco de dados inacessível ainda está registrado no agente. Quando você tenta excluir o agente, o processo de exclusão tenta acessar o banco de dados, o que falha.

### <a name="resolution-or-workaround"></a>Resolução ou solução alternativa

Use uma “exclusão forçada” para excluir o banco de dados inacessível.

> [!NOTE]
> Se, após uma “exclusão forçada”, as tabelas de metadados de sincronização permanecerem, use deprovisioningutil.exe para limpá-las.

## <a name="a-sync-group-cannot-be-deleted-within-three-minutes-of-uninstallingstopping-the-agent"></a>Um grupo de sincronização não pode ser excluído em até três minutos após a desinstalação/interrupção do Agente

### <a name="description-and-symptoms"></a>Descrição e sintomas

Não é possível excluir um grupo de sincronização em até três minutos após a desinstalação/interrupção do agente cliente da Sincronização de Dados SQL (versão prévia) associado.

### <a name="resolution-or-workaround"></a>Resolução ou solução alternativa

1. Remova um grupo de sincronização enquanto os agentes de sincronização associados estiverem online (recomendado).

2. Se o agente estiver offline, mas estiver instalado, coloque-o online no computador local. Em seguida, aguarde até que o status do agente seja exibido como online no portal da Sincronização de Dados SQL (versão prévia) e remova o grupo de sincronização.

3. Se o agente estiver offline porque foi desinstalado, faça o seguinte. Em seguida, tente excluir o grupo de sincronização.

    a.  Remova o arquivo XML do agente da pasta de instalação da Sincronização de Dados SQL (versão prévia) caso o arquivo exista.

    b.  Instale o agente no mesmo computador local ou em outro computador, envie a chave do agente por meio do portal gerada para o agente que é mostrado offline.

## <a name="my-sync-group-is-stuck-in-the-processing-state"></a>Meu grupo de sincronização travou no estado de processamento

### <a name="description-and-symptoms"></a>Descrição e sintomas

Um grupo de sincronização na Sincronização de Dados SQL (versão prévia) está no estado de processamento por um longo período, não responde ao comando de parada e os logs não mostram novas entradas.

### <a name="causes"></a>Causas

Qualquer uma das condições a seguir pode fazer com que um grupo de sincronização trave no estado de processamento.

-   **O agente cliente está offline.** Verifique se o agente cliente está online e tente novamente.

-   **O agente cliente está desinstalado ou ausente.** Se o agente cliente estiver desinstalado ou de outra maneira ausente:

    1. Remova o arquivo XML do agente da pasta de instalação da Sincronização de Dados SQL (versão prévia) caso o arquivo exista.

    2. Instale o agente no mesmo computador ou em outro computador local. Em seguida, envie a chave do agente por meio do portal gerada para o agente que é mostrado como offline.

-   **O serviço Sincronização de Dados SQL (versão prévia) foi interrompido.**

    1. No menu **Iniciar**, pesquise Serviços.

    2. Clique em Serviços nos resultados da pesquisa.

    3. Localize o serviço **Sincronização de Dados SQL (versão prévia)**.

    4. Se o status do serviço for **Parado**, clique com o botão direito do mouse no nome do serviço e selecione **Iniciar** no menu suspenso.

### <a name="solution-or-workaround"></a>Solução ou solução alternativa

Se não for possível corrigir o problema, o status do grupo de sincronização poderá ser redefinido pelo suporte da Microsoft. Para ter o status redefinido, crie uma postagem no [fórum do Banco de Dados SQL do Azure](https://social.msdn.microsoft.com/Forums/azure/home?forum=ssdsgetstarted) e inclua sua ID da assinatura e a ID do grupo de sincronização para o grupo que precisa ser redefinido. Um engenheiro de suporte da Microsoft responderá à sua postagem e o informará quando o status tiver sido redefinido.

## <a name="next-steps"></a>Próximas etapas
Para saber mais sobre a Sincronização de Dados SQL, veja:

-   [Sincronizar dados entre vários bancos de dados locais e de nuvem com a Sincronização de Dados SQL do Azure](sql-database-sync-data.md)
-   [Introdução à Sincronização de Dados SQL do Azure](sql-database-get-started-sql-data-sync.md)
-   [Melhores práticas para a Sincronização de Dados SQL do Azure](sql-database-best-practices-data-sync.md)

-   Conclua os exemplos do PowerShell que mostram como configurar a Sincronização de Dados SQL:
    -   [Usar o PowerShell para sincronização entre vários banco de dados SQL do Azure](scripts/sql-database-sync-data-between-sql-databases.md)
    -   [Usar o PowerShell para sincronizar entre um Banco de Dados SQL do Azure e um banco de dados local do SQL Server](scripts/sql-database-sync-data-between-azure-onprem.md)

-   [Baixe a documentação da API REST de Sincronização de Dados SQL](https://github.com/Microsoft/sql-server-samples/raw/master/samples/features/sql-data-sync/Data_Sync_Preview_REST_API.pdf?raw=true)

Para saber mais sobre o Banco de Dados SQL, veja:

-   [Visão geral do Banco de Dados SQL](sql-database-technical-overview.md)
-   [Gerenciamento de ciclo de vida do banco de dados](https://msdn.microsoft.com/library/jj907294.aspx)
