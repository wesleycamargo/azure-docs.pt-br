---
title: Política de manutenção de pilha do Azure | Microsoft Docs
description: Saiba mais sobre a manutenção de política e como manter um sistema integrado em um estado com suporte a pilha do Azure.
services: azure-stack
documentationcenter: ''
author: brenduns
manager: femila
editor: ''
ms.assetid: caac3d2f-11cc-4ff2-82d6-52b58fee4c39
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/05/2018
ms.author: brenduns
ms.reviewer: harik
ms.openlocfilehash: c3cc8857373238079fee06c61faec962d7e3a6b2
ms.sourcegitcommit: 6116082991b98c8ee7a3ab0927cf588c3972eeaa
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/05/2018
ms.locfileid: "34796410"
---
# <a name="azure-stack-servicing-policy"></a>Política de manutenção de pilha do Azure
Este artigo descreve a política de serviço para sistemas de pilha do Azure integradas, e que você deve fazer para manter o sistema em um estado com suporte. 

## <a name="update-package-types"></a>Tipos de pacote de atualização

Há dois tipos de pacotes de atualização para sistemas integrados: 

- **As atualizações de software**. A Microsoft é responsável para o ciclo de vida de serviço de ponta a ponta para os pacotes de atualização de software da Microsoft. Esses pacotes podem incluir as últimas atualizações de segurança do Windows Server, atualizações de segurança não e atualizações de recurso da pilha do Azure. Você pode baixar pacotes de atualização essas políticas diretamente da Microsoft.

- **Atualizações de fornecido pelo fornecedor de hardware de OEM**. Parceiros de hardware de pilha do Azure são responsáveis por de ponta a ponta manutenção do ciclo de vida (incluindo orientação) para o firmware relacionado ao hardware e os pacotes de atualização de driver. Além disso, os parceiros de hardware de pilha do Azure possuam e mantêm orientação de hardware no host de ciclo de vida de hardware e software de todos os. O fornecedor do hardware OEM hospeda esses pacotes em seu próprio site de download de atualização.


## <a name="update-package-release-cadence"></a>Cadência de lançamento do pacote de atualização
A Microsoft espera liberar os pacotes de atualização de software em um ritmo mensal. No entanto, é possível ter várias ou nenhuma versões de atualização em um mês. Fornecedores de hardware de OEM liberar suas atualizações em uma base conforme necessário. 

Localizar a documentação sobre como planejar e gerenciar atualizações e como determinar a versão atual no [visão geral de atualizações de gerenciar](azure-stack-updates.md). Para obter informações sobre uma atualização específica, incluindo como baixá-lo, consulte as notas de versão para que a atualização: 
- [Atualização de pilha 1805 do Azure](azure-stack-update-1805.md)
- [Atualização de pilha 1804 do Azure](azure-stack-update-1804.md)
- [Atualização de pilha 1803 do Azure](azure-stack-update-1803.md)


## <a name="hotfixes"></a>Hotfixes
Ocasionalmente, Microsoft fornece hotfixes para pilha Azure esse endereço de um problema específico que é geralmente preventiva ou detecção de hora.  Cada hotfix é lançado com correspondente artigo da Base de Conhecimento da Microsoft que fornece detalhes sobre o problema, a causa e a resolução. 

Hotfixes são baixados e instalados assim como os pacotes de atualização completa regular para a pilha do Azure. No entanto, ao contrário de uma atualização completa, pode instalar os hotfixes em minutos. É recomendável que Azure pilha operadores defina janelas de manutenção durante a instalação de hotfixes. Hotfixes atualizar a versão da sua nuvem de pilha do Azure para que possa determinar facilmente se o hotfix foi aplicado. Um hotfix separado é fornecido para cada versão da pilha do Azure que ainda está em suporte. Cada correção para uma iteração específica é cumulativa e inclui as atualizações anteriores para a mesma versão. Você pode ler mais sobre a aplicabilidade de um hotfix específico em um correções correspondente da Base de dados de conhecimento do artigo.  


## <a name="keep-your-system-under-support"></a>Manter o sistema com suporte
Para continuar a receber suporte, você deve manter sua implantação do Azure pilha atual. A política de adiamento de atualizações é: para sua implantação do Azure pilha permaneça no suporte, deve executar a versão lançada mais recentemente de atualização ou executar qualquer uma das duas versões de atualização anterior. Hotfixes não são considerados para versões de atualização principal. Se sua nuvem do Azure pilha atrás por *mais de duas atualizações*, ele é considerado fora de conformidade e atualize pelo menos a versão mínima com suporte para receber suporte. 

Por exemplo, se a versão disponível mais recente de atualização é 1805 e os dois pacotes de atualização anteriores eram versões 1804 e 1803, 1803 e 1804 permanecem no suporte. No entanto, 1802 tem suporte. A política se aplica quando não há nenhuma versão para um ou dois meses. Por exemplo, se a versão atual é 1805 e não houve nenhuma versão 1804, os dois pacotes de atualização anterior de 1803 e 1802 permanecem no suporte.

Pacotes de atualização de software Microsoft não cumulativa e requerem o pacote de atualização anterior como um pré-requisito. Se você optar por adiar uma ou mais atualizações, considere o tempo de execução geral, se você deseja obter a versão mais recente. 

## <a name="get-support"></a>Obtenha suporte
A pilha do Azure segue o mesmo processo de suporte do Azure. Os clientes corporativos podem seguir o processo descrito em [como criar uma solicitação de suporte do Azure](/azure/azure-supportability/how-to-create-azure-support-request). Se você for um cliente de um provedor de serviços de nuvem (CSP), entre em contato com seu CSP para obter suporte.  Para obter mais informações, consulte as [Perguntas Frequentes do Suporte do Azure](https://azure.microsoft.com/support/faq/). 


## <a name="next-steps"></a>Próximas etapas

- [Gerenciar atualizações na pilha do Azure](azure-stack-updates.md)


