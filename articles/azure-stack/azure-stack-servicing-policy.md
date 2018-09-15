---
title: Política de manutenção de pilha do Azure | Microsoft Docs
description: Saiba mais sobre o Azure Stack como manter um sistema integrado em um estado com suporte e política de manutenção.
services: azure-stack
documentationcenter: ''
author: sethmanheim
manager: femila
editor: ''
ms.assetid: caac3d2f-11cc-4ff2-82d6-52b58fee4c39
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/05/2018
ms.author: sethm
ms.reviewer: harik
ms.openlocfilehash: 523be365e8b66c157546470a70ddec73d3e1e5d1
ms.sourcegitcommit: ab9514485569ce511f2a93260ef71c56d7633343
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/15/2018
ms.locfileid: "45630674"
---
# <a name="azure-stack-servicing-policy"></a>Política de manutenção de pilha do Azure
Este artigo descreve a política de manutenção para sistemas integrados do Azure Stack e o que você deve fazer para manter seu sistema em um estado com suporte. 

## <a name="update-package-types"></a>Tipos de pacote de atualização

Há dois tipos de pacotes de atualização para sistemas integrados: 

- **Atualizações de software da Microsoft**. A Microsoft é responsável para o ciclo de vida de manutenção de ponta a ponta para os pacotes de atualização de software da Microsoft. Esses pacotes podem incluir as últimas atualizações de segurança do Windows Server, atualizações não relacionado à segurança e atualizações de recursos do Azure Stack. Você pode baixar os seguintes pacotes de atualização diretamente da Microsoft.

- **Atualizações de fornecido pelo fornecedor de hardware de OEM**. Parceiros de hardware do Azure Stack são responsáveis por de ponta a ponta manutenção do ciclo de vida (incluindo orientação) para o firmware relacionado ao hardware e os pacotes de atualização de driver. Além disso, parceiros de hardware do Azure Stack possuem e mantêm diretrizes para todos os software e hardware no host de ciclo de vida do hardware. O fornecedor de hardware OEM hospeda esses pacotes em seu próprio site de download de atualização.


## <a name="update-package-release-cadence"></a>Cadência de lançamento do pacote de atualização
A Microsoft espera liberar os pacotes de atualização de software em uma cadência mensal. No entanto, é possível ter várias ou nenhuma versões de atualização em um mês. Fornecedores de hardware de OEM liberar suas atualizações em uma base conforme necessário. 

Encontre a documentação sobre como planejar e gerenciar atualizações e como determinar a versão atual no [visão geral de gerenciar atualizações](azure-stack-updates.md). Para obter informações sobre uma atualização específica, incluindo como baixá-lo, consulte as notas de versão para que a atualização: 
- [Atualização da pilha 1808 do Azure](azure-stack-update-1808.md)
- [Atualização de 1807 de pilha do Azure](azure-stack-update-1807.md)
- [Atualização da pilha 1805 do Azure](azure-stack-update-1805.md)


## <a name="hotfixes"></a>Hotfixes
Ocasionalmente, Microsoft fornece os hotfixes para o Azure Stack esse endereço de um problema específico que costuma ser preventiva ou sensível ao tempo.  Cada hotfix é lançado com um artigo de Base de Conhecimento Microsoft correspondente que fornece detalhes sobre o problema, causa e resolução. 

Os hotfixes são baixados e instalados assim como os pacotes de atualização regular de completo para o Azure Stack. No entanto, ao contrário de uma atualização completa, pode instalar os hotfixes em minutos. É recomendável que operadores do Azure Stack defina janelas de manutenção durante a instalação de hotfixes. Hotfixes atualizar a versão de nuvem do Azure Stack para que você possa determinar facilmente se o hotfix foi aplicado. Um hotfix separado é fornecido para cada versão do Azure Stack que ainda está em suporte. Cada correção para uma iteração específica é cumulativa e inclui atualizações anteriores para a mesma versão. Você pode ler mais sobre a aplicabilidade de um hotfix específico em um correções correspondente da Base de dados de conhecimento do artigo.  


## <a name="keep-your-system-under-support"></a>Manter seu sistema de suporte
Para continuar a receber suporte, você deve manter sua implantação do Azure Stack atual. A política de adiamento de atualizações é: para a implantação do Azure Stack permanecerão com suporte, ele deve executar a versão lançada mais recentemente de atualização ou executar qualquer uma das duas versões de atualização anterior. Hotfixes não serão considerados para versões de atualização importante. Se a nuvem do Azure Stack está atrasado em *mais de duas atualizações*, ele é considerado fora de conformidade e atualize pelo menos a versão mínima com suporte para receber suporte. 

Por exemplo, se a versão disponível mais recente de atualização é 1805 e os dois pacotes de atualização anteriores foram versões 1804 e 1803, 1803 e 1804 permanecem no suporte. No entanto, 1802 é sem suporte. A política se aplica quando não há nenhuma versão de um ou dois meses. Por exemplo, se a versão atual está 1805 e não havia nenhuma versão 1804, os dois pacotes de atualização anterior do 1803 e 1802 permanecerão com suporte.

Pacotes de atualização de software Microsoft são não cumulativas e requerem que o pacote de atualização anterior como um pré-requisito. Se você optar por adiar uma ou mais atualizações, considere o tempo de execução geral, se você deseja obter a versão mais recente. 

## <a name="get-support"></a>Obtenha suporte
O Azure Stack segue o mesmo processo de suporte do Azure. Os clientes empresariais podem seguir o processo descrito em [como criar uma solicitação de suporte do Azure](/azure/azure-supportability/how-to-create-azure-support-request). Se você for um cliente de um provedor de serviços de nuvem (CSP), entre em contato com seu CSP para obter suporte.  Para obter mais informações, consulte as [Perguntas Frequentes do Suporte do Azure](https://azure.microsoft.com/support/faq/). 


## <a name="next-steps"></a>Próximas etapas

- [Gerenciar atualizações no Azure Stack](azure-stack-updates.md)


