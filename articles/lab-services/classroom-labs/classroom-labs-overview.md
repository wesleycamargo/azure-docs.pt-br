---
title: Sobre o Azure Lab Services | Microsoft Docs
description: Saiba como serviços de laboratório podem facilitar a criação, o gerenciamento e a proteção de laboratórios com máquinas virtuais que podem ser usados por desenvolvedores, testadores, educadores, alunos e outros.
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
ms.date: 05/21/2018
ms.author: spelluru
ms.openlocfilehash: e9c3cae7c7129cc489ddd38b5b2de18dd6f52e58
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/01/2018
ms.locfileid: "34660465"
---
# <a name="introduction-to-classroom-labs"></a>Introdução aos laboratórios de sala de aula
O Azure Lab Services permite que você configure rapidamente um ambiente de laboratório de sala de aula na nuvem. Um educador cria um laboratório de sala de aula, provisiona máquinas virtuais do Windows ou do Linux, instala o software e as ferramentas necessárias na sala de aula e disponibiliza-os para os alunos. Os alunos na sala de aula se conectam às máquinas virtuais (VM) no laboratório e as usam para projetos, tarefas e exercícios em sala de aula. 

Os laboratórios de sala de aula são laboratórios gerenciados que são gerenciados pelo Azure. O próprio serviço cuida de todo o gerenciamento de infraestrutura para um laboratório gerenciado, desde executar as máquinas virtuais (VMs) até o tratamento de erros e dimensionamento da infraestrutura. Especifique o tipo de infraestrutura necessário e instale quaisquer ferramentas ou software necessários para a sala de aula. Os laboratórios gerenciados estão em visualização no momento.  

## <a name="scenarios"></a>Cenários
Aqui está o cenário principal que oferece suporte a laboratórios de sala de aula do Azure Lab Services: 

### <a name="set-up-a-resizable-computer-lab-in-the-cloud-for-your-classroom"></a>Configure um laboratório de computação redimensionável na nuvem para sua sala de aula  

- Crie um laboratório de sala de aula gerenciada. Diga ao serviço exatamente o que você precisa, e ele criará e gerenciará a infraestrutura do laboratório para você, para que você possa se concentrar em dar a aula e não nos detalhes técnicos de um laboratório. 
- Forneça aos alunos um laboratório de máquinas virtuais configuradas exatamente com o necessário para uma aula. Dê a cada aluno um número limitado de horas para usar as VMs para trabalho em sala.  
- Mova o laboratório de computadores físicos da escola para a nuvem. Dimensione automaticamente o número de VMs até o limite máximo de uso e de custo definido no laboratório. 
- Exclua o laboratório com um único clique quando terminar. 

## <a name="user-profiles"></a>Perfis do usuário
Este artigo descreve os diferentes perfis de usuário no Azure Lab Services. 

### <a name="lab-account-owner"></a>Proprietário da conta do laboratório
Normalmente, e o administrador de TI dos recursos de nuvem da organização, que tem a assinatura do Azure atua como um proprietário de conta de laboratório e realiza as seguintes tarefas:   

- Define uma conta de laboratório para sua organização.
- Gerencia e configura as políticas em todos os laboratórios.
- Concede permissões para as pessoas na organização para criarem um laboratório usando a conta do laboratório.

### <a name="educator"></a>Educador
Normalmente, os usuários como um professor ou um instrutor online criam laboratórios de sala de aula em uma conta de laboratório. Um educador executa as seguintes tarefas: 

- Cria um laboratório de sala de aula.
- Cria máquinas virtuais no laboratório. 
- Instala o software apropriado em máquinas virtuais.
- Especifica quem pode acessar o laboratório.
- Fornece um link de registro para o laboratório para alunos.

### <a name="student"></a>Aluno
Um estudante executa as seguintes tarefas:

- Usa o link de registro que o usuário do laboratório recebe de um criador do laboratório para registrar com o laboratório. 
- Conecta-se a uma máquina virtual no laboratório e usa-a para fazer trabalhos, tarefas e projetos em sala de aula. 

## <a name="next-steps"></a>Próximas etapas
Introdução à configuração de uma conta de laboratório necessária para criar um laboratório de sala de aula usando o Azure Lab Services:

- [Configurar uma conta de laboratório](tutorial-setup-lab-account.md)
