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
ms.date: 04/19/2018
ms.author: spelluru
ms.openlocfilehash: 8f8de53f03cc1378e8aaafa9a2f486eadc23fe14
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/01/2018
ms.locfileid: "34637846"
---
# <a name="an-introduction-to-azure-lab-services"></a>Uma introdução ao Azure Lab Services
O Azure Lab Services permite que você configure rapidamente um ambiente para a sua equipe (por exemplo: ambiente de desenvolvimento, ambiente de teste, ambiente de laboratório de sala de aula) na nuvem. Um proprietário de laboratório cria um laboratório, provisiona máquinas virtuais do Windows ou do Linux, instala o software e as ferramentas necessárias e disponibiliza-os para os usuários do laboratório. Os usuários do laboratório se conectam às VMs (máquinas virtuais) do laboratório e as usam para o trabalho diário, para projetos de curto prazo ou para fazer exercícios em sala de aula. Depois que os usuários começam a usar recursos no laboratório, um administrador de laboratório pode analisar o custo e o uso em vários laboratórios e definir políticas abrangentes para otimizar os custos da equipe ou sua organização.

> [!IMPORTANT]
> O **Azure DevTest Labs** está sendo expandido com novos tipos de laboratórios (Azure Lab Services)! 
> 
> O Azure Lab Services permite criar laboratórios gerenciados, como laboratórios de sala de aula. O próprio serviço cuida de todo o gerenciamento de infraestrutura para um laboratório gerenciado, desde girar VMs até tratamento de erros e dimensionamento da infraestrutura. Os laboratórios gerenciados estão em visualização no momento. Quando a visualização terminar, os tipos do novo laboratório e os DevTest Labs existentes farão parte de um novo nome, o Azure Lab Services, onde todos os tipos de laboratório continuarão a evoluir. 

## <a name="key-capabilities"></a>Principais recursos
O Azure Lab Services dá suporte aos seguintes recursos/funcionalidades principais: 

- **Configuração rápida e flexível de um laboratório**. Usando o Azure Lab Services, os proprietários de laboratório podem rapidamente configurar um laboratório para suas necessidades. O serviço oferece a opção para cuidar de todo o trabalho de infraestrutura do Azure para laboratórios gerenciados, ou habilitar os proprietários do laboratório para autogerenciar e personalizar a infraestrutura na assinatura do proprietário do laboratório. O serviço fornece dimensionamento interno e a resiliência da infraestrutura para laboratórios que o serviço gerencia para você. 
- **Simplificado experiência para os usuários do laboratório**. Em um laboratório gerenciado, como um laboratório de sala de aula, os usuários do laboratório podem se registrar em um laboratório com um código de registro e acessar o laboratório a qualquer momento para usar os recursos. Em um laboratório criado no DevTest Labs, um proprietário de laboratório pode atribuir permissões para usuários do laboratório criarem e acessarem as máquinas virtuais, gerenciar e reutilizar os discos de dados e configurar segredos reutilizáveis.  
- **Otimização e a análise de custo**. Um proprietário de laboratório pode definir programações para desligar e inicializar máquinas virtuais automaticamente. O proprietário do laboratório pode definir uma programação para especificar os horários quando as máquinas virtuais do laboratório estarão acessíveis para os usuários, definir políticas de uso por usuário ou por laboratório para otimizar custos e analisar as tendências de uso e atividade em um laboratório. Para laboratórios gerenciados como laboratórios de sala de aula, no momento, um subconjunto menor de opções de otimização e a análise de custo está disponível. 
- **Segurança inserida**. O proprietário de um laboratório pode configurar uma rede virtual privada e uma sub-rede para um laboratório e habilitar um endereço IP público compartilhado. Os usuários do laboratório podem acessar recursos com segurança usando a rede virtual configurada com o ExpressRoute ou o VPN site a site. (atualmente disponível apenas no DevTest Labs)
- **Integração com seus fluxos de trabalho e suas ferramentas**. O Azure Lab Services permite integrar os laboratórios no site e nos sistemas de gerenciamento da sua organização. Você pode provisionar automaticamente ambientes de dentro de suas ferramentas de integração contínua/implantação contínua (CI/CD). (atualmente disponível apenas no DevTest Labs)

## <a name="scenarios"></a>Cenários
Aqui estão alguns dos cenários aos quais o Azure Lab Services dá suporte: 

### <a name="set-up-a-resizable-computer-lab-in-the-cloud-for-your-classroom"></a>Configure um laboratório de computação redimensionável na nuvem para sua sala de aula  

- Crie um laboratório de sala de aula gerenciada. Diga ao serviço exatamente o que você precisa, e ele criará e gerenciará a infraestrutura do laboratório para você, para que você possa se concentrar em dar a aula e não nos detalhes técnicos não de um laboratório. 
- Forneça aos alunos um laboratório de máquinas virtuais configuradas exatamente com o necessário para uma aula. Dê a cada aluno um número limitado de horas para usar as VMs para trabalho em sala.  
- Mova o laboratório de computadores físicos da escola para a nuvem. Dimensione automaticamente o número de VMs até o limite máximo de uso e de custo definido no laboratório. 
- Exclua o laboratório com um único clique quando terminar. 

### <a name="use-devtest-labs-for-development-environments"></a>Usar o DevTest Labs para ambientes de desenvolvimento 
O Azure DevTest Labs pode ser usado para implementar vários cenários importantes, mas um dos principais cenários envolve o uso DevTest Labs para hospedar computadores de desenvolvimento para desenvolvedores. Neste cenário, o DevTest Labs oferece estes benefícios: 

- Permita que os desenvolvedores provisionem rapidamente seus computadores de desenvolvimento sob demanda.
- Provisione os ambientes do Windows e do Linux usando modelos e artefatos reutilizáveis.
- Os desenvolvedores podem personalizar facilmente seus computadores de desenvolvimento sempre que for necessário.
- Os administradores podem controlar os custos garantindo que os desenvolvedores não podem ter mais VMs que precisam para o desenvolvimento e as VMs sejam desligadas quando não estiverem em uso. 

Para obter mais informações, consulte [Use DevTest Labs para desenvolvimento](devtest-lab-developer-lab.md). 

### <a name="use-devtest-labs-for-test-environments"></a>Usar DevTest Labs para ambientes de teste
Use o Azure DevTest Labs para implementar vários cenários importantes. No entanto, um cenário principal envolve o uso do DevTest Labs para hospedar computadores para testadores. Neste cenário, o DevTest Labs oferece estes benefícios:

- Os testadores podem testar a última versão de seus aplicativos provisionando ambientes Windows e Linux rapidamente com modelos e artefatos reutilizáveis.
- Os testadores podem escalar verticalmente seu teste de carga provisionando vários agentes de teste.
- Os administradores podem controlar os custos garantindo que os testadores não podem ter mais VMs que precisam para o teste e as VMs sejam desligadas quando não estiverem em uso.

Para obter mais informações, consulte [Use DevTest Labs para teste](devtest-lab-test-env.md).

## <a name="user-profiles"></a>Perfis do usuário
Este artigo descreve os diferentes perfis de usuário no Azure Lab Services. 

### <a name="lab-account-owner"></a>Proprietário da conta do laboratório
Normalmente, e o administrador de TI dos recursos de nuvem da organização, que tem a assinatura do Azure atua como um proprietário de conta de laboratório e realiza as seguintes tarefas:   

- Define uma conta de laboratório para sua organização.
- Gerencia e configura as políticas em todos os laboratórios.
- Concede permissões para as pessoas na organização para criarem um laboratório usando a conta do laboratório.

### <a name="lab-creator"></a>Criador de laboratório 
Normalmente, os usuários como um líder/gerente de desenvolvimento, um professor, um host de hackathon, um instrutor online cria laboratórios usando uma conta do laboratório. Um criador do laboratório executa as seguintes tarefas: 

- Cria um laboratório.
- Cria máquinas virtuais no laboratório. 
- Instala o software apropriado em máquinas virtuais.
- Especifica quem pode acessar o laboratório.
- Fornece um link para o laboratório para os usuários.

### <a name="lab-user"></a>Usuário do laboratório
Um usuário do laboratório realiza as seguintes tarefas:

- Usa o link de registro que o usuário do laboratório recebe de um criador do laboratório para registrar com o laboratório. 
- Conecta-se a uma máquina virtual no laboratório e usa-o para o desenvolvimento, teste ou fazer o trabalho de classe. 

## <a name="next-steps"></a>Próximas etapas
Introdução à configuração de um laboratório usando o Azure Lab Services:

- [Configurar um laboratório de sala de aula](classroom-labs/tutorial-setup-classroom-lab.md)
- [Configurar um laboratório](tutorial-create-custom-lab.md)
