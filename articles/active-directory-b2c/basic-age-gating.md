---
title: Usando a restrição de idade no Azure Active Directory B2C | Microsoft Docs
description: Saiba mais sobre como identificar menores de idade usando seu aplicativo.
services: active-directory-b2c
author: davidmu1
manager: mtillman
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 04/29/2018
ms.author: davidmu
ms.component: B2C
ms.openlocfilehash: 9d24e37642a41e4d60b33f42a60d7e56cb4b35b5
ms.sourcegitcommit: 86cb3855e1368e5a74f21fdd71684c78a1f907ac
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/03/2018
ms.locfileid: "37446717"
---
#<a name="using-age-gating-in-azure-ad-b2c"></a>Usando restrição de idade no Azure AD B2C

>[!IMPORTANT]
>Este recurso está em versão prévia privada.  Confira nosso [blog de serviço](https://blogs.msdn.microsoft.com/azureadb2c/) para obter detalhes quando ele ficar disponível ou entre em contato com AADB2CFeedback@microsoft.com.  NÃO use em diretórios de produção, pois o uso desses novos recursos pode causar perda de dados e mudanças de comportamento inesperadas até que eles estejam com disponibilidade geral.  
>

##<a name="age-gating"></a>Restrição de idade
A restrição de idade permite que você use o Azure AD B2C para identificar menores em seu aplicativo.  Você pode optar por impedir o usuário de se conectar ao aplicativo ou permitir que voltem ao aplicativo com declarações adicionais que identificam a faixa etária do usuário e o status de consentimento dos pais.  

>[!NOTE]
>O consentimento dos pais é rastreado em um atributo do usuário chamado `consentProvidedForMinor`.  É possível atualizar esta propriedade por meio da API do Graph e ela usará esse campo ao atualizar `legalAgeGroupClassification`.
>

##<a name="setting-up-your-directory-for-age-gating"></a>Configurando seu diretório para restrição de idade
Para usar a restrição de idade em um fluxo do usuário, você precisa configurar seu diretório para ter propriedades adicionais. Essa operação pode ser feita usando `Properties` no menu (que estará disponível somente se você fizer parte da versão prévia privada).  
1. Na extensão do Azure AD B2C, clique nas **Propriedades** de seu locatário no menu à esquerda.
2. Na seção **Restrição de idade**, clique no botão **Configurar**.
3. Aguarde a conclusão da operação e seu diretório estará configurado para a restrição de idade.

##<a name="enabling-age-gating-in-your-user-flow"></a>Habilitando a restrição de idade em seu fluxo de usuário
Depois que o diretório estiver configurado para usar a restrição de idade, você poderá usar esse recurso nos fluxos de usuário da versão prévia.  O recurso requer alterações que o tornam incompatível com tipos de fluxos de usuário existentes.  Habilite a restrição de idade seguindo estas etapas:
1. Crie um fluxo de usuário na versão prévia.
2. Após criá-lo, acesse o menu **Propriedades**.
3. Na seção **Restrição de idade**, pressione o botão de alternância para habilitar o recurso.
4. Você pode, então, escolher como deseja gerenciar os usuários identificados como menores.

##<a name="what-does-enabling-age-gating-do"></a>O que acontece quando a restrição de idade é habilitada?
Depois que a restrição de idade é habilitada em seu fluxo de usuário, a experiência do usuário muda.  Na inscrição, passarão a ser solicitados a data de nascimento e o país de residência dos usuários, bem como os atributos do usuário configurados no fluxo de usuário.  Ao se conectarem, os usuários que não tiverem fornecido a data de nascimento e o país de residência anteriormente precisarão fornecer essas informações na próxima vez em que entrarem.  Usando esses dois valores, o Azure AD B2C identificará se o usuário é menor de idade e atualizará o campo `ageGroup`; o valor pode ser `null`, `Undefined`, `Minor`, `Adult` e `NotAdult`.  Em seguida, os campos `ageGroup` e `consentProvidedForMinor` são usados para calcular `legalAgeGroupClassification`. 

##<a name="age-gating-options"></a>Opções de restrição de idade
Você pode optar por fazer com que o Active Directory AD B2C bloqueie menores que não tiverem consentimento dos pais ou permitir seu acesso e deixar que o aplicativo decida o que fazer com eles.  

###<a name="allowing-minors-without-parental-consent"></a>Permitindo menores sem consentimento dos pais
Para fluxos de usuário que permitem inscrição, entrada ou ambas, você pode optar por permitir a entrada de menores sem consentimento dos pais em seu aplicativo.  Para menores sem o consentimento dos pais, eles têm permissão para entrar ou se inscrever normalmente, e o Active Directory B2C emite um token de ID com a declaração `legalAgeGroupClassification`.  Usando essa declaração, você pode escolher a experiência desses usuários, como uma experiência com coleta do consentimento dos pais (e atualização do campo `consentProvidedForMinor`).

###<a name="blocking-minors-without-parental-consent"></a>Bloqueando menores sem consentimento dos pais
Para fluxos de usuário que permitem inscrição, entrada ou ambas, você pode optar por bloquear a entrada de menores sem consentimento dos pais no aplicativo.  Há duas opções para lidar com usuários bloqueados no Azure AD B2C:
* Enviar um JSON de volta ao aplicativo – esta opção enviará uma resposta de volta para o aplicativo informando que um foi bloqueado.
* Mostrar uma página de erro – o usuário verá uma página informando que ele não pode acessar o aplicativo

##<a name="known-issues"></a>Problemas conhecidos
###<a name="format-for-the-response-when-a-minor-is-blocked"></a>Formato da resposta quando um menor é bloqueado.
Atualmente, a resposta não está formada corretamente. Esse bug será corrigido em uma atualização futura.

###<a name="deleting-specific-attributes-that-were-added-during-setup-can-make-your-directory-unable-to-use-age-gating"></a>Excluir atributos específicos que foram adicionados durante a configuração pode impossibilitar seu diretório não de usar a restrição de idade.
Na configuração de restrição de idade, você configurou o diretório por meio de uma opção em `Properties`.  Se você excluir `legalCountry` ou `dateOfBirth` usando o Graph, o diretório não poderá mais usar a restrição de idade, e essas propriedades não poderão ser recriadas.

###<a name="list-of-countries-is-incomplete"></a>A lista de países está incompleta
Atualmente, a lista de países para legalCountry está incompleta, adicionaremos o restante dos países em uma atualização futura.