---
title: "Azure Active Directory B2C: Notas do desenvolvedor sobre o uso de políticas personalizadas | Microsoft Docs"
description: "Observações para os desenvolvedores que estejam configurando e mantendo o B2C com Políticas Personalizadas"
services: active-directory-b2c
documentationcenter: 
author: rojasja
manager: krassk
editor: rojasja
ms.assetid: 
ms.service: active-directory-b2c
ms.workload: identity
ms.tgt_pltfrm: na
ms.topic: article
ms.devlang: na
ms.date: 05/05/2017
ms.author: joroja
ms.translationtype: Human Translation
ms.sourcegitcommit: 2db2ba16c06f49fd851581a1088df21f5a87a911
ms.openlocfilehash: 72447a234c5889f36b10b828d28775fc5995c4d4
ms.contentlocale: pt-br
ms.lasthandoff: 05/09/2017


---
# <a name="release-notes-for-azure-active-directory-b2c-custom-policy-public-preview"></a>Notas de versão para a versão prévia da política personalizada do Azure Active Directory B2C
O conjunto de recursos de **política personalizada** agora está disponível para avaliação em versão prévia para todos os clientes do Azure Active Directory B2C(Azure AD B2C).  Esse conjunto de recursos se destina a desenvolvedores avançados de identidade para criar as soluções de identidade mais complexas.  

Atualmente, esse conjunto de recursos requer que o desenvolvedor configure a Identity Experience Framework (IEF) diretamente por meio da edição do arquivo XML.  Esse método de configuração é poderoso e complexo.  Os desenvolvedores de identidade avançados que usam a IEF devem planejar investir um tempo no aprendizado concluindo passo a passos e lendo os documentos de referência. 

## <a name="features-included-in-this-public-preview"></a>Recursos incluídos nesta versão prévia
Os novos recursos introduzidos na revisão pública permitem que os desenvolvedores executem as seguintes tarefas:
1. Criar e carregar percursos do usuário de autenticação personalizada usando políticas personalizadas
* Descrever percursos do usuário passo a passo como trocas entre provedores de declarações
* Definir a ramificação condicional em percursos do usuário
2. Integrar os serviços habilitados para API REST aos seus percursos do usuário de autenticação personalizada
3. Adicionar a federação a provedores de identidade em conformidade com o padrão OpenIDConnect
4. Adicionar a federação com provedores de identidade que seguem o protocolo SAML 2.0



## <a name="terms-of-the-public-preview"></a>Termos da versão prévia
1. Os novos recursos devem ser usados somente para fins de avaliação
2. Os novos recursos não se destinam ao uso em um ambiente de produção
3. Os contratos de nível de serviço (SLAs) não se aplicam aos novos recursos4. As solicitações de suporte podem ser arquivadas por meio dos canais de suporte normais
5. Não há nenhuma data prometida para disponibilidade geral
6. A nosso critério, e por qualquer motivo, a Microsoft pode sinalizar e rejeitar ou restringir os cenários e percursos do usuário que excedam o escopo da lista de produtos Azure AD B2C para servir como uma plataforma de gerenciamento de identidade e acesso de cliente (CIAM)

## <a name="responsibilities-for-custom-policy-feature-set-developers"></a>Responsabilidades dos desenvolvedores de conjunto de recursos de política personalizada
A configuração de política manual concede acesso de nível inferior para a plataforma subjacente do Azure AD B2C e resulta na criação de uma estrutura de confiança exclusiva e totalmente personalizável.  As permutas quase infinitas de provedores de identidade personalizada, relações de confiança, integrações com serviços externos e fluxos de trabalho passo a passo criam demandas ainda maiores para que os desenvolvedores avançados as consumam.
Para se beneficiar totalmente da versão prévia, sugerimos que os desenvolvedores que utilizarem o conjunto de recursos de política personalizada façam o seguinte:
* Se familiarize com a linguagem de configuração do IEE (Mecanismo de Experiência de Identidade) e o gerenciamento de chave/segredos
* Apropriar-se de cenários e de integrações personalizadas
* Executar testes de cenário metódicos
* Siga as práticas recomendadas com um mínimo de um desenvolvimento/teste e o ambiente de produção de uma de preparo e de desenvolvimento de software
* Mantenha-se informado sobre novos desenvolvimentos com provedores de identidade e serviços com quem você se integra, por exemplo, controle de alterações em segredos, alterações agendadas/não agendadas no serviço e assim por diante — configure o monitoramento ativo e monitore a capacidade de resposta de seus ambientes de produção
* Mantenha atualizados os emails do contato e permaneça responsivo aos emails da equipe de site ativo da Microsoft
* Executar ação em tempo hábil quando for aconselhado a fazer isso pela equipe de site ativo da Microsoft 


>[!NOTE]
>Esses recursos, eventualmente, podem ser incluídos em políticas internas do Azure AD, tornando-os mais acessíveis para todos os desenvolvedores.

## <a name="next-steps"></a>Próximas etapas
[Introdução às políticas personalizadas](active-directory-b2c-get-started-custom.md).

