---
title: Ações de nó de unidade de dimensionamento no Azure Stack | Microsoft Docs
description: Saiba como exibir o status do nó e usar a ativação, desativar, desabilitar e retomar as ações de nó em um sistema integrado do Azure Stack.
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
editor: ''
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: PowerShell
ms.topic: article
ms.date: 01/22/2019
ms.author: mabrigg
ms.reviewer: ppacent
ms.lastreviewed: 01/22/2019
ms.openlocfilehash: cd7e66961a0b9a80150a3d3e132efd29485cdb66
ms.sourcegitcommit: 0dd053b447e171bc99f3bad89a75ca12cd748e9c
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/26/2019
ms.locfileid: "58483141"
---
# <a name="scale-unit-node-actions-in-azure-stack"></a>Ações de nó de unidade de escala no Azure Stack

*Aplica-se a: Sistemas integrados do Azure Stack*

Este artigo descreve como exibir o status de uma unidade de escala. Você pode exibir nós da unidade. Você pode executar ações de nó, como power, power off, desligar, drenar, retomar e reparar. Normalmente, você usa essas ações de nó durante a substituição de campo de partes de ou para ajudar a recuperar um nó.

> [!Important]  
> Todas as ações de nó descritas neste artigo devem ter como destino um nó por vez.

## <a name="view-the-node-status"></a>Exibir o status do nó

No portal do administrador, você pode exibir o status de uma unidade de escala e seus nós associados.

Para exibir o status de uma unidade de escala:

1. Sobre o **gerenciamento de região** bloco, selecione a região.
2. À esquerda, sob **recursos de infraestrutura**, selecione **unidades de escala**.
3. Nos resultados, selecione a unidade de escala.
4. À esquerda, sob **gerais**, selecione **nós**.

   Exiba as seguintes informações:

   - A lista de nós individuais
   - Status operacional (consulte a lista abaixo)
   - Status de energia (em execução ou parado)
   - modelo de servidor
   - Endereço IP do baseboard management controller (BMC)
   - Número total de núcleos
   - quantidade total de memória

![status de uma unidade de escala](media/azure-stack-node-actions/multinodeactions.png)

### <a name="node-operational-states"></a>Estados operacionais do nó

| Status | DESCRIÇÃO |
|----------------------|-------------------------------------------------------------------|
| Executando | O nó está participando ativamente na unidade de escala. |
| Parado | O nó não está disponível. |
| Adicionando | O nó está ativamente sendo adicionado para a unidade de escala. |
| Reparando | O nó está ativamente sendo reparado. |
| Manutenção  | O nó está em pausa e nenhuma carga de trabalho do usuário ativa está em execução. |
| Requer correção | Foi detectado um erro que exige que o nó ser reparado. |

## <a name="scale-unit-node-actions"></a>Ações de nó de unidade de escala

Quando você exibe informações sobre um nó de unidade de escala, você também pode executar ações de nó, como:
 - Iniciar e parar (dependendo do status atual de energia)
 - Desabilitar e continuar (dependendo do status de operações)
 - Reparar
 - Shutdown

O estado operacional do nó determina quais opções estão disponíveis.

Você precisa instalar os módulos do PowerShell do Azure Stack. Esses cmdlets estão na **Azs.Fabric.Admin** módulo. Para instalar ou verificar a instalação do PowerShell para o Azure Stack, consulte [instalar o PowerShell para Azure Stack](azure-stack-powershell-install.md).

## <a name="stop"></a>Stop

O **parar** ação desativa o nó. É o mesmo como se você pressionar o botão de energia. Ele não envia um sinal de desligamento para o sistema operacional. Para operações de parada planejado, sempre tente a operação de desligamento pela primeira vez. 

Essa ação normalmente é usada quando um nó está em um estado suspenso e não responde às solicitações.

Para executar a ação de parada, abra um prompt do PowerShell com privilégios elevados e execute o seguinte cmdlet:

```powershell  
  Stop-AzsScaleUnitNode -Location <RegionName> -Name <NodeName>
```

No caso improvável de que a ação de parada não funcionar, tente a operação novamente e se ele falhar uma segunda vez usam a interface da web do BMC.

Para obter mais informações, consulte [Stop-AzsScaleUnitNode](https://docs.microsoft.com/powershell/module/azs.fabric.admin/stop-azsscaleunitnode).

## <a name="start"></a>Iniciar

O **iniciar** ação ativa o nó. É o mesmo como se você pressionar o botão de energia. 
 
Para executar a ação inicial, abra um prompt do PowerShell com privilégios elevados e execute o seguinte cmdlet:

```powershell  
  Start-AzsScaleUnitNode -Location <RegionName> -Name <NodeName>
```

No caso improvável de que a ação inicial não funcionar, tente a operação novamente e se ele falhar uma segunda vez usam a interface da web do BMC.

Para obter mais informações, consulte [AzsScaleUnitNode início](https://docs.microsoft.com/powershell/module/azs.fabric.admin/start-azsscaleunitnode).

## <a name="drain"></a>Esvaziar

O **drenar** ação move todas as cargas de trabalho ativas para os nós restantes dessa unidade de escala específica.

Essa ação normalmente é usada durante a substituição de campo de partes, como a substituição de um nó inteiro.

> [!Important]
> Certifique-se de que você use uma operação de drenagem em um nó durante uma janela de manutenção planejada, onde os usuários foram notificados. Sob algumas condições, cargas de trabalho ativas podem enfrentar interrupções.

Para executar a ação de esvaziar, abra um prompt do PowerShell com privilégios elevados e execute o seguinte cmdlet:

```powershell  
  Disable-AzsScaleUnitNode -Location <RegionName> -Name <NodeName>
```

Para obter mais informações, consulte [Disable-AzsScaleUnitNode](https://docs.microsoft.com/powershell/module/azs.fabric.admin/disable-azsscaleunitnode).

## <a name="resume"></a>Continuar

O **retomar** ação retoma um nó desativado e a marca Active Directory para o posicionamento da carga de trabalho. As cargas de trabalho anteriores que estavam em execução no nó não realizar o failback. (Se você usar uma operação de drenagem em um nó Certifique-se para desligar. Quando você liga o nó de volta, ele não está marcado como ativo para o posicionamento da carga de trabalho. Quando estiver pronto, você deve usar a ação de continuação para marcar o nó como o Active Directory.)

Para executar a ação de retomada, abra um prompt do PowerShell com privilégios elevados e execute o seguinte cmdlet:

```powershell  
  Enable-AzsScaleUnitNode -Location <RegionName> -Name <NodeName>
```

Para obter mais informações, consulte [Enable-AzsScaleUnitNode](https://docs.microsoft.com/powershell/module/azs.fabric.admin/enable-azsscaleunitnode).

## <a name="repair"></a>Reparar

O **reparar** ação repara um nó. Use-o somente para qualquer um dos seguintes cenários:
 - Substituição de nó completo (com ou sem novos discos de dados)
 - Após a falha de componente de hardware e substituição (se for avisado na documentação de FRU (unidade) do campo).

> [!Important]  
> Consulte a documentação de FRU do seu fornecedor hardware de OEM para obter as etapas exatas quando você precisar substituir um nó ou componentes de hardware individuais. A documentação de FRU especificará se você precisa executar a ação de reparo após a substituição de um componente de hardware. 

Quando você executa a ação de reparo, você precisa especificar o endereço IP do BMC. 

Para executar a ação de reparo, abra um prompt do PowerShell com privilégios elevados e execute o seguinte cmdlet:

  ```powershell
  Repair-AzsScaleUnitNode -Location <RegionName> -Name <NodeName> -BMCIPv4Address <BMCIPv4Address>
  ```

## <a name="shutdown"></a>Shutdown

O **desligamento** fist ação move todas as cargas de trabalho ativas para os nós restantes na mesma unidade de escala. Em seguida, a ação desliga normalmente o nó de unidade de escala.

Depois de iniciar um nó que foi desligado, você precisará executar o [retomar](#resume) ação. As cargas de trabalho anteriores que estavam em execução no nó não realizar o failback.

Se a operação de desligamento falhar, tente o [drenar](#drain) operação seguido da operação de desligamento.

Para executar a ação de desligamento, abra um prompt do PowerShell com privilégios elevados e execute o seguinte cmdlet:

  ```powershell
  Stop-AzsScaleUnitNode -Location <RegionName> -Name <NodeName> -Shutdown
  ```



## <a name="next-steps"></a>Próximas etapas

Para saber mais sobre o módulo de administrador de malha do Azure Stack, confira [Azs.Fabric.Admin](https://docs.microsoft.com/powershell/module/azs.fabric.admin/?view=azurestackps-1.6.0).
