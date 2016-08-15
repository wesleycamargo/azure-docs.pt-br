<properties
   pageTitle="Diretrizes de resiliência de serviço | Microsoft Azure"
   description="Links para a recuperação de desastres e diretrizes proativas de resiliência e disponibilidade dos serviços do Microsoft Azure."
   services=""
   documentationCenter="na"
   authors="adamglick"
   manager="hongfeig"
   editor=""/>

<tags
   ms.service="resiliency"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="08/01/2016"
   ms.author="aglick"/>

# Diretrizes de resiliência de serviço do Microsoft Azure
O Microsoft Azure foi projetado para fornecer os recursos necessários, quando necessários. Como parte de um bom design e práticas operacionais, você deve saber como arquitetar o uso dos serviços do Azure para obter alta disponibilidade e o que fazer se seu aplicativo for afetado por uma interrupção de serviço. Para ajudar você nesse processo, este documento contém links para diretrizes de recuperação de desastres, bem como diretrizes de design para vários serviços do Azure.

##Guia de recuperação de desastres
Os links abaixo para as diretrizes de recuperação de desastres podem fornecer as informações necessárias para ajudá-lo a colocar seu aplicativo online rapidamente, caso você seja afetado por uma interrupção de serviço do Azure. Esses links foram criados para ajudá-lo a responder à pergunta, "Eu estou sendo afetado por uma interrupção de serviço do Azure, o que posso fazer?"

##Diretrizes de design
Os links de diretrizes de design abaixo são diretrizes arquiteturais e de design criadas para ajudá-lo a entender melhor como usar cada serviço do Azure de forma a maximizar o tempo de atividade do aplicativo. Esses links foram criados para ajudá-lo a responder à pergunta "Como garantir que um bug, uma falha de hardware, uma interrupção do serviço ou outra falha não afete a disponibilidade geral do meu aplicativo?" Caso não haja nenhuma orientação específica para o serviço que você está atualmente procurando, o artigo sobre [alta disponibilidade para aplicativos criados no Microsoft Azure](./resiliency-high-availability-azure-applications.md) pode ter informações adicionais que podem ajudá-lo em seu design.

##Diretrizes do serviço
| O Barramento de | Guia de recuperação de desastres | Diretrizes de design |
|:---------|:--------------------------:|:------------------:|
| [Serviços de Nuvem](https://azure.microsoft.com/services/cloud-services/ "Serviços de nuvem do Azure") | [link](../cloud-services/cloud-services-disaster-recovery-guidance.md "Diretrizes de recuperação de desastres de serviços de nuvem do Azure") | Não disponível |
| [Cofre da Chave](https://azure.microsoft.com/services/key-vault/ "Cofre da Chave do Azure") | [link](../key-vault/key-vault-disaster-recovery-guidance.md "Diretrizes de recuperação de desastres do Cofre de Chaves do Azure") | Não disponível |
| [Armazenamento](https://azure.microsoft.com/services/storage/ "Armazenamento do Azure") | [link](../storage/storage-disaster-recovery-guidance.md "Diretrizes de recuperação de desastres de armazenamento do Azure") | Não disponível |
| [Bancos de dados SQL](https://azure.microsoft.com/services/sql-database/ "Bancos de dados SQL do Azure") | [link](../sql-database/sql-database-disaster-recovery.md "Diretrizes de recuperação de desastres do banco de dados SQL do Azure") | [link](../sql-database/sql-database-business-continuity-design.md "Diretrizes de design do banco de dados SQL do Azure") |
| [Máquinas virtuais](https://azure.microsoft.com/services/virtual-machines/ "Máquinas Virtuais do Azure") | [link](../virtual-machines/virtual-machines-disaster-recovery-guidance.md "Diretrizes de recuperação de desastres de máquinas virtuais do Azure") | Não disponível |
| [Rede Virtual](https://azure.microsoft.com/services/virtual-network/ "Rede Virtual do Azure") | [link](../virtual-network/virtual-network-disaster-recovery-guidance.md "Diretrizes de recuperação de desastres de Rede Virtual do Azure") | Não disponível |

##Próximas etapas
Se você estiver procurando diretrizes que se concentrem mais amplamente em sistemas e soluções, leia [Recuperação de desastres e alta disponibilidade para aplicativos criados no Microsoft Azure](https://aka.ms/drtechguide).

<!---HONumber=AcomDC_0803_2016-->