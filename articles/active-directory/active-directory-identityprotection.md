<properties
	pageTitle="Azure Active Directory Identity Protection | Microsoft Azure"
	description="Saiba como o Azure AD Identity Protection permite limitar a capacidade de um invasor de explorar uma identidade ou um dispositivo comprometidos ou um dispositivo que sofreu comprometimento conhecido ou suspeito anteriormente."
	services="active-directory"
	keywords="azure active directory identity protection, cloud app discovery, gerenciamento de aplicativos, segurança, risco, nível de risco, vulnerabilidade, política de segurança"
	documentationCenter=""
	authors="markusvi"
	manager="stevenpo"
	editor=""/>

<tags
	ms.service="active-directory"
	ms.workload="identity"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="03/02/2016"
	ms.author="markvi"/>

#Azure Active Directory Identity Protection 

O Azure Active Directory Identity Protection é um serviço de segurança que fornece uma exibição consolidada dos eventos de risco e das possíveis vulnerabilidades que afetam as identidades da sua organização. A Microsoft tem protegido identidades baseadas em nuvem há mais de uma década e, com o Azure AD Identity Protection, ela está disponibilizando esses mesmos sistemas de proteção para os clientes empresariais. O Identity Protection aproveita os recursos de detecção de anomalias existentes do Azure AD (disponíveis por meio dos Relatórios de Atividade Anômala do Azure AD) e apresenta novos tipos de evento de risco que pode detectar anomalias em tempo real.

> [AZURE.NOTE] A preview do Identity Protection está disponível atualmente apenas para locatários do Azure AD provisionados nos Estados Unidos.
 

A grande maioria das violações de segurança ocorre quando os invasores conseguem acessar a um ambiente roubando a identidade de um usuário. Os invasores têm se tornado cada vez mais eficazes em aproveitar as violações de terceiros e usar ataques de phishing sofisticados. Depois que um invasor obtém acesso até mesmo a uma conta de usuário com privilégios baixos, é relativamente fácil para ele conseguir acessar recursos importantes da empresa por meio de movimentação lateral. Portanto, é essencial proteger todas as identidades e, quando uma identidade é comprometida, evitar proativamente que a identidade comprometida seja utilizada de maneira abusiva.

Descobrir identidades comprometidas não é uma tarefa fácil. Felizmente, o Identity Protection pode ajudar. O Identity Protection usa algoritmos de aprendizado de máquina adaptáveis e heurística para detectar anomalias e eventos de risco que podem indicar que uma identidade foi comprometida.
 
Usando esses dados, o Identity Protection gera relatórios e alertas que permitem investigar tais eventos de risco e tomar as devidas ações de mitigação ou correção.
 
Contudo, o Azure Active Directory Identity Protection é mais do que apenas uma ferramenta de monitoramento e criação de relatórios. Baseando-se em eventos de risco, o Identity Protection calcula um nível de risco para cada usuário, permitindo a você definir políticas baseadas em risco para proteger automaticamente as identidades da sua organização. Essas políticas baseadas em risco, entre outros controles de acesso condicional fornecidos pelo Azure Active Directory e pelo EMS, podem bloquear automaticamente ou oferecer ações de correção adaptáveis que incluem redefinições de senha e a imposição de autenticação multifator.

####Explore as funcionalidades do Identity Protection 

**Detecção de eventos de risco e contas arriscadas:**

- Detecção de seis tipos de evento de risco usando regras de aprendizado de máquina e heurística 

- Calcular os níveis de risco do usuário

- Fornecer recomendações personalizadas para melhorar a postura de segurança geral ao realçar as vulnerabilidades

<br>

**Investigação dos eventos de risco:**

- Enviar notificações para eventos de risco

- Investigar os eventos de risco usando informações relevantes e contextuais

- Fornecer fluxos de trabalho básicos para acompanhar as investigações

- Fornecer acesso fácil às ações de correção, tais como redefinição de senha

<br>

**Políticas de acesso condicional baseadas em risco:**

- Política para reduzir entradas arriscadas ao bloquear entradas ou exigir desafios de autenticação multifator.

- Política para bloquear ou proteger contas de usuário arriscadas

- Política para exigir o registro de usuários para autenticação multifator


## Detecção e Risco

### Eventos de risco

Eventos de risco são eventos que foram marcados como suspeitos pelo Identity Protection e indicam que uma identidade pode ter sido comprometida. Para obter uma lista completa dos eventos de risco, consulte [Tipos de Eventos de Risco](#types-of-risk-events). Alguns desses eventos de risco estão disponíveis por meio dos relatórios de Atividade Anômala do Azure AD no Portal de Gerenciamento do Azure. A tabela abaixo lista os diversos tipos de evento de risco e o relatório de **Atividade Anômala do Azure AD** correspondente. A Microsoft continua a investir nesta área e planeja melhorar continuamente a precisão da detecção dos eventos de risco existentes e adicionar novos tipos de evento de risco continuamente.



| Tipo de Evento de Risco do Identity Protection | Relatório de Atividade Anômala do Azure AD Correspondente |
| :-- | :-- |
| Credenciais vazadas | Usuários com credenciais insuficientes |
| Viagem impossível a locais atípicos |	Atividades de entrada irregulares |
| Entradas de dispositivos infectados | Entradas de dispositivos possivelmente infectados |
| Entradas de endereços IP anônimos | Entradas de fontes desconhecidas |
| Entradas de endereços IP com atividade suspeita |	Entradas de endereços IP com atividade suspeita |
| Entradas de locais desconhecidos | - | | Eventos de bloqueio (que não estão na preview pública) | - |

Os seguintes relatórios de Atividade Anômala do Azure AD não estão incluídos como eventos de risco no Azure AD Identity Protection, portanto, não estão disponíveis por meio da proteção do Identity Protection. Esses relatórios ainda estão disponíveis no Portal de Gerenciamento do Azure, contudo serão preteridos futuramente, pois estão sendo substituídos pelos eventos de risco do Identity Protection.

- Entradas após várias falhas
- Entradas de várias geografias

### Nível de risco

O Nível de risco de um evento de risco é uma indicação (Alta, Média ou Baixa) da severidade do evento de risco. O nível de risco ajuda os usuários do Identity Protection a priorizarem as ações que devem ser executadas para reduzir os riscos para sua organização. A severidade do evento de risco representa a intensidade do sinal como um indicador de comprometimento da identidade combinado com a quantidade de ruído que ela normalmente apresenta.

- **Alta**: alta confiabilidade e evento de risco de alta severidade. Esses eventos são fortes indicadores de que a identidade do usuário foi comprometida e as contas de usuário afetadas devem ser corrigidas imediatamente.

- **Média**: severidade alta, porém com evento de risco de baixa confiabilidade ou vice-versa. Esses eventos são potencialmente arriscados e quaisquer contas de usuário afetadas devem ser corrigidas.

- **Baixa**: baixa confiabilidade e evento de risco de baixa severidade. Esse evento pode não exigir uma ação imediata, porém quando combinado com outros eventos de risco, pode fornecer uma forte indicação de que a identidade está comprometida.


![Nível de Risco](./media/active-directory-identityprotection/01.png "Nível de Risco")

 

Eventos de risco ou são identificados em **tempo real** ou no pós-processamento depois que o evento de risco já ocorreu (offline). Atualmente a maioria dos eventos de risco do Identity Protection é computada offline e aparece no Identity Protection dentro de duas a quatro horas. Embora sejam avaliados em tempo real, os eventos de risco em tempo real aparecerão no Console do Identity Protection dentro de cinco a 10 minutos.

Vários clientes herdados não dão suporte à detecção e prevenção de eventos de risco em tempo real atualmente. Como resultado, entradas desses clientes não podem ser detectadas ou prevenidas em tempo real.

### Tipos de eventos de risco

Esta seção fornece uma visão geral dos tipos de eventos de risco disponíveis

#### Credenciais vazadas

Credenciais vazadas são encontradas por pesquisadores de segurança da Microsoft postadas publicamente na dark web. Essas credenciais são normalmente encontradas em texto sem formatação. Elas são comparadas com as credenciais do Azure AD e, se houver uma correspondência, são relatadas como "Credenciais vazadas" no Identity Protection.

Eventos de risco de credenciais vazadas são classificados como um evento de risco de severidade "Alta", pois fornecem uma clara indicação de que o nome de usuário e senha estão à disposição de invasores.

#### Viagem impossível a locais atípicos

Esse tipo de evento de risco identifica duas entradas provenientes de locais geograficamente distantes, onde pelo menos um deles também pode ser atípico para o usuário, considerando seu comportamento anterior. Além disso, o tempo entre as duas entradas é menor que o tempo necessário para o trajeto do primeiro local até o segundo, o que indica que um usuário diferente está usando as mesmas credenciais.

Esse algoritmo de aprendizado de máquina ignora "*falsos positivos*" óbvios que contribuem para a condição de viagem impossível, como VPNs e locais regularmente usados por outros usuários na organização. O sistema tem um período inicial de aprendizado de 14 dias, durante o qual ele assimila o comportamento de entrada do novo usuário.

Uma viagem impossível geralmente é um bom indicador de que um hacker conseguiu entrar com êxito. No entanto, podem ocorrer falsos positivos quando um usuário estiver viajando usando um novo dispositivo ou usando uma VPN que normalmente não é usada por outros usuários na organização. Outra fonte de falsos positivos são os aplicativos que transmitem IPs de servidor incorretamente como IPs de cliente, o que pode dar a entender que as entradas estão ocorrendo do datacenter em que o aplicativo de back-end está hospedado (geralmente são datacenters Microsoft, o que dá a entender que as entradas estão ocorrendo de endereços IP de propriedade da Microsoft). Como resultado desses falsos positivos, o nível de risco desse evento de risco é “**Médio**”.

####Entradas de dispositivos infectados

Esse tipo de evento de risco identifica as entradas de dispositivos infectados com malware, que são conhecidos por comunicar-se ativamente com um servidor de bot. Isso é determinado ao correlacionar os endereços IP do dispositivo do usuário com os endereços IP que estavam em contato com um servidor de bot.

Esse evento de risco identifica os endereços IP, não os dispositivos de usuário. Se vários dispositivos estiverem atrás de um único endereço IP e apenas alguns forem controlados por uma rede de bot, entradas de outros dispositivos podem disparar esse evento desnecessariamente, que é o motivo para classificar esse evento de risco como “**Baixo**”.

Recomendamos entrar em contato com o usuário e verificar todos os seus dispositivos. Também é possível que o dispositivo pessoal de um usuário esteja infectado ou então, como mencionado anteriormente, que outra pessoa estava usando um dispositivo infectado do mesmo endereço IP como se fosse o usuário. Dispositivos infectados geralmente estão infectados por malware ainda não identificado pelo software antivírus, também podendo indicar como maus hábitos do usuário que podem ter causado a infecção do dispositivo.

Para obter mais informações sobre como abordar infecções por malware, consulte o [Centro de Proteção contra Malware](http://go.microsoft.com/fwlink/?linkid=335773&clcid=0x409).


#### Entradas de endereços IP anônimos

Esse tipo de evento de risco identifica os usuários que entraram com êxito de um endereço IP que foi identificado como um endereço IP de proxy anônimo. Esses proxies geralmente são usados por usuários que desejam ocultar o endereço IP de seu dispositivo e podem ser usados com objetivos mal-intencionados.

Recomendamos contatar o usuário imediatamente para verificar se ele estava usando endereços IP anônimos. O nível de risco desse tipo de evento de risco é “**Médio**” porque um IP anônimo não é por si só uma indicação de um comprometimento de conta.

#### Entradas de endereços IP com atividade suspeita

Esse tipo de evento de risco identifica os endereços IP dos quais um grande número de tentativas de entrada com falha foram observadas, de várias contas de usuário e em um curto período de tempo. Isso corresponde aos padrões de tráfego de endereços IP usados por invasores e é um forte indicador de que contas já se encontram comprometidas ou estão prestes a ser. Esse é um algoritmo de aprendizado de máquina que ignora "*falsos positivos*" óbvios, como endereços IP que são regularmente usados por outros usuários na organização. O sistema tem um período inicial de aprendizado de 14 dias, no qual ele assimila o comportamento de entrada do novo usuário e do novo locatário.

Recomendamos contatar o usuário para verificar se ele realmente entrou de um endereço IP que foi marcado como suspeito. O nível de risco desse tipo de evento é “**Médio**”, pois vários dispositivos podem estar atrás do mesmo endereço IP, enquanto apenas alguns podem ser responsáveis pela atividade suspeita.


#### Entrada de locais desconhecidos

Esse tipo de evento de risco é um mecanismo de avaliação de entrada em tempo real que considera locais de entrada (IP, Latitude / Longitude e ASN) para determinar os locais novos / desconhecidos. O sistema armazena informações sobre locais anteriores usados por um usuário e considera esses locais "familiares". O risco ainda é disparado quando a entrada ocorre em um local não presente na lista de locais familiares. O sistema tem um período inicial de aprendizado de 14 dias, durante o qual ele não sinaliza nenhum local novo como desconhecido. O sistema também ignora entradas de dispositivos conhecidos e locais que são geograficamente próximos de uma localização familiar. <br> Locais desconhecidos podem fornecer uma forte indicação de que um invasor pode tentar usar uma identidade roubada. Falsos positivos podem ocorrer quando um usuário estiver viajando, experimentando um novo dispositivo ou usando uma nova VPN. Como resultado desses falsos positivos, o nível de risco desse tipo de evento é “**Médio**”.

### Vulnerabilidades

Vulnerabilidades são pontos fracos no seu ambiente que podem ser explorados por um invasor. Recomendamos resolver essas vulnerabilidades para melhorar a postura de segurança da sua organização e impedir que invasores possam explorá-las.

#### Registro de autenticação multifator não configurado 

Essa vulnerabilidade ajuda a controlar a implantação do Azure Multi-Factor Authentication na sua organização.

A Azure Multi-Factor Authentication fornece uma segunda camada de segurança para a autenticação do usuário. Ela ajuda a proteger o acesso a dados e aplicativos ao mesmo tempo que atende à demanda dos usuários para um processo de entrada simples. Ele fornece autenticação forte por meio de uma variedade de opções de fácil verificação — chamada telefônica, mensagem de texto, notificação de aplicativo móvel ou verificação de código e tokens OATH de terceiros.

Recomendamos exigir a autenticação multifator para entradas de usuário. A autenticação multifator desempenha um papel fundamental nas políticas de acesso condicional baseadas em risco disponíveis por meio do Identity Protection.

Para obter mais detalhes, veja [O que é o Azure Multi-Factor Authentication?](../multi-factor-authentication/multi-factor-authentication.md)


#### Aplicativos de nuvem não gerenciados

Essa vulnerabilidade ajuda a identificar aplicativos de nuvem não gerenciados na sua organização.
 
Nas empresas modernas, os departamentos de TI geralmente não estão cientes de todos os aplicativos de nuvem que os membros da sua organização usam para realizar seu trabalho. É fácil ver por que os administradores teriam preocupações sobre o acesso não autorizado aos dados corporativos, perda de dados e outros riscos de segurança.

Recomendamos que sua organização implante o Cloud App Discovery para descobrir aplicativos de nuvem não gerenciados e gerencie esses aplicativos usando o Azure Active Directory.

Para obter mais detalhes, consulte [Encontrando aplicativos de nuvem não gerenciados com o Cloud App Discovery](active-directory-cloudappdiscovery-whatis.md).



####Alertas de Segurança do Privileged Identity Management

Essa vulnerabilidade ajuda você a descobrir e resolver alertas sobre identidades com privilégios na sua organização.

Para permitir que os usuários executem operações com privilégios, as organizações precisam oferecer aos usuários acesso privilegiado temporário ou permanente ao Azure AD, a recursos do Azure ou do Office 365 ou a outros aplicativos SaaS. Cada um desses usuários privilegiados aumenta a superfície de ataque da sua organização. Essa vulnerabilidade ajuda você a identificar os usuários com acesso privilegiado desnecessário e tomar as devidas providências para reduzir ou eliminar o risco que eles representam.

Recomendamos que sua organização use o Azure AD Privileged Identity Management para gerenciar, controlar e monitorar as identidades com privilégios e o acesso delas a recursos no Azure AD e em outros serviços online da Microsoft como o Office 365 ou o Microsoft Intune.

Para obter mais detalhes, consulte [Azure AD Privileged Identity Management](active-directory-privileged-identity-management-configure.md).

## Investigação
Sua jornada pelo Identity Protection normalmente inicia no Painel do Identity Protection.

<br><br> ![Correção](./media/active-directory-identityprotection/29.png "Correção") <br>

O painel concede acesso a:
 
- relatórios, como **Usuários sinalizados por riscos**, **Eventos de risco** e **Vulnerabilidades**
- configurações como a definição das suas **Políticas de Segurança**, **Notificações** e **registro de autenticação multifator**
 

Este é normalmente seu ponto de partida para investigação, que é o processo de revisão de atividades, logs e outras informações relevantes relacionadas a um evento de risco para decidir se as etapas de correção e mitigação são necessárias, como a identidade foi comprometida e entender como ela foi usada.


### Notificações

O Azure AD Identity Protection envia dois tipos de emails de notificação automatizados para ajudar você a gerenciar o risco do usuário e eventos de risco:

- Email de alerta de usuário comprometido

- Email de resumo semanal

#### Email de alerta de usuário comprometido

Um email de alerta de usuário comprometido é gerado quando o Azure AD Identity Protection identifica uma conta como comprometida. O email inclui um link para os Usuários sinalizados para o relatório de risco no Console do Identity Protection. Recomendamos que as notificações de usuários comprometidos sejam investigadas imediatamente.


#### Email de resumo semanal

O email de resumo semanal contém um resumo dos novos eventos de risco.<br> Ele inclui:
- Usuários em risco
- Eventos de risco relatados
- Vulnerabilidades detectadas
- Links para os relatórios relacionados no Identity Protection


<br> ![Correção](./media/active-directory-identityprotection/400.png "Correção") <br>

Por padrão, o alerta de usuário comprometido é enviado para todos os administradores do Azure Active Directory. É possível personalizar os destinatários da seguinte maneira:

- Seguindo o link para gerenciar destinatários na parte inferior do alerta

- Clicando em Notificações nas configurações do Identity Protection
 
<br> ![Riscos do usuário](./media/active-directory-identityprotection/62.png "Riscos do usuário") <br>
 



## O que é um nível de risco do usuário?

Um nível de risco do usuário é uma indicação (Alta, Média ou Baixa) da probabilidade de que a identidade do usuário foi comprometida. Ele é calculado com base nos eventos de risco do usuário associados à identidade do usuário.

O status de um evento de risco é **Ativo** ou **Fechado**. Somente eventos de risco **Ativos** contribuem para o cálculo de risco do usuário.

O nível de risco do usuário é calculado usando as seguintes entradas:

- Eventos de risco ativos que afetam o usuário
- Nível de risco desses eventos 
- Se foram tomadas ações de correção 

<br> ![Riscos do usuário](./media/active-directory-identityprotection/86.png "Riscos do usuário") <br>



Você pode usar os níveis de risco do usuário para criar políticas de acesso condicional a fim de impedir que usuários arriscados entrem ou forçá-los a alterar sua senha com segurança.


## Fechar eventos de risco manualmente

Na maioria dos casos, você tomará ações de correção, como uma redefinição de senha de segurança, para fechar automaticamente os eventos de risco. No entanto, isso nem sempre é possível. <br> Como eventos de risco **Ativos** contribuem para o cálculo de risco do usuário, talvez seja necessário reduzir manualmente um nível de risco fechando eventos de risco manualmente. <br> Durante a investigação, você pode optar por executar uma das seguintes ações para alterar o status de um evento de risco:

<br> ![Ações](./media/active-directory-identityprotection/34.png "Ações") <br>

- **Resolver** - Se, após investigar um evento de risco, você tomou uma ação de correção apropriada fora do Identity Protection e acredita que o evento de risco deve ser considerado fechado, marque o evento como Resolvido. Eventos resolvidos definirão o status do evento de risco como Fechado e ele não contribuirá com o risco do usuário.

- **Marcar como falso positivo** - Em alguns casos, você pode investigar um evento de risco e descobrir que ele foi sinalizado incorretamente como uma situação arriscada. Você pode ajudar a reduzir o número de tais ocorrências marcando o evento de risco como falso positivo. Isso ajudará os algoritmos de aprendizado de máquina a melhorarem a classificação de eventos semelhantes no futuro. O status de eventos falso positivos é **Fechado** e não contribui mais com o risco do usuário.

- **Ignorar** - Se você não executou nenhuma ação de correção, mas deseja remover o evento de risco da lista ativa, marque um evento de risco como Ignorar e o status do evento será Fechado. Eventos ignorados não contribuem com o risco do usuário. Essa opção deve ser usada somente em circunstâncias incomuns.

- **Reativar** - Eventos de risco fechados manualmente (escolhendo **Resolver**, **Falso positivo** ou **Ignorar**) podem ser reativados, definindo o status do evento novamente como **Ativo**. Eventos de risco reativados contribuem no cálculo de nível de risco do usuário. Eventos de risco fechados por meio de correção (como redefinir uma senha de segurança) não podem ser reativados.



## Corrigir eventos de risco do usuário

Uma correção é uma ação que visa proteger uma identidade ou um dispositivo que já sofreu comprometimento conhecido ou suspeito anteriormente. Uma correção restaura a identidade ou dispositivo para um estado seguro e resolve eventos de risco anteriores associados à identidade ou ao dispositivo.

Para corrigir os eventos de risco do usuário, você pode:

- executar uma redefinição de senha de segurança para corrigir eventos de risco do usuário manualmente 

- configurar uma política de segurança de risco do usuário para mitigar ou corrigir eventos de risco do usuário automaticamente

- recriar imagem no dispositivo infectado


### Redefinição de senha de segurança

Uma redefinição de senha de segurança é uma correção eficiente para muitos eventos de risco e quando executada, automaticamente fecha esses eventos de risco e recalcula o nível de risco do usuário. Você pode usar o console do Identity Protection para iniciar uma redefinição de senha para um usuário arriscado.

O console fornece duas maneiras diferentes de redefinir uma senha:

**Redefinir senha** - Selecione **Exigir que o usuário redefina a senha** para permitir que o usuário se recupere automaticamente se ele estiver registrado para autenticação multifator. Durante da próxima entrada do usuário, ele precisará resolver um desafio de autenticação multifator com êxito e em seguida, será forçado a alterar a senha. Essa opção não estará disponível se a conta de usuário não estiver registrada para autenticação multifator.

**Senha temporária** - Selecione **Gerar uma senha temporária** para invalidar imediatamente a senha existente e criar uma nova senha temporária para o usuário. Envie a nova senha temporária para um endereço de email alternativo para o usuário ou para o gerente dele. Como a senha é temporária, o usuário precisará alterá-la após entrar.

<br> ![Política](./media/active-directory-identityprotection/71.png "Política") <br>



## Política de segurança de risco do usuário

Uma política de segurança de risco do usuário é uma política de acesso condicional que avalia o nível de risco de um usuário específico e aplica ações de correção e mitigação com base em regras e condições predefinidas. <br><br> ![Política de risco do usuário](./media/active-directory-identityprotection/500.png "Política de risco do usuário") <br>

O Azure AD Identity Protection ajuda a gerenciar a mitigação e correção de usuários sinalizados para riscos, permitindo:

- definir os usuários e grupos aos quais a política se aplica <br><br> ![Política de risco do usuário](./media/active-directory-identityprotection/501.png "Política de risco do usuário") <br>

- definir o limite de nível de risco do usuário (baixo, médio ou alto) que dispara uma alteração de senha <br><br> ![Política de risco do usuário](./media/active-directory-identityprotection/502.png "Política de risco do usuário") <br>

- definir o limite de nível de risco do usuário (baixo, médio ou alto) que dispara o bloqueio de um usuário <br><br> ![Política de risco do usuário](./media/active-directory-identityprotection/503.png "Política de risco do usuário") <br>

- examinar e avaliar o impacto de uma alteração antes de ativá-la <br><br> ![Política de risco do usuário](./media/active-directory-identityprotection/504.png "Política de risco do usuário") <br>


Escolher um limite **Alto** reduz o número de vezes que uma política é disparada e minimiza o impacto para os usuários. No entanto, isso exclui usuários sinalizados com **Baixo** e **Médio** risco da política, o que pode não proteger as identidades ou os dispositivos que sofreram comprometimento conhecido ou suspeito.

Ao definir a política,

- exclua os usuários que tendem a gerar muitos falsos positivos (desenvolvedores e analistas de segurança)

- exclua os usuários em localidades onde não é viável habilitar a política (por exemplo, sem acesso à assistência técnica)

- use um limite **Alto** durante a distribuição inicial de política ou se você precisar minimizar os desafios encontrados pelos usuários finais.

- use um limite **Baixo** se sua organização exigir uma segurança maior. Selecionar um limite **Baixo** apresenta desafios de entrada do usuário adicionais, porém representa uma segurança maior.

O padrão recomendado na maioria das organizações é configurar uma regra para um limite **Médio** para atingir um equilíbrio entre segurança e usabilidade.



### Mitigar eventos de risco do usuário
Os administradores podem definir uma política de segurança de risco do usuário para bloquear os usuários ao entrar dependendo do nível de risco.

Bloquear a entrada:
 
- impede a geração de novos eventos de risco do usuário para o usuário afetado

- permite aos administradores corrigir os eventos de risco que afetam a identidade do usuário, bem como restaurá-la para um estado seguro





### Fluxos de correção e mitigação de risco do usuário  

As seções a seguir fornecem uma visão geral da experiência do usuário para fluxos de correção e mitigação de risco do usuário.

#### Fluxo de Recuperação de Conta Comprometida

Após configurar uma política de segurança de risco do usuário, os usuários que atendem o usuário nível de risco especificado na política (sendo, portanto, considerados comprometidos) devem passar pelo fluxo de recuperação de comprometimento de usuário antes de poderem entrar.

O fluxo de recuperação de comprometimento do usuário tem três etapas:

1. O usuário é informado de que a segurança da conta está em risco devido à atividade suspeita ou credenciais vazadas.

<br> ![Correção](./media/active-directory-identityprotection/101.png "Correção") <br>

2.	O usuário precisa provar sua identidade resolvendo um desafio de segurança. Se o usuário estiver registrado para autenticação multifator, ele pode recuperar-se automaticamente do comprometimento. Eles precisarão fazer uma viagem de ida e volta de um código de segurança para o número de telefone. 

<br> ![Correção](./media/active-directory-identityprotection/110.png "Correção") <br>


3.	Por fim, o usuário será forçado a alterar a senha após outra pessoa ter tido acesso à sua conta. Veja as capturas de tela desta experiência abaixo.
 
<br> ![Correção](./media/active-directory-identityprotection/111.png "Correção") <br>







 
#### Redefinir senha
Se um usuário for impedido de entrar, um administrador pode gerar uma senha temporária para ele. Ele precisará alterar sua senha na próxima vez que entrar. Uma alteração de senha corrige e fecha a maioria dos tipos de eventos de risco do usuário.

<br> ![Correção](./media/active-directory-identityprotection/160.png "Correção") <br>


### Mitigação de nível de risco do usuário

### Conta comprometida: bloqueada 

Para desbloquear um usuário que foi bloqueado por uma política de segurança de risco do usuário, este deve contatar um administrador ou o suporte técnico. Não é possível recuperar-se sozinho com uma solução de autenticação multifator neste caso.

<br> ![Correção](./media/active-directory-identityprotection/104.png "Correção") <br>


## Mitigação de eventos de risco de entrada 
Uma mitigação é uma ação que visa limitar a capacidade de um invasor explorar uma identidade ou um dispositivo comprometidos sem restaurá-los para um estado seguro. Uma mitigação não resolve eventos de risco de entrada anteriores associados à identidade ou ao dispositivo.

Você pode usar o acesso condicional no Azure AD Identity Protection para mitigar automaticamente os eventos de risco de entrada. Ao usar essas políticas, considere o nível de risco do usuário ou da entrada para bloquear entradas arriscadas ou exigir que o usuário realize a autenticação multifator. Essas ações podem impedir que um invasor explore uma identidade roubada para causar danos, fazendo você ganhar algum tempo para proteger a identidade.


## Política de segurança de risco de entrada

Uma política de segurança de risco de entrada é uma política de acesso condicional que avalia o risco de uma entrada específica e aplica mitigações com base em regras e condições predefinidas. <br><br> ![Política de risco de entrada](./media/active-directory-identityprotection/700.png "Política de risco de entrada") <br>

O Azure AD Identity Protection ajuda a gerenciar a mitigação de entradas arriscadas, permitindo:

- definir os usuários e grupos aos quais a política se aplica <br><br> ![Política de risco de entrada](./media/active-directory-identityprotection/701.png "Política de risco de entrada") <br>

- definir o limite do nível de risco da entrada (baixo, médio ou alto) que dispara um desafio de autenticação multifator para as entradas afetadas <br><br> ![Política de risco de entrada](./media/active-directory-identityprotection/702.png "Política de risco de entrada") <br>

- definir o limite de nível de risco de entrada (baixo, médio ou alto) que bloqueia as entradas afetadas. <br><br> ![Política de risco de entrada](./media/active-directory-identityprotection/703.png "Política de risco de entrada") <br>

- examinar e avaliar o impacto de uma alteração antes de ativá-la <br><br> ![Política de risco de entrada](./media/active-directory-identityprotection/704.png "Política de risco de entrada") <br>

 
Escolher um limite **Alto** reduz o número de vezes que uma política é disparada e minimiza o impacto para os usuários.<br> No entanto, isso exclui entradas sinalizadas com **Baixa** e **Médio** risco da política, o que pode não impedir que um invasor explore uma identidade comprometida.

Ao definir a política,

- exclua os usuários que não tem / não podem ter a autenticação multifator

- exclua os usuários em localidades onde não é viável habilitar a política (por exemplo, sem acesso à assistência técnica)

- exclua os usuários que tendem a gerar muitos falsos positivos (desenvolvedores e analistas de segurança)

- use um limite **Alto** durante a distribuição inicial de política ou se você precisar minimizar os desafios encontrados pelos usuários finais.

- use um limite **Baixo** se sua organização exigir uma segurança maior. Selecionar um limite **Baixo** apresenta desafios de entrada do usuário adicionais, porém representa uma segurança maior.

O padrão recomendado na maioria das organizações é configurar uma regra para um limite **Médio** para atingir um equilíbrio entre segurança e usabilidade.

 
A política de risco de entrada:

- é aplicada a todo o tráfego do navegador e entradas que usam autenticação moderna.
- não é aplicada a aplicativos que usam protocolos de segurança mais antigos desabilitando o ponto de extremidade WS-Trust no IDP federado, como o ADFS.

A página **Eventos de Risco** no console do Identity Protection lista todos os eventos:

- aos quais essa política se aplica
- para os quais você pode examinar a atividade e determinar se a ação foi apropriada ou não 




## Política de registro de autenticação multifator

A autenticação multifator é usada para obter garantia adicional da identidade do usuário.<br> Registrar uma autenticação multifator é uma etapa essencial na preparação da sua organização para proteger e recuperar comprometimentos de conta. <br> ![Registro de MFA](./media/active-directory-identityprotection/600.png "Registro de MFA") <br>

O Azure AD Identity Protection ajuda a gerenciar a implementação do registro de autenticação multifator, permitindo:

- definir os usuários e grupos aos quais a política se aplica <br><br> ![Registro de MFA](./media/active-directory-identityprotection/601.png "Registro de MFA") <br>

- definir por quanto tempo eles têm permissão para ignorar o registro <br><br> ![Registro de MFA](./media/active-directory-identityprotection/602.png "Registro de MFA") <br>

- exibir o estado atual do registro de usuários afetados. <br><br> ![Registro de MFA](./media/active-directory-identityprotection/603.png "Registro de MFA") <br>


##Fluxos de mitigação de risco de entrada 

#### Fluxo de recuperação de entrada arriscada

Após um administrador configurar uma política para riscos de entrada, os usuários afetados são notificados ao tentarem entrar.

O fluxo de entrada arriscada tem duas etapas:

1. O usuário será informado de que algo incomum foi detectado na sua entrada, tal como entrar de um novo local, dispositivo ou aplicativo. <br> ![Correção](./media/active-directory-identityprotection/120.png "Correção") <br>

2. O usuário precisa provar sua identidade resolvendo um desafio de segurança. Se o usuário estiver registrado para autenticação multifator, ele precisará fazer uma viagem de ida e volta com um código de segurança para seu número de telefone. Como essa é apenas uma entrada arriscada e não uma conta comprometida, o usuário não precisará alterar a senha nesse fluxo. <br> ![Correção](./media/active-directory-identityprotection/121.png "Correção") <br>
 
#### Entrada arriscada bloqueada
Os administradores podem optar por definir uma política de Entrada Arriscada para bloquear os usuários ao entrar dependendo do nível de risco. Para ser desbloqueado, os usuários finais devem contatar um administrador ou o suporte técnico, ou então pode tentar entrar de um dispositivo ou local familiar. Não é possível recuperar-se sozinho com uma solução de autenticação multifator neste caso.

<br> ![Correção](./media/active-directory-identityprotection/130.png "Correção") <br>
 
#### Registro de autenticação multifator

A melhor experiência do usuário para ambos, o fluxo de recuperação de conta comprometida e o fluxo de entrada arriscado, é quando o usuário pode recuperar-se automaticamente. Se um usuário estiver registrado para autenticação multifator, ele já terá um número de telefone associado à sua conta que pode ser usada para resolver os desafios de segurança. Não é necessário qualquer envolvimento do suporte técnico ou do administrador para recuperar-se do comprometimento da conta. Por isso, é altamente recomendado registra seus usuários para autenticação multifator.

Os administradores podem:

- definir uma política que exige que os usuários configurem suas contas para verificação de segurança adicional. 
- permitir que o registro de autenticação multifator seja ignorado por até 30 dias, caso desejem oferecer um período de carência antes de registrar-se.

 
 <br> ![Correção](./media/active-directory-identityprotection/140.png "Correção") <br> <br> ![Correção](./media/active-directory-identityprotection/141.png "Correção") <br> <br> ![Correção](./media/active-directory-identityprotection/142.png "Correção") <br>

 

#### Registro da autenticação multifator durante a entrada arriscada

É importante que os usuários registrem-se para autenticação multifator para que estejam preparados e consigam resolver os desafios de segurança. Se um usuário não estiver registrado para a autenticação multifator, porém a política exige que ele esteja, ele pode receber a solicitação para registrar-se durante uma entrada arriscada. Isso significa que um invasor pode acabar recebendo uma solicitação para adicionar um número de telefone em vez do usuário válido. <br> Para evitar essa situação, exija que os usuários registrem-se para a autenticação multifator assim que possível, de maneira que um número de telefone já fique associado à conta deles caso sejam comprometidos. Como alternativa, os administradores podem bloquear completamente usuários comprometido que não estão registrados para autenticação multifator.

 <br> ![Correção](./media/active-directory-identityprotection/150.png "Correção") <br> <br> ![Correção](./media/active-directory-identityprotection/151.png "Correção") <br>






 
## Manual

### Simulação de Eventos de Risco

Esta seção fornecerá uma visão geral da simulação dos seguintes tipos de evento de risco:

- Entradas de endereços IP anônimos (fácil)

- Entradas de locais desconhecidos (moderado)

- Viagem impossível a locais atípicos (difícil)

Outros eventos de risco não podem ser simulados de maneira segura.


#### Entradas de endereços IP anônimos

Esse tipo de evento de risco identifica os usuários que entraram com êxito de um endereço IP que foi identificado como um endereço IP de proxy anônimo. Esses proxies geralmente são usados por usuários que desejam ocultar o endereço IP de seu dispositivo e podem ser usados com objetivos mal-intencionados.

Para simular uma entrada de um IP anônimo, siga estas etapas:

1.	Baixe o [Tor Browser](https://www.torproject.org/projects/torbrowser.html.en)
2.	Usando o Tor Browser, navegue para https://myapps.microsoft.com   
3.	Insira as credenciais da conta que você deseja exibir nas Entradas do relatório de endereços IP anônimos

Pronto. A entrada deve ser exibida no Identity Protection dentro de cinco minutos.


####Entradas de locais desconhecidos
O risco de locais desconhecidos é um mecanismo de avaliação de entrada em tempo real que considera locais de entrada (IP, Latitude/Longitude e ASN) para determinar os locais novos/desconhecidos. O sistema armazena IPs, Latitude/Longitude e ASNs anteriores de um usuário e considera-os como locais "familiares". Um local de entrada é considerado desconhecido se não corresponder a nenhum dos locais familiares existentes. O sistema tem um período inicial de aprendizado de 14 dias, durante o qual ele não sinaliza nenhum local novo como desconhecido. O sistema também ignora entradas de dispositivos conhecidos e locais que são geograficamente próximos de uma localização familiar existente.

Para simular locais não familiares, você terá que entrar de um local e um dispositivo nunca utilizado para entrada na conta. Passo a passo:

1.	Escolha uma conta com um histórico de entrada de pelo menos 14 dias 

2.	Realize uma das seguintes opções:
	
    a. Usando uma VPN, navegue até [https://myapps.microsoft.com](https://myapps.microsoft.com) e insira as credenciais da conta para qual você deseja simular o evento de risco

    b. Peça a um colega de um local diferente entrar usando as credenciais da conta (isso não é recomendado)

Pronto. A entrada deve ser exibida no Identity Protection dentro de cinco minutos.
 
#### Viagem impossível a um local atípico
É difícil simular a condição de viagem impossível porque o algoritmo usa o aprendizado de máquina para eliminar falsos positivos, tais como viagens impossíveis de dispositivos conhecidos ou entradas de VPNs usadas por outros usuários no diretório. Além disso, o algoritmo exige um histórico de entrada de três a 14 dias para o usuário antes de começar a gerar eventos de risco.

1.	Usando o seu navegador padrão, navegue para [https://myapps.microsoft.com](https://myapps.microsoft.com)  

2.	Insira as credenciais da conta para a qual você deseja gerar um evento de risco de viagem impossível

3.	Agora altere o agente do usuário. Você pode alterar o agente do usuário no Internet Explorer nas Ferramentas de Desenvolvedor, ou então no Firefox ou Chrome usando um complemento de alternador de agente do usuário

4.	Agora altere seu endereço IP. Você pode alterar seu endereço IP usando uma VPN, o complemento Tor ou criando um novo computador no Azure em um datacenter diferente

5.	Entre em [https://myapps.microsoft.com](https://myapps.microsoft.com) usando as mesmas credenciais de antes, alguns minutos após a entrada anterior

A entrada deve ser exibida no Identity Protection dentro de duas a quatro horas. No entanto, devido aos complexos modelos de aprendizado de máquina envolvidos, há uma chance que isso será não captado. É aconselhável replicar essas etapas para várias contas do Azure AD.


#### Simulação de vulnerabilidades 
Vulnerabilidades são pontos fracos no seu ambiente do Azure AD que podem ser explorados por um ator maligno. Atualmente, três tipos de vulnerabilidades são exibidas no Azure AD Identity Protection que aproveitam os outros recursos do Azure AD. Essas vulnerabilidades serão exibidas no Identity Protection automaticamente depois que esses recursos são configurados.

-	Azure AD [Multi-Factor Authentication?](../multi-factor-authentication/multi-factor-authentication.md)
-	Azure AD [Cloud App Discovery](active-directory-cloudappdiscovery-whatis.md).
-	Azure AD [Privileged Identity Management](active-directory-privileged-identity-management-configure.md). 



###Políticas de Acesso Condicional Baseadas em Risco
####Risco de comprometimento do usuário

**Para testar o Risco de comprometimento do usuário, execute as seguintes etapas**:

1.	Entre em [https://portal.azure.com](https://portal.azure.com) com as credenciais de administrador global para seu locatário.

2.	Navegue para **Identity Protection**.

3.	Na folha principal do Azure AD Identity Protection, clique em **Configurações**.

4.	Na folha **Configurações do Portal**, em **Regras de segurança**, clique em **Risco de comprometimento do usuário**.

5.	Na folha **Risco de Entrada**, desative **Habilitar regra** e clique em **Salvar** configurações.

6.	Para uma determinada conta de usuário, simule um evento de risco de locais desconhecidos ou IP anônimo. Isso elevará o nível de risco desse usuário para **Médio**.

7.	Aguarde alguns minutos e verifique se o nível desse usuário é **Médio**.

8.	Vá para a folha **Configurações do Portal**.

9.	Na folha **Risco de Comprometimento do Usuário**, selecione **Ativado** em **Habilitar regra**.

10.	Selecione uma das seguintes opções:

    a. Para bloquear, selecione **Médio** em **Bloquear Entrada**

    b. Para impor a alteração de senha de segurança, selecione **Médio** em **Exigir autenticação multifator**.

13.	Clique em **Salvar**.

14. Agora você pode testar o acesso condicional baseado em risco ao entrar usando um usuário com um nível de risco elevado. Se o risco do usuário for Médio, sua entrada será bloqueada ou você será forçado a alterar a senha, dependendo da política definida. <br><br> ![Manual](./media/active-directory-identityprotection/201.png "Manual") <br>

 
####Risco de entrada

 


Para testar o Risco de entrada, execute as seguintes etapas:

1.	Entre em [https://portal.azure.com ](https://portal.azure.com) com as credenciais de administrador global para seu locatário.

2.	Navegue para **Identity Protection**.

3.	Na folha principal do **Azure AD Identity Protection**, clique em **Configurações**.

4.	Na folha **Configurações do Portal**, em **Regras de segurança**, clique em **Risco de entrada**.

5.	Na folha **Risco de Entrada**, selecione **Ativado** em **Habilitar regra**.

7.	Selecione uma das seguintes opções:

    a. Para bloquear, selecione **Médio** em **Bloquear Entrada**

    b. Para impor a alteração de senha de segurança, selecione **Médio** em **Exigir autenticação multifator**.

8.	Para bloquear, selecione Médio em Bloquear Entrada...

9.	Para impor a autenticação multifator, selecione Médio em Exigir autenticação multifator...

10.	Clique em **Save**.

11.	Agora você pode testar o acesso condicional baseado em risco simulando eventos de risco de locais desconhecidos ou IP anônimo, ambos considerados eventos de risco Médio.

<br> ![Manual](./media/active-directory-identityprotection/200.png "Manual") <br>


## Consulte também

 - [Azure AD e Identity Show: Preview do Identity Protection](https://channel9.msdn.com/Series/Azure-AD-Identity/Azure-AD-and-Identity-Show-Identity-Protection-Preview)
 - [O glossário do Identity Protection](active-directory-identityprotection-glossary.md)

<!---HONumber=AcomDC_0302_2016-->