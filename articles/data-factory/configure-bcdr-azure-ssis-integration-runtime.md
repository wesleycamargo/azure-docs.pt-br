---
title: Recomendações de continuidade de negócios e recuperação de desastre (BCDR) para o Tempo de Execução de Integração do SSR-Azure | Microsoft Docs
description: Este artigo descreve as recomendações de continuidade de negócios e recuperação de desastre para o Tempo de Execução de Integração do SSR-Azure.
services: data-factory
documentationcenter: ''
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: ''
ms.devlang: powershell
ms.topic: conceptual
ms.date: 07/26/2018
author: swinarko
ms.author: sawinark
ms.reviewer: douglasl
manager: craigg
ms.openlocfilehash: 37347df2d543116085f52fed76c692b60fac2ad6
ms.sourcegitcommit: 068fc623c1bb7fb767919c4882280cad8bc33e3a
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/27/2018
ms.locfileid: "39295147"
---
# <a name="business-continuity-and-disaster-recovery-bcdr-recommendations-for-azure-ssis-integration-runtime"></a>Recomendações de continuidade de negócios e recuperação de desastre (BCDR) para o Tempo de Execução de Integração do SSR-Azure

Para fins de recuperação de desastres, você pode interromper o tempo de execução de integração do Azure-SSIS na região em que está sendo executado e alternar para outra região para iniciá-lo novamente. Recomendamos que você use [Regiões Emparelhadas do Azure](../best-practices-availability-paired-regions.md) para essa finalidade.

## <a name="prerequisites"></a>Pré-requisitos

- Certifique-se de que você habilitou a recuperação de desastres para o servidor de banco de dados SQL, caso o servidor tenha uma interrupção ao mesmo tempo. Para obter mais informações, consulte [visão geral da continuidade dos negócios com o banco de dados do Azure SQL](../sql-database/sql-database-business-continuity.md).

- Se você estiver usando uma rede virtual na região atual, você precisa usar outra rede virtual na nova região para conectar seu tempo de execução de integração do Azure-SSIS. Para obter mais informações, consulte [unir um tempo de execução de integração do Azure-SSIS a uma rede virtual](join-azure-ssis-integration-runtime-virtual-network.md).

- Se você estiver usando uma configuração personalizada, talvez seja necessário preparar outro URI do SAS para o contêiner de blob que armazena seu script de instalação personalizado e os arquivos associados, para que ele continue acessível durante uma interrupção. Para obter mais informações, consulte [Configurar uma configuração personalizada em um tempo de execução de integração do Azure-SSIS](how-to-configure-azure-ssis-ir-custom-setup.md).

## <a name="steps"></a>Etapas

Siga estas etapas para interromper o IR do Azure-SSIS, alternar o IR para uma nova região e iniciá-lo novamente.

1. Pare o IR na região original.

2. Chame o seguinte comando no PowerShell para atualizar o IR

    ```powershell
    Set-AzureRmDataFactoryV2IntegrationRuntime -Location "new region" `
                    -CatalogServerEndpoint "Azure SQL Database server endpoint" `
                    -CatalogAdminCredential "Azure SQL Database server admin credentials" `
                    -VNetId "new VNet" `
                    -Subnet "new subnet" `
                    -SetupScriptContainerSasUri "new custom setup SAS URI"
    ```

    Para obter mais informações sobre esse comando do PowerShell, consulte [criar o tempo de execução de integração do Azure-SSIS no Azure Data Factory](create-azure-ssis-integration-runtime.md)

3. Inicie o IR novamente.

## <a name="next-steps"></a>Próximas etapas

Considere estas outras opções de configuração para o IR do Azure-SSIS:

- [Configurar o tempo de execução de integração do Azure-SSIS para alto desempenho](configure-azure-ssis-integration-runtime-performance.md)

- [Personalizar a instalação para o tempo de execução de integração do Azure-SSIS](how-to-configure-azure-ssis-ir-custom-setup.md)

- [Provisionar a Enterprise Edition para o tempo de execução de integração do Azure-SSIS](how-to-configure-azure-ssis-ir-enterprise-edition.md)
