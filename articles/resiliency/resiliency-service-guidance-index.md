---
title: "Diretrizes de resiliência de serviço | Microsoft Azure"
description: "Links para a recuperação de desastres e diretrizes proativas de resiliência e disponibilidade dos serviços do Microsoft Azure."
services: 
documentationcenter: na
author: adamglick
manager: saladki
editor: 
ms.assetid: 919a197d-ba47-4e49-a64c-118e27d5a7df
ms.service: resiliency
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 08/18/2016
ms.author: aglick
translationtype: Human Translation
ms.sourcegitcommit: aafb03c86884d1af3e2cae1134c9e6d7ac42397b
ms.openlocfilehash: 4186211af944c0c0109799a94e4163cfceeee4c4


---
# <a name="microsoft-azure-service-resiliency-guidance"></a>Diretrizes de resiliência de serviço do Microsoft Azure
O Microsoft Azure foi projetado para fornecer os recursos necessários, quando necessários. Como parte de um bom design e práticas operacionais, você deve saber como arquitetar o uso dos serviços do Azure para obter alta disponibilidade e o que fazer se seu aplicativo for afetado por uma interrupção de serviço. Para ajudar você nesse processo, este documento contém links para diretrizes de recuperação de desastres, bem como diretrizes de design para vários serviços do Azure.

## <a name="disaster-recovery-guidance"></a>Guia de recuperação de desastres
Os links abaixo para as diretrizes de recuperação de desastres podem fornecer as informações necessárias para ajudá-lo a colocar seu aplicativo online rapidamente, caso você seja afetado por uma interrupção de serviço do Azure. Esses links foram criados para ajudá-lo a responder à pergunta, "Eu estou sendo afetado por uma interrupção de serviço do Azure, o que posso fazer?"

## <a name="design-guidance"></a>Diretrizes de design
Os links de diretrizes de design abaixo são diretrizes arquiteturais e de design criadas para ajudá-lo a entender melhor como usar cada serviço do Azure de forma a maximizar o tempo de atividade do aplicativo. Esses links foram criados para ajudá-lo a responder à pergunta "Como garantir que um bug, uma falha de hardware, uma interrupção do serviço ou outra falha não afete a disponibilidade geral do meu aplicativo?" Caso não haja nenhuma diretriz específica para o serviço que você está atualmente procurando, o artigo sobre [Alta disponibilidade para aplicativos criados no Microsoft Azure](resiliency-high-availability-azure-applications.md) pode ter informações adicionais que podem ajudá-lo em seu design.

## <a name="service-guidance"></a>Diretrizes do serviço
| O Barramento de | Guia de recuperação de desastres | Diretrizes de design |
|:--- |:---:|:---:|
| [Serviços de Nuvem](https://azure.microsoft.com/services/cloud-services/ "Azure Serviços de Nuvem") |[link](../cloud-services/cloud-services-disaster-recovery-guidance.md "Diretrizes de recuperação de desastres de serviços de nuvem do Azure") |Não disponível |
| [Cofre da Chave](https://azure.microsoft.com/services/key-vault/ "Azure Cofre da Chave") |[link](../key-vault/key-vault-disaster-recovery-guidance.md "Diretrizes de recuperação de desastres do Cofre de Chaves do Azure") |Não disponível |
| [Armazenamento](https://azure.microsoft.com/services/storage/ "Azure Armazenamento") |[link](../storage/storage-disaster-recovery-guidance.md "Diretrizes de recuperação de desastres de armazenamento do Azure") |Não disponível |
| [Bancos de dados SQL](https://azure.microsoft.com/services/sql-database/ "Azure Bancos de dados SQL") |[link](../sql-database/sql-database-disaster-recovery.md "Diretrizes de recuperação de desastres do banco de dados SQL do Azure") |[link](../sql-database/sql-database-performance-guidance.md "Diretrizes de design do banco de dados SQL do Azure") |
| [Máquinas virtuais](https://azure.microsoft.com/services/virtual-machines/ "Azure Máquinas virtuais") |[link](../virtual-machines/virtual-machines-disaster-recovery-guidance.md "Diretrizes de recuperação de desastres de máquinas virtuais do Azure") |Não disponível |
| [Rede Virtual](https://azure.microsoft.com/services/virtual-network/ "Azure Rede Virtual") |[link](../virtual-network/virtual-network-disaster-recovery-guidance.md "Diretrizes de recuperação de desastres de Rede Virtual do Azure") |Não disponível |

## <a name="next-steps"></a>Próximas etapas
Se você estiver procurando diretrizes que se concentrem mais amplamente em sistemas e soluções, leia [Recuperação de desastres e alta disponibilidade para aplicativos criados no Microsoft Azure](https://aka.ms/drtechguide).



<!--HONumber=Nov16_HO3-->


