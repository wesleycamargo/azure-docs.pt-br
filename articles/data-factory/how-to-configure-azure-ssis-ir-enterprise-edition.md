---
title: Provisionar Enterprise Edition para o Integration Runtime do Azure-SSIS| Microsoft Docs
description: Este artigo descreve os recursos do Enterprise Edition para o Integration Runtime do Azure-SSIS e como provisioná-lo
services: data-factory
documentationcenter: ''
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 04/13/2018
author: swinarko
ms.author: sawinark
ms.reviewer: douglasl
manager: craigg
ms.openlocfilehash: c475a9c820e577a321491b87d806ed08ddff8693
ms.sourcegitcommit: 25936232821e1e5a88843136044eb71e28911928
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/04/2019
ms.locfileid: "54013870"
---
# <a name="provision-enterprise-edition-for-the-azure-ssis-integration-runtime"></a>Provisionar Enterprise Edition para o Integration Runtime do Azure-SSIS

A Enterprise Edition do Microsoft Integration Runtime do Azure-SSIS permite que você use os recursos avançados e premium a seguir:
-   Componentes da Captura de Dados de Alterações (CDA)
-   Conectores do Oracle, Teradata e SAP BW
-   Conectores e transformações do SQL Server Analysis Services (SSAS) e do Azure Analysis Services (AAS)
-   Transformações agrupamento difuso e pesquisa difusa
-   Transformações de pesquisa de extração e de termos

Alguns desses recursos exigem a instalação de componentes adicionais para personalizar o Tempo de Execução de Integração do Azure-SSIS. Para obter mais informações sobre como instalar componentes adicionais, consulte [Instalação personalizada para o Tempo de Execução de Integração do Azure-SSIS](how-to-configure-azure-ssis-ir-custom-setup.md).

## <a name="enterprise-features"></a>Recursos da empresa

| **Recursos da Empresa** | **Descrições** |
|---|---|
| Componentes da CDA | A fonte da CDC, a tarefa de controle e a transformação de divisor são pré-instaladas no Tempo de Execução de Integração do Azure-SSIS Enterprise Edition. Para se conectar ao Oracle, você também precisa instalar o CDC Designer e o serviço em outro computador. |
| Conectores Oracle | O Gerenciador de Conexões da Oracle, a fonte e o destino são pré-instalados no Tempo de Execução de Integração do Azure-SSIS Enterprise Edition. Você também precisa instalar o driver Oracle Call Interface (OCI) e, se necessário, configurar o Oracle Transport Network Substrate (TNS), no Tempo de Execução de Integração do Azure-SSIS Enterprise Edition. Para obter mais informações, confira [Configuração personalizada para o tempo de execução de integração do Azure-SSIS](how-to-configure-azure-ssis-ir-custom-setup.md). |
| Conectores de teradata | Você precisa instalar o Gerenciador de Conexões Teradata, a fonte e o destino, bem como a API Teradata Parallel Transporter (TPT) e o driver Teradata ODBC, no Tempo de Execução de Integração do Azure-SSIS Enterprise Edition. Para obter mais informações, confira [Configuração personalizada para o tempo de execução de integração do Azure-SSIS](how-to-configure-azure-ssis-ir-custom-setup.md). |
| Conectores SAP BW | O Gerenciador de Conexões do SAP BW, a fonte e o destino são pré-instalados no Tempo de Execução de Integração do Azure-SSIS Enterprise Edition. Você também precisa instalar o driver do SAP BW no Tempo de Execução de Integração do Azure-SSIS. Esses conectores oferecem suporte a SAP BW 7.0 ou versões anteriores. Para se conectar a versões posteriores do SAP BW ou outros produtos SAP, você pode comprar e instalar conectores do SAP de ISVs de terceiros no Tempo de Execução de Integração do Azure-SSIS. Para obter mais informações sobre como instalar componentes adicionais, consulte [Instalação personalizada para o Tempo de Execução de Integração do Azure-SSIS](how-to-configure-azure-ssis-ir-custom-setup.md). |
| Componentes do Analysis Services               | O destino de treinamento do modelo de mineração de dados, o destino do processamento de dimensão e o destino de processamento de partições, além da transformação de consulta de mineração de dados, são pré-instalados no Tempo de Execução de Integração do Azure-SSIS Enterprise Edition. Todos esses componentes oferecem suporte a SQL Server Analysis Services (SSAS), mas apenas o destino de processamento de partições oferece suporte ao Azure Analysis Services (AAS). Para se conectar ao SSAS, você também precisa [configurar as credenciais de autenticação do Windows no SSISDB](https://docs.microsoft.com/sql/integration-services/lift-shift/ssis-azure-connect-with-windows-auth). Além desses componentes, a tarefa do Analysis Services Execute DDL, a tarefa de processamento do Analysis Services e a tarefa de consulta de mineração de dados também são pré-instalados no Tempo de Execução de Integração do Azure-SSIS Standard/Enterprise Edition. |
| Transformações agrupamento difuso e pesquisa difusa  | As transformações de agrupamento difuso e pesquisa difusa são pré-instaladas no Tempo de Execução de Integração do Azure-SSIS Enterprise Edition. Esses componentes oferecem suporte a SQL Server e banco de dados do SQL Azure para armazenar dados de referência. |
| Transformações de pesquisa de extração e de termos | As transformações de extração de termos e pesquisa de termos são pré-instaladas no Tempo de Execução de Integração do Azure-SSIS Enterprise Edition. Esses componentes oferecem suporte a SQL Server e banco de dados do SQL Azure para armazenar dados de referência. |

## <a name="instructions"></a>Instruções

1.  Faça o download e instale o [Azure PowerShell (versão 5.4 ou posterior)](https://github.com/Azure/azure-powershell/releases/tag/v5.5.0-March2018).

2.  Quando você configurar ou reconfigurar o Tempo de Execução de Integração do Azure-SSIS com o PowerShell, execute `Set-AzureRmDataFactoryV2IntegrationRuntime` com **Enterprise** como o valor para o parâmetro **Edição** antes de iniciar o Tempo de Execução de Integração do Azure-SSIS. Veja abaixo um script de exemplo:

    ```powershell
    $MyAzureSsisIrEdition = "Enterprise"

    Set-AzureRmDataFactoryV2IntegrationRuntime -DataFactoryName $MyDataFactoryName
                                               -Name $MyAzureSsisIrName
                                               -ResourceGroupName $MyResourceGroupName
                                               -Edition $MyAzureSsisIrEdition

    Start-AzureRmDataFactoryV2IntegrationRuntime -DataFactoryName $MyDataFactoryName
                                                 -Name $MyAzureSsisIrName
                                                 -ResourceGroupName $MyResourceGroupName
    ```

## <a name="next-steps"></a>Próximas etapas

-   [Instalação personalizada do tempo de execução de integração do Azure-SSIS](how-to-configure-azure-ssis-ir-custom-setup.md)

-   [Como desenvolver componentes personalizados pagos ou licenciados para o Tempo de Execução de Integração do Azure-SSIS](how-to-develop-azure-ssis-ir-licensed-components.md)
