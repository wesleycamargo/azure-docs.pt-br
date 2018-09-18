---
title: O Azure Stack adicionar nós de dimensionamento | Microsoft Docs
description: Adicione nós para unidades de escala no Azure Stack.
services: azure-stack
documentationcenter: ''
author: jeffgilb
manager: femila
editor: ''
ms.assetid: ''
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/17/2018
ms.author: jeffgilb
ms.reviewer: thoroet
ms.openlocfilehash: 3ce74cdb610f2902133459b913f53bb7809cb4b7
ms.sourcegitcommit: 776b450b73db66469cb63130c6cf9696f9152b6a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/18/2018
ms.locfileid: "45982990"
---
# <a name="add-additional-scale-unit-nodes-in-azure-stack"></a>Adicionar nós de unidade de escala adicional no Azure Stack

Operadores do Azure Stack podem aumentar a capacidade geral de uma unidade de escala existente com a adição de um computador físico adicional. O computador físico também é referenciado como um nó de unidade de escala. Cada novo nó de unidade de escala que você adicionar deve ser homogêneo no tipo de CPU, memória e número do disco e tamanho para os nós que já estão presentes na unidade de escala.

> [!NOTE]  
Você deve executar a escala adicional do Azure Stack 1807 ou posterior para adicionar nós de unidade.

Para adicionar um nó de unidade de escala, você agir no Azure Stack e executar as ferramentas do fabricante de equipamento de hardware (OEM). As ferramentas de OEM é executada no host de ciclo de vida do hardware (HLH) para garantir que o novo computador físico coincide com o mesmo nível de firmware que nós existentes.

O diagrama de fluxo a seguir mostra o processo geral para adicionar um nó de unidade de escala.

![Adicionar fluxo de unidade de escala](media/azure-stack-add-scale-node/add-node-flow.png) &#42; *se o fornecedor do hardware OEM ele aplica o posicionamento de rack de servidor físico e atualiza o firmware varia de acordo com seu contrato de suporte.*

A operação para adicionar um novo nó pode levar várias horas ou dias para ser concluída.

> [!Note]  
> Não tente qualquer uma das seguintes operações enquanto uma operação adição de nó de unidade de escala já está em andamento:
>
>  - Atualizar o Azure Stack
>  - Girar certificados
>  - Parar o Azure Stack
>  - Nó de unidade de escala de reparo


## <a name="add-scale-unit-nodes"></a>Adicionar nós de unidade de escala

As etapas a seguir são uma visão geral de como adicionar um nó. Não siga estas etapas sem primeiro referindo-se a sua documentação de expansão de capacidade fornecidos pelo OEM.

1. Coloque o novo servidor físico no rack e cabeá-lo adequadamente. 
2. Habilite as portas de comutador físico e ajuste a listas de controle de acesso (ACLs), se aplicável.
3. Configure o endereço IP correto na baseboard management controller (BMC) e aplique todas as configurações de BIOS, por sua documentação fornecida pelo OEM.
4. Aplica a linha de base atual do firmware para todos os componentes usando as ferramentas que são fornecidas pelo fabricante do hardware que são executados no HLH.
5. Execute a operação de adição de nó no portal de administração do Azure Stack.
6. Valide que a operação de adição de nó tenha êxito. Para fazer isso, verifique as [ **Status** da unidade de escala](#monitor-add-node-operations). 

## <a name="add-the-node"></a>Adicionar o nó

Você pode usar o portal de administração ou o PowerShell para adicionar novos nós. A operação de adição de nó primeiro adiciona o novo nó de unidade de escala como a capacidade de computação e, em seguida, estende automaticamente a capacidade de armazenamento. A capacidade de se expande automaticamente porque o Azure Stack é um sistema hiperconvergente onde *compute* e *armazenamento* dimensionar juntos.

### <a name="use-the-admin-portal"></a>Usar o portal de administração

1. Entre no portal de administração do Azure Stack como um operador do Azure Stack.
2. Navegue até **+ criar um recurso** > **capacidade** > **nó da unidade de escala**.
   ![Nó de unidade de escala](media/azure-stack-add-scale-node/select-node1.png)
3. Sobre o **adicionar nó** painel, selecione o *região*e, em seguida, selecione o *unidade de escala* que você deseja adicionar o nó para. Especifique também o *endereço de IP do BMC* para o nó de unidade de escala que você está adicionando. Você só pode adicionar um nó por vez.
   ![Adicionar detalhes do nó](media/azure-stack-add-scale-node/select-node2.png)
 

### <a name="use-powershell"></a>Usar o PowerShell

Use o **New-AzsScaleUnitNodeObject** cmdlet para adicionar um nó.  

Antes de usar qualquer um dos seguintes scripts do PowerShell de exemplo, substitua os valores *nomes de nó* e *endereços IP* com os valores do seu ambiente do Azure Stack.

  > [!Note]  
  > Ao nomear um nó, você deve manter o nome de menos de 15 caracteres. Você também não é possível usar um nome que contém um espaço ou qualquer um dos seguintes caracteres: `\`, `/`, `:`, `*`, `?`, `"`, `<`, `>`, `|`, `\`, `~`, `!`, `@`, `#`, `$`, `%`, `^`, `&`, `(`, `)`, `{`,` }`, `_`.

**Adicione um nó:**
  ```powershell
  ## Add a single Node 
  $NewNode=New-AzsScaleUnitNodeObject -computername "<name_of_new_node>" -BMCIPv4Address "<BMCIP_address_of_new_node>" 
 
  Add-AzsScaleUnitNode -NodeList $NewNode -ScaleUnit "<name_of_scale_unit_cluster>" 
  ```  

## <a name="monitor-add-node-operations"></a>Monitor de adicionar as operações de nó 
Você pode usar o portal de administração ou o PowerShell para obter o status da operação de adição de nó. Adicione operações de nó podem levar várias horas a dias para ser concluída.

### <a name="use-the-admin-portal"></a>Usar o portal de administração 
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
  ```powershell
     Get-VirtualDisk -CimSession s-cluster | Get-StorageJob
  ```
 
**Cenário 3:** você recebeu um alerta que indica o trabalho de expansão de armazenamento falhou.  
- Correção: nesse caso, a tarefa de configuração de armazenamento falhou. Esse problema requer que você entre em contato com o suporte.


## <a name="next-steps"></a>Próximas etapas 
[Adicionar endereços IP públicos](azure-stack-add-ips.md) 
