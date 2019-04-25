---
title: Migrar pacotes do SQL Server Integration Services para o Azure | Microsoft Docs
description: Saiba como migrar os pacotes do SQL Server Integration Services para o Azure.
services: database-migration
author: HJToland3
ms.author: jtoland
manager: craigg
ms.reviewer: craigg
ms.service: dms
ms.workload: data-services
ms.custom: mvc
ms.topic: article
ms.date: 03/12/2019
ms.openlocfilehash: 884af4624c1e92ee765353c90fd189220664381d
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60532364"
---
# <a name="migrate-sql-server-integration-services-packages-to-azure"></a>Migrar pacotes do SQL Server Integration Services para o Azure
Se você usa o SSIS (SQL Server Integration Services) e quer migrar seus projetos/pacotes do SSIS do SSISDB de origem hospedado pelo SQL Server para o SSISDB de destino hospedado pelo servidor do Banco de Dados SQL do Azure ou pela Instância Gerenciada do Banco de Dados SQL do Azure, é possível reimplantá-los usando o Assistente de Implantação do Integration Services. Você pode iniciar o Assistente de dentro do SSMS (SQL Server Management Studio).

Se a versão do SSIS que você usa for anterior a 2012, antes de reimplantar os projetos/pacotes do SSIS no modelo de implantação de projeto, primeiro será necessário convertê-los usando o 	Assistente de Conversão de Projetos do Integration Services, que também pode ser iniciado pelo SSMS. Para obter mais informações, consulte o artigo [Converter projetos para o modelo de implantação de projeto](https://docs.microsoft.com/sql/integration-services/packages/deploy-integration-services-ssis-projects-and-packages?view=sql-server-2017#convert).

> [!NOTE]
> O DMS (Serviço de Migração de Banco de Dados do Azure) atualmente não dá suporte para migração de uma fonte de SSISDB, mas é possível reimplantar os projetos/pacotes do SSIS usando o processo a seguir. 

Neste artigo, você aprenderá a:
> [!div class="checklist"]
> * Avaliar projetos/pacotes do SSIS de origem.
> * Migrar projetos/pacotes do SSIS para o Azure.

## <a name="prerequisites"></a>Pré-requisitos
Para concluir essas etapas, você precisa:

- SSMS versão 17.2 ou posterior.
- Uma instância de servidor de banco de dados de destino para hospedar o SSISDB.
 
  Se você ainda não tiver uma:
    - Para um Banco de Dados SQL do Azure, crie um servidor do Banco de Dados SQL do Azure (sem um banco de dados) usando o portal do Azure, navegando até o [formulário](https://ms.portal.azure.com/#create/Microsoft.SQLServer) do SQL Server (somente servidor lógico).
    - Para uma Instância Gerenciada do Banco de Dados SQL do Azure, siga os detalhes no artigo [Criar uma Instância Gerenciada do Banco de Dados SQL do Azure](https://docs.microsoft.com/azure/sql-database/sql-database-managed-instance-get-started).

- SSIS devem ser provisionados no ADF (Azure Data Factory) contendo o Azure-SSIS IR (Integration Runtime) com o SSISDB de destino hospedado pela instância do servidor do Banco de Dados SQL do Azure (conforme descrito no artigo [Provisionar o Azure-SSIS Integration Runtime no Azure Data Factory](https://docs.microsoft.com/azure/data-factory/tutorial-deploy-ssis-packages-azure)) ou pela Instância Gerenciada do Banco de Dados SQL do Azure (conforme descrito no artigo [Criar o Azure-SSIS Integration Runtime no Azure Data Factory](https://docs.microsoft.com/azure/data-factory/create-azure-ssis-integration-runtime)). 

## <a name="assess-source-ssis-projectspackages"></a>Avaliar projetos/pacotes do SSIS de origem
Enquanto a avaliação do SSISDB de origem ainda não tiver sido integrada ao DMA (Assistente de Migração de Banco de Dados) ou ao DMS (Serviço de Migração de Banco de Dados do Azure), os projetos/pacotes do SSIS serão avaliados/validados conforme são reimplantados no SSISDB de destino hospedado no servidor do Banco de Dados SQL do Azure ou na Instância Gerenciada do Banco de Dados SQL do Azure.

## <a name="migrate-ssis-projectspackages"></a>Migrar projetos/pacotes do SSIS
Para migrar projetos/pacotes do SSIS para o servidor do Banco de Dados SQL do Azure ou para a Instância Gerenciada do Banco de Dados SQL do Azure, execute as etapas a seguir.

1.  Abra o SSMS e, em seguida, selecione **Opções** para exibir a caixa de diálogo **Conectar ao Servidor**.

2.  Na guia **Logon**, especifique as informações necessárias para conectar ao servidor do Banco de Dados SQL do Azure ou à Instância Gerenciada do Banco de Dados SQL do Azure, hospedando o SSISDB de destino.

    ![Guia Logon do SSIS](media/how-to-migrate-ssis-packages/dms-ssis-login-tab.png)
 
3.  Na guia **Propriedades da Conexão**, na caixa de texto **Conectar ao banco de dados**, selecione ou insira **SSISDB** e, em seguida, selecione **Conectar**.

    ![Guia Propriedades da Conexão do SSIS](media/how-to-migrate-ssis-packages/dms-ssis-conncetion-properties-tab.png)

4.  No Pesquisador de Objetos do SSMS, expanda o nó **Catálogo do Integration Services**, expanda **SSISDB** e, se não houver nenhuma pasta existente, clique com botão direito em **SSISDB** e crie uma nova pasta.

5.  Em **SSISDB**, expanda qualquer pasta, clique com botão direito em **Projetos** e, em seguida, selecione **Implantar Projeto**.

    ![Nó SSISDB do SSIS expandido](media/how-to-migrate-ssis-packages/dms-ssis-ssisdb-node-expanded.png)

6.  No Assistente de Implantação do Integration Services, na página **Introdução**, examine as informações e, em seguida, selecione **Avançar**.

    ![Página Introdução do Assistente de Implantação](media/how-to-migrate-ssis-packages/dms-deployment-wizard-introduction-page.png)

7.  Na página **Selecionar Origem**, especifique o projeto do SSIS existente que você deseja implantar.

    Se o SSMS também está conectado ao SQL Server que hospeda o SSISDB de origem, selecione **Catálogo do Integration Services** e, em seguida, insira o nome do servidor e o caminho do projeto no catálogo para implantar o projeto diretamente.

    Como alternativa, selecione **Arquivo de implantação do projeto** e, em seguida, especifique o caminho para um arquivo de implantação do projeto existente (.ispac) para implantar o projeto.

    ![Página Selecionar Origem do Assistente de Implantação](media/how-to-migrate-ssis-packages/dms-deployment-wizard-select-source-page.png)
 
8.  Selecione **Avançar**.
9.  Na página **Selecionar Destino**, especifique o destino para o projeto.

        a. Na caixa de texto do nome do servidor, insira o nome totalmente qualificado do servidor de Banco de Dados SQL do Azure (<nome_do_servidor>.database.windows.net) ou o nome da Instância Gerenciada do Banco de Dados SQL do Azure (<nome_do_servidor.prefixo_dns>.database.windows.net).
 
       b. Forneça as informações de autenticação e, em seguida, selecione **Conectar**.
    
       ![Página Selecionar Destino do Assistente de Implantação](media/how-to-migrate-ssis-packages/dms-deployment-wizard-select-destination-page.png)

    c. Clique em Procurar para especificar a pasta de destino no SSISDB e, em seguida, selecione Avançar.

    > [!NOTE]
    > O botão **Avançar** será habilitado somente após você ter selecionado **Conectar**. 

10. Na página **Validar**, exiba quaisquer erros/avisos e, se necessário, modifique os pacotes adequadamente.

    ![Página Validar do Assistente de Implantação](media/how-to-migrate-ssis-packages/dms-deployment-wizard-validate-page.png)

11. Selecione **Avançar**.

12. Na página **Examinar**, examine as configurações de implantação.

    > [!NOTE]
    > É possível alterar as configurações selecionando Anterior ou selecionando qualquer um dos links a etapa no painel esquerdo.

13. Selecione **Implantar** para iniciar o processo de implantação.

14. Após a conclusão do processo de implantação, você pode exibir a página de resultados, que exibe o êxito ou a falha de cada ação de implantação.
     a. Se qualquer ação falhou, na coluna **Resultado**, selecione **Falhou** para exibir uma explicação do erro.
    b. Opcionalmente, selecione **Salvar Relatório** para salvar os resultados em um arquivo XML.

15. Selecione **Fechar** para sair do Assistente de Implantação do Integration Services.

Se a implantação do seu projeto teve êxito sem falhas, é possível selecionar todos os pacotes contidos nele para executar no Azure-SSIS IR.

## <a name="next-steps"></a>Próximas etapas
- Verificar as diretrizes de migração no [Guia de Migração de Banco de Dados da Microsoft](https://datamigration.microsoft.com/).