---
title: "Recuperação da perda de dados catastrófica na pilha do Azure usando o serviço de Backup de infraestrutura de | Microsoft Docs"
description: "Quando uma falha catastrófica faz com que a pilha do Azure falha, você pode restaurar sua infraestrutura de dados quando restabelecer a implantação de pilha do Azure."
services: azure-stack
documentationcenter: 
author: mattbriggs
manager: femila
editor: 
ms.assetid: 2ECE8580-0BDE-4D4A-9120-1F6771F2E815
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/13/2017
ms.author: mabrigg
ms.openlocfilehash: 141641b01b338e3426861dad7424a1de1bd2c63c
ms.sourcegitcommit: 95500c068100d9c9415e8368bdffb1f1fd53714e
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/14/2018
---
# <a name="recover-from-catastrophic-data-loss"></a>Recuperação de perda de dados catastrófica

*Aplica-se a: sistemas integrados de pilha do Azure.*

A pilha do Azure executa os serviços do Azure em seu data center. A pilha do Azure pode executar em ambientes pequenos quatro nós instalado em um único rack. Em contraste, o Azure é executado em mais de 40 regiões em vários data centers e várias regiões em cada região. Recursos do usuário podem abranger vários servidores, racks, data centers e regiões. Com a pilha do Azure, você só tem a opção de implantar a nuvem inteira em um único rack. Isso expõe sua nuvem para o risco de eventos catastróficos no seu datacenter ou falhas devido a erros de principal do produto. Quando um desastre ocorrer, a instância do Azure pilha ficar offline. Todos os dados é potencialmente irrecuperável.

Dependendo da causa raiz da perda de dados, talvez seja necessário reparar um única infraestrutura serviço ou restaurar a instância do Azure pilha inteira. Você ainda precisará restaurar para um hardware diferente no mesmo local ou em um local diferente.

Este endereços cenário recuperar toda a instalação em caso de falha do equipamento e a reimplantação da nuvem privada.

| Cenário                                                           | Perda de Dados                            | Considerações                                                             |
|--------------------------------------------------------------------|--------------------------------------|----------------------------------------------------------------------------|
| Recuperar de perda de dados catastrófica devido a desastres ou produto de bug | Todos os dados de aplicativo, usuário e infraestrutura | Aplicativo de usuário e os dados estão protegidos separadamente da infraestrutura de dados |

## <a name="workflows"></a>Fluxos de trabalho

A jornada de proteção do Azure iniciar começa com backup dos dados de infraestrutura e aplicativo/locatário separadamente. Este documento aborda como proteger a infraestrutura. 

![Implantação inicial da pilha do Azure](media\azure-stack-backup\azure-stack-backup-workflow1.png)

No pior cenário em que todos os dados são perdidos, recuperando a pilha do Azure é o processo de restauração a infraestrutura de dados exclusiva para que a implantação de pilha do Azure e todos os dados de usuário. 

![Reimplantar a pilha do Azure](media\azure-stack-backup\azure-stack-backup-workflow2.png)

## <a name="restore"></a>Restore

Se houver perda de dados catastrófica, mas o hardware é utilizável, reimplantação da pilha do Azure é necessária. Durante a reimplantação, você pode especificar o local de armazenamento e as credenciais necessárias para acessar os backups. Nesse modo, não é necessário especificar os serviços que precisam ser restaurados. Controlador de infraestrutura de Backup injeta estado do plano de controle como parte do fluxo de trabalho de implantação.

Se houver um desastre que inutiliza o hardware, a reimplantação só é possível em um novo hardware. Reimplantação pode levar várias semanas, enquanto a substituição de hardware é ordenada e chega no data center. Restauração de dados do plano de controle é possível a qualquer momento. No entanto, a restauração não tem suporte se a versão da instância reimplantada é mais de uma versão maior do que a versão usada no último backup. 

| Modo de implantação | Ponto de partida | Ponto de extremidade                                                                                                                                                                                                     |
|-----------------|----------------|---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Instalação limpa   | Compilação de linha de base | OEM implanta a pilha do Azure e atualizações para a versão mais recente com suporte.                                                                                                                                          |
| Modo de recuperação   | Compilação de linha de base | OEM implanta a pilha do Azure no modo de recuperação e lida com a versão correspondente a requisitos com base no backup mais recente disponível. OEM conclui a implantação, atualizando a versão mais recente com suporte. |

## <a name="data-in-backups"></a>Dados de backups

A pilha do Azure oferece suporte a um tipo de implantação de chamada de modo de recuperação de nuvem. Esse modo é usado somente se você escolher recuperar a pilha do Azure após um desastre ou bugs de produto processado a solução irrecuperável. Este modo de implantação não recupera os dados de usuário armazenados na solução. O escopo do modo de implantação é limitado a restaurar os dados a seguir:

 - Entradas de implantação
 - Sistemas de identidade interna
 - Federado identificar configuração (implantações desconectadas)
 - Certificados raiz usados pela autoridade de certificação interna
 - Azure dados de usuário de configuração do Gerenciador de recursos, como assinaturas, planos, ofertas e cotas de armazenamento, rede e recursos de computação
 - Cofres e segredos KeyVault
 - As atribuições de diretivas RBAC e atribuições de função 

Nenhum usuário infraestrutura como serviço (IaaS) ou plataforma como um serviço (PaaS) os recursos são recuperadas durante a implantação. Isto é IaaS VMs, contas de armazenamento, blobs, tabelas, configuração de rede e assim por diante, são perdidas. O objetivo de recuperação de nuvem é garantir que os operadores e os usuários podem registrar novamente no portal após a conclusão da implantação. Logon de usuários não verão qualquer um dos seus recursos. Os usuários têm suas assinaturas restauradas e juntamente com que o original planos e oferece políticas definidas pelo administrador. Usuários que fizerem logon no sistema opera com as mesmas restrições impostas pela solução original antes do desastre. Depois de concluir a recuperação de nuvem, o operador pode restaurar manualmente de valor agregado e RPs de terceiros e dados associados.

## <a name="next-steps"></a>Próximas etapas

 - Saiba mais sobre as práticas recomendadas para [usando a infraestrutura de serviço de Backup](azure-stack-backup-best-practices.md).
