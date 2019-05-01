---
title: Referência a eventos de risco do Azure Active Directory Identity Protection | Microsoft Docs
description: Referência a eventos de risco do Azure Active Directory Identity Protection.
services: active-directory
documentationcenter: ''
author: MicrosoftGuyJFlo
manager: mtillman
ms.assetid: 14f7fc83-f4bb-41bf-b6f1-a9bb97717c34
ms.service: active-directory
ms.subservice: identity-protection
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/25/2018
ms.author: joflore
ms.reviewer: sahandle
ms.collection: M365-identity-device-management
ms.openlocfilehash: e988d03b60469940d8750cc07188a61fc7ab8b3a
ms.sourcegitcommit: 44a85a2ed288f484cc3cdf71d9b51bc0be64cc33
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/28/2019
ms.locfileid: "64709603"
---
# <a name="azure-active-directory-identity-protection-risk-events-reference"></a>Referência a eventos de risco do Azure Active Directory Identity Protection

A grande maioria das violações de segurança ocorre quando os invasores conseguem acessar a um ambiente roubando a identidade de um usuário. Descobrir identidades comprometidas não é uma tarefa fácil. O Azure Active Directory usa algoritmos de aprendizado de máquina e heurística adaptáveis para detectar ações suspeitas relacionadas às contas do usuário. Cada ação suspeita detectada é armazenada em um registro chamado evento de risco.


## <a name="anonymous-ip-address"></a>Endereço IP anônimo

**Tipo de Detecção:** Tempo real  
**Nome antigo:** Entradas de endereço IP anônimo


Esse tipo de evento de risco indica entradas de um endereço IP anônimo (por exemplo, navegador Tor e VPNs para anonimato).
Normalmente, esses endereços IP são usados por atores que desejam ocultar sua telemetria de logon (endereço IP, localização, dispositivo etc.) para, potencialmente, más intenções.


## <a name="atypical-travel"></a>Viagem atípica

**Tipo de Detecção:** Off-line  
**Nome antigo:** Viagem impossível a locais atípicos


Esse tipo de evento de risco identifica duas entradas provenientes de locais geograficamente distantes, onde pelo menos um deles também pode ser atípico para o usuário, considerando seu comportamento anterior. Entre muitos outros fatores, esse algoritmo de aprendizado de máquina leva em consideração o tempo entre as duas entradas e o tempo que seria necessário para o usuário ir do primeiro até o segundo local, indicando que um usuário diferente está usando as mesmas credenciais.

Esse algoritmo ignora “falsos positivos” óbvios que contribuem para condições impossíveis de viagem, como VPNs e locais regularmente usados por outros usuários na organização. O sistema tem um período inicial de aprendizado dos primeiros 14 dias ou 10 logons, durante o qual ele aprende o comportamento de entrada de um novo usuário.


## <a name="leaked-credentials"></a>Credenciais vazadas

**Tipo de Detecção:** Off-line  
**Nome antigo:** Usuários com credenciais vazadas


Este tipo de evento de risco indica que as credenciais válidas do usuário foram vazadas.
Quando cibercriminosos comprometem senhas válidas de usuários legítimos, os criminosos muitas vezes compartilham essas credenciais. Geralmente, isso é feito postando-as publicamente na dark Web ou em paste sites ou então permutando-as ou vendendo-as no mercado negro. O serviço de credenciais vazadas da Microsoft adquire pares de nome de usuário / senha pelo monitoramento de sites públicos e da dark Web, e também trabalhando com:

- Pesquisadores

- Representantes legais

- Equipes de segurança da Microsoft

- Outras fontes confiáveis

Quando o serviço obtém as credenciais de usuário da dark Web, paste sites ou das fontes acima, elas são comparadas com credenciais válidas atuais de usuários do Azure AD para encontrar correspondências válidas.


## <a name="malware-linked-ip-address"></a>Endereço IP vinculado a malware

**Tipo de Detecção:** Off-line  
**Nome antigo:** Entradas de dispositivos infectados


Esse tipo de evento de risco indica as entradas de endereços IP infectados com malware que são conhecidos por se comunicar ativamente com um servidor de bot. Isso é determinado ao correlacionar os endereços IP do dispositivo do usuário com os endereços IP que estavam em contato com um servidor de bot enquanto o servidor de bot estava ativo.


## <a name="unfamiliar-sign-in-properties"></a>Propriedades de entrada desconhecidas

**Tipo de Detecção:** Em tempo real **nome antigo:** Entradas de locais desconhecidos

Esse tipo de evento de risco considera histórico de entrada (IP, Latitude / Longitude e ASN) para procurar por entradas anômalas. O sistema armazena informações sobre locais anteriores usados por um usuário e considera esses locais "familiares". O evento de risco é disparado quando a entrada ocorre em uma localização que ainda não está na lista de localizações conhecidas. Usuários recém-criado será em "modo de aprendizado" por um período de tempo em quais propriedades de entrada não familiares eventos de risco serão desativados Embora nossos algoritmos aprender o comportamento do usuário. O aprendizado de duração do modo é dinâmica e depende de quanto tempo ele leva o algoritmo para coletar informações suficientes sobre padrões de logon do usuário. A duração mínima é de cinco dias. Um usuário pode voltar no modo de aprendizado após um longo período de inatividade. O sistema também ignora entradas de dispositivos conhecidos e locais que são geograficamente próximos de uma localização familiar. 

Também podemos executar essa detecção para a autenticação Básica (ou protocolos herdados). Como esses protocolos não têm propriedades modernas como a ID do cliente, há uma telemetria limitada para reduzir os falsos positivos. Recomendamos que nossos clientes mudem para a autenticação moderna.


## <a name="azure-ad-threat-intelligence"></a>Inteligência de ameaças do Azure AD

**Tipo de Detecção:** Off-line <br>
**Nome antigo:** Essa detecção será mostrada nos relatórios do Azure AD Identity Protection herdados (usuários sinalizados para risco, eventos de risco) como "Usuários com credenciais vazadas"

Esse tipo de evento de risco indica atividade do usuário que é incomum para o usuário especificado ou é consistente com os padrões de ataques conhecidos com base em fontes de inteligência de ameaças internas e externas da Microsoft.
