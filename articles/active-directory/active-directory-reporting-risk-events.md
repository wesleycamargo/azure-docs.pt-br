---
title: Eventos de risco do Azure Active Directory | Microsoft Docs
description: "Este tópico fornece uma visão geral detalhada do que são os eventos de risco."
services: active-directory
keywords: "proteção de identidade do azure active directory, segurança, risco, nível de risco, vulnerabilidade, política de segurança"
author: MarkusVi
manager: femila
ms.assetid: fa2c8b51-d43d-4349-8308-97e87665400b
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/02/2017
ms.author: markvi
translationtype: Human Translation
ms.sourcegitcommit: 56e5a634412a21302e0583f4585530480056a918
ms.openlocfilehash: c83597d16354bb9bb5507d77cae5ff0799922750
ms.lasthandoff: 03/02/2017


---
# <a name="azure-active-directory-risk-events"></a>Eventos de risco do Azure Active Directory

O Azure Active Directory ajuda a proteger suas identidades. Uma parte do processo de proteção é a detecção de ações suspeitas que estão relacionadas às suas contas de usuário. A detecção baseia-se em algoritmos adaptáveis de aprendizado de máquina e heurística. Cada ação suspeita detectada é armazenada em um registro chamado *evento de risco*. 


![Evento de risco](./media/active-directory-reporting-risk-events/91.png)


Atualmente, o Azure Active Directory detecta seis tipos de eventos de risco.

| Tipo de evento de risco | Nível de risco | Tipo de Detecção |
| :-- | --- | --- |
| [Usuários com credenciais vazadas](#leaked-credentials) | Alto | Off-line |
| [Entradas de endereços de IP anônimos](#sign-ins-from-anonymous-ip-addresses) | Média | Tempo real |
| [Viagem impossível a locais atípicos](#impossible-travel-to-atypical-locations) | Média | Off-line |
| [Entradas de locais desconhecidos](#sign-in-from-unfamiliar-locations) | Média | Tempo real |
| [Entradas de dispositivos infectados](#sign-ins-from-infected-devices) | Baixo | Off-line |
| [Entradas de endereços de IP com atividade suspeita](#sign-ins-from-ip-addresses-with-suspicious-activity) | Média | Off-line|

## <a name="risk-level"></a>Nível de risco

A propriedade de nível de risco é um indicador (Alta, Média ou Baixa) da gravidade de um evento de risco. Essa propriedade ajuda você a priorizar as ações que deve executar. 

A severidade do evento de risco representa a intensidade do sinal como um indicador de comprometimento da identidade combinado com a quantidade de ruído que ela normalmente apresenta.

* **Alta**: alta confiabilidade e evento de risco de alta severidade. Esses eventos são fortes indicadores de que a identidade do usuário foi comprometida e as contas de usuário afetadas devem ser corrigidas imediatamente.

* **Média**: severidade alta, porém com evento de risco de baixa confiabilidade ou vice-versa. Esses eventos são potencialmente arriscados e quaisquer contas de usuário afetadas devem ser corrigidas.

* **Baixa**: baixa confiabilidade e evento de risco de baixa severidade. Esse evento pode não exigir uma ação imediata, porém quando combinado com outros eventos de risco, pode fornecer uma forte indicação de que a identidade está comprometida.

![Nível de risco](./media/active-directory-reporting-risk-events/01.png)


## <a name="detection-type"></a>Tipo de detecção

A propriedade de tipo de detecção é um indicador (Tempo Real, Offline) do período de tempo de detecção de um evento de risco.  
Atualmente, a maioria dos eventos de risco é detectada offline em uma operação de pós-processamento depois que o evento ocorreu.

A tabela a seguir lista a quantidade de tempo que leva para um tipo de detecção aparecer em um relatório relacionado.

| Tipo de detecção | Relatório de latência |
| --- | --- |
| Tempo real | De 5 a 10 minutos |
| Off-line | De 2 a 4 horas |



## <a name="risk-event-types"></a>Tipos de evento de risco

A propriedade de tipo de evento de risco é um identificador de ação suspeita que gerou a criação de um registro de evento de risco.  
Atualmente, o Azure Active Directory detecta seis tipos de evento.

Os investimentos contínuos da Microsoft no processo de detecção levaram a:

- Aprimoramento na precisão da detecção de eventos de risco existentes 
- Novos tipos de evento de risco que serão adicionados no futuro

### <a name="leaked-credentials"></a>Credenciais vazadas
Credenciais vazadas são encontradas por pesquisadores de segurança da Microsoft postadas publicamente na dark web. Essas credenciais são normalmente encontradas em texto sem formatação. Elas são comparadas com as credenciais do Azure AD e, se houver uma correspondência, são relatadas como "Credenciais vazadas" no Identity Protection.

Eventos de risco de credenciais vazadas são classificados como um evento de risco de severidade "Alta", pois fornecem uma clara indicação de que o nome de usuário e senha estão à disposição de invasores.

### <a name="sign-ins-from-anonymous-ip-addresses"></a>Entradas de endereços IP anônimos
Esse tipo de evento de risco identifica os usuários que entraram com êxito de um endereço IP que foi identificado como um endereço IP de proxy anônimo. Esses proxies geralmente são usados por usuários que desejam ocultar o endereço IP de seu dispositivo e podem ser usados com objetivos mal-intencionados.

Recomendamos contatar o usuário imediatamente para verificar se ele estava usando endereços IP anônimos. O nível de risco desse tipo de evento de risco é “**Médio**” porque um IP anônimo não é por si só uma indicação de um comprometimento de conta.


### <a name="impossible-travel-to-atypical-locations"></a>Viagem impossível a locais atípicos
Esse tipo de evento de risco identifica duas entradas provenientes de locais geograficamente distantes, onde pelo menos um deles também pode ser atípico para o usuário, considerando seu comportamento anterior. Além disso, o tempo entre as duas entradas é menor que o tempo necessário para o trajeto do primeiro local até o segundo, o que indica que um usuário diferente está usando as mesmas credenciais. 

Esse algoritmo de aprendizado de máquina ignora “*falsos positivos*” óbvios que contribuem para a condição de viagem impossível, como VPNs e locais regularmente usados por outros usuários na organização.  O sistema tem um período inicial de aprendizado de 14 dias, durante o qual ele assimila o comportamento de entrada do novo usuário.

Uma viagem impossível geralmente é um bom indicador de que um hacker conseguiu entrar com êxito. No entanto, podem ocorrer falsos positivos quando um usuário estiver viajando usando um novo dispositivo ou usando uma VPN que normalmente não é usada por outros usuários na organização. Outra fonte de falsos positivos são os aplicativos que transmitem IPs de servidor incorretamente como IPs de cliente, o que pode dar a entender que as entradas estão ocorrendo do datacenter em que o aplicativo de back-end está hospedado (geralmente são datacenters Microsoft, o que dá a entender que as entradas estão ocorrendo de endereços IP de propriedade da Microsoft). Como resultado desses falsos positivos, o nível de risco desse evento de risco é “**Médio**”.

### <a name="sign-in-from-unfamiliar-locations"></a>Entrada de locais desconhecidos
Esse tipo de evento de risco é um mecanismo de avaliação de entrada em tempo real que considera locais de entrada (IP, Latitude / Longitude e ASN) para determinar os locais novos / desconhecidos. O sistema armazena informações sobre locais anteriores usados por um usuário e considera esses locais "familiares". O risco ainda é disparado quando a entrada ocorre em um local não presente na lista de locais familiares. O sistema tem um período inicial de aprendizado de 14 dias, durante o qual ele não sinaliza nenhum local novo como desconhecido. O sistema também ignora entradas de dispositivos conhecidos e locais que são geograficamente próximos de uma localização familiar. 

Locais desconhecidos podem fornecer uma forte indicação de que um invasor pode tentar usar uma identidade roubada. Falsos positivos podem ocorrer quando um usuário estiver viajando, experimentando um novo dispositivo ou usando uma nova VPN. Como resultado desses falsos positivos, o nível de risco desse tipo de evento é “**Médio**”.



### <a name="sign-ins-from-infected-devices"></a>Entradas de dispositivos infectados
Esse tipo de evento de risco identifica as entradas de dispositivos infectados com malware, que são conhecidos por comunicar-se ativamente com um servidor de bot. Isso é determinado ao correlacionar os endereços IP do dispositivo do usuário com os endereços IP que estavam em contato com um servidor de bot. 

Esse evento de risco identifica os endereços IP, não os dispositivos de usuário. Se vários dispositivos estiverem atrás de um único endereço IP e apenas alguns forem controlados por uma rede de bot, entradas de outros dispositivos podem disparar esse evento desnecessariamente, que é o motivo para classificar esse evento de risco como “**Baixo**”.  

Recomendamos entrar em contato com o usuário e verificar todos os seus dispositivos. Também é possível que o dispositivo pessoal de um usuário esteja infectado ou então, como mencionado anteriormente, que outra pessoa estava usando um dispositivo infectado do mesmo endereço IP como se fosse o usuário. Dispositivos infectados geralmente estão infectados por malware ainda não identificado pelo software antivírus, também podendo indicar como maus hábitos do usuário que podem ter causado a infecção do dispositivo.

Para obter mais informações sobre como abordar infecções por malware, consulte o [Centro de Proteção contra Malware](http://go.microsoft.com/fwlink/?linkid=335773&clcid=0x409).


### <a name="sign-ins-from-ip-addresses-with-suspicious-activity"></a>Entradas de endereços IP com atividade suspeita
Esse tipo de evento de risco identifica os endereços IP dos quais um grande número de tentativas de entrada com falha foram observadas, de várias contas de usuário e em um curto período de tempo. Isso corresponde aos padrões de tráfego de endereços IP usados por invasores e é um forte indicador de que contas já se encontram comprometidas ou estão prestes a ser. Esse é um algoritmo de aprendizado de máquina que ignora “*falsos positivos*” óbvios, como endereços IP que são regularmente usados por outros usuários na organização.  O sistema tem um período inicial de aprendizado de 14 dias, no qual ele assimila o comportamento de entrada do novo usuário e do novo locatário.

Recomendamos contatar o usuário para verificar se ele realmente entrou de um endereço IP que foi marcado como suspeito. O nível de risco desse tipo de evento é “**Médio**”, pois vários dispositivos podem estar atrás do mesmo endereço IP, enquanto apenas alguns podem ser responsáveis pela atividade suspeita. 



## <a name="next-steps"></a>Próximas etapas
* [Azure Active Directory Identity Protection](active-directory-identityprotection.md)


