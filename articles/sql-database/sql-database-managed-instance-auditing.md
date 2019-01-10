---
title: Auditoria da Instância Gerenciada do Banco de Dados SQL do Azure | Microsoft Docs
description: Saiba como começar com a Auditoria da Instância Gerenciada do Banco de Dados SQL do Azure usando o T-SQL
services: sql-database
ms.service: sql-database
ms.subservice: security
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
f1_keywords:
- mi.azure.sqlaudit.general.f1
author: ronitr
ms.author: ronitr
ms.reviewer: vanto
manager: craigg
ms.date: 09/20/2018
ms.openlocfilehash: b295f7a2a454e3987e8639814f785b7457dd452b
ms.sourcegitcommit: 803e66de6de4a094c6ae9cde7b76f5f4b622a7bb
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/02/2019
ms.locfileid: "53973087"
---
# <a name="get-started-with-azure-sql-database-managed-instance-auditing"></a>Comece com a Auditoria da Instância Gerenciada do Banco de Dados SQL do Azure

A Auditoria da [Instância Gerenciada do Banco de Dados SQL do Azure](sql-database-managed-instance.md) rastreia eventos de banco de dados e os grava em um log de auditoria na conta de armazenamento do Azure. A auditoria também:
- Ajuda você a manter a conformidade regulatória, entender a atividade do banco de dados e obter informações sobre discrepâncias e anomalias que podem indicar preocupações para os negócios ou suspeitas de violações de segurança.
- Permite e facilita a adesão aos padrões de conformidade, embora não garanta a conformidade. Para obter mais informações sobre os programas Azure que oferecem suporte à conformidade com os padrões, consulte o [Azure Trust Center](https://azure.microsoft.com/support/trust-center/compliance/).


## <a name="set-up-auditing-for-your-server"></a>Configurar a auditoria para o servidor

A seção a seguir descreve a configuração de auditoria na Instância Gerenciada.
1. Vá para o [Portal do Azure](https://portal.azure.com).
2. As etapas a seguir criam um **contêiner** de Armazenamento do Microsoft Azure onde os logs de auditoria são armazenados.

   - Navegue até o Armazenamento do Microsoft Azure onde você deseja armazenar seus logs de auditoria.

     > [!IMPORTANT]
     > Use uma conta de armazenamento na mesma região que o servidor da Instância Gerenciada para evitar leituras/gravações entre regiões.

   - Na página da conta de armazenamento, vá até **Visão geral** e clique em **Blobs**.

     ![Painel de navegação][1]

   - No menu superior, clique em **+ Contêiner** para criar um novo contêiner.

     ![Painel de navegação][2]

   - Forneça um contêiner **Nome**, defina o nível de acesso Público para **Privado** e, em seguida, clique em **OK**.

     ![Painel de navegação][3]

   - Na lista de contêineres, clique no contêiner recém-criado e, em seguida, clique em **Propriedades do contêiner**.

     ![Painel de navegação][4]

   - Copie a URL do contêiner, clicando no ícone de cópia e salve-a (por exemplo, no bloco de notas) para uso futuro. O formato da URL do contêiner deve ser `https://<StorageName>.blob.core.windows.net/<ContainerName>`

     ![Painel de navegação][5]

3. As etapas a seguir geram um **Token SAS** do Armazenamento do Microsoft Azure usado para conceder direitos de acesso de Auditoria de Instância Gerenciada para a conta de armazenamento.

   - Navegue até a Conta de Armazenamento do Microsoft Azure em que você criou o contêiner na etapa anterior.

   - Clique em **Assinatura de acesso compartilhado** no menu Configurações de Armazenamento.

     ![Painel de navegação][6]

   - Configure a SAS da seguinte forma:
     - **Serviços permitidos**: Blob
     - **Data de início**: para evitar problemas relacionados a fuso horário, é recomendável usar a data de ontem.
     - **Data de término**: escolha a data de vencimento desse Token SAS. 

       > [!NOTE]
       > Renove o token após o vencimento para evitar falhas de auditoria.

     - Clique em **Gerar SAS**.

       ![Painel de navegação][7]

   - Depois de clicar em Gerar SAS, o Token SAS aparece na parte inferior. Copie o token clicando no ícone de cópia e salve-o (por exemplo, no bloco de notas) para uso futuro.

     > [!IMPORTANT]
     > Remova o caractere de ponto de interrogação ("?") do início do token.

     ![Painel de navegação][8]

4. Conecte-se a sua Instância Gerenciada por meio do SQL Server Management Studio (SSMS).

5. Execute a seguinte instrução T-SQL para **criar uma nova Credencial** usando a URL do Contêiner e o Token SAS criado nas etapas anteriores:

    ```SQL
    CREATE CREDENTIAL [<container_url>]
    WITH IDENTITY='SHARED ACCESS SIGNATURE',
    SECRET = '<SAS KEY>'
    GO
    ```

6. Execute a seguinte instrução T-SQL para criar um novo Servidor de Auditoria (escolha seu próprio nome de auditoria, use a URL do Contêiner que você criou nas etapas anteriores):

    ```SQL
    CREATE SERVER AUDIT [<your_audit_name>]
    TO URL ( PATH ='<container_url>' [, RETENTION_DAYS =  integer ])
    GO
    ```

    Se não for especificado, `RETENTION_DAYS` padrão é 0 (retenção ilimitada).

    Para informações adicionais:
    - [Diferenças de auditoria entre a Instância Gerenciada, o banco de dados SQL do Azure e o SQL Server](#auditing-differences-between-managed-instance-azure-sql-database-and-sql-server)
    - [CRIAR AUDITORIA DE SERVIDOR](https://docs.microsoft.com/sql/t-sql/statements/create-server-audit-transact-sql)
    - [ALTERAR AUDITORIA DE SERVIDOR](https://docs.microsoft.com/sql/t-sql/statements/alter-server-audit-transact-sql)

7. Crie uma especificação de Auditoria de Servidor ou Especificação de Auditoria de Banco de Dados como faria para o SQL Server:
    - [Guia Criar T-SQL de especificação de auditoria de servidor](https://docs.microsoft.com/sql/t-sql/statements/create-server-audit-specification-transact-sql)
    - [Guia Criar T-SQL de especificação de auditoria de banco de dados](https://docs.microsoft.com/sql/t-sql/statements/create-database-audit-specification-transact-sql)

8. Habilite a auditoria de servidor criada na etapa 6:

    ```SQL
    ALTER SERVER AUDIT [<your_audit_name>]
    WITH (STATE=ON);
    GO
    ```

## <a name="analyze-audit-logs"></a>Analisar os logs de auditoria
Há vários métodos que podem ser usados para exibir os logs de auditoria de blob.

- Use a função do sistema caractere `sys.fn_get_audit_file` (T-SQL) para retornar os dados do log de auditoria em um formato tabular. Para obter mais informações sobre como usar essa função, consulte a [documentação de sys.fn_get_audit_file](https://docs.microsoft.com/sql/relational-databases/system-functions/sys-fn-get-audit-file-transact-sql).

- Para obter uma lista completa de métodos de consumo de log de auditoria, consulte o [Introdução à auditoria de banco de dados do SQL](https://docs.microsoft.com/ azure/sql-database/sql-database-auditing).

> [!IMPORTANT]
> O método para exibir os registros de auditoria do Portal do Azure (painel de ‘registros de auditoria’) está atualmente disponível para a Instância Gerenciada.

## <a name="auditing-differences-between-managed-instance-azure-sql-database-and-sql-server"></a>Diferenças de auditoria entre a Instância Gerenciada, o banco de dados SQL do Azure e o SQL Server

As principais diferenças entre a Auditoria do SQL na Instância Gerenciada, banco de dados SQL do Azure e SQL Server local são:

- Na Instância Gerenciada, a Auditoria do SQL funciona no nível de servidor e armazena arquivos de log de `.xel` na conta de armazenamento de blobs do Azure.
- No banco de dados SQL do Azure, a Auditoria do SQL funciona no nível do banco de dados.
- No SQL Server local/máquinas virtuais, a Auditoria do SQL funciona no nível do servidor, mas armazena os eventos em logs de eventos do Windows/sistema de arquivos.

A auditoria XEvent na Instância Gerenciada oferece suporte a destinos de armazenamento de blobs do Azure. **Não há suporte** para logs de arquivo e do Windows.

As principais diferenças na sintaxe `CREATE AUDIT` para a auditoria do armazenamento de blobs do Azure são:
- Uma nova sintaxe `TO URL` é fornecida e permite que você especifique a URL do contêiner de armazenamento de blobs do Azure onde arquivos `.xel` são colocados.
- A sintaxe `TO FILE` **não é compatível** porque a instância gerenciada não pode acessar compartilhamentos de arquivos do Windows.
- A opção de desligamento **não é compatível**.
- **Não há suporte** para `queue_delay` de 0.


## <a name="next-steps"></a>Próximas etapas

- Para obter uma lista completa de métodos de consumo de log de auditoria, consulte o [Introdução à auditoria de banco de dados do SQL](https://docs.microsoft.com/azure/sql-database/sql-database-auditing).
- Para obter mais informações sobre os programas Azure que oferecem suporte à conformidade com os padrões, consulte o [Azure Trust Center](https://azure.microsoft.com/support/trust-center/compliance/).


<!--Anchors-->
[Set up auditing for your server]: #subheading-1
[Analyze audit logs]: #subheading-2
[Auditing differences between Managed Instance, Azure SQL DB and SQL Server]: #subheading-3

<!--Image references-->
[1]: ./media/sql-managed-instance-auditing/1_blobs_widget.png
[2]: ./media/sql-managed-instance-auditing/2_create_container_button.png
[3]: ./media/sql-managed-instance-auditing/3_create_container_config.png
[4]: ./media/sql-managed-instance-auditing/4_container_properties_button.png
[5]: ./media/sql-managed-instance-auditing/5_container_copy_name.png
[6]: ./media/sql-managed-instance-auditing/6_storage_settings_menu.png
[7]: ./media/sql-managed-instance-auditing/7_sas_configure.png
[8]: ./media/sql-managed-instance-auditing/8_sas_copy.png
