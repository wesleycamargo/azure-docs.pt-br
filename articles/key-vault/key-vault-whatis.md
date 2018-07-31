---
title: O que é o Cofre da Chave do Azure? | Microsoft Docs
description: O Cofre da Chave do Azure ajuda a proteger chaves criptográficas e segredos usados por aplicativos e serviços em nuvem. Usando o Cofre da Chave do Azure, os clientes podem criptografar chaves e segredos (como chaves de autenticação, chaves de conta de armazenamento, chaves de criptografia de dados, arquivos .PFX e senhas) usando chaves que são protegidas por HSMs (módulos de segurança de hardware).
services: key-vault
documentationcenter: ''
author: barclayn
manager: mbaldwin
tags: azure-resource-manager
ms.assetid: e759df6f-0638-43b1-98ed-30b3913f9b82
ms.service: key-vault
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 07/23/2018
ms.author: barclayn
ms.openlocfilehash: b34b05ae86aed199d80a86c8e1a073cb54b5e75f
ms.sourcegitcommit: 44fa77f66fb68e084d7175a3f07d269dcc04016f
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/24/2018
ms.locfileid: "39226706"
---
# <a name="what-is-azure-key-vault"></a>O que é o Cofre da Chave do Azure?

O Cofre da Chave do Azure ajuda a proteger chaves criptográficas e segredos usados por aplicativos e serviços em nuvem. Usando o Key Vault, você pode criptografar chaves e segredos (como chaves de autenticação, chaves de conta de armazenamento, chaves de criptografia de dados, arquivos .PFX e senhas) usando chaves protegidas por HSMs (módulos de segurança de hardware). Para garantia extra, você pode importar ou gerar chaves em HSMs. Se você optar por fazer isso, a Microsoft processará suas chaves em HSMs FIPS 140-2 Nível 2 validados (hardware e firmware).  

O Cofre da Chave simplifica o processo de gerenciamento de chaves e permite que você tenha controle das chaves que acessam e criptografam seus dados. Desenvolvedores podem criar chaves para desenvolvimento e teste em minutos e depois migrá-las diretamente para chaves de produção. Administradores de segurança podem conceder (e revogar) permissão a chaves conforme for necessário.

## <a name="basic-concepts"></a>Conceitos básicos

O Azure Key Vault é uma ferramenta para armazenar e acessar segredos de forma segura. Um segredo é tudo o que você deseja controlar rigorosamente o acesso, como certificados, senhas ou chaves de API.
Veja abaixo alguns termos principais:
- **Locatário** - Um locatário é a organização que possui e gerencia uma instância específica de serviços de nuvem da Microsoft. Geralmente, ele é usado de maneira exata para referir-se ao conjunto de serviços do Azure e do Office 365 para uma organização
- **Proprietário do Cofre**: pode criar um Key Vault obtendo acesso completo e controle sobre ele. O proprietário do cofre também pode configurar a auditoria para registrar quem acessa os segredos e as chaves. Os administradores podem controlar o ciclo de vida da chave. Eles podem reverter para uma nova versão da chave, fazer o backup etc.
- **Consumidor do cofre**: pode executar ações nos ativos dentro do Key Vault quando o proprietário do cofre concede acesso que depende das permissões concedidas.
- **[Azure Active Directory](../active-directory/active-directory-whatis.md)** é o serviço do Azure AD para um determinado locatário. Cada diretório tem um ou mais domínios. Um diretório pode ter várias assinaturas associadas a ele, mas apenas um locatário. 
- **ID do locatário do Azure**: essa é uma maneira exclusiva para identificar um Azure Active Directory dentro de uma Assinatura do Azure. 
- **Identidade de Serviço Gerenciada**: o Azure Key Vault fornece uma maneira de armazenar com segurança as credenciais e outras chaves e segredos, mas seu código precisa autenticar para o Key Vault para recuperá-los. A Identidade de Serviço Gerenciada (MSI) torna a solução desse problema mais simples, fornecendo aos serviços do Azure uma identidade gerenciada automaticamente no Azure Active Directory (Azure AD). Você pode usar essa identidade para autenticar o Key Vault ou qualquer serviço que dê suporte à autenticação do Azure AD sem ter as credenciais no código. Para ler mais sobre o MSI, consulte [aqui](../active-directory/managed-service-identity/overview.md)

## <a name="key-vault-roles"></a>Funções do cofre de chaves

Use a tabela a seguir para entender melhor como o Cofre da Chave pode ajudar a atender às necessidades de desenvolvedores e administradores de segurança.

| Função | Problema declarado | Solucionado pelo Cofre da Chave do Azure |
| --- | --- | --- |
| Desenvolvedor de um aplicativo do Azure |"Eu quero escrever um aplicativo para o Azure que use chaves de assinatura e criptografia, mas quero que as chaves sejam externas ao aplicativo, para que a solução seja adequada a um aplicativo distribuído geograficamente. <br/><br/>Também quero que essas chaves e segredos sejam protegidos, sem precisar escrever o código sozinho. E também que essas chaves e segredos sejam fáceis de usar em meu aplicativo, com desempenho ideal.” |√ As chaves são armazenadas em um cofre e invocadas pelo URI quando necessário.<br/><br/> √ As chaves são protegidas pelo Azure, usando módulos de segurança de hardware (HSMs), comprimentos da chave e algoritmos padrão da indústria.<br/><br/> √ As chaves são processadas em HSMs que residem nos mesmos datacenters do Azure que os aplicativos. Isso permite uma maior confiabilidade e uma latência reduzida do que quando as chaves ficam em um local separado, como localmente. |
| Desenvolvedor de SaaS (software como serviço) |"Eu não quero a responsabilidade ou ser o possível culpado por problemas com as chaves e segredos de locatário dos meus clientes. <br/><br/>Quero que os clientes tenham e gerenciem suas chaves para que eu possa me concentrar em fazer o que faço melhor, que é fornecer os recursos centrais do software." |√ Os clientes podem importar suas próprias chaves para o Azure e gerenciá-las. Quando um aplicativo de SaaS precisa executar operações criptográficas usando as chaves de seus clientes, o Cofre de Chaves faz essas operações em nome dele. O aplicativo não vê as chaves dos clientes. |
| Diretor-chefe de segurança (CSO) |"Quero saber que nossos aplicativos estão em conformidade com HSMs FIPS 140-2 Nível 2, para um gerenciamento de chaves seguro. <br/><br/>Quero garantir que minha organização controle o ciclo de vida da chave e possa monitorar seu uso. <br/><br/>E embora usemos vários recursos e serviços do Azure, quero gerenciar as chaves de um único local no Azure. |√ Os HSMs têm certificação FIPS 140-2 Nível 2.<br/><br/>√ O Cofre de Chaves foi projetado para que a Microsoft não veja nem extraia suas chaves.<br/><br/>√ Registro em log quase em tempo real do uso da chave.<br/><br/>√ O cofre fornece uma única interface, independentemente de quantos cofres você tenha no Azure, quais sejam as regiões com suporte e quais aplicativos as usem. |

Qualquer pessoa com uma assinatura do Azure pode criar e usar cofres de chaves. Embora o Cofre da Chave beneficie desenvolvedores e administradores de segurança, ele pode ser implementado e gerenciado pelo administrador de uma organização que gerencie outros serviços do Azure para a organização. Por exemplo, esse administrador faria logon com uma assinatura do Azure, criaria um cofre para a organização na qual as chaves seriam armazenadas e seria responsável por tarefas operacionais, como:

* Criar ou importar uma chave ou segredo
* Revogar ou excluir uma chave ou segredo
* Autorizar usuários ou aplicativos a acessar o cofre da chave para que eles possam gerenciar ou usar suas chaves e segredos
* Configurar o uso de chaves (por exemplo, assinar ou criptografar)
* Monitorar o uso de chaves

Esse administrador, então, forneceria aos desenvolvedores URIs a serem chamados de seus aplicativos e forneceria ao administrador de segurança as informações de log de uso de chave. 

   ![Visão geral da o Cofre da Chave do Azure][1]

Os desenvolvedores também podem gerenciar as chaves diretamente, por meio de APIs. Para saber mais, confira o [guia do desenvolvedor do Cofre da Chave](key-vault-developers-guide.md).

## <a name="next-steps"></a>Próximas etapas

Para ver um tutorial de introdução para um administrador, consulte [Introdução ao Cofre da Chave do Azure](key-vault-get-started.md).

Para saber mais sobre o log de uso do Cofre da Chave, confira [Log do Cofre da Chave do Azure](key-vault-logging.md).

Para obter mais informações sobre como usar as chaves e os segredos com o Cofre de Chaves do Azure, consulte [Sobre Chaves, Segredos e Certificados](https://msdn.microsoft.com/library/azure/dn903623\(v=azure.1\).aspx).

<!--Image references-->
[1]: ./media/key-vault-whatis/AzureKeyVault_overview.png
O Cofre da Chave do Azure está disponível na maioria das regiões. Para obter mais informações, consulte a [Página de preços do Cofre da Chave](https://azure.microsoft.com/pricing/details/key-vault/).
