---
title: Visão geral do Azure Key Vault | Microsoft Docs
description: O Azure Key Vault é um serviço de nuvem que funciona como um repositório de segredos seguro.
services: key-vault
author: barclayn
manager: mbaldwin
tags: azure-resource-manager
ms.assetid: 34af20ee-3fa7-4f28-9d98-6168b1759764
ms.service: key-vault
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: overview
ms.custom: mvc
ms.date: 07/17/2018
ms.author: barclayn
ms.openlocfilehash: 2cda30c85ce8a8dc9b7a6c0134b7cabc58b842a4
ms.sourcegitcommit: 7827d434ae8e904af9b573fb7c4f4799137f9d9b
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/18/2018
ms.locfileid: "39115313"
---
# <a name="what-is-azure-key-vault"></a>O que é o Cofre da Chave do Azure?

O Azure Key Vault é um serviço de nuvem que funciona como um repositório de segredos seguro.

Você tem senhas, cadeias de conexão e outras informações que são necessários para manter seus aplicativos funcionando. Essas informações devem estar disponíveis, mas protegidas. É isso o que o Azure Key Vault pode fazer. O Azure Key Vault pode ajudar a armazenar e gerenciar os segredos do aplicativo com segurança.

O Key Vault permite que você crie vários contêineres seguros, chamado cofres. Esses cofres contam com HSMs (Módulos de Segurança de Hardware). Os cofres ajudam a reduzir a possibilidade de perda acidental de informações de segurança pela centralização do armazenamento de segredos do aplicativo. Os Key Vaults também controlam e registram o acesso a todas as coisas armazenadas neles. O Azure Key Vault pode tratar da solicitação e da renovação de certificados TLS, fornecendo os recursos necessários para uma solução de gerenciamento de ciclo de vida de certificados robusta.

 O Azure Key Vault foi projetado para dar suporte a segredos e chaves do aplicativo. O Key Vault não deve ser usado como um repositório de senhas de usuário.

## <a name="why-use-azure-key-vault"></a>Por que usar o Azure Key Vault?

### <a name="centralize-application-secrets"></a>Centralizar segredos do aplicativo

O armazenamento centralizado de segredos do aplicativo no Azure Key Vault permite que você controle sua distribuição. Isso reduz consideravelmente a probabilidade de os segredos serem vazados acidentalmente. Ao usar o Key Vault, os desenvolvedores de aplicativos não precisam mais armazenar informações de segurança no aplicativo. Isso elimina a necessidade de tornar essa informação parte do código. Por exemplo, um aplicativo pode precisar se conectar a um banco de dados. Em vez de armazenar a cadeia de conexão nos códigos do aplicativo, armazene-o com segurança no Key Vault.

Os aplicativos podem acessar as informações necessárias com segurança por meio de URIs que permitem recuperar versões específicas de um segredo depois que a chave ou o segredo do aplicativo é armazenado no Azure Key Vault. Isso ocorre sem a necessidade de escrever código personalizado para proteger as informações secretas.

### <a name="securely-store-secrets-and-keys"></a>Armazene segredos e chaves com segurança

Os segredos e as chaves são protegidas pelo Azure, usando módulos de segurança de hardware (HSMs), comprimentos da chave e algoritmos padrão da indústria. Os HSMs usados são validados pelo padrão FIPS (Federal Information Processing Standards) 140-2 Nível 2.

O acesso a um cofre de chaves requer a devida autenticação e autorização antes de um chamador (usuário ou aplicativo) poder obter acesso. A autenticação estabelece a identidade do chamador e a autorização determina as operações que ele tem permissão para executar.

A autenticação é feita pelo Azure Active Directory. A autorização pode ser feita por meio de RBAC (controle de acesso baseado em função) ou de política de acesso do Key Vault. O RBAC é usado quando é necessário lidar com o gerenciamento dos cofres e a política de acesso do cofre de chaves é usada na tentativa de acessar dados armazenados em um cofre.

Os Azure Key Vaults podem ser qualquer software ou hardware protegido por HSM. Para situações que exijam garantia extra, você pode importar ou gerar chaves em HSMs (módulos de segurança de hardware) que nunca deixam os limites do HSM. A Microsoft usa módulos de segurança de hardware Thales. Você pode usar as ferramentas Thales para mover uma chave de seu HSM para o Azure Key Vault.

Por fim, o Azure Key Vault foi projetado para que a Microsoft não veja nem extraia seus dados.

### <a name="monitor-access-and-use"></a>Monitorar o acesso e o uso

Depois de criar alguns Key Vaults, é bom monitorar como e quando suas chaves e segredos são acessados. Você pode fazer isso habilitando o registro em log para o Key Vault. Você pode configurar o Azure Key Vault para:

- Arquive em uma conta de armazenamento.
- Transmita para um hub de eventos.
- Enviar os logs ao Log Analytics.

Você tem controle sobre os logs e pode protegê-los restringindo o acesso, e também pode excluir logs que não são mais necessários.

### <a name="simplified-administration-of-application-secrets"></a>Administração simplificada de segredos do aplicativo

Ao armazenar dados valiosos, é necessário executar várias etapas. As informações de segurança devem ser protegidas, elas devem seguir um ciclo de vida e devem estar altamente disponíveis. O Azure Key Vault simplifica grande parte disso:

- Removendo a necessidade de conhecimento interno sobre HSMs.
- Escalando verticalmente e rapidamente para atender aos picos de uso da sua organização.
- Replicando o conteúdo de seu Key Vault dentro de uma região e para uma região secundária. Isso garante a alta disponibilidade e retira a necessidade de qualquer ação do administrador para disparar o failover.
- Fornecendo opções de administração do Azure padrão por meio do portal, da CLI do Azure e do PowerShell.
- Automatizando algumas tarefas em certificados que você compra de autoridades de certificação pública, como registro e renovação.

Além disso, os Azure Key Vaults permitem que você separe os segredos do aplicativo. Os aplicativos podem acessar apenas o cofre que eles têm permissão para acessar, e ficam limitados a executar operações específicas. Você pode criar um Azure Key Vault por aplicativo e restringir os segredos armazenados em um Key Vault para um aplicativo e uma equipe de desenvolvedores específicos.

### <a name="integrate-with-other-azure-services"></a>Integração com outros serviços do Azure

Como um repositório seguro no Azure, o Key Vault foi usado para simplificar cenários como o [Azure Disk Encryption](../security/azure-security-disk-encryption.md), a funcionalidade [sempre criptografado]( https://docs.microsoft.com/sql/relational-databases/security/encryption/always-encrypted-database-engine) no SQL Server e no SQL do Azure e os [aplicativos Web do Azure]( https://docs.microsoft.com/azure/app-service/web-sites-purchase-ssl-web-site). O próprio Key Vault pode se integrar às contas de armazenamento, aos hubs de eventos e ao Log Analytics.

## <a name="next-steps"></a>Próximas etapas

- [Início Rápido: Criar um Azure Key Vault usando a CLI](quick-create-cli.md)
- [Configurar um aplicativo Web do Azure para ler um segredo do Key Vault](tutorial-web-application-keyvault.md)