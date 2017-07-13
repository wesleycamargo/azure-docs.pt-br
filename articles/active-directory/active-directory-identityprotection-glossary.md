---
title: "Glossário do Azure Active Directory Identity Protection | Microsoft Docs"
description: "Glossário do Azure Active Directory Identity Protection"
services: active-directory
keywords: "azure active directory identity protection, cloud app discovery, gerenciamento de aplicativos, segurança, risco, nível de risco, vulnerabilidade, política de segurança, glossário"
documentationcenter: 
author: MarkusVi
manager: femila
ms.assetid: 833119a5-33d6-4482-adda-fa35218c72c3
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/12/2017
ms.author: markvi
ms.translationtype: Human Translation
ms.sourcegitcommit: dcda8b30adde930ab373a087d6955b900365c4cc
ms.openlocfilehash: f31188ed3216034ef8ec056023472844ec4debd6
ms.contentlocale: pt-br
ms.lasthandoff: 12/29/2016

---
<a id="azure-active-directory-identity-protection-glossary" class="xliff"></a>

# Glossário do Azure Active Directory Identity Protection
<a id="at-risk-user" class="xliff"></a>

### Em risco (Usuário)
Um usuário com um ou mais eventos de risco ativos. 

<a id="atypical-sign-in-location" class="xliff"></a>

### Local de entrada atípico
Uma entrada de um local geográfico incomum para o usuário específico, usuários semelhantes ou o locatário.

<a id="azure-ad-identity-protection" class="xliff"></a>

### Azure AD Identity Protection
Um módulo de segurança do Azure Active Directory que fornece uma exibição consolidada dos eventos de risco e das possíveis vulnerabilidades que afetam as identidades de uma organização.

<a id="conditional-access" class="xliff"></a>

### Acesso condicional
Uma política para proteger o acesso aos recursos. Regras de acesso condicional são armazenadas no Azure Active Directory e são avaliadas pelo Azure AD antes de conceder acesso ao recurso.  As regras de exemplo incluem restrição baseada na localização do usuário, integridade do dispositivo ou método de autenticação do usuário.

<a id="credentials" class="xliff"></a>

### Credenciais
Informações que incluem a identificação e prova de identificação usadas para obter acesso ao local e aos recursos da rede. Exemplos de credenciais são nomes de usuário e senhas, cartões inteligentes e certificados.

<a id="event" class="xliff"></a>

### Evento
Um registro de uma atividade no Azure Active Directory.

<a id="false-positive-risk-event" class="xliff"></a>

### Falsos positivos (evento de risco)
Um status de evento de risco definido manualmente por um usuário do Identity Protection que indica que o evento foi investigado e foi marcado incorretamente como um evento de risco.

<a id="identity" class="xliff"></a>

### Identidade
Uma pessoa ou entidade que deve ser verificada por meio de autenticação com base em critérios como senha ou certificado.

<a id="identity-risk-event" class="xliff"></a>

### Evento de risco de identidade
Um evento AAD que foi marcado como anômalo pelo Identity Protection e pode indicar que uma identidade foi comprometida.

<a id="ignored-risk-event" class="xliff"></a>

### Ignorado (evento de risco)
Um status de evento de risco definido manualmente por um usuário do Identity Protection que indica que o evento foi fechado sem realizar uma ação de correção.

<a id="impossible-travel-from-atypical-locations" class="xliff"></a>

### Viagem impossível de locais atípicos
Um evento de risco disparado quando duas entradas para o mesmo usuário são detectadas, sendo pelo menos uma delas de um local de entrada atípico e em que o tempo entre as entradas é menor do que o tempo mínimo necessário para viajar fisicamente entre esses locais.  

<a id="investigation" class="xliff"></a>

### Investigação
O processo de revisão de atividades, logs e outras informações relevantes relacionadas a um evento de risco para decidir se as etapas de correção e mitigação são necessárias, como a identidade foi comprometida e entender como ela foi usada.

<a id="leaked-credentials" class="xliff"></a>

### Credenciais vazadas
Um evento de risco disparado quando as credenciais do usuário atual (nome de usuário e senha) são encontradas como postadas publicamente por nossos pesquisadores na Dark Web.

<a id="mitigation" class="xliff"></a>

### Redução
Uma ação que visa limitar ou eliminar a capacidade de um invasor explorar uma identidade ou um dispositivo comprometidos sem restaurá-los para um estado seguro. Uma mitigação não resolve eventos de risco anteriores associados à identidade ou ao dispositivo.

<a id="multi-factor-authentication" class="xliff"></a>

### Autenticação multifator
Um método de autenticação que exige dois ou mais métodos de autenticação, que podem incluir algo que o usuário tem, como um certificado; algo que o usuário conhece, como nomes de usuário, senhas ou frases secretas; atributos físicos, como uma impressão digital; e atributos pessoais, como uma assinatura pessoal.

<a id="offline-detection" class="xliff"></a>

### Detecção offline
A detecção de anomalias e a avaliação do risco de um evento, tal como uma tentativa de entrada após o ocorrido, para um evento que já aconteceu.

<a id="policy-condition" class="xliff"></a>

### Condição da política
Uma parte de uma política de segurança que define as entidades (grupos, usuários, aplicativos, plataformas de dispositivos, Estados de dispositivo, intervalos de IP e tipos de cliente) incluídas na política ou excluídas dela.

<a id="policy-rule" class="xliff"></a>

### Regra de política
A parte de uma política de segurança que descreve as circunstâncias que vai disparar a política e as ações executadas quando ela é acionada.

<a id="prevention" class="xliff"></a>

### Prevenção
Uma ação para evitar danos à organização por abuso de uma identidade ou dispositivo que sofreu comprometimento conhecido ou suspeito. Uma ação de prevenção não protege o dispositivo ou a identidade, e não resolve eventos de risco anteriores.

<a id="privileged-user" class="xliff"></a>

### Privilegiado (usuário)
Um usuário que, no momento de um evento de risco, tinha permissões de administrador permanentes ou temporárias a um ou mais recursos no Azure Active Directory, como um Administrador Global, Administrador de Cobrança, Administrador de Serviços, Administrador de Usuários e Administrador de Senha. 

<a id="real-time" class="xliff"></a>

### Tempo real
Consulte Detecção em tempo real.

<a id="real-time-detection" class="xliff"></a>

### Detecção em tempo real.
A detecção de anomalias e a avaliação do risco de um evento, tal como uma tentativa de entrada antes de permitir que o evento prossiga.

<a id="remediated-risk-event" class="xliff"></a>

### Corrigido (evento de risco)
Um status de evento de risco definido automaticamente pelo Identity Protection, que indica que o evento foi corrigido usando a ação de correção padrão para esse tipo de evento de risco. Por exemplo, quando a senha do usuário é redefinida, muitos eventos de risco que indicam que a senha anterior foi comprometida são corrigidos automaticamente.

<a id="remediation" class="xliff"></a>

### Correção
Uma ação que visa proteger uma identidade ou um dispositivo que sofreu comprometimento conhecido ou suspeito anteriormente. Uma correção restaura a identidade ou dispositivo para um estado seguro e resolve eventos de risco anteriores associados à identidade ou ao dispositivo.

<a id="resolved-risk-event" class="xliff"></a>

### Resolvido (evento de risco)
Um status de evento de risco definido manualmente por um usuário do Identity Protection que indica que o usuário executou uma ação de correção apropriada fora do Identity Protection e que o evento deve ser considerado fechado.

<a id="risk-event-status" class="xliff"></a>

### Status de evento de risco
Uma propriedade de um evento de risco que indica se o evento está ativo e, se fechado, o motivo para estar fechado..

<a id="risk-event-type" class="xliff"></a>

### Tipo de evento de risco
Uma categoria para o evento de risco que indica o tipo de anomalia que fez com que o evento fosse considerado arriscado.

<a id="risk-level-risk-event" class="xliff"></a>

### Nível de risco (evento de risco)
Uma indicação (Alta, Média ou Baixa) da severidade do evento de risco para ajudar os usuários do Identity Protection a priorizar as ações tomadas para reduzir o risco para a organização. 

<a id="risk-level-sign-in" class="xliff"></a>

### Nível de risco (entrada)
Uma indicação (Alta, Média ou Baixa) da probabilidade de que outra pessoa esteja tentando usar a identidade do usuário para uma entrada específica.

<a id="risk-level-user-compromise" class="xliff"></a>

### Nível de risco (comprometimento do usuário)
Uma indicação (Alta, Média ou Baixa) da probabilidade de uma identidade ter sido comprometida.

<a id="risk-level-vulnerability" class="xliff"></a>

### Nível de risco (vulnerabilidade)
Uma indicação (Alta, Média ou Baixa) da severidade da vulnerabilidade para ajudar os usuários do Identity Protection a priorizar as ações tomadas para reduzir o risco para a organização.

<a id="secure-identity" class="xliff"></a>

### Proteger (identidade)
Realizar uma ação de correção como uma alteração de senha ou reimplantação da imagem no computador para restaurar uma identidade potencialmente comprometida para um estado não comprometido.

<a id="security-policy" class="xliff"></a>

### Política de segurança
Uma coleção de regras e condição da política. Uma política pode ser aplicada a entidades como usuários, grupos, aplicativos, dispositivos, plataformas de dispositivos, estados de dispositivo, intervalos de IP e tipos de cliente do Auth2.0. Quando uma política está habilitada, ela é avaliada sempre que um token para um recurso é emitido para uma entidade incluída na política.

<a id="sign-in-v" class="xliff"></a>

### Entrar (v)
Autenticar-se em uma identidade no Azure Active Directory.

<a id="sign-in-n" class="xliff"></a>

### Entrada (n)
O processo ou a ação de autenticar uma identidade no Azure Active Directory e o evento que captura essa operação.

<a id="sign-in-from-anonymous-ip-address" class="xliff"></a>

### Entrada de um endereço IP anônimo
Um evento de risco disparado após uma entrada bem-sucedida de um endereço IP que foi identificado como um endereço IP de proxy anônimo.

<a id="sign-in-from-infected-device" class="xliff"></a>

### Entradas de um dispositivo infectado
Um evento de risco disparado em que uma entrada origina um endereço IP conhecido por ser usado por um ou mais dispositivos comprometidos, que estão tentando ativamente comunicar-se com um servidor de bot.

<a id="sign-in-from-ip-address-with-suspicious-activity" class="xliff"></a>

### Entrada de um endereço IP com atividade suspeita
Um evento de risco acionado depois de uma entrada bem-sucedida de um endereço IP com um grande número de tentativas de logon com falha em várias contas de usuário em um curto período de tempo.

<a id="sign-in-from-unfamiliar-location" class="xliff"></a>

### Entrada de um local desconhecido
Um evento de risco disparado quando um usuário entra com êxito de um novo local (IP, Latitude/Longitude e ASN).

<a id="sign-in-risk" class="xliff"></a>

### Risco de entrada
Consulte Nível de risco (entrada)

<a id="sign-in-risk-policy" class="xliff"></a>

### Política de risco de entrada
Uma política de acesso condicional que avalia o risco de uma entrada específica e aplica mitigações com base em regras e condições predefinidas.

<a id="user-compromise-risk" class="xliff"></a>

### Risco de comprometimento do usuário
Consulte Nível de risco (comprometimento do usuário)

<a id="user-risk" class="xliff"></a>

### Risco do usuário
Consulte Nível de risco (comprometimento do usuário).

<a id="user-risk-policy" class="xliff"></a>

### Política de risco do usuário
Uma política de acesso condicional que considera a entrada e aplica mitigações com base em regras e condições predefinidas.

<a id="users-flagged-for-risk" class="xliff"></a>

### Usuários sinalizados por risco
Usuários que têm eventos de risco ativos ou corrigidos

<a id="vulnerability" class="xliff"></a>

### Vulnerabilidade
Uma configuração ou condição no Azure Active Directory que torna o diretório suscetível a vulnerabilidades e ameaças.

<a id="see-also" class="xliff"></a>

## Confira também
* [Azure Active Directory Identity Protection](active-directory-identityprotection.md)


