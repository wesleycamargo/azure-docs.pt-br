---
title: Configurar Sincronização de Dados SQL do Azure | Microsoft Docs
description: Este tutorial mostra como configurar a Sincronização de Dados SQL do Azure
services: sql-database
ms.service: sql-database
ms.subservice: data-movement
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: allenwux
ms.author: xiwu
ms.reviewer: douglasl
manager: craigg
ms.date: 11/07/2018
ms.openlocfilehash: 0a248ec5137a6de43910b1d11184dfeda18601f5
ms.sourcegitcommit: ba4570d778187a975645a45920d1d631139ac36e
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/08/2018
ms.locfileid: "51280340"
---
# <a name="tutorial-set-up-sql-data-sync-to-sync-data-between-azure-sql-database-and-sql-server-on-premises"></a>Tutorial: Configuração da Sincronização de Dados SQL para sincronizar dados entre o banco de dados SQL do Azure e SQL Server local

Neste tutorial, você aprenderá a configurar a Sincronização de Dados SQL do Azure criando um grupo de sincronização híbrido que contém as instâncias de Banco de Dados SQL do Azure e do SQL Server. O novo grupo de sincronização ficará totalmente configurado e sincronizado no agendamento que você definir.

Este tutorial presume que você tem pelo menos alguma experiência anterior com o Banco de Dados SQL e o SQL Server.

Para obter uma visão geral da Sincronização de Dados SQL, consulte [Sincronizar dados entre vários bancos de dados locais e de nuvem com a Sincronização de Dados SQL do Azure](sql-database-sync-data.md).

Para obter exemplos completos do PowerShell que mostrem como configurar a Sincronização de Dados SQL, veja os seguintes artigos:

- [Usar o PowerShell para sincronização entre vários banco de dados SQL do Azure](scripts/sql-database-sync-data-between-sql-databases.md)
- [Usar o PowerShell para sincronizar entre um Banco de Dados SQL do Azure e um banco de dados local do SQL Server](scripts/sql-database-sync-data-between-azure-onprem.md)

## <a name="step-1---create-sync-group"></a>Etapa 1: Criar grupo de sincronização

### <a name="locate-the-data-sync-settings"></a>Localizar as configurações de Sincronização de Dados

1. Em seu navegador, navegue até o portal do Azure.

2. No portal, localize os bancos de dados SQL no seu Painel ou no ícone Bancos de Dados SQL na barra de ferramentas.

    ![Lista de bancos de dados SQL do Azure](media/sql-database-get-started-sql-data-sync/datasync-preview-sqldbs.png)

3. Na página **Bancos de dados SQL**, selecione o banco de dados SQL existente que você deseja usar como o banco de dados hub para a Sincronização de Dados. A página do banco de dados SQL é aberta.

    O banco de dados hub é o ponto de extremidade central da topologia de sincronização, no qual um grupo de sincronização tem vários pontos de extremidade do banco de dados. Todos os outros pontos de extremidade do banco de dados em que o grupo de sincronização, ou seja, todos os bancos de dados membros, sincronizam com mesmo o banco de dados hub.

4. Na página do banco de dados SQL do banco de dados selecionado, selecione **Sincronizar para outros bancos de dados**. A página da Sincronização de Dados é aberta.

    ![Opção Sincronizar para outros bancos de dados](media/sql-database-get-started-sql-data-sync/datasync-preview-newsyncgroup.png)

### <a name="create-a-new-sync-group"></a>Criar um novo grupo de sincronização

1. Na página Sincronização de Dados, selecione **Novo Grupo de Sincronização**. A página **Novo grupo de sincronização** é aberta com a Etapa 1, **Criar grupo de sincronização**, realçada. A página **Criar Grupo de Sincronização de Dados** também é aberta.

2. Na página **Criar Grupo de Sincronização de Dados**, faça o seguinte:

   1. No campo **Nome do Grupo de Sincronização**, digite um nome para o novo grupo de sincronização.

   2. Na seção **Banco de Dados de Metadados de Sincronização**, escolha se deseja criar um novo banco de dados (recomendado) ou usar um banco de dados existente.

        > [!NOTE]
        > A Microsoft recomenda que você crie um novo banco de dados vazio para usar como o Banco de Dados de Metadados de Sincronização. A Sincronização de Dados cria tabelas nesse banco de dados e executa uma carga de trabalho frequente. Esse banco de dados é compartilhado automaticamente como o Banco de Dados de Metadados de Sincronização para todos os seus Grupos de sincronização na região selecionada. Você não pode alterar o Banco de Dados de Metadados de Sincronização ou o nome dele sem remover todos os Grupos de Sincronização e Agentes de Sincronização na região.

        Se você escolheu **Novo banco de dados**, selecione **Criar novo banco de dados.** A página do **Banco de Dados SQL** é aberta. Na página **Banco de Dados SQL**, nomeie e configure o novo banco de dados. Depois, selecione **OK**.

        Se você escolheu **Usar banco de dados existente**, selecione o banco de dados na lista.

   3. Na seção **Sincronização Automática**, primeiro selecione **Ativa** ou **Inativa**.

        Se você escolheu **Ativa**, na seção **Frequência de Sincronização**, insira um número e selecione segundos, minutos, horas ou dias.

        ![Especificar frequência de sincronização](media/sql-database-get-started-sql-data-sync/datasync-preview-syncfreq.png)

   4. Na seção **Resolução de Conflitos**, selecione "Hub ganha" ou "Membro ganha".

        "Hub ganha" significa que, quando ocorre um conflito, os dados no banco de dados hub substituem os dados conflitantes no banco de dados de membro. "Membro ganha" significa que, quando ocorre um conflito, os dados no banco de dados de membro substituem os dados conflitantes no banco de dados hub.

       ![Especificar como os conflitos são resolvidos](media/sql-database-get-started-sql-data-sync/datasync-preview-conflictres.png)

   5. Selecione **OK** e aguarde até que o novo grupo de sincronização seja criado e implantado.

## <a name="step-2---add-sync-members"></a>Etapa 2: Adicionar membros de sincronização

Depois que o novo grupo de sincronização é criado e implantado, a Etapa 2, **Adicionar membros de sincronização**, fica realçada na página **Novo grupo de sincronização**.

Na seção **Banco de Dados Hub**, insira as credenciais existentes para o servidor de Banco de Dados SQL em que o banco de dados hub está localizado. Não insira *novas* credenciais nesta seção.

![O banco de dados hub foi adicionado ao grupo de sincronização](media/sql-database-get-started-sql-data-sync/datasync-preview-hubadded.png)

### <a name="add-an-azure-sql-database"></a>Adicionar um Banco de Dados SQL do Azure

Na seção **Banco de Dados Membro**, opcionalmente, adicione um Banco de Dados SQL do Azure ao grupo de sincronização selecionando **Adicionar um Banco de Dados do Azure**. A página **Configurar Banco de Dados do Azure** é aberta.

Na página **Configurar Banco de Dados do Azure**, faça o seguinte:

1. No campo **Nome de Membro de Sincronização**, forneça um nome para o novo membro de sincronização. Esse nome é diferente do nome do banco de dados.

2. No campo **Assinatura**, selecione a assinatura associada do Azure para fins de cobrança.

3. No **Azure SQL Server**, selecione o servidor de banco de dados SQL existente.

4. No **Banco de Dados SQL do Azure**, selecione o banco de dados SQL existente.

5. No campo **Direções de Sincronização**, selecione Sincronização Bidirecional, Para o Hub ou Do Hub.

    ![Adicionar um novo membro de sincronização do Banco de Dados SQL](media/sql-database-get-started-sql-data-sync/datasync-preview-memberadding.png)

6. Nos campos **Nome de Usuário** e **Senha**, insira as credenciais existentes para o servidor de Banco de Dados SQL em que o banco de dados membro está localizado. Não insira *novas* credenciais nesta seção.

7. Selecione **OK** e aguarde até que o novo membro de sincronização seja criado e implantado.

    ![Novo membro de sincronização do banco de dados SQL foi adicionado](media/sql-database-get-started-sql-data-sync/datasync-preview-memberadded.png)

### <a name="add-on-prem"></a> Adicionar um Banco de dados do SQL Server local

Na seção **Banco de Dados Membro**, opcionalmente, adicione um SQL Server local ao grupo de sincronização selecionando **Adicionar um Banco de Dados Local**. A página **Configurar Local** é aberta.

Na página **Configurar Local**, faça o seguinte:

1. Selecione **Escolher o Gateway de Agente de Sincronização**. A página **Selecionar Agente de Sincronização** é aberta.

    ![Escolher o gateway de agente de sincronização](media/sql-database-get-started-sql-data-sync/datasync-preview-choosegateway.png)

2. Na página **Escolher o Gateway de Agente de Sincronização**, escolha se deseja usar um agente existente ou criar um novo agente.

    Se você escolheu **Agentes existentes**, selecione o agente existente na lista.

    Se você escolheu **Criar um novo agente**, faça o seguinte:

   1. Baixe o software cliente do agente de sincronização no link fornecido e instale-o no computador em que o SQL Server está localizado. Você também pode baixar o agente de sincronização de dados diretamente do [Agente de Sincronização de Dados do SQL Azure](https://www.microsoft.com/download/details.aspx?id=27693).

        > [!IMPORTANT]
        > Você precisa abrir a porta de saída TCP 1433 no firewall para permitir que o agente do cliente se comunique com o servidor.

   2. Insira um nome para o agente.

   3. Selecione **Criar e Gerar Chave**.

   4. Copie a chave do agente para a área de transferência.

        ![Criando um novo agente de sincronização](media/sql-database-get-started-sql-data-sync/datasync-preview-selectsyncagent.png)

   5. Selecione **OK** para fechar a página **Selecionar o Agente de Sincronização**.

   6. No computador do SQL Server, localize e execute o aplicativo Agente de Sincronização do Cliente.

        ![Os dados do aplicativo cliente do agente de sincronização de dados](media/sql-database-get-started-sql-data-sync/datasync-preview-clientagent.png)

   7. No aplicativo do agente de sincronização, selecione **Enviar Chave do Agente**. A caixa de diálogo **Configuração de Banco de Dados de Metadados de Sincronização** é aberta.

   8. Na caixa de diálogo **Configuração de Banco de Dados de Metadados de sincronização**, cole a chave do agente copiada do portal do Azure. Insira também as credenciais existentes para o servidor de Banco de Dados SQL do Azure em que o banco de dados de metadados está localizado. (Se você criou um novo banco de dados de metadados, esse banco de dados está no mesmo servidor do banco de dados hub.) Selecione **OK** e aguarde até que a configuração seja concluída.

        ![Insira as credenciais de chave e servidor do agente](media/sql-database-get-started-sql-data-sync/datasync-preview-agent-enterkey.png)

        > [!NOTE]
        > Se você receber um erro de firewall nesta hora, precisará criar uma regra de firewall no Azure para permitir o tráfego de entrada do computador do SQL Server. Você pode criar a regra manualmente no portal, mas pode achar mais fácil criá-la no SSMS (SQL Server Management Studio). No SSMS, tente se conectar ao banco de dados hub no Azure. Insira o nome como <hub_database_name>.database.windows.net. Para configurar a regra de firewall do Azure, siga as etapas da caixa de diálogo. Em seguida, retorne ao aplicativo Agente de Sincronização do Cliente.

   9. No aplicativo Agente de Sincronização do Cliente, clique em **Registrar** para registrar um banco de dados do SQL Server no agente. A caixa de diálogo **Configuração do SQL Server** é aberta.

        ![Adicionar e configurar um banco de dados do SQL Server](media/sql-database-get-started-sql-data-sync/datasync-preview-agent-adddb.png)

   10. Na caixa de diálogo **Configuração do SQL Server**, escolha se deseja se conectar usando a autenticação do SQL Server ou a autenticação do Windows. Se você escolher a autenticação do SQL Server, insira as credenciais existentes. Forneça o nome do SQL Server e o nome do banco de dados que você deseja sincronizar. Selecione **Testar conexão** para testar suas configurações. Em seguida, selecione **Salvar**. O banco de dados registrado aparece na lista.

        ![O banco de dados do SQL Server agora está registrado](media/sql-database-get-started-sql-data-sync/datasync-preview-agent-dbadded.png)

   11. Agora você pode fechar o aplicativo Agente de Sincronização do Cliente.

   12. No portal, na página **Configurar Local**, selecione **Selecionar o Banco de Dados.** A página **Selecionar Banco de Dados** é aberta.

   13. Na página **Selecionar Banco de Dados**, no campo **Nome do Membro de Sincronização**, forneça um nome para o novo membro de sincronização. Esse nome é diferente do nome do banco de dados. Selecione o banco de dados na lista. No campo **Direções de Sincronização**, selecione Sincronização Bidirecional, Para o Hub ou Do Hub.

        ![Selecione o banco de dados local](media/sql-database-get-started-sql-data-sync/datasync-preview-selectdb.png)

   14. Selecione **OK** para fechar a página **Selecionar Banco de Dados**. Em seguida, selecione **OK** para fechar a página **Configurar Local** e aguarde até o novo membro de sincronização ser criado e implantado. Por fim, clique em **OK** para fechar a página **Selecionar membros de sincronização**.

        ![Banco de dados local adicionado ao grupo de sincronização](media/sql-database-get-started-sql-data-sync/datasync-preview-onpremadded.png)

3. Para conectar-se à Sincronização de Dados SQL e ao agente local, adicione seu nome de usuário à função `DataSync_Executor`. A Sincronização de Dados cria essa função na instância do SQL Server.

## <a name="step-3---configure-sync-group"></a>Etapa 3: Configurar grupo de sincronização

Depois que os novos membros do grupo de sincronização são criados e implantados, a Etapa 3, **Configurar grupo de sincronização**, fica realçada na página **Novo grupo de sincronização**.

1. Na página **Tabelas**, selecione um banco de dados na lista de membros do grupo de sincronização e, em seguida, selecione **Atualizar esquema**.

2. Na lista de tabelas disponíveis, selecione as tabelas que você deseja sincronizar.

    ![Selecione as tabelas para sincronizar](media/sql-database-get-started-sql-data-sync/datasync-preview-tables.png)

3. Por padrão, todas as colunas na tabela são selecionadas. Se não quiser sincronizar todas as colunas, desmarque a caixa de seleção das colunas que você não deseja sincronizar. Deixe a coluna de chave primária selecionada.

    ![Selecionar os campos para sincronizar](media/sql-database-get-started-sql-data-sync/datasync-preview-tables2.png)

4. Por fim, selecione **Salvar**.

## <a name="faq-about-setup-and-configuration"></a>Perguntas frequentes sobre instalação e configuração

### <a name="how-frequently-can-data-sync-synchronize-my-data"></a>A frequência com que a sincronização de dados pode sincronizar meus dados

A frequência mínima é a cada cinco minutos.

### <a name="does-sql-data-sync-fully-create-and-provision-tables"></a>Sincronização de dados SQL totalmente cria e provisiona tabelas

Se as tabelas do esquema de sincronização já não são criadas no banco de dados de destino, a Sincronização de Dados SQL criará com as colunas que você selecionou. No entanto, esse comportamento não resulta em um esquema de fidelidade completa, pelos seguintes motivos:

- Somente as colunas selecionadas são criadas na tabela de destino. Portanto, se algumas colunas não fazem parte do grupo de sincronização, essas colunas não são provisionadas nas tabelas de destino.
- Índices são criados somente para as colunas selecionadas. Se o índice da tabela de origem tem colunas que não fazem parte do grupo de sincronização, esses índices não são provisionados nas tabelas de destino.
- Índices em colunas de tipo XML não são provisionados.
- Restrições CHECK não são provisionadas.
- Os gatilhos existentes nas tabelas de origem não são provisionados.
- Exibições e procedimentos armazenados não são criados no banco de dados de destino.

Devido a essas limitações, recomendamos as seguintes ações:

- Para ambientes de produção, provisione o esquema de fidelidade total por conta própria.
- Para experimentar o serviço, o recurso de provisionamento automático de Sincronização de Dados SQL funciona bem.

### <a name="why-do-i-see-tables-that-i-did-not-create"></a>Por que vejo tabelas que eu não criei

A Sincronização de Dados cria tabelas secundárias no banco de dados para controle de alterações. Não as exclua, pois a Sincronização de Dados deixará de funcionar.

### <a name="is-my-data-convergent-after-a-sync"></a>Meus dados são convergentes após uma sincronização?

Não necessariamente. Em um grupo de sincronização com um hub e três spokes (A, B e C), as sincronizações são Hub para A, Hub para B e Hub para C. Se uma alteração for feita para o banco de dados A *depois* o Hub para A sincronizar, a alteração não será gravada para o banco de dados B ou C até a próxima tarefa de sincronização.

### <a name="how-do-i-get-schema-changes-into-a-sync-group"></a>Como posso obter as alterações de esquema em um grupo de sincronização

Você precisa fazer e propagar todas as alterações de esquema manualmente.

1. Replique as alterações de esquema manualmente para o hub e para todos os membros de sincronização.
2. Atualize o esquema de sincronização.

**Adicionar novas tabelas e colunas**.

Novas tabelas e colunas não impactam a sincronização atual. A Sincronização de Dados ignorará as novas tabelas e colunas até que você adicione-as ao esquema de sincronização. Ao adicionar novos objetos de banco de dados, esta é a melhor sequência a ser seguida:

1. Adicione as novas tabelas ou colunas ao hub e a todos os membros de sincronização.
2. Adicione as novas tabelas ou colunas ao esquema de sincronização.
3. Comece a inserir valores nas novas tabelas e colunas.

**Alterar o tipo de dados de uma coluna**.

Ao alterar o tipo de dados de uma coluna existente, a Sincronização de Dados continuará funcionando, desde que os novos valores ajustem-se ao tipo de dados original definido no esquema de sincronização. Por exemplo, se você alterar o tipo no banco de dados de origem de **int** para **bigint**, a Sincronização de Dados continuará funcionando até que você insira um valor muito grande para **int**. Para concluir a alteração, replique a alteração do esquema manualmente para o hub e para todos os membros de sincronização e, em seguida, atualize o esquema de sincronização.

### <a name="how-can-i-export-and-import-a-database-with-data-sync"></a>Como exportar e importar um banco de dados com a sincronização de dados

Depois de exportar um banco de dados como um arquivo `.bacpac` e importar o arquivo para criar um novo banco de dados, você precisa realizar as duas seguintes ações para usar a Sincronização de Dados no novo banco de dados:

1. Limpe os objetos de Sincronização de Dados e tabelas laterais no **novo banco de dados** usando [esse script](https://github.com/vitomaz-msft/DataSyncMetadataCleanup/blob/master/Data%20Sync%20complete%20cleanup.sql). Esse script exclui todos os objetos de Sincronização de Dados necessários do banco de dados.
2. Recrie o grupo de sincronização com o novo banco de dados. Se você não precisar mais do grupo de sincronização antigo, exclua-o.

## <a name="faq-about-the-client-agent"></a>Perguntas Frequentes sobre o agente cliente

Para perguntas frequentes sobre o agente cliente, confira [Perguntas frequentes sobre o agente](sql-database-data-sync-agent.md#agent-faq).

## <a name="next-steps"></a>Próximas etapas

Parabéns. Você criou um grupo de sincronização que inclui uma instância do Banco de Dados SQL e um banco de dados do SQL Server.

Para saber mais sobre a Sincronização de Dados SQL, veja:

- [Sincronizar dados entre vários bancos de dados locais e de nuvem com a Sincronização de Dados SQL do Azure](sql-database-sync-data.md)
- [Melhores práticas para a Sincronização de Dados SQL do Azure](sql-database-best-practices-data-sync.md)
- [Monitorar a Sincronização de Dados SQL do Azure com o Log Analytics](sql-database-sync-monitor-oms.md)
- [Solucionar problemas com a Sincronização de Dados SQL do Azure](sql-database-troubleshoot-data-sync.md)

- Conclua os exemplos do PowerShell que mostram como configurar a Sincronização de Dados SQL:
  - [Usar o PowerShell para sincronização entre vários banco de dados SQL do Azure](scripts/sql-database-sync-data-between-sql-databases.md)
  - [Usar o PowerShell para sincronizar entre um Banco de Dados SQL do Azure e um banco de dados local do SQL Server](scripts/sql-database-sync-data-between-azure-onprem.md)

- [Baixe a documentação da API REST de Sincronização de Dados SQL](https://github.com/Microsoft/sql-server-samples/raw/master/samples/features/sql-data-sync/Data_Sync_Preview_REST_API.pdf?raw=true)

Para saber mais sobre o Banco de Dados SQL, veja:

- [Visão geral do Banco de Dados SQL](sql-database-technical-overview.md)
- [Gerenciamento de ciclo de vida do banco de dados](https://msdn.microsoft.com/library/jj907294.aspx)
