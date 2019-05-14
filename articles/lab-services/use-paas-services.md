---
title: Usar os serviços de plataforma-como um serviço (PaaS) no Azure DevTest Labs | Microsoft Docs
description: Saiba como usar os serviços de plataforma-como-um-serviço (Pass) no Azure DevTest Labs.
services: devtest-lab,lab-services
documentationcenter: na
author: spelluru
manager: femila
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/02/2019
ms.author: spelluru
ms.openlocfilehash: 7cdd185cddbd2403b72ff0e06530913af0b031de
ms.sourcegitcommit: 2ce4f275bc45ef1fb061932634ac0cf04183f181
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/07/2019
ms.locfileid: "65233129"
---
# <a name="use-platform-as-a-service-paas-services-in-azure-devtest-labs"></a>Usar os serviços de plataforma-como um serviço (PaaS) no Azure DevTest Labs
Há suporte para o PaaS através do recurso de ambientes no DevTest Labs. Ambientes no DevTest Labs são suportados por modelos pré-configurados do Azure Resource Manager em um repositório Git. Ambientes podem conter recursos de PaaS e IaaS. Eles permitem que você crie sistemas complexos que podem incluir recursos do Azure como máquinas virtuais, bancos de dados, redes virtuais e aplicativos Web, que são personalizados para trabalhar juntos. Esses modelos permitem a implantação consistente e melhor gerenciamento de ambientes usando o controle do código fonte. 

Um conjunto corretamente o sistema permite que os seguintes cenários: 

- Desenvolvedores têm independente e em vários ambientes
- Testes em diferentes configurações de forma assíncrona
- Integração em pipelines de preparo e produção sem qualquer alteração de modelo
- Presença de computadores de desenvolvimento e ambientes de dentro do mesmo laboratório melhora a facilidade de gerenciamento e relatório de custo.  

O provedor de recursos do DevTest Labs cria recursos em nome do usuário de laboratório, portanto, nenhuma permissão extra precisa ser fornecido para o usuário de laboratório para habilitar o uso dos recursos de PaaS. A imagem a seguir mostra um cluster do Service Fabric como um ambiente de laboratório.

![Cluster do Service Fabric como um ambiente](./media/create-environment-service-fabric-cluster/cluster-created.png)

Para obter mais informações sobre como configurar ambientes, consulte [criar ambientes de várias VMs e recursos de PaaS com modelos do Azure Resource Manager](devtest-lab-create-environment-from-arm.md). DevTest Labs tem um repositório público dos modelos do Azure Resource Manager que você pode usar para criar ambientes sem precisar se conectar a uma fonte externa do GitHub por conta própria. Para obter mais informações sobre ambientes públicos, consulte [configurar e usar ambientes públicos no Azure DevTest Labs](devtest-lab-configure-use-public-environments.md).

Em grandes organizações, as equipes de desenvolvimento normalmente fornecem ambientes, como ambientes de teste personalizados/isolado. Pode haver ambientes que devem ser usados por todas equipes dentro de uma unidade de negócios ou uma divisão. O grupo de TI talvez queira fornecer seus ambientes que podem ser usados por todas as equipes na organização.  

## <a name="customizations"></a>Personalizações

#### <a name="sandbox"></a>Área Restrita 
O proprietário de laboratório pode personalizar os ambientes de laboratório para alterar a função do usuário do **reader** à **Colaborador** dentro do grupo de recursos. Esse recurso está no **configurações do laboratório** página sob a **configuração e políticas** do laboratório. Essa alteração na função permite que o usuário adicionar ou remover recursos dentro desse ambiente. Se você quiser restringir ainda mais o acesso, use as políticas do Azure. Essa funcionalidade permite que você personalize os recursos ou a configuração sem o acesso no nível da assinatura.

#### <a name="custom-tokens"></a>Tokens personalizados
Há algumas informações de laboratório personalizado que está fora do grupo de recursos e é específica para ambientes em que o modelo pode acessar. Aqui estão alguns deles: 

- Identificação de rede do laboratório
- Local padrão
- Conta de armazenamento na qual os arquivos de modelos do Resource Manager são armazenados. 
 
#### <a name="lab-virtual-network"></a>Rede virtual do laboratório
O [ambientes conectar-se à rede de virtual do laboratório](connect-environment-lab-virtual-network.md) artigo descreve como modificar o modelo do Resource Manager para usar o `$(LabSubnetId)` token. Quando um ambiente é criado, o `$(LabSubnetId)` token é substituído pela primeira marca de sub-rede em que o **criar usados em máquina virtual** opção for definida como **true**. Ele permite que o nosso ambiente a ser usado anteriormente criado redes. Se você quiser usar os mesmos modelos do Resource Manager em ambientes de teste como preparo e produção, use `$(LabSubnetId)` como um valor padrão em um parâmetro de modelo do Resource Manager. 

#### <a name="environment-storage-account"></a>Conta de armazenamento do ambiente
DevTest Labs oferece suporte ao uso de [modelos do Gerenciador de recursos aninhados](../azure-resource-manager/resource-group-linked-templates.md). O [como o Azure DevTest Labs facilita o Gerenciador de recursos aninhados implantações de modelo para ambientes de teste](https://azure.microsoft.com/updates/azure-devtest-labs-streamlined-nested-arm-template-deployment-support-for-arm-template-based-environments) artigo explica como usar `_artifactsLocation` e `_artifactsLocationSasToken` tokens para criar um URI para um modelo do Resource Manager em a mesma pasta como ou em uma pasta aninhada do modelo principal. Para obter mais informações sobre esses dois tokens, consulte o **artefatos de implantação** seção [Azure Resource Manager – guia de práticas recomendadas](https://github.com/Azure/azure-quickstart-templates/blob/master/1-CONTRIBUTION-GUIDE/best-practices.md).

## <a name="user-experience"></a>Experiência do usuário

## <a name="developer"></a>Desenvolvedor
Os desenvolvedores usam o mesmo fluxo de trabalho para a criação de uma VM para criar um ambiente específico. Eles selecione o ambiente versus a imagem de máquina e insira as informações necessárias pelo modelo. Cada desenvolvedor ter um ambiente permite a implantação das alterações e depuração aprimorada de loop interno. O ambiente pode ser criado a qualquer momento usando o modelo mais recente.  Esse recurso permite que os ambientes para ser destruída e recriada para ajudar a reduzir o tempo de inatividade de ter que manualmente criando o sistema ou recuperação de teste de falha.  

### <a name="testing"></a>Testes
Ambientes do DevTest Labs permitem testes independentes de configurações e o código específico do modo assíncrono. A prática comum é configurar o ambiente com o código da solicitação de pull individual e iniciar todos os testes automatizados. Depois que os testes automatizados tiver sido executado até a conclusão, todos os testes manuais podem ser executados no ambiente específico. Geralmente, esse processo é feito como parte do pipeline de CI/CD. 

## <a name="management-experience"></a>Experiência de gerenciamento

### <a name="cost-tracking"></a>Controle de custos
O recurso de rastreamento de custo inclui recursos do Azure em diferentes ambientes como parte do geral tendência de custo. O custo por recursos não interrompe os diferentes recursos dentro do ambiente, mas o ambiente é exibido como um custo único.

### <a name="security"></a>Segurança
Uma assinatura do Azure configurada corretamente com o DevTest Labs pode [limitar o acesso aos recursos do Azure por meio de laboratório](devtest-lab-add-devtest-user.md). Com os ambientes, um proprietário de laboratório pode permitir que os usuários acessem recursos de PaaS com configurações aprovadas sem permitir o acesso a todos os outros recursos do Azure. No cenário em que os usuários do laboratório personalizar ambientes, o proprietário de laboratório pode permitir acesso de Colaborador. O acesso de Colaborador permite ao usuário de laboratório adicionar ou remover recursos do Azure somente dentro do grupo de recursos gerenciado. Ele permite que facilita o controle e gerenciamento em vez de permitir o acesso de Colaborador à assinatura do usuário.

### <a name="automation"></a>Automação
A automação é um componente fundamental para grande escala, ecossistema em vigor. Automação é necessária lidar com o gerenciamento ou o acompanhamento de vários ambientes em laboratórios e assinaturas.

### <a name="cicd-pipeline"></a>Pipeline de CI/CD
Os serviços de PaaS no DevTest Labs podem ajudar a melhorar o pipeline de CI/CD por ter voltada para implantações em que o acesso é controlado pelo laboratório.

## <a name="next-steps"></a>Próximas etapas
Consulte os seguintes artigos para obter detalhes sobre os ambientes: 

- 
- [Crie ambientes de várias VMs e recursos de PaaS com modelos do Azure Resource Manager](devtest-lab-create-environment-from-arm.md)
- [Configurar e usar ambientes públicos no Azure DevTest Labs](devtest-lab-configure-use-public-environments.md)
- [Criar um ambiente com cluster independente do Service Fabric no Azure DevTest Labs](create-environment-service-fabric-cluster.md)
- [Conectar-se a um ambiente para a rede virtual do seu laboratório no Azure DevTest Labs](connect-environment-lab-virtual-network.md)
- [Integrar ambientes em seus pipelines do Azure DevOps CI/CD](integrate-environments-devops-pipeline.md)
 





