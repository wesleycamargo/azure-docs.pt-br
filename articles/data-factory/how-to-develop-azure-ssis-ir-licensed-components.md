---
title: Desenvolver componentes pagos ou licenciados para o Tempo de Execução de Integração do Azure-SSIS  | Microsoft Docs
description: Este artigo descreve como um ISV pode desenvolver e instalar componentes personalizados pagos ou licenciadas para o tempo de execução de integração do Azure-SSIS
services: data-factory
documentationcenter: ''
author: douglaslMS
manager: craigg
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/13/2018
ms.author: douglasl
ms.openlocfilehash: e22ca4bd5b749e8752f800590938199e06abbd34
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/16/2018
---
# <a name="develop-paid-or-licensed-custom-components-for-the-azure-ssis-integration-runtime"></a>Desenvolver componentes personalizados pagos ou licenciados para o Tempo de Execução de Integração do Azure-SSIS

## <a name="problem---the-azure-ssis-ir-requires-a-different-approach"></a>Problema: o IR do Azure-SSIS exige uma abordagem diferente

A natureza do tempo de execução de integração do Azure-SSIS apresenta vários desafios, o que torna inadequados os métodos de licenciamento comuns usados para a instalação local de componentes personalizados.

-   Os nós de IR do Azure-SSIS são voláteis e podem ser alocados ou liberados a qualquer momento. Por exemplo, você pode iniciar ou parar nós para gerenciar o custo ou aumentar e diminuir vários tamanhos de nó. Como resultado, a associação de uma licença de componente de terceiros a um nó específico usando as informações específicas do computador, como endereço MAC ou ID de CPU, não é mais viável.

-   Você também pode expandir ou reduzir o IR do Azure-SSIS para que o número de nós possa diminuir ou aumentar a qualquer momento.

## <a name="solution---windows-environment-variables-and-ssis-system-variables-for-license-binding-and-validation"></a>Solução: variáveis de ambiente do Windows e variáveis de sistema do SSIS para validação e associação da licença

Como resultado das limitações dos métodos tradicionais de licenciamento descritos na seção anterior, o IR do Azure-SSIS fornece variáveis de ambiente do Windows e variáveis de sistema do SSIS para a validação e associação da licença de componentes de terceiros. Os ISVs podem usar essas variáveis para obter informações exclusivas e persistente para um IR do Azure-SSIS, como ID do Cluster e Contagem de Nós do Cluster. Com essas informações, ISVs podem vincular a licença para seu componente a um IR do Azure-SSIS *como cluster*, com uma ID que não se altera quando os clientes iniciam ou param, expandem ou reduzem, ou reconfiguram o IR do Azure-SSIS de qualquer forma.

O diagrama a seguir mostra a instalação, a ativação e a associação típicas da licença e os fluxos de validação para componentes de terceiros que usam essas novas variáveis:

![Instalação de componentes licenciados](media/how-to-configure-azure-ssis-ir-licensed-components/licensed-component-installation.png)

## <a name="instructions"></a>Instruções
1. Os ISVs podem oferecer seus componentes licenciados em várias SKUs ou níveis (por exemplo, único nó, até 5 nós, até 10 nós e assim por diante). O ISV fornece a chave do produto (Product Key) correspondente quando clientes adquirem um produto. O ISV também pode fornecer um contêiner de blob de Armazenamento do Azure que contém um script de instalação do ISV e arquivos associados. Os clientes podem copiar esses arquivos em seu próprio contêiner de armazenamento e modificá-los com sua própria chave do produto (Product Key) (por exemplo, executando `IsvSetup.exe -pid xxxx-xxxx-xxxx`). Os clientes podem, então, provisionar ou reconfigurar o IR do Azure-SSIS com o URI de SAS do contêiner como parâmetro. Para obter mais informações, confira [Configuração personalizada para o tempo de execução de integração do Azure-SSIS](how-to-configure-azure-ssis-ir-custom-setup.md).

2. Quando o IR do Azure-SSIS é provisionado ou reconfigurado, a instalação do ISV é executada em cada nó para consultar as variáveis de ambiente do Windows, `SSIS_CLUSTERID` e `SSIS_CLUSTERNODECOUNT`. Em seguida, o IR do Azure-SSIS envia sua ID de Cluster e a chave do produto (Product Key) licenciado para o servidor de ativação do ISV para gerar uma chave de ativação.

3. Depois de receber a chave de ativação, a instalação do ISV pode armazenar a chave localmente em cada nó (por exemplo, no Registro).

4. Quando os clientes executam um pacote que usa o componente licenciado do ISV em um nó do IR do Azure-SSIS, o pacote lê a chave de ativação armazenada localmente e valida em relação à ID de Cluster do nó. O pacote também pode relatar a Contagem de Nós de Cluster para o servidor de ativação do ISV.

    Aqui está um exemplo de código que valida a chave de ativação e relata a contagem de nós do cluster:

    ```csharp
    public override DTSExecResult Validate(Connections, VariableDispenser, IDTSComponentEvents componentEvents, IDTSLogging log) 
                                                                                                                               
    {                                                                                                                             
                                                                                                                               
    Variables vars = null;                                                                                                        
                                                                                                                               
    variableDispenser.LockForRead("System::ClusterID");                                                                           
                                                                                                                               
    variableDispenser.LockForRead("System::ClusterNodeCount");                                                                    
                                                                                                                               
    variableDispenser.GetVariables(ref vars);                                                                                     
                                                                                                                               
    // Validate Activation Key with ClusterID                                                                                     
                                                                                                                               
    // Report on ClusterNodeCount                                                                                                 
                                                                                                                               
    vars.Unlock();                                                                                                                
                                                                                                                               
    return base.Validate(connections, variableDispenser, componentEvents, log);                                                   
                                                                                                                               
    }
    ```

## <a name="next-steps"></a>Próximas etapas

-   [Instalação personalizada do tempo de execução de integração do Azure-SSIS](how-to-configure-azure-ssis-ir-custom-setup.md)

-   [Enterprise Edition do Tempo de Execução de Integração do Azure-SSIS](how-to-configure-azure-ssis-ir-enterprise-edition.md)