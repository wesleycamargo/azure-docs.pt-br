---
title: Comparando recursos para o gerenciamento de identidades externas usando o Azure Active Directory | Microsoft Docs
description: "Compara a colaboração B2B do Active Directory do Azure, o B2C e o Aplicativo Multilocatário para o suporte à autenticação e à autorização para identidades externas"
services: active-directory
documentationcenter: 
author: arvindsuthar
manager: cliffdi
editor: 
tags: 
ms.assetid: 749b2e3a-2b8e-45ba-9f2a-6ca56eb1053b
ms.service: active-directory
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: identity
ms.date: 02/24/2016
ms.author: asuthar
translationtype: Human Translation
ms.sourcegitcommit: 219dcbfdca145bedb570eb9ef747ee00cc0342eb
ms.openlocfilehash: 407bf5b5c608df6e5942d1abf3601bb2ab599782


---
# <a name="comparing-capabilities-for-managing-external-identities-using-azure-active-directory"></a>Comparando recursos para o gerenciamento de identidades externas usando o Active Directory do Azure
Além de gerenciar o acesso de força de trabalho móvel para aplicativos SaaS, o AD do Azure (Active Directory do Azure) pode ajudar sua organização a compartilhar recursos com parceiros de negócios e a fornecer aplicativos para empresas e consumidores.

## <a name="developing-applications-for-businesses"></a>Desenvolvendo aplicativos para empresas
Você fornece um serviço ou um aplicativo como um serviço de folha de pagamento para as empresas? O AD do Azure fornece a plataforma de identidade que permite que você crie aplicativos que se integrem perfeitamente com milhões de organizações que já tenham configurado o AD do Azure como parte da implantação do Office 365 ou de outros serviços corporativos.

**Exemplo:** um distribuidor farmacêutico fornece suprimentos médicos e sistemas de informação para o setor de assistência médica. Eles precisavam de um aplicativo de análise de campo para práticas médicas e queriam que os clientes gerenciassem suas próprias identidades. Essa empresa escolheu o AD do Azure como a plataforma de identidade para seu aplicativo de gerenciamento de prática, fornecendo as identidades do AD do Azure para seus clientes na inscrição, quando necessário. Para obter mais informações, consulte o [Guia do desenvolvedor do Azure Active Directory](active-directory-developers-guide.md).

## <a name="enabling-business-partner-access-to-your-corporate-resources"></a>Habilitando o acesso de parceiros comerciais a seus recursos corporativos
Você tem parceiros comerciais ou outros usuários fora da sua empresa que precisam acessar os recursos de sua empresa, como um site do SharePoint ou seu sistema ERP? O AD do Azure permite que os administradores concedam a usuários externos (que podem ou não existir no AD do Azure) logon único no acesso a aplicativos corporativos. Isso melhora a segurança, pois os usuários perderão acesso quando deixarem a organização parceira, enquanto você controla as políticas de acesso em sua organização. Isso também simplifica a administração, já que você também não precisa gerenciar um diretório de parceiro externo ou por relacionamentos de federação de parceiro.

**Exemplo:** uma empresa de geração de imagens fornece aos varejistas serviços fotográficos e opera a maior rede de varejo de quiosques de impressão. Eles escolheram o Azure AD para permitir que centenas de usuários em seus parceiros de negócios de varejo usem suas próprias credenciais para baixar os materiais de marketing de parceiro mais recentes e solicitem novamente suprimentos de processamento de fotos da extranet do fornecedor da empresa. Para obter mais informações, consulte [Colaboração B2B do Azure AD](active-directory-b2b-what-is-azure-ad-b2b.md).

## <a name="developing-applications-for-consumers"></a>Desenvolvendo aplicativos para consumidores
Você precisa publicar aplicativos online de forma segura e econômica, como uma frente de loja de varejo, para milhões de consumidores? O Azure AD fornece uma plataforma que permite as credenciais sociais e de nome de usuário/senha, inscrição de autoatendimento com marca e redefinição de senha por autoatendimento para os consumidores do seu aplicativo. Isso aumenta a conveniência para seus consumidores, reduzindo a carga sobre os desenvolvedores.

**Exemplo:** a maior franquia \# de esportes no mundo precisou interagir diretamente com 450 milhões de fãs. Para fazer isso, eles criaram um aplicativo móvel usando o AD do Azure para autenticação de usuário e armazenamento de perfis. Os fãs obtêm um registro e credenciais simplificados por meio de contas sociais, como o Facebook, ou podem usar nomes de usuário/senhas tradicionais para usufruírem de uma experiência simplificada em smartphones iOS, Android e Windows. A criação na plataforma do AD do Azure estabelecida reduziu significativamente o código personalizado, oferecendo à franquia uma identidade visual personalizada e diminuindo preocupações sobre segurança, violações de dados e escalabilidade. Para obter mais informações, consulte [Azure AD B2C](https://azure.microsoft.com/documentation/services/active-directory-b2c/).

## <a name="comparison-of-azure-ad-capabilities"></a>Comparação de recursos do AD do Azure
Cada um dos cenários já discutidos neste artigo é tratado por recursos no AD do Azure. Esta tabela deve ajudar a esclarecer quais recursos são mais relevantes para você:

| **Considere este produto...** | [Aplicativo SaaS multilocatário do Azure AD](active-directory-developers-guide.md) | [Colaboração B2B do AD do Azure](active-directory-b2b-what-is-azure-ad-b2b.md) | [B2C do AD do Azure](https://azure.microsoft.com/documentation/services/active-directory-b2c/) |
| --- | --- | --- | --- |
| **Se eu precisar fornecer...** |um serviço para empresas |acesso de parceiro a meus aplicativos |um serviço para consumidores |
| **E sou semelhante a...** |Distribuidor Pharma |Empresa de geração de imagens |Franquia de esportes |
| **Implantando um aplicativo para...** |Gerenciamento de prática |Extranet do fornecedor |Fãs de futebol |
| **Direcionamento...** |Consultórios médicos |Parceiros de negócios aprovados |Qualquer pessoa com email |
| **Acessível quando...** |Consentimentos do administrador do consumidor |Meu administrador convida |O consumidor se inscreve |




<!--HONumber=Nov16_HO3-->


