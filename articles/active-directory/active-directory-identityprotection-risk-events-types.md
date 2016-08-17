<properties
	pageTitle="Tipos de eventos de risco detectados pelo Azure Active Directory Identity Protection | Microsoft Azure"
	description="Este tópico fornece uma visão geral detalhada dos tipos de eventos de risco disponíveis no Azure Active Directory Identity Protection"
	services="active-directory"
	keywords="azure active directory identity protection, cloud app discovery, gerenciamento de aplicativos, segurança, risco, nível de risco, vulnerabilidade, política de segurança"
	documentationCenter=""
	authors="markusvi"
	manager="femila"
	editor=""/>

<tags
	ms.service="active-directory"
	ms.workload="identity"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="08/02/2016"
	ms.author="markvi"/>

#Tipos de eventos de risco detectados pelo Azure Active Directory Identity Protection 

No Azure Active Directory Identity Protection, eventos de risco são eventos que:

- foram sinalizados como suspeitos

- indicam que uma identidade pode ter sido comprometida.

Este tópico fornece uma visão geral detalhada dos tipos de eventos de risco disponíveis.


## Credenciais vazadas

Credenciais vazadas são encontradas por pesquisadores de segurança da Microsoft postadas publicamente na dark web. Essas credenciais são normalmente encontradas em texto sem formatação. Elas são comparadas com as credenciais do Azure AD e, se houver uma correspondência, são relatadas como "Credenciais vazadas" no Identity Protection.

Eventos de risco de credenciais vazadas são classificados como um evento de risco de severidade "Alta", pois fornecem uma clara indicação de que o nome de usuário e senha estão à disposição de invasores.

## Viagem impossível a locais atípicos

Esse tipo de evento de risco identifica duas entradas provenientes de locais geograficamente distantes, onde pelo menos um deles também pode ser atípico para o usuário, considerando seu comportamento anterior. Além disso, o tempo entre as duas entradas é menor que o tempo necessário para o trajeto do primeiro local até o segundo, o que indica que um usuário diferente está usando as mesmas credenciais.

Esse algoritmo de aprendizado de máquina ignora “*falsos positivos*” óbvios que contribuem para a condição de viagem impossível, como VPNs e locais regularmente usados por outros usuários na organização. O sistema tem um período inicial de aprendizado de 14 dias, durante o qual ele assimila o comportamento de entrada do novo usuário.

Uma viagem impossível geralmente é um bom indicador de que um hacker conseguiu entrar com êxito. No entanto, podem ocorrer falsos positivos quando um usuário estiver viajando usando um novo dispositivo ou usando uma VPN que normalmente não é usada por outros usuários na organização. Outra fonte de falsos positivos são os aplicativos que transmitem IPs de servidor incorretamente como IPs de cliente, o que pode dar a entender que as entradas estão ocorrendo do datacenter em que o aplicativo de back-end está hospedado (geralmente são datacenters Microsoft, o que dá a entender que as entradas estão ocorrendo de endereços IP de propriedade da Microsoft). Como resultado desses falsos positivos, o nível de risco desse evento de risco é “**Médio**”.

##Entradas de dispositivos infectados

Esse tipo de evento de risco identifica as entradas de dispositivos infectados com malware, que são conhecidos por comunicar-se ativamente com um servidor de bot. Isso é determinado ao correlacionar os endereços IP do dispositivo do usuário com os endereços IP que estavam em contato com um servidor de bot.

Esse evento de risco identifica os endereços IP, não os dispositivos de usuário. Se vários dispositivos estiverem atrás de um único endereço IP e apenas alguns forem controlados por uma rede de bot, entradas de outros dispositivos podem disparar esse evento desnecessariamente, que é o motivo para classificar esse evento de risco como “**Baixo**”.

Recomendamos entrar em contato com o usuário e verificar todos os seus dispositivos. Também é possível que o dispositivo pessoal de um usuário esteja infectado ou então, como mencionado anteriormente, que outra pessoa estava usando um dispositivo infectado do mesmo endereço IP como se fosse o usuário. Dispositivos infectados geralmente estão infectados por malware ainda não identificado pelo software antivírus, também podendo indicar como maus hábitos do usuário que podem ter causado a infecção do dispositivo.

Para obter mais informações sobre como abordar infecções por malware, consulte o [Centro de Proteção contra Malware](http://go.microsoft.com/fwlink/?linkid=335773&clcid=0x409).


## Entradas de endereços IP anônimos

Esse tipo de evento de risco identifica os usuários que entraram com êxito de um endereço IP que foi identificado como um endereço IP de proxy anônimo. Esses proxies geralmente são usados por usuários que desejam ocultar o endereço IP de seu dispositivo e podem ser usados com objetivos mal-intencionados.

Recomendamos contatar o usuário imediatamente para verificar se ele estava usando endereços IP anônimos. O nível de risco desse tipo de evento de risco é “**Médio**” porque um IP anônimo não é por si só uma indicação de um comprometimento de conta.

## Entradas de endereços IP com atividade suspeita

Esse tipo de evento de risco identifica os endereços IP dos quais um grande número de tentativas de entrada com falha foram observadas, de várias contas de usuário e em um curto período de tempo. Isso corresponde aos padrões de tráfego de endereços IP usados por invasores e é um forte indicador de que contas já se encontram comprometidas ou estão prestes a ser. Esse é um algoritmo de aprendizado de máquina que ignora “ *falsos positivos* ” óbvios, como endereços IP que são regularmente usados por outros usuários na organização. O sistema tem um período inicial de aprendizado de 14 dias, no qual ele assimila o comportamento de entrada do novo usuário e do novo locatário.

Recomendamos contatar o usuário para verificar se ele realmente entrou de um endereço IP que foi marcado como suspeito. O nível de risco desse tipo de evento é “**Médio**”, pois vários dispositivos podem estar atrás do mesmo endereço IP, enquanto apenas alguns podem ser responsáveis pela atividade suspeita.


## Entrada de locais desconhecidos

Esse tipo de evento de risco é um mecanismo de avaliação de entrada em tempo real que considera locais de entrada (IP, Latitude / Longitude e ASN) para determinar os locais novos / desconhecidos. O sistema armazena informações sobre locais anteriores usados por um usuário e considera esses locais "familiares". O risco ainda é disparado quando a entrada ocorre em um local não presente na lista de locais familiares. O sistema tem um período inicial de aprendizado de 14 dias, durante o qual ele não sinaliza nenhum local novo como desconhecido. O sistema também ignora entradas de dispositivos conhecidos e locais que são geograficamente próximos de uma localização familiar. <br> Locais desconhecidos podem fornecer uma forte indicação de que um invasor pode tentar usar uma identidade roubada. Falsos positivos podem ocorrer quando um usuário estiver viajando, experimentando um novo dispositivo ou usando uma nova VPN. Como resultado desses falsos positivos, o nível de risco desse tipo de evento é “**Médio**”.





## Consulte também

- [Azure Active Directory Identity Protection](active-directory-identityprotection.md)

<!---HONumber=AcomDC_0803_2016-->