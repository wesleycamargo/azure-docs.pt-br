---
title: Usando restrição de idade no Azure AD B2C | Microsoft Docs
description: Saiba mais sobre como identificar menores de idade usando seu aplicativo.
services: active-directory-b2c
documentationcenter: ''
author: davidmu1
manager: mtillman
editor: ''
ms.service: active-directory-b2c
ms.workload: identity
ms.topic: article
ms.date: 04/29/2018
ms.author: davidmu
ms.openlocfilehash: 3d6804f7e546547d734f966656362111b31078a4
ms.sourcegitcommit: c47ef7899572bf6441627f76eb4c4ac15e487aec
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/04/2018
ms.locfileid: "33206193"
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
Você pode optar por fazer com que o Azure AD B2C bloqueio menores que não tiverem consentimento dos pais ou permitir seu acesso e deixar que o aplicativo decida o que fazer com eles.  

###<a name="allowing-minors-without-parental-consent"></a>Permitindo menores sem consentimento dos pais
Para fluxos de usuário que têm inscrição, entrada ou ambas, você pode optar por permitir a entrada de menores sem consentimento dos pais em seu aplicativo.  Para menores sem o consentimento dos pais, eles têm permissão para entrar ou se inscrever normalmente e é emitido um token de ID com a declaração `legalAgeGroupClassification`.  Usando essa declaração, você pode escolher a experiência desses usuários, como uma experiência com coleta do consentimento dos pais (e atualização do campo `consentProvidedForMinor`).

###<a name="blocking-minors-without-parental-consent"></a>Bloqueando menores sem consentimento dos pais
Para fluxos de usuário que têm inscrição, entrada ou ambas, você pode optar por bloquear a entrada de menores sem consentimento dos pais em seu aplicativo.  Há duas opções para lidar com usuários bloqueados no Azure AD B2C:
* Enviar um JSON de volta ao aplicativo – esta opção enviará uma resposta de volta para o aplicativo informando que um foi bloqueado.
* Mostrar uma página de erro – o usuário verá uma página informando que ele não pode acessar o aplicativo

##<a name="known-issues"></a>Problemas conhecidos
###<a name="customization-unavailable-for-new-pages"></a>Personalização indisponível para novas páginas
Há duas novas páginas que podem ficar disponíveis em seu fluxo de usuário quando você habilita a restrição de idade.  Essas páginas para coletar o país e a data de nascimento ao entrar e a página de erro não podem ser usadas com personalização de idioma ou layout da página.  Essa opção estará disponível em uma atualização futura.

###<a name="format-for-the-response-when-a-minor-is-blocked"></a>Formato da resposta quando um menor é bloqueado.
Atualmente, a resposta não está formada corretamente. Esse bug será corrigido em uma atualização futura.

###<a name="deleting-specific-attributes-that-were-added-during-setup-can-make-your-directory-unable-to-use-age-gating"></a>Excluir atributos específicos que foram adicionados durante a configuração pode impossibilitar seu diretório não de usar a restrição de idade.
Na configuração de restrição de idade, você configurou o diretório por meio de uma opção em `Properties`.  Se você excluir `legalCountry` ou `dateOfBirth`, o locatário não poderá mais usar a restrição de idade e essas propriedades não poderão ser recriadas.

###<a name="list-of-countries-is-incomplete"></a>A lista de países está incompleta
Atualmente, a lista de países para legalCountry está incompleta, adicionaremos o restante dos países em uma atualização futura.