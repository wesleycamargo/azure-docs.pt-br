---
title: Importar um arquivo do Power BI Desktop no Azure Analysis Services | Microsoft Docs
description: Descreve como importar um arquivo do Power BI Desktop (pbix) usando o portal do Azure.
author: minewiskan
manager: kfile
ms.service: azure-analysis-services
ms.topic: conceptual
ms.date: 01/09/2019
ms.author: owend
ms.reviewer: minewiskan
ms.openlocfilehash: 47223f22c797d892bc7cbdc0086439ee9cae9fcb
ms.sourcegitcommit: 63b996e9dc7cade181e83e13046a5006b275638d
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/10/2019
ms.locfileid: "54187715"
---
# <a name="import-a-power-bi-desktop-file"></a>Importar um arquivo do Power BI Desktop

É possível importar um modelo de dados em um arquivo do Power BI Desktop (pbix) para o Azure Analysis Services. Metadados de modelo, dados armazenados em cache e conexões de fonte de dados são importados. Relatórios e visualizações não serão importadas. Modelos de dados importados do Power BI Desktop estão no nível de compatibilidade 1400 e 1465.

> [!IMPORTANT]
> Esse recurso foi preterido. Ele pode ser removido ou alterado significativamente em uma atualização futura. Recomenda-se que você descontinue o uso desses recursos em projetos novos e existentes para manter a compatibilidade com atualizações futuras. Para desenvolvimento e teste de modelos mais avançados, é melhor usar o Visual Studio (SSDT) e SQL Server Management Studio (SSMS).

## <a name="restrictions"></a>Restrições  

- Se o modelo de dados tiver sido criado no Power BI Desktop (2.60.5169.3201) Atualização de julho de 2018 ou posterior, assegure que nenhuma versão prévia do recurso esteja habilitada. Versões prévias do recurso ainda não são compatíveis com o Azure Analysis Services. Se você recebe o erro a seguir durante a importação, isso significa que o arquivo pbix tem versões prévias do recurso habilitadas que ainda não dão suporte ao Azure Analysis Services.

    ![Aviso de nível de compatibilidade](./media/analysis-services-import-pbix/aas-import-pbix-cl-warning.png)   
- Você deve ter permissões de administrador de servidor para importar de um arquivo pbix.
- O modelo pbix pode se conectar apenas a fontes de dados do **Banco de Dados SQL do Azure** e do **SQL Data Warehouse do Azure**.
- O modelo pbix não pode ter conexões dinâmica ou DirectQuery. 


## <a name="to-import-from-pbix"></a>Para importar do pbix

1. Na **Visão Geral** > **Web Designer** do servidor, clique em **Abrir**.

    ![Criar um modelo no portal do Azure](./media/analysis-services-create-model-portal/aas-create-portal-overview-wd.png)

2. Em **Designer de Web** > **Modelos**, clique em **+ Adicionar**.

    ![Criar um modelo no portal do Azure](./media/analysis-services-create-model-portal/aas-create-portal-models.png)

3. Em **Novo modelo**, digite um nome de modelo e, em seguida, selecione arquivo do Power BI Desktop.

    ![Caixa de diálogo Novo modelo no portal do Azure](./media/analysis-services-import-pbix/aas-import-pbix-new-model.png)

4. Em **Importar**, localize e selecione seu arquivo.

     ![Caixa de diálogo Conectar no portal do Azure](./media/analysis-services-import-pbix/aas-import-pbix-select-file.png)

## <a name="change-credentials"></a>Alterar credenciais

Quando você importar um modelo de dados de um arquivo pbix, por padrão, as credenciais usadas para se conectar a uma fonte de dados são definidas como ServiceAccount. Depois que um modelo tiver sido importado de um pbix, você poderá alterar as credenciais por meio dos métodos a seguir:

- Use a versão de julho de 2018 (versão 17.8.1) ou posterior do SSMS para editar as credenciais. Essa é a forma mais fácil.
- Use o [comando Alter](https://docs.microsoft.com/sql/analysis-services/tabular-models-scripting-language-commands/alter-command-tmsl) do TMSL no [objeto DataSources](https://docs.microsoft.com/sql/analysis-services/tabular-models-scripting-language-objects/datasources-object-tmsl) para modificar a propriedade de cadeia de caracteres de conexão. 
- Abra o modelo no Visual Studio, edite as credenciais para a conexão de fonte de dados e, em seguida, reimplante o modelo.

Para alterar as credenciais usando o SSMS. 

1. No SSMS, expanda o banco de dados > **Conexões**. 
2. Clique com o botão direito do mouse na conexão de banco de dados e, em seguida, clique em **Atualizar Credenciais**. 

    ![Atualizar credenciais](./media/analysis-services-import-pbix/aas-import-pbix-creds.png)

3. Na caixa de diálogo de credenciais, selecione um tipo de credencial e insira as credenciais. Para a autenticação SQL, selecione o banco de dados. Para a conta da organização (OAuth), selecione a conta da Microsoft.
    ![Editar credenciais](./media/analysis-services-import-pbix/aas-import-pbix-edit-creds.png)

A versão de julho de 2018 do Power BI Desktop inclui um novo recurso para alterar as permissões de fonte de dados. Na guia **Início**, clique em **Editar Consultas**  > **Configurações da fonte de dados**. Selecione a conexão de fonte de dados e, em seguida, clique em **Editar Permissões**.


## <a name="see-also"></a>Consulte também

[Como criar um modelo no portal do Azure](analysis-services-create-model-portal.md)   
[Conectar-se ao Azure Analysis Services](analysis-services-connect.md)  
