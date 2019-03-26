---
title: Políticas personalizadas do Azure Active Directory B2C | Microsoft Docs
description: Saiba mais sobre as políticas personalizadas do Azure Active Directory B2C.
services: active-directory-b2c
author: davidmu1
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 03/20/2019
ms.author: davidmu
ms.subservice: B2C
ms.openlocfilehash: 7921454cc9269278db58fcc50bc63ca49b41b1e0
ms.sourcegitcommit: 72cc94d92928c0354d9671172979759922865615
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/25/2019
ms.locfileid: "58417926"
---
# <a name="custom-policies-in-azure-active-directory-b2c"></a>Políticas personalizadas no Azure Active Directory B2C

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

Políticas personalizadas são arquivos de configuração que definem o comportamento do locatário do Azure AD (Azure Active Directory) B2C. Fluxos de usuário são predefinidos no portal do Azure AD B2C para as tarefas de identidade mais comuns. As políticas personalizadas podem ser totalmente editadas por um desenvolvedor de identidades para concluir várias tarefas diferentes.

## <a name="comparing-user-flows-and-custom-policies"></a>Comparando fluxos dos usuários e as políticas personalizadas

| | Fluxos de usuário | Políticas personalizadas |
|-|-------------------|-----------------|
| Usuários de destino | Todos os desenvolvedores de aplicativos, com ou sem experiência em identidade. | Profissionais de identidade, integradores de sistemas, consultores e equipes internas de identidade. Estão familiarizados com fluxos do OpenIDConnect e têm conhecimento sobre a autenticação baseada em declarações e os provedores de identidade. |
| Método de configuração | Portal do Azure com uma UI (interface do usuário) amigável ao usuário. | Editando arquivos XML diretamente e carregando-os no portal do Azure. |
| Personalização da interface do usuário | Personalização da interface do usuário completa incluindo HTML, CSS e JavaScript.<br><br>Suporte a várias linguagens com cadeias de caracteres Personalizadas. | Idêntico |
| Personalização de atributos | Atributos padrão e personalizados. | Idêntico |
| Gerenciamento de tokens e sessões | Token personalizado e opções de várias sessões. | Idêntico |
| Provedores de Identidade | Provedor predefinido de local ou social e a maioria dos provedores de identidade do OIDC, como a federação com locatários do Active Directory do Azure. | SAML, OAUTH e OIDC baseado em padrões.  Autenticação também é possível usando a integração com as APIs REST. |
| Tarefas de identidade | Inscrever-se-se ou entrar com contas locais ou muitas contas sociais.<br><br>Redefinição de senha de autoatendimento.<br><br>Edição de perfil.<br><br>Autenticação Multifator.<br><br>Personalizar tokens e sessões.<br><br>Acessar fluxos de token. | Concluir as mesmas tarefas como fluxos de usuários usando os provedores de identidade personalizados ou usar escopos personalizados.<br><br>Provisione uma conta de usuário em outro sistema no momento do registro.<br><br>Enviar um email de boas-vindas usando seu próprio provedor de serviços de email.<br><br>Use um repositório de usuários fora do Azure AD B2C.<br><br>Valide as informações fornecidas pelo usuário com um sistema confiável usando uma API. |

## <a name="policy-files"></a>Arquivos de política

Esses três tipos de arquivos de política são usados:

- **Arquivo base** - contém a maioria das definições. É recomendável que você faça um número mínimo de alterações nesse arquivo para ajudar na solução de problemas e na manutenção de longo prazo das políticas.
- **Arquivo de extensões** - retém as alterações de configuração exclusivas do locatário.
- **Arquivo RP (Terceira Parte Confiável)** - O arquivo único focado na tarefa que é chamado diretamente pelo aplicativo ou serviço (também conhecido como Terceira Parte Confiável). Cada tarefa exclusiva requer o próprio RP e, dependendo dos requisitos de marca, o número pode ser "total de aplicativos x número total de casos de uso."

Fluxos de usuário no Azure AD B2C seguem o padrão de três arquivos descrito acima, mas o desenvolvedor somente vê o arquivo RP, enquanto o portal do Azure faz alterações em segundo plano do arquivo de extensões.

## <a name="custom-policy-core-concepts"></a>Conceitos básicos de política personalizada

O serviço de CIAM (gerenciamento de acesso e identidade do cliente) no Azure inclui:

- Um diretório do usuário que pode ser acessado usando o Microsoft Graph e que contém dados do usuário para contas locais e federadas.
- Acesso ao **Estrutura de Experiência de Identidade** que orquestra a confiança entre usuários e entidades e passa as declarações entre eles para concluir uma tarefa de gerenciamento de acesso ou identidade. 
- Um STS (serviço de token de segurança) que emite tokens de ID, atualiza tokens e acessa tokens (e asserções de SAML equivalentes) e os valida para proteger recursos.

O Azure AD B2C interage com provedores de identidade, usuários, outros sistemas e com o diretório do usuário local em sequência para obter uma tarefa de identidade. Por exemplo, entre em um usuário, registre um novo usuário ou redefina uma senha. A Estrutura de Experiência de Identidade e uma política (também chamada de percurso do usuário ou uma política de estrutura confiável) estabelecem a confiança de vários participantes e define explicitamente os atores, as ações, os protocolos e a sequência de etapas a serem concluídas.

A Estrutura de Experiência de Identidade é uma plataforma do Azure totalmente configurável, orientada por política e baseada em nuvem que orquestra a confiança entre entidades em formatos de protocolo padrão, como OpenIDConnect, OAuth, SAML, WSFed e alguns não padrão, por exemplo, trocas de declarações de sistema a sistema baseadas em API REST. A estrutura cria experiências fáceis de usar e rotuladas em branco que dão suporte a HTML e CSS.

Uma política personalizada é representada como um ou vários arquivos formatados em XML que se referenciam entre si em uma cadeia hierárquica. Os elementos XML definem o esquema de declarações, as transformações de declarações, as definições de conteúdo, os provedores de declarações, os perfis técnicos e as etapas de orquestração do percurso do usuário, entre outros elementos. Uma política personalizada é acessível como um ou vários arquivos XML que são executados pela Estrutura de Experiência de Identidade quando invocados por uma terceira parte confiável. Os desenvolvedores que configuram as políticas personalizadas devem definir as relações de confiança em detalhes para incluir pontos de extremidade de metadados, definições exatas de troca de declarações e configurar segredos, chaves e certificados, conforme necessário, para cada provedor de identidade.

### <a name="inheritance-model"></a>Modelo de herança

Quando um aplicativo chama o arquivo de política RP, a Estrutura de Experiência de Identidade no Azure AD B2C adiciona todos os elementos do arquivo base, do arquivo de extensões e, em seguida, do arquivo de política de RP para montar a política atual em vigor.  Elementos do mesmo tipo e nome no arquivo RP substituirão aqueles nas extensões e as extensões substituem a base.

## <a name="next-steps"></a>Próximas etapas

> [!div class="nextstepaction"]
> [Introdução às políticas personalizadas](active-directory-b2c-get-started-custom.md)
