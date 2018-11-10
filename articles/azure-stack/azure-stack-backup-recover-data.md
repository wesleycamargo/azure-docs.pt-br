---
title: Recuperar da perda de dados catastrófica no Azure Stack usando o serviço de Backup de infraestrutura | Microsoft Docs
description: Quando uma falha catastrófica faz com que o Azure Stack para falhar, você pode restaurar sua infraestrutura de dados quando restabelecer a sua implantação do Azure Stack.
services: azure-stack
documentationcenter: ''
author: jeffgilb
manager: femila
editor: ''
ms.assetid: 2ECE8580-0BDE-4D4A-9120-1F6771F2E815
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/05/2018
ms.author: jeffgilb
ms.reviewer: hectorl
ms.openlocfilehash: bd72abd4f8f23414ea1003c74d2f90cde3b51d34
ms.sourcegitcommit: f0c2758fb8ccfaba76ce0b17833ca019a8a09d46
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/06/2018
ms.locfileid: "51034330"
---
# <a name="recover-from-catastrophic-data-loss"></a>Recuperar da perda de dados catastrófica

*Aplica-se a: sistemas integrados do Azure Stack.*

O Azure Stack executa serviços do Azure em seu datacenter. O Azure Stack pode executar em ambientes pequenos como quatro nós instalado em um único rack. Em contraste, o Azure é executado em mais de 40 regiões em vários data centers e várias zonas em cada região. Recursos do usuário podem abranger vários servidores, racks, datacenters e regiões. Com o Azure Stack, você atualmente só tem a opção de implantar sua nuvem inteira em um único rack. Isso expõe sua nuvem ao risco de eventos catastróficos no seu datacenter ou falhas devido a bugs do produto principal. Quando um desastre, a instância do Azure Stack fica offline. Todos os dados é potencialmente irrecuperável.

Dependendo da causa raiz da perda de dados, talvez você precise reparar um única infraestrutura serviço ou restaurar toda a instância do Azure Stack. Até mesmo, você talvez precise restaurar para um hardware diferente no mesmo local ou em um local diferente.

Este endereços cenário recuperar toda a instalação em caso de falha de equipamento e a reimplantação da nuvem privada.

| Cenário                                                           | Perda de Dados                            | Considerações                                                             |
|--------------------------------------------------------------------|--------------------------------------|----------------------------------------------------------------------------|
| Recuperar da perda de dados catastrófica devido a bugs de produto ou desastre | Todos os dados de infraestrutura e de usuário e aplicativo | Aplicativo de usuário e os dados estão protegidos separadamente da infraestrutura de dados |

## <a name="workflows"></a>Fluxos de trabalho

A jornada de proteger o início do Azure começa com o backup de dados de infraestrutura e aplicativo/locatário separadamente. Este documento aborda como proteger a infraestrutura. 

![Implantação inicial do Azure Stack](media\azure-stack-backup\azure-stack-backup-workflow1.png)

No piores cenários em que todos os dados são perdidos, a recuperação do Azure Stack é o processo de restauração a infraestrutura de dados exclusiva para essa implantação do Azure Stack e todos os dados de usuário. 

![Reimplantar o Azure Stack](media\azure-stack-backup\azure-stack-backup-workflow2.png)

## <a name="restore"></a>Restaurar

Se houver perda de dados catastrófica, mas o hardware ainda é utilizável, a reimplantação do Azure Stack é necessária. Durante a reimplantação, você pode especificar o local de armazenamento e as credenciais necessárias para acessar os backups. Nesse modo, não é necessário especificar os serviços que precisam ser restaurados. Controlador de infraestrutura de Backup injeta o estado do plano de controle como parte do fluxo de trabalho de implantação.

Se houver um desastre que inutiliza o hardware, a reimplantação só é possível em um novo hardware. Reimplantação pode levar várias semanas, enquanto a substituição de hardware é ordenada e chega ao datacenter. Restauração de dados do plano de controle é possível a qualquer momento. No entanto, a restauração não há suporte se a versão da instância reimplantada for mais de uma versão maior do que a versão usada no último backup. 

| Modo de implantação | Ponto de partida | Ponto de extremidade                                                                                                                                                                                                     |
|-----------------|----------------|---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Instalação limpa   | Compilação de linha de base | OEM implanta o Azure Stack e é atualizado para a versão mais recente com suporte.                                                                                                                                          |
| Modo de recuperação   | Compilação de linha de base | OEM do Azure Stack é implantado em modo de recuperação e lida com a versão correspondente de requisitos com base no último backup disponível. O OEM conclui a implantação, atualizando a versão mais recente com suporte. |

## <a name="data-in-backups"></a>Dados de backups

O Azure Stack oferece suporte a um tipo de implantação chamado modo de recuperação de nuvem. Esse modo é usado somente se você optar por recuperar o Azure Stack após um desastre ou bug de produto processado a solução irrecuperável. Esse modo de implantação não se recupera os dados de usuário armazenados na solução. O escopo desse modo de implantação é limitado a restaurar os dados a seguir:

 - Entradas de implantação
 - Sistemas de identidade interna
 - Federado identificar configuration (implantações desconectadas)
 - Certificados raiz usados pela autoridade de certificação interna
 - Azure dados de usuário de configuração do Gerenciador de recursos, como assinaturas, planos, ofertas e cotas de armazenamento, rede e recursos de computação
 - Segredos do Cofre de chaves e cofres
 - As atribuições de política RBAC e as atribuições de função 

Nenhum usuário infraestrutura como serviço (IaaS) ou plataforma como um serviço (PaaS) os recursos podem ser recuperadas durante a implantação. Que é IaaS VMs, contas de armazenamento, blobs, tabelas, configuração de rede e assim por diante, são perdidas. O objetivo de recuperação de nuvem é garantir que seus operadores e os usuários podem fazer no portal após a conclusão da implantação. Usuários que fizerem logon em não verão qualquer um dos seus recursos. Os usuários têm suas assinaturas restauradas e, juntamente com que o original planos e oferece políticas definidas pelo administrador do. Usuários que fizerem logon no sistema opera com as mesmas restrições impostas pela solução original antes do desastre. Depois de concluir a recuperação de nuvem, o operador pode restaurar manualmente com valor agregado e RPs de terceiros e dados associados.

## <a name="next-steps"></a>Próximas etapas

Saiba mais sobre as práticas recomendadas para [usando o serviço de Backup de infraestrutura](azure-stack-backup-best-practices.md).
