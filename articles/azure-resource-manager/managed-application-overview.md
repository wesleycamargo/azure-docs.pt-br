---
title: "Visão geral de aplicativo gerenciado do Azure | Microsoft Docs"
description: Descreve os conceitos de aplicativo gerenciado do Azure
services: azure-resource-manager
author: ravbhatnagar
manager: rjmax
ms.service: azure-resource-manager
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.date: 05/11/2017
ms.author: gauravbh; tomfitz
ms.translationtype: Human Translation
ms.sourcegitcommit: 97fa1d1d4dd81b055d5d3a10b6d812eaa9b86214
ms.openlocfilehash: a4348a4d2348d744c03ad3a89d0548526fa2f9f8
ms.contentlocale: pt-br
ms.lasthandoff: 05/11/2017


---
# <a name="azure-managed-applications-overview"></a>Visão geral de aplicativos gerenciados do Azure

Atualmente, o Azure fornece um Marketplace sólido em que ISVs e empresas emergentes podem oferecer suas soluções para clientes em todo o mundo. O Azure Marketplace é uma galeria que consiste em centenas de modelos complexos com vários recursos, tanto próprios quanto de terceiros. Os clientes podem, em minutos, implantar e começar a usar aplicativos PaaS e SaaS. Embora ele forneça uma ótima maneira de implantar uma oferta rapidamente, o cliente ainda é responsável por manter e atualizar a solução. Para fornecedores, não é possível cobrar os clientes pelo uso de um aplicativo além da cobrança de imagem de máquina virtual. Além disso, os fornecedores não têm nenhuma maneira de impedir que os clientes modifiquem recursos de aplicativos essenciais ou de bloquear o acesso à propriedade intelectual que compõe um aplicativo. Os aplicativos gerenciados do Azure fornecem uma solução para esses problemas. 

## <a name="advantages-of-managed-applications"></a>Vantagens dos aplicativos gerenciados

Os aplicativos gerenciados do Azure oferecem um ecossistema que permite aos fornecedores disponibilizar serviços de PaaS ou SaaS como aplicativos independentes. Os clientes implantam aplicativos gerenciados em suas assinaturas, mas os fornecedores podem gerenciá-los. Ele permite que os fornecedores cobrem os clientes que usam o sistema de cobrança do Azure e usem modelos para gerenciar o ciclo de vida dos aplicativos implantados. Por outro lado, ele permite que os clientes adquiram atualizações automaticamente e paguem pela manutenção e pelo suporte. Eles não precisam manter ou atualizar o aplicativo por conta própria ou diagnosticar e corrigir problemas quando o aplicativo falha.

Um ecossistema assim no Azure não apenas beneficiaria fornecedores de PaaS e SaaS, mas também as equipes de plataforma central corporativas e integradores de sistema que desejem empacotar e revender suas soluções.

## <a name="how-managed-applications-work"></a>Como funcionam os aplicativos gerenciados
Há duas experiências ao se trabalhar com aplicativos gerenciados:

1. O fornecedor ou ISV (fornecedor de software independente) que cria um aplicativo gerenciado e o torna disponível para uso mais amplo. 
2. O cliente ou consumidor que deseja criar e usar o aplicativo publicado. 

Este artigo aborda as duas experiências. Primeiro, vamos entender como funciona um aplicativo gerenciado. 

Um aplicativo gerenciado é semelhante a um modelo de solução do mercado, com uma diferença importante. Em um aplicativo gerenciado, os recursos são provisionados em um grupo de recursos que é gerenciado pelo ISV/fornecedor. O grupo de recursos está presente na assinatura do cliente, mas um usuário, grupo de usuários ou aplicativo no locatário do ISV tem acesso ao grupo de recursos. Para gerenciar e atender às necessidades do aplicativo, a identidade do fornecedor é adicionada à função Proprietário, Colaborador, Leitor ou outra função interna. 

## <a name="key-concepts"></a>Principais conceitos

### <a name="managed-resource-group"></a>Grupo de recursos gerenciado
O grupo de recursos onde todos os recursos do Azure provisionados no modelo são criados. Por exemplo, se o dispositivo está criando uma conta de armazenamento, esse grupo de recursos contém o recurso de conta de armazenamento. Ele não contém o recurso de dispositivo.

### <a name="appliance-package"></a>Pacote de dispositivo
O editor cria um pacote que contém os arquivos de modelo e o arquivo createUIDefinition. Especificamente, ele contém os seguintes arquivos:

- **applianceMainTemplate.json** - o arquivo de modelo que define todos os recursos que são provisionados pelo dispositivo. Esse arquivo é um arquivo de modelo regular usado para a criação de recursos.

- **MainTemplate.json** - arquivo de modelo que define o recurso de dispositivo (Microsoft.Solutions/appliances). Uma propriedade essencial definida neste recurso é o ManagedResourceGroupId. Essa propriedade indica qual grupo de recursos é usado para hospedar recursos reais definidos em applianceMainTemplate.json.

- **createUIDefinition.json** - esse arquivo descreve como a interface do usuário necessária para os parâmetros definidos no modelo é processada.

### <a name="authorization"></a>Autorização
O editor precisa especificar as permissões exigidas pelo fornecedor para gerenciar os recursos em nome do cliente. Essa permissão se aplica ao grupo de recursos gerenciado. Defina os seguintes valores:

- **PrincipalID** - o identificador do Azure AD para o usuário, grupo ou aplicativo que é usado para conceder acesso ao grupo de recursos gerenciado. Esse identificador pertence ao locatário do editor.

- **RoleDefinitionID** - o identificador do Azure AD da função atribuída à ID da entidade anterior. Pode ser qualquer uma das funções RBAC internas no locatário do editor.

## <a name="next-steps"></a>Próximas etapas

* Para entender a experiência de fornecedor, consulte [Criar e publicar um aplicativo gerenciado do Azure](managed-application-publishing.md).
* Para entender a experiência do usuário, consulte [Consumir um aplicativo gerenciado do Azure](managed-application-consumption.md).
* Para criar um arquivo de definição de interface do usuário, consulte [Introdução a CreateUiDefinition](managed-application-createuidefinition-overview.md).
