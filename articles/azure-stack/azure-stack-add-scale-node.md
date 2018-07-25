---
title: O Azure Stack adicionar nós de dimensionamento | Microsoft Docs
description: Adicione nós para unidades de escala no Azure Stack.
services: azure-stack
documentationcenter: ''
author: brenduns
manager: femila
editor: ''
ms.assetid: ''
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/24/2018
ms.author: brenduns
ms.reviewer: thoroet
ms.openlocfilehash: 15c46bb0fee4689b614636d8c9e01e36f7f0be3d
ms.sourcegitcommit: 44fa77f66fb68e084d7175a3f07d269dcc04016f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/24/2018
ms.locfileid: "39228218"
---
# <a name="add-additional-scale-unit-nodes-in-azure-stack"></a>Adicionar nós de unidade de escala adicional no Azure Stack

Operadores do Azure Stack podem aumentar a capacidade geral de uma unidade de escala existente com a adição de um computador físico adicional. O computador físico também é referenciado como um nó de unidade de escala. Cada novo nó de unidade de escala que você adicionar deve ser homogêneo no tipo de CPU, memória e número do disco e tamanho para os nós que já estão presentes na unidade de escala.  

> [!NOTE]  
Você deve executar a escala adicional do Azure Stack 1807 ou posterior para adicionar nós de unidade.

Para adicionar um nó de unidade de escala, você agir no Azure Stack e executar as ferramentas do fabricante de equipamento de hardware (OEM). As ferramentas de OEM é executada no host de ciclo de vida do hardware (HLH) para garantir que o novo computador físico coincide com o mesmo nível de firmware que nós existentes.

O diagrama de fluxo a seguir mostra o processo geral para adicionar um nó de unidade de escala.

![Adicionar fluxo de unidade de escala](media/azure-stack-add-scale-node/add-node-flow.png) &#42; *se o fornecedor do hardware OEM ele aplica o posicionamento de rack de servidor físico e atualiza o firmware varia de acordo com seu contrato de suporte.*

A operação para adicionar um novo nó pode levar várias horas ou dias para ser concluída.

## <a name="add-scale-unit-nodes"></a>Adicionar nós de unidade de escala 
As etapas a seguir são uma visão geral de como adicionar um nó. Não siga estas etapas sem primeiro referindo-se a sua documentação de expansão de capacidade fornecidos pelo OEM.

1. Coloque o novo servidor físico no rack e cabeá-lo adequadamente. 
2. Configure o endereço IP correto na baseboard management controller (BMC) e aplique todas as configurações de BIOS, por sua documentação fornecida pelo OEM.
3. Aplica a linha de base atual do firmware para todos os componentes usando as ferramentas que são fornecidas pelo fabricante do hardware que são executados no HLH.
4. Execute a operação de adição de nó no portal de administração do Azure Stack.
5. Valide que a operação de adição de nó tenha êxito. Para fazer isso, verifique as [ **Status** da unidade de escala](#monitor-add-node-operations). 

## <a name="add-the-node"></a>Adicionar o nó 
Você pode usar o console de administração ou o PowerShell para adicionar novos nós. A operação de adição de nó primeiro adiciona o novo nó de unidade de escala como a capacidade de computação e, em seguida, estende automaticamente a capacidade de armazenamento. A capacidade de se expande automaticamente porque o Azure Stack é um sistema hiperconvergente onde *compute* e *armazenamento* dimensionar juntos.

### <a name="use-the-admin-console"></a>Use o console de administração
1. Entre no portal de administração do Azure Stack como um operador do Azure Stack.
2. Navegue até **gerenciamento de região** > **unidades de escala**e, em seguida, selecione a unidade de escala que você deseja expandir a capacidade para.  
   ![Selecione a unidade de escala](media/azure-stack-add-scale-node/select-node1.png)

   ![Exibir detalhes de unidade de escala](media/azure-stack-add-scale-node/select-node2.png)
 
3. Os parâmetros a seguir são necessários:  
   - **ENDEREÇO de IP do BMC** para cada nó de unidade de escala que você adicionar. (Um endereço IP por linha.) ![Adicionar nó](media/azure-stack-add-scale-node/add-node.png)

### <a name="use-powershell"></a>Usar o PowerShell

Use o **New-AzsScaleUnitNodeObject** cmdlet para adicionar um ou mais nós.  

Antes de usar qualquer um dos seguintes scripts do PowerShell de exemplo, substitua os valores *nomes de nó* e *endereços IP* com os valores do seu ambiente do Azure Stack.

**Para um único nó:**
  ```powershell
  ## Add a single Node 
  $NewNode=New-AzsScaleUnitNodeObject -computername "<name_of_new_node>" -BMCIPv4Address "<BMCIP_address_of_new_node>" 
 
  Add-AzsScaleUnitNode -NodeList $NewNode -ScaleUnit "<name_of_scale_unit_cluster>" 
  ```  

**Adicione vários nós:**
  ```powershell
  ## Add multiple nodes 
  $NewNode1=New-AzsScaleUnitNodeObject -computername "<name_of_new_node01>"  -BMCIPv4Address "<BMCIP_address_of_new_node01>" 
 
  $NewNode2=New-AzsScaleUnitNodeObject -computername "<name_of_new_node02>" -BMCIPv4Address “<BMCIP_address_of_new_node02>”$ 
 
  Add-AzsScaleUnitNode -NodeList @($NewNode1,$NewNode2) -ScaleUnit "<name_of_scale_unit_cluster>" 
  ```

## <a name="monitor-add-node-operations"></a>Monitorar as operações de adicionar nó 
Você pode usar o portal de administração ou o PowerShell para obter o status da operação de adição de nó. Adicione operações de nó podem levar várias horas a dias para ser concluída.

### <a name="use-the-admin-console"></a>Use o console de administração 
Para monitorar a adição de um novo nó, examine a unidade de escala no portal de administração ou dimensionar objetos de nó de unidade. Para fazer isso, vá para **gerenciamento de região** > **unidades de escala**. Em seguida, selecione a unidade de escala ou o nó de unidade de escala que você deseja examinar. 

### <a name="use-powershell"></a>Usar o PowerShell
O status de unidade de escala e nós de unidade de escala podem ser recuperado usando o PowerShell da seguinte maneira:
  ```powershell
  #Retrieve Status for the Scale Unit
  Get-AzsScaleUnit|select name,state
 
  #Retrieve Status for each Scale Unit Node
  Get-AzsScaleUnitNode |Select Name, ScaleUnitNodeStatus
```

### <a name="status-for-the-add-node-operation"></a>Status de operação de adição de nó 
**Para uma unidade de escala:**
|Status               |DESCRIÇÃO  |
|---------------------|---------|
|Executando              |Todos os nós estão participando ativamente na unidade de escala.|
|Parado              |O nó de unidade de escala está inativo ou inacessível.|
|Expandindo            |No momento estão sendo adicionados a um ou mais nós de unidade de escala como a capacidade de computação.|
|Configurando o armazenamento  |A capacidade de computação expandiu e a configuração de armazenamento está em execução.|
|Requer correção |Foi detectado um erro que exige um ou mais nós de unidade de escala a ser reparada.|


**Para um nó de unidade de escala:**
|Status                |DESCRIÇÃO  |
|----------------------|---------|
|Executando               |O nó está participando ativamente na unidade de escala.|
|Parado               |O nó não está disponível.|
|Adicionando                |O nó está ativamente sendo adicionado para a unidade de escala.|
|Reparando             |O nó está ativamente sendo reparado.|
|Manutenção            |O nó está em pausa e nenhuma carga de trabalho do usuário ativa está em execução. |
|Requer correção  |Foi detectado um erro que exige que o nó ser reparado.|


## <a name="troubleshooting"></a>solução de problemas
A seguir estão os problemas comuns enfrentados ao adicionar um nó. 

**Cenário 1:** falha a operação adição de nó de unidade de escala, mas um ou mais nós são listados com um status parado.  
- Correção: Use a operação de reparo para reparar um ou mais nós. Apenas uma operação de reparo único pode executar ao mesmo tempo.

**Cenário 2:** um ou mais nós de unidade de escala foram adicionados, mas a expansão de armazenamento falhou. Nesse cenário, o objeto de nó da unidade de escala relata um status de execução, mas a tarefa de configuração de armazenamento não for iniciada.  
- Correção: Use o ponto de extremidade com privilégios para analisar a integridade de armazenamento, executando o seguinte cmdlet do PowerShell:
 
**Cenário 3:** você recebeu um alerta que indica o trabalho de expansão de armazenamento falhou.  
- Correção: nesse caso, a tarefa de configuração de armazenamento falhou. Esse problema requer que você entre em contato com o suporte.


## <a name="next-steps"></a>Próximas etapas 
Revisão [ações de nó](azure-stack-node-actions.md) 
