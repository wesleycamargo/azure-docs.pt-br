---
title: Proteger o banco de dados SQL do Azure | Microsoft Docs
description: "Conheça técnicas e recursos para proteger o banco de dados SQL do Azure."
services: sql-database
documentationcenter: 
author: DRediske
manager: johammer
editor: 
tags: 
ms.assetid: 
ms.service: sql-database
ms.custom: tutorial-secure
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: 
ms.date: 05/03/2017
ms.author: daredis
ms.translationtype: Human Translation
ms.sourcegitcommit: be3ac7755934bca00190db6e21b6527c91a77ec2
ms.openlocfilehash: eb2c8ec946a08ed3b538d613199706779b80bd1f
ms.contentlocale: pt-br
ms.lasthandoff: 05/03/2017


---
# <a name="secure-your-azure-sql-database"></a>Proteger o banco de dados SQL do Azure

Neste tutorial, você aprende os conceitos básicos de proteção do banco de dados SQL. Com apenas algumas etapas simples, você pode usar qualquer banco de dados e melhorar consideravelmente sua proteção contra usuários mal-intencionados ou o acesso não autorizado.

Se você não tiver uma assinatura do Azure, crie uma conta [gratuita](https://azure.microsoft.com/free/) antes de começar.

Para concluir este tutorial, verifique se você instalou o Excel e a versão mais nova do [SSMS](https://msdn.microsoft.com/library/ms174173.aspx) (SQL Server Management Studio).



## <a name="set-up-firewall-rules-for-your-database"></a>Configurar regras de firewall para o banco de dados

Os banco de dados SQL do Azure são protegidos por um firewall. Por padrão, todas as conexões com o servidor e os bancos de dados dentro do servidor são rejeitadas, exceto as conexões de outros serviços do Azure. A configuração mais segura é definir “Permitir acesso aos serviços do Azure” como DESATIVADO. Se você precisar se conectar ao banco de dados em uma VM do Azure ou um serviço de nuvem, deverá criar um [IP Reservado](../virtual-network/virtual-networks-reserved-public-ip.md) e permitir somente o acesso ao endereço IP reservado por meio do firewall. 

Siga estas etapas para criar uma [regra de firewall no nível do servidor de Banco de Dados SQL](sql-database-firewall-configure.md) para que o servidor permita conexões de um endereço IP específico. 

1. Faça logon no [Portal do Azure](https://portal.azure.com/).
1. Clique em **Bancos de dados SQL** no menu esquerdo e clique no banco de dados para o qual você gostaria de configurar a regra de firewall na página **Bancos de dados SQL**. A página de visão geral de seu banco de dados é aberta, mostrando o nome totalmente qualificado do servidor (como **mynewserver-20170313.database.windows.net**) e fornece opções para configurações adicionais.

      ![regra de firewall do servidor](./media/sql-database-security-tutorial/server-firewall-rule.png) 

2. Clique em **Definir o firewall do servidor** na barra de ferramentas, conforme mostrado na imagem anterior. A página **Configurações do firewall** do servidor de Banco de Dados SQL é aberta. 

3. Clique em **Adicionar IP do cliente** na barra de ferramentas para adicionar o endereço IP público do computador conectado ao portal ou inserir a regra de firewall manualmente e, em seguida, clique em **Salvar**.

      ![definir regra de firewall do servidor](./media/sql-database-security-tutorial/server-firewall-rule-set.png) 

4. Clique em **OK** e clique no **X** para fechar a página **Configurações do firewall**.

Agora você pode se conectar a qualquer banco de dados do servidor com o endereço IP ou o intervalo de endereços IP especificado.

> [!NOTE]
> O Banco de Dados SQL se comunica pela porta 1433. Se você estiver tentando conectar-se a partir de uma rede corporativa, o tráfego de saída pela porta 1433 poderá não ser permitido pelo firewall de sua rede. Se isto acontecer, você não conseguirá conectar seu servidor do Banco de Dados SQL do Azure, a menos que o departamento de TI abra a porta 1433.
>

Se você precisar de configurações de firewall diferentes para diferentes bancos de dados no mesmo servidor lógico, deverá criar uma regra no nível do banco de dados para cada banco de dados. As regras de firewall no nível do banco de dados só podem ser configuradas usando instruções Transact-SQL e somente depois de ter configurado a primeira regra de firewall no nível do servidor. Siga estas etapas para criar uma regra de firewall específica ao banco de dados.

1. Conecte-se ao banco de dados, por exemplo, usando o [SSMS](./sql-database-connect-query-ssms.md).

2. No Pesquisador de Objetos, clique com o botão direito do mouse no banco de dados ao qual você deseja adicionar uma regra de firewall e clique em **Nova Consulta**. Uma janela de consulta em branco conectada ao seu banco de dados é aberta.

3. Na janela de consulta, insira a seguinte consulta:

    ```sql
    EXECUTE sp_set_database_firewall_rule N'Example DB Rule','0.0.0.4','0.0.0.4';
    ```

4. Na barra de ferramentas, clique em **Executar** para criar a regra de firewall.

## <a name="connect-to-the-database-using-a-secure-connection-string"></a>Conectar ao banco de dados usando uma cadeia de conexão segura

Para garantir uma conexão segura e criptografada entre o cliente e o Banco de Dados SQL, a cadeia de conexão deve ser configurada para 1) solicitar uma conexão criptografada e 2) não confiar no certificado do servidor. Isso estabelece uma conexão usando o protocolo TLS e reduz o risco de ataques man-in-the-middle. Você pode obter cadeias de conexão configuradas corretamente para seu Banco de Dados SQL do Azure para os drivers cliente com suporte no portal do Azure, conforme mostrado para o ADO.net nesta captura de tela.

1. Selecione **Bancos de dados SQL** no menu à esquerda e clique em seu banco de dados na página **Bancos de dados SQL**.

2. Na página **Visão Geral** do banco de dados, clique em **Mostrar cadeias de conexão de banco de dados**.

3. Examine a cadeia de conexão completa do **ADO.NET**.

    ![Cadeia de conexão do ADO.NET](./media/sql-database-security-tutorial/adonet-connection-string.png)


## <a name="user-management"></a>Gerenciamento de Usuários

Antes de criar os usuários, primeiro você deve escolher um dos dois tipos de autenticação com suporte no Banco de Dados SQL do Azure: 

**Autenticação do SQL**, que usa o nome de usuário e a senha para logons e usuários que são válidos somente no contexto de um banco de dados específico em um servidor lógico. 

**Autenticação do Azure Active Directory**, que usa identidades gerenciadas pelo Azure Active Directory. 

Se você desejar usar o [Azure Active Directory](./sql-database-aad-authentication.md) para realizar a autenticação no Banco de Dados SQL, deverá haver um Azure Active Directory populado antes que você possa continuar.

Siga estas etapas para criar um usuário usando a Autenticação do SQL:

1. Conecte-se ao banco de dados, por exemplo, usando o [SSMS](./sql-database-connect-query-ssms.md) com suas credenciais de administrador do servidor.

2. No Pesquisador de Objetos, clique com o botão direito do mouse no banco de dados ao qual você deseja adicionar um novo usuário e clique em **Nova Consulta**. Uma janela de consulta em branco conectada ao banco de dados selecionado é aberta.

3. Na janela de consulta, insira a seguinte consulta:

    ```sql
    CREATE USER ApplicationUserUser WITH PASSWORD = 'strong_password';
    ```

4. Na barra de ferramentas, clique em **Executar** para criar o usuário.

5. Por padrão, o usuário pode se conectar ao banco de dados, mas não tem permissões para ler nem gravar dados. Para conceder essas permissões ao usuário recém-criado, execute os dois comandos a seguir em uma nova janela de consulta

    ```sql
    ALTER ROLE db_datareader ADD MEMBER ApplicationUserUser;
    ALTER ROLE db_datawriter ADD MEMBER ApplicationUserUser;
    ```

É uma melhor prática criar essas contas não administrador no nível do banco de dados para se conectar ao banco de dados, a menos que você precise executar tarefas de administrador como criar novos usuários. Consulte o [tutorial do Azure Active Directory](./sql-database-aad-authentication-configure.md) para saber como realizar a autenticação usando o Azure Active Directory.


## <a name="protect-your-data-with-encryption"></a>Proteger seus dados com criptografia

A TDE (Transparent Data Encryption) do Banco de Dados SQL do Azure criptografa seus dados em repouso automaticamente, sem a necessidade de alterações no aplicativo que acessa o banco de dados criptografado. Para habilitar a TDE no banco de dados, siga estas etapas:

1. Selecione **Bancos de dados SQL** no menu à esquerda e clique em seu banco de dados na página **Bancos de dados SQL**. 

2. Clique em **Transparent Data Encryption** para abrir a página de configuração da TDE.

    ![Transparent Data Encryption](./media/sql-database-security-tutorial/transparent-data-encryption-enabled.png)

3. Defina **Criptografia de dados** como ATIVADO e clique em **Salvar**.

O processo de criptografia é iniciado em segundo plano. Você pode monitorar o progresso conectando-se ao Banco de Dados SQL usando, por exemplo, o [SSMS](./sql-database-connect-query-ssms.md) como um banco de dados e consultar a coluna encryption_state da exibição sys.dm_database_encryption_keys.

## <a name="enable-sql-database-auditing"></a>Habilitar a auditoria do Banco de Dados SQL

A Auditoria do Azure SQL Database rastreia eventos do banco de dados e os grava em um log de auditoria em sua conta do Azure Storage. A auditoria pode ajudar você a manter uma conformidade regulatória, a entender a atividade do banco de dados e a obter informações sobre discrepâncias e anomalias que poderiam indicar preocupações de negócios ou suspeitas de violações de segurança. Siga estas etapas para criar uma política de auditoria padrão para o banco de dados:

1. Selecione **Bancos de dados SQL** no menu à esquerda e clique em seu banco de dados na página **Bancos de dados SQL**.

2. Na folha Configurações, selecione **Auditoria e Detecção de Ameaças**.

    ![Folha Auditoria](./media/sql-database-security-tutorial/auditing-get-started-settings.png)

3. Na folha de configuração de auditoria do banco de dados, você pode marcar a caixa de seleção **Herdar configurações do servidor** para designar que este banco de dados será auditado de acordo com as configurações do seu servidor. Se essa opção for marcada, você verá um link **Exibir configurações de auditoria do servidor** que permite exibir ou modificar as configurações de auditoria do servidor neste contexto.

    ![Herdar configurações](./media/sql-database-security-tutorial/auditing-get-started-server-inherit.png)

4. Se preferir habilitar a auditoria de blob no nível do banco de dados (adicionalmente ou em vez da auditoria de nível de serviço), **desmarque** a opção **Herdar configurações de auditoria do servidor**, **ATIVE** a Auditoria e selecione o Tipo de Auditoria **Blob**.

    > Se a auditoria de blob do servidor estiver habilitada, a auditoria de banco de dados configurada existirá lado a lado com a auditoria de blob do servidor.

    ![Ativar a auditoria](./media/sql-database-security-tutorial/auditing-get-started-turn-on.png)

5. Selecione **Detalhes de Armazenamento** para abrir a Folha de Armazenamento de Logs de Auditoria. Selecione a conta do Azure Storage em que os logs serão salvos e o período de retenção, após o qual os logs antigos serão excluídos, e clique em **OK** na parte inferior. **Dica:** use a mesma conta de armazenamento para todos os bancos de dados auditados para aproveitar ao máximo os modelos de relatórios de auditoria.

    ![Painel de navegação](./media/sql-database-security-tutorial/auditing-get-started-storage-details.png)

6. Se desejar personalizar os eventos auditados, você pode fazer isso por meio do PowerShell ou API REST. Consulte a seção [Automação (PowerShell/API REST)](#subheading-7) para obter mais detalhes.

7. Clique em **Salvar**.


## <a name="enable-sql-database-threat-detection"></a>Habilitar a detecção de ameaças do Banco de Dados SQL

A Detecção de Ameaças fornece uma nova camada de segurança, que permite que os clientes detectem e respondam às ameaças potenciais conforme elas ocorrem, fornecendo alertas de segurança nas atividades anormais. Os usuários podem explorar os eventos suspeitos usando a Auditoria do Banco de Dados SQL para determinar se eles resultam de uma tentativa de acesso, violação ou exploração dos dados no banco de dados. A Detecção de Ameaças torna simples tratar as possíveis ameaças no banco de dados sem a necessidade de ser um especialista em segurança ou gerenciar os sistemas de monitoramento de segurança avançados.
Por exemplo, a Detecção de Ameaças detecta determinadas atividades anormais do banco de dados que indicam possíveis tentativas de injeção de SQL. A injeção de SQL é um dos problemas comuns de segurança do aplicativo da Web na Internet, usada para atacar os aplicativos controlados por dados. Os invasores aproveitam as vulnerabilidades do aplicativo para inserir instruções SQL mal-intencionadas nos campos de entrada do aplicativo, para violar ou modificar os dados no banco de dados.

1. Navegue para a folha de configuração do banco de dados SQL que você deseja monitorar. Na folha Configurações, selecione **Auditoria e Detecção de Ameaças**.

    ![Painel de navegação](./media/sql-database-security-tutorial/auditing-get-started-settings.png)
2. Na folha de configuração **Auditoria e Detecção de Ameaças**, **ATIVE** a auditoria, o que exibirá as configurações de detecção de ameaças.

3. **ATIVE** a detecção de ameaças.

4. Configure a lista de emails que receberão os alertas de segurança após a detecção das atividades anormais do banco de dados.

5. Clique em **Salvar** na folha **Auditoria e detecção de ameaças** para salvar a auditoria nova ou atualizada e a política de detecção de ameaças.

    ![Painel de navegação](./media/sql-database-security-tutorial/td-turn-on-threat-detection.png)

    Você receberá uma notificação por email na detecção das atividades anormais do banco de dados. O email fornecerá informações sobre o evento de segurança suspeito, incluindo a natureza das atividades anormais, nome do banco de dados, nome do servidor e a hora do evento. Além disso, ele fornecerá informações sobre as possíveis causas e ações recomendadas para investigar e atenuar a ameaça em potencial no banco de dados. As próximas etapas explicam o que deverá ser feito caso você receba um email desse tipo:

    ![Email de detecção de ameaças](./media/sql-database-threat-detection-get-started/4_td_email.png)

6. No email, clique no link **Log de Auditoria do SQL do Azure**, que iniciará o portal do Azure e mostrará os registros de auditoria relevantes do período do evento suspeito.

    ![Registros de auditoria](./media/sql-database-threat-detection-get-started/5_td_audit_records.png)

7. Clique nos registros de auditoria para exibir mais detalhes sobre as atividades suspeitas do banco de dados, como a instrução SQL, motivo da falha e IP do cliente.

    ![Detalhes do registro](./media/sql-database-security-tutorial/6_td_audit_record_details.png)

8. Na folha Registros de Auditoria, clique em **Abrir no Excel** para abrir um modelo pré-configurado do Excel para importar e executar uma análise mais profunda do log de auditoria na época do evento suspeito.

    > [!NOTE]
    > No Excel 2010 ou posterior, o Power Query e a configuração **Combinação Rápida** são necessários.

    ![Abrir registros no Excel](./media/sql-database-threat-detection-get-started/7_td_audit_records_open_excel.png)

9. Para definir a configuração **Combinação Rápida**: na guia de faixa de opções **POWER QUERY**, selecione **Opções** para exibir a caixa de diálogo Opções. Selecione a seção Privacidade e escolha a segunda opção - 'Ignorar os Níveis de Privacidade e melhorar potencialmente o desempenho':

    ![Combinação Rápida do Excel](./media/sql-database-threat-detection-get-started/8_td_excel_fast_combine.png)

10. Para carregar os logs de auditoria do SQL, verifique se os parâmetros na guia de configurações estão definidos corretamente e, em seguida, selecione a faixa de opções 'Dados' e clique no botão 'Atualizar Tudo'.

    ![Parâmetros do Excel](./media/sql-database-threat-detection-get-started/9_td_excel_parameters.png)

11. Os resultados aparecem na folha **Logs de Auditoria do SQL** , que permite executar uma análise mais profunda das atividades anormais detectadas e minimizar o impacto do evento de segurança em seu aplicativo.


## <a name="next-steps"></a>Próximas etapas

* Para obter uma visão geral de todos os recursos de segurança do Banco de Dados SQL, consulte a [Visão geral de segurança do Banco de Dados SQL](sql-database-security-overview.md).
* Para uma criptografia adicional de colunas confidenciais no banco de dados, considere o uso da criptografia do cliente com o [Always Encrypted](https://docs.microsoft.com/sql/relational-databases/security/encryption/always-encrypted-database-engine).
* Para funcionalidades de controle de acesso adicionais, a [Segurança em Nível de Linha](https://docs.microsoft.com/sql/relational-databases/security/row-level-security) permite restringir o acesso a linhas em um banco de dados com base na associação a um grupo do usuário ou no contexto de execução, enquanto a [Máscara de Dados Dinâmicos](https://docs.microsoft.com/azure/sql-database/sql-database-dynamic-data-masking-get-started) limita a exposição de dados confidenciais mascarando-os para usuários não privilegiados na camada do aplicativo. 

