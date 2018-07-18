---
title: Comparar tipos diferentes de laboratórios no Azure Lab Services | Microsoft Docs
description: Explica e compara os tipos diferentes de laboratórios que você pode criar usando o Azure Lab Services (conhecido antes como DevTest Labs).
services: lab-services
documentationcenter: na
author: spelluru
manager: ''
editor: ''
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: overview
ms.date: 05/17/2018
ms.author: spelluru
ms.openlocfilehash: 22a1c90dd1a1ca305431d91a801e5293a6d08703
ms.sourcegitcommit: b6319f1a87d9316122f96769aab0d92b46a6879a
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/20/2018
ms.locfileid: "34361175"
---
# <a name="compare-managed-and-devtest-labs-in-azure-lab-services"></a>Comparar laboratórios gerenciados e de DevTest no Azure Lab Services
Você pode criar dois tipos de laboratórios, **laboratórios gerenciados** com o Azure Lab Services, e **laboratórios personalizados** com o Azure DevTest Labs. Se você quiser apenas inserir o que precisa em um laboratório e permitir que o serviço configure e gerencie a infraestrutura necessária para o laboratório, escolha entre um dos **laboratórios gerenciados**. Atualmente, o **laboratório de sala de aula** é o único tipo de laboratório gerenciado que você pode criar com o Azure Lab Services. Se você quiser gerenciar sua própria infraestrutura, crie um **laboratório personalizado** usando o Azure DevTest Labs.

As seções a seguir fornecem mais detalhes sobre esses laboratórios. 

## <a name="managed-labs"></a>Laboratórios gerenciados
Os laboratórios gerenciados oferecem tipos diferentes de laboratórios que se ajustam às suas necessidades específicas. Atualmente, o Azure Lab Services oferece suporte apenas ao **laboratório de sala de aula** como um laboratório gerenciado. Os laboratórios gerenciados permitem o início imediato, com o mínimo de configuração. O próprio serviço trata de todo o gerenciamento da infraestrutura do laboratório, desde executar as VMs até o tratamento de erros e dimensionamento da infraestrutura. Para criar um laboratório gerenciado, primeiro você precisa criar uma conta de laboratório para sua organização. A conta de laboratório serve como a conta central no qual todos os laboratórios na organização são gerenciados. 

Quando você cria e usa recursos do Azure nesses laboratórios gerenciados, o serviço cria e gerencia os recursos em assinaturas internas da Microsoft. Eles não são criados em sua própria assinatura do Azure. O serviço mantém o controle de uso desses recursos em assinaturas internas da Microsoft. Esse uso é cobrado para sua assinatura do Azure que contém a conta de laboratório.   

Aqui estão alguns dos **casos de uso dos laboratórios gerenciados**: 

- Forneça aos alunos um laboratório de máquinas virtuais configuradas exatamente com o necessário para uma aula. Dê a cada aluno um número limitado de horas para usar as VMs para deveres ou projetos pessoais.
- Configure um pool de VMs de alto desempenho para executar uma pesquisa com uso intenso de computação ou de elementos gráficos. Execute as VMs conforme o necessário e limpe as máquinas quando terminar. 
- Mova o laboratório de computadores físicos da escola para a nuvem. Dimensione automaticamente o número de VMs até o limite máximo de uso e de custo definido no laboratório.  
- Provisione rapidamente um laboratório de máquinas virtuais para hospedar um hackathon. Exclua o laboratório com um único clique quando terminar. 


## <a name="devtest-labs"></a>Laboratórios de desenvolvimento/teste
Pode haver situações nas quais você deseja gerenciar toda a infraestrutura e configuração por conta própria, dentro da sua própria assinatura. Para fazer isso, você pode criar um laboratório personalizado com o Azure DevTest Labs no Portal do Azure. Para esses laboratórios, não é necessário criar uma conta de laboratório. Esses laboratórios não aparecem na conta de laboratório (que existe para os laboratórios gerenciados).  

Aqui estão alguns dos **casos de uso dos laboratórios de DevTest**: 

- Provisione rapidamente um laboratório de máquinas virtuais para hospedar um hackathon ou uma sessão prática em uma conferência. Exclua o laboratório com um único clique quando terminar. 
- Crie um pool de VMs configuradas com o seu aplicativo e permita que sua equipe use facilmente uma VM para correção de bugs.  
- Fornece aos desenvolvedores máquinas virtuais configuradas com todas as ferramentas necessárias. Agende inicialização e desligamento automáticos para minimizar os custos. 
- Crie repetidamente um laboratório de máquinas de teste como parte da sua implantação. Teste os bits mais recentes e limpe as máquinas de teste quando terminar. 
- Defina várias máquinas virtuais configuradas de forma diferente e vários agentes de teste para teste de desempenho e de escala. 
- Ofereça sessões de treinamento aos clientes com um laboratório configurado com a versão mais recente do produto. Dê a que cada cliente um número limitado de horas para uso do laboratório. 


## <a name="managed-labs-vs-devtest-labs"></a>Laboratórios gerenciados X Laboratórios de desenvolvimento/teste
A tabela a seguir compara dois tipos de laboratórios que têm suporte dos Azure Lab Services: 

| Recursos | Laboratórios gerenciados | Laboratórios de desenvolvimento/teste |
| -------- | ----------------  | ---------- |
| Gerenciamento da infraestrutura do Azure no laboratório. |  Gerenciada automaticamente pelo serviço | Você gerencia a sua própria  |
| Resiliência interna para problemas de infraestrutura | Tratada automaticamente pelo serviço | Você gerencia a sua própria  |
| Gerenciamento de assinaturas | O serviço lida com a alocação de recursos nas assinaturas do Microsoft que dão apoio ao serviço. A escala é tratada automaticamente pelo serviço. | Você gerencia a sua própria em sua assinatura do Azure. Sem dimensionamento automático de assinaturas. |
| Implantação do Azure Resource Manager dentro do laboratório | Não disponível | Disponível |

## <a name="next-steps"></a>Próximas etapas
Introdução à configuração de um laboratório usando o Azure Lab Services:

- [Configurar um laboratório de sala de aula](tutorial-setup-classroom-lab.md)
- [Configurar um laboratório personalizado](tutorial-create-custom-lab.md)
