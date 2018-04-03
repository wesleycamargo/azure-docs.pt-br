---
title: Política de manutenção de pilha do Azure | Microsoft Docs
description: Saiba mais sobre a manutenção de política e como manter um sistema integrado em um estado com suporte a pilha do Azure.
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
editor: ''
ms.assetid: caac3d2f-11cc-4ff2-82d6-52b58fee4c39
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/02/2018
ms.author: mabrigg
ms.openlocfilehash: 2c10dcf185c62f3672be80ad2e3d049eae82fe6b
ms.sourcegitcommit: 20d103fb8658b29b48115782fe01f76239b240aa
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/03/2018
---
# <a name="azure-stack-servicing-policy"></a>Política de manutenção de pilha do Azure
Este artigo descreve a política de serviço para sistemas de pilha do Azure integradas, e que você deve fazer para manter o sistema em um estado com suporte. 

## <a name="update-package-types"></a>Tipos de pacote de atualização

Há dois tipos de pacotes de atualização para os sistemas integrados; As atualizações de software e atualizações que são específicas para o fornecedor do hardware fabricante (OEM), como drivers e firmware. Essas atualizações são entregues como pacotes de atualização separados pilha do Azure e são gerenciadas de forma independente.

- **As atualizações de software**. A Microsoft é responsável para o ciclo de vida de serviço de ponta a ponta para os pacotes de atualização de software da Microsoft. Esses pacotes podem incluir as últimas atualizações de segurança do Windows Server, atualizações de segurança não e atualizações de recurso da pilha do Azure. Você pode baixar pacotes de atualização essas políticas diretamente da Microsoft.
- **Atualizações de fornecido pelo fornecedor de hardware de OEM**. Parceiros de hardware de pilha do Azure são responsáveis por de ponta a ponta manutenção do ciclo de vida (incluindo orientação) para o firmware relacionado ao hardware e os pacotes de atualização de driver. Além disso, os parceiros de hardware de pilha do Azure possuam e mantêm orientação de hardware no host de ciclo de vida de hardware e software de todos os. O fornecedor do hardware OEM hospeda esses pacotes em seu próprio site de download de atualização.

## <a name="update-package-release-cadence"></a>Cadência de lançamento do pacote de atualização

A Microsoft espera liberar os pacotes de atualização de software em um ritmo mensal. No entanto, é possível ter várias ou nenhuma versões de atualização em um mês. Fornecedores de hardware de OEM liberar suas atualizações em uma base conforme necessário.

Um pacote de atualização da Microsoft tem a seguinte convenção de nomenclatura para ajudá-lo a identificar facilmente a data de liberação:

*MajorProductVersion.MinorProductVersion.YYMMDD.BuildNumber*

Por exemplo, uma atualização de software da Microsoft lançada em 15 de junho de 2017 teria a versão "1.0.170615.1".

## <a name="keep-your-system-under-support"></a>Manter o sistema com suporte
Para continuar a receber suporte, você deve manter sua implantação do Azure pilha atual. A política de adiamento de atualizações é que, pilha do Azure permanecer no suporte, ele deve executar a versão lançada mais recentemente de atualização ou executar qualquer uma das duas versões de atualização principal anterior.  Hotfixes não são considerados para versões de atualização principal.  Se sua nuvem do Azure pilha atrás por *mais de duas atualizações*, ele é considerado fora de conformidade e atualize pelo menos a versão mínima com suporte para receber suporte. 

Por exemplo, se a versão disponível mais recente de atualização é 1805 e os dois pacotes de atualização anteriores eram versões 1804 e 1803, 1803 e 1804 permanecem no suporte. No entanto, 1802 tem suporte. A política se aplica quando não há nenhuma versão para um ou dois meses. Por exemplo, se a versão atual é 1805 e não houve nenhuma versão 1804, os dois pacotes de atualização anterior de 1803 e 1802 permaneceria no suporte.

Pacotes de atualização de software Microsoft não cumulativa e requerem o pacote de atualização anterior como um pré-requisito. Se você optar por adiar uma ou mais atualizações, considere o tempo de execução geral, se você deseja obter a versão mais recente. 

A tabela a seguir mostra as versões de pacote de atualização de exemplo, seus pré-requisitos e a versão mínima com suporte que o sistema deve estar no manter o suporte. A tabela se baseia na versão inicial do sistemas de pilha do Azure integrado (compilação 1708), com a primeira versão de pacote de atualização (1709) em setembro de 2017. 

| Pacote de atualização mais recente (*exemplo*) | Pré-requisito | Versão mínima com suporte |
| -- | -- | -- |
| 1710 | 1709 | N/D |
| 1711 | 1710 | 1709 |
| 1712 | 1711 | 1710 |
| 1802 | 1712 | 1711 |
| 1803 | 1802 | 1712 |
| 1804 | 1803 | 1802 |
| 1805 | 1804 | 1803 |
| | | 
Na tabela anterior, não há nenhuma versão 1801.

## <a name="next-steps"></a>Próximas etapas

- [Gerenciar atualizações na pilha do Azure](azure-stack-updates.md)


