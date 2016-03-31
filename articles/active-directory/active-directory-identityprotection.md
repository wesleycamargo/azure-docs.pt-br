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
	ms.date="03/09/2016"
	ms.author="markvi"/>

#Azure Active Directory Identity Protection 

O Azure Active Directory Identity Protection é um serviço de segurança que fornece uma exibição consolidada dos eventos de risco e das possíveis vulnerabilidades que afetam as identidades da sua organização. A Microsoft tem protegido identidades baseadas em nuvem há mais de uma década e, com o Azure AD Identity Protection, ela está disponibilizando esses mesmos sistemas de proteção para os clientes empresariais. O Identity Protection aproveita os recursos de detecção de anomalias existentes do Azure AD (disponíveis por meio dos Relatórios de Atividade Anômala do Azure AD) e apresenta novos tipos de evento de risco que pode detectar anomalias em tempo real.

## Limitações da visualização atual
Esta seção lista as limitações que se aplicam à visualização atual do Azure Active Directory Identity Protection.

### Limitação de país ou região

A visualização do Azure Active Directory Identity Protection está disponível atualmente apenas para diretórios com um valor **país ou região** **Estados Unidos**. <br><br> ![Correção](./media/active-directory-identityprotection/222.png "Correção")


### Identity Protection e domínios federados

A visualização do Azure Active Directory Identity Protection tem as seguintes limitações em conjunto com domínios federados:

- As políticas de segurança não funcionam para domínios federados

- Os eventos de risco não são detectados para domínios federados

##Introdução

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

- Política para exigir o registro para autenticação multifator


## Detecção e Risco

### Eventos de risco

Eventos de risco são eventos que foram marcados como suspeitos pelo Identity Protection e indicam que uma identidade pode ter sido comprometida. Para obter uma lista completa de eventos de risco, confira [Tipos de eventos de risco detectados pelo Azure Active Directory Identity Protection](active-directory-identityprotection-risk-events-types.md).

Alguns desses eventos de risco estão disponíveis por meio dos relatórios de Atividade Anômala do Azure AD no Portal de Gerenciamento do Azure. A tabela abaixo lista os diversos tipos de evento de risco e o relatório de Atividade **Anômala do Azure AD** correspondente. A Microsoft continua a investir nesta área e planeja melhorar continuamente a precisão da detecção dos eventos de risco existentes e adicionar novos tipos de evento de risco continuamente.



| Tipo de Evento de Risco do Identity Protection | Relatório de Atividade Anômala do Azure AD Correspondente |
| :-- | :-- |
| Credenciais vazadas | Usuários com credenciais insuficientes |
| Viagem impossível a locais atípicos |	Atividades de entrada irregulares |
| Entradas de dispositivos infectados | Entradas de dispositivos possivelmente infectados |
| Entradas de endereços IP anônimos | Entradas de fontes desconhecidas |
| Entradas de endereços IP com atividade suspeita |	Entradas de endereços IP com atividade suspeita |
| Entradas de locais desconhecidos | - |
| Eventos de bloqueio (que não estão na preview pública) | - |

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


## Investigação
Sua jornada pelo Identity Protection normalmente inicia no Painel do Identity Protection.

<br><br> ![Correção](./media/active-directory-identityprotection/29.png "Correção") <br>

O painel concede acesso a:
 
- relatórios, como **Usuários sinalizados por riscos**, **Eventos de risco** e **Vulnerabilidades**
- configurações como a definição das suas **Políticas de Segurança**, **Notificações** e **registro de autenticação multifator**
 

Este é normalmente seu ponto de partida para investigação, que é o processo de revisão de atividades, logs e outras informações relevantes relacionadas a um evento de risco para decidir se as etapas de correção e mitigação são necessárias, como a identidade foi comprometida e entender como ela foi usada.

Você pode vincular suas atividades de investigação para as [notificações](active-directory-identityprotection-notifications.md) que o Azure Active Directory Protection envia por email.

As seções a seguir fornecerão mais detalhes e as etapas que estão relacionadas a uma investigação.



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

Na maioria dos casos, você tomará ações de correção, como uma redefinição de senha de segurança, para fechar automaticamente os eventos de risco. No entanto, isso nem sempre é possível. <br> É o caso, por exemplo, quando:

- um usuário com eventos de risco ativo foi excluído
- uma investigação revela que um evento de risco relatado foi executado pelo usuário legítimo

Já que eventos de risco **Ativos** contribuem para o cálculo de risco do usuário, talvez seja necessário reduzir manualmente um nível de risco fechando eventos de risco manualmente. <br> Durante a investigação, você pode optar por executar uma das seguintes ações para alterar o status de um evento de risco:

<br> ![Ações](./media/active-directory-identityprotection/34.png "Ações") <br>

- **Resolver** - Se, após investigar um evento de risco, você tomou uma ação de correção apropriada fora do Identity Protection e acredita que o evento de risco deve ser considerado fechado, marque o evento como Resolvido. Eventos resolvidos definirão o status do evento de risco como Fechado e ele não contribuirá com o risco do usuário.

- **Marcar como falso positivo** - Em alguns casos, você pode investigar um evento de risco e descobrir que ele foi sinalizado incorretamente como uma situação arriscada. Você pode ajudar a reduzir o número de tais ocorrências marcando o evento de risco como falso positivo. Isso ajudará os algoritmos de aprendizado de máquina a melhorarem a classificação de eventos semelhantes no futuro. O status de eventos falso positivos é **Fechado** e não contribui mais com o risco do usuário.

- **Ignorar** - Se você não executou nenhuma ação de correção, mas deseja remover o evento de risco da lista ativa, marque um evento de risco como Ignorar e o status do evento será Fechado. Eventos ignorados não contribuem com o risco do usuário. Essa opção deve ser usada somente em circunstâncias incomuns.

- **Reativar** - Eventos de risco fechados manualmente (escolhendo **Resolver**, **Falso positivo** ou **Ignorar**) podem ser reativados, definindo o status do evento novamente como **Ativo**. Eventos de risco reativados contribuem no cálculo de nível de risco do usuário. Eventos de risco fechados por meio de correção (como redefinir uma senha de segurança) não podem ser reativados.

**Para abrir o diálogo de configurações relacionadas**:

1. Na folha **Azure AD Identity Protection**, clique em **Usuários com riscos sinalizados**. <br><br> ![Redefinição de senha manual](./media/active-directory-identityprotection/408.png "Redefinição de senha manual") <br>

2. Clique com o botão direito do mouse no usuário afetado. <br><br> ![Redefinição de senha manual](./media/active-directory-identityprotection/437.png "Redefinição de senha manual") <br>

## Corrigir eventos de risco do usuário

Uma correção é uma ação que visa proteger uma identidade ou um dispositivo que já sofreu comprometimento conhecido ou suspeito anteriormente. Uma correção restaura a identidade ou dispositivo para um estado seguro e resolve eventos de risco anteriores associados à identidade ou ao dispositivo.

Para corrigir os eventos de risco do usuário, você pode:

- executar uma redefinição de senha de segurança para corrigir eventos de risco do usuário manualmente 

- configurar uma política de segurança de risco do usuário para mitigar ou corrigir eventos de risco do usuário automaticamente

- recriar imagem no dispositivo infectado


### Redefinição de senha de segurança manual

Uma redefinição de senha de segurança é uma correção eficiente para muitos eventos de risco e quando executada, automaticamente fecha esses eventos de risco e recalcula o nível de risco do usuário. Você pode usar o painel do Identity Protection para iniciar uma redefinição de senha para um usuário arriscado.

A caixa de diálogo correspondente fornece dois métodos diferentes para redefinir uma senha:

**Redefinir senha** - selecione **Exigir que o usuário redefina a senha** para permitir que o usuário se recupere automaticamente se ele estiver registrado para autenticação multifator. Durante da próxima entrada do usuário, ele precisará resolver um desafio de autenticação multifator com êxito e em seguida, será forçado a alterar a senha. Essa opção não estará disponível se a conta de usuário não estiver registrada para autenticação multifator.

**Senha temporária** - selecione **Gerar uma senha temporária** para invalidar imediatamente a senha existente e criar uma nova senha temporária para o usuário. Envie a nova senha temporária para um endereço de email alternativo para o usuário ou para o gerente dele. Como a senha é temporária, o usuário precisará alterá-la após entrar.

<br> ![Política](./media/active-directory-identityprotection/71.png "Política") <br>

**Para abrir o diálogo de configurações relacionadas**:

1. Na folha **Azure AD Identity Protection**, clique em **Usuários com riscos sinalizados**. <br><br> ![Redefinição de senha manual](./media/active-directory-identityprotection/408.png "Redefinição de senha manual") <br>

2. Clique no usuário afetado <br><br> ![Redefinição de senha manual](./media/active-directory-identityprotection/404.png "Redefinição de senha manual") <br>

2. Clique em Redefinir senha <br><br>![Redefinição de senha manual](./media/active-directory-identityprotection/420.png "Redefinição de senha manual") <br>





## Política de segurança de risco do usuário

Uma política de segurança de risco do usuário é uma política de acesso condicional que avalia o nível de risco de um usuário específico e aplica ações de correção e mitigação com base em regras e condições predefinidas. <br><br> ![Política de risco do usuário](./media/active-directory-identityprotection/500.png "Política de risco do usuário") <br>

O Azure AD Identity Protection ajuda a gerenciar a mitigação e correção de usuários sinalizados para riscos, permitindo:

- definir os usuários e grupos aos quais a política se aplica:
<br><br>
![Política de risco do usuário](./media/active-directory-identityprotection/501.png "Política de risco do usuário")
<br>

- definir o limite de nível de risco do usuário (baixo, médio ou alto) que dispara uma alteração de senha:
<br><br>
![Política de risco do usuário](./media/active-directory-identityprotection/502.png "Política de risco do usuário")
<br>

- definir o limite de nível de risco do usuário (baixo, médio ou alto) que dispara o bloqueio de um usuário:
<br><br>
![Política de risco do usuário](./media/active-directory-identityprotection/503.png "Política de risco do usuário")
 <br>

- alternar o estado de sua política:
<br><br>
![Registro de MFA](./media/active-directory-identityprotection/403.png "Registro de MFA")
<br>

- examinar e avaliar o impacto de uma alteração antes de ativá-la:
<br><br>
![Política de risco do usuário](./media/active-directory-identityprotection/504.png "Política de risco do usuário")
<br>


Escolher um limite **Alto** reduz o número de vezes que uma política é disparada e minimiza o impacto para os usuários. No entanto, isso exclui usuários sinalizados com **Baixo** e **Médio** risco da política, o que pode não proteger as identidades ou os dispositivos que sofreram comprometimento conhecido ou suspeito.

Ao definir a política,

- exclua os usuários que tendem a gerar muitos falsos positivos (desenvolvedores e analistas de segurança)

- exclua os usuários em localidades em que não é viável habilitar a política (por exemplo, sem acesso à assistência técnica)

- use um limite **Alto** durante a distribuição inicial de política ou se você precisar minimizar os desafios encontrados pelos usuários finais.

- use um limite **Baixo** se sua organização exigir uma segurança maior. Selecionar um limite **Baixo** apresenta desafios de entrada do usuário adicionais, porém representa uma segurança maior.

O padrão recomendado na maioria das organizações é configurar uma regra para um limite **Médio** para atingir um equilíbrio entre segurança e usabilidade.

Para obter uma visão geral da experiência do usuário relacionada, confira:

- [Fluxo de recuperação de conta comprometida](active-directory-identityprotection-flows.md#compromised-account-recovery).  

- [Fluxo de conta comprometida bloqueada](active-directory-identityprotection-flows.md#compromised-account-blocked).


**Para abrir o diálogo de configurações relacionadas**:

1. Na folha **Azure AD Identity Protection**, clique em **Configurações**.<br><br> ![Política de risco do usuário](./media/active-directory-identityprotection/401.png "Política de risco do usuário") <br>

2. Na seção **Políticas de Segurança**, clique em **Risco de usuário**. <br><br> ![Política de risco do usuário](./media/active-directory-identityprotection/500.png "Política de risco do usuário") <br>






## Mitigar eventos de risco do usuário
Os administradores podem definir uma política de segurança de risco do usuário para bloquear os usuários ao entrar dependendo do nível de risco.

Bloquear a entrada:
 
- impede a geração de novos eventos de risco do usuário para o usuário afetado

- Permite aos administradores corrigir os eventos de risco que afetam a identidade do usuário manualmente, bem como restaurá-la para um estado seguro



## O que é um nível de risco de entrada?

Um nível de risco de entrada é uma indicação (Alta, Média ou Baixa) da probabilidade de que outra pessoa esteja tentando autenticar com a identidade do usuário. O nível de risco de entrada é avaliado na hora do logon e considera os eventos de risco e indicadores detectados em tempo real dessa entrada específica.

## Mitigação de eventos de risco de entrada 
Uma mitigação é uma ação que visa limitar a capacidade de um invasor explorar uma identidade ou um dispositivo comprometidos sem restaurá-los para um estado seguro. Uma mitigação não resolve eventos de risco de entrada anteriores associados à identidade ou ao dispositivo.

Você pode usar o acesso condicional no Azure AD Identity Protection para mitigar automaticamente os eventos de risco de entrada. Ao usar essas políticas, considere o nível de risco do usuário ou da entrada para bloquear entradas arriscadas ou exigir que o usuário realize a autenticação multifator. Essas ações podem impedir que um invasor explore uma identidade roubada para causar danos, fazendo você ganhar algum tempo para proteger a identidade.


## Política de segurança de risco de entrada

Uma política de segurança de risco de entrada é uma política de acesso condicional que avalia o risco de uma entrada específica e aplica mitigações com base em regras e condições predefinidas. <br><br> ![Política de risco de entrada](./media/active-directory-identityprotection/700.png "Política de risco de entrada") <br>

O Azure AD Identity Protection ajuda a gerenciar a mitigação de entradas arriscadas, permitindo:

- definir os usuários e grupos aos quais a política se aplica:
<br><br>
![Política de risco de entrada](./media/active-directory-identityprotection/701.png "Política de risco de entrada")
<br>

- definir o limite do nível de risco de credenciais (baixo, médio ou alto) que dispara um desafio de autenticação multifator para as entradas afetadas: <br><br> ![Política de risco de entrada](./media/active-directory-identityprotection/702.png "Política de risco de entrada") <br>

- definir o limite de nível de risco de credenciais (baixo, médio ou alto) que bloqueia as entradas afetadas:
<br><br>
![Política de risco de entrada](./media/active-directory-identityprotection/703.png "Política de risco de entrada")
<br>

- alternar o estado de sua política:
<br><br>
![Registro de MFA](./media/active-directory-identityprotection/403.png "Registro de MFA")
 <br>

- examinar e avaliar o impacto de uma alteração antes de ativá-la:
<br><br>
![Política de risco de entrada](./media/active-directory-identityprotection/704.png "Política de risco de entrada")
<br>

 
Escolher um limite **Alto** reduz o número de vezes que uma política é disparada e minimiza o impacto para os usuários.<br> No entanto, isso exclui entradas sinalizadas com **Baixa** e **Médio** risco da política, o que pode não impedir que um invasor explore uma identidade comprometida.

Ao definir a política,

- exclua os usuários que não tem / não podem ter a autenticação multifator

- exclua os usuários em localidades em que não é viável habilitar a política (por exemplo, sem acesso à assistência técnica)

- exclua os usuários que tendem a gerar muitos falsos positivos (desenvolvedores e analistas de segurança)

- use um limite **Alto** durante a distribuição inicial de política ou se você precisar minimizar os desafios encontrados pelos usuários finais.

- use um limite **Baixo** se sua organização exigir uma segurança maior. Selecionar um limite **Baixo** apresenta desafios de entrada do usuário adicionais, porém representa uma segurança maior.

O padrão recomendado na maioria das organizações é configurar uma regra para um limite **Médio** para atingir um equilíbrio entre segurança e usabilidade.

 
A política de risco de entrada:

- é aplicada a todo o tráfego do navegador e entradas que usam autenticação moderna.
- Não é aplicada a aplicativos que usam protocolos de segurança mais antigos desabilitando o ponto de extremidade WS-Trust no IDP federado, como o ADFS.

A página **Eventos de Risco** no console do Identity Protection lista todos os eventos:

- aos quais essa política se aplica
- para os quais você pode examinar a atividade e determinar se a ação foi apropriada ou não 

Para obter uma visão geral da experiência do usuário relacionada, confira:

- [Recuperação de entrada arriscada](active-directory-identityprotection-flows.md#risky-sign-in-recovery) 

- [Entrada arriscada bloqueada](active-directory-identityprotection-flows.md#risky-sign-in-blocked)

- [Registro da autenticação multifator durante a entrada arriscada](active-directory-identityprotection-flows.md#multi-factor-authentication-registration-during-a-risky-sign-in)





**Para abrir o diálogo de configurações relacionadas**:

1. Na folha **Azure AD Identity Protection**, clique em **Configurações**.<br><br> ![Registro de MFA](./media/active-directory-identityprotection/401.png "Registro de MFA") <br>

1. Na seção **políticas de segurança**, clique em **Risco de entrada**. <br><br> ![Registro de MFA](./media/active-directory-identityprotection/700.png "Registro de MFA") <br>




## Política de registro de autenticação multifator

O Azure Multi-Factor Authentication é um método de verificar quem você é e que requer o uso de mais do que apenas um nome de usuário e uma senha. Ele fornece uma segunda camada de segurança para logons de usuário e transações. <br> Recomendamos exigir o Azure Multi-Factor Authentication para entradas de usuário porque:

- fornece autenticação forte com uma variedade de opções de verificação fácil

- desempenha um papel fundamental na preparação de sua organização para proteger e recuperar comprometimentos de conta

Para obter mais detalhes, veja [O que é o Azure Multi-Factor Authentication?](../multi-factor-authentication/multi-factor-authentication.md)


O Azure AD Identity Protection ajuda a gerenciar a implementação do registro de autenticação multifator configurando uma política que permite:

- exibir o status atual do registro:
<br><br>
![Registro de MFA](./media/active-directory-identityprotection/603.png "Registro de MFA")
<br>

- definir os usuários e grupos aos quais a política se aplica:
<br><br>
![Registro de MFA](./media/active-directory-identityprotection/601.png "Registro de MFA")
<br>

- definir por quanto tempo eles têm permissão para ignorar o registro:
<br><br>
![Registro de MFA](./media/active-directory-identityprotection/602.png "Registro de MFA")
<br>

- alternar o estado de sua política:
<br><br>
![Registro de MFA](./media/active-directory-identityprotection/403.png "Registro de MFA")
<br>

Para obter uma visão geral da experiência do usuário relacionada, confira:

- [Fluxo do registro de autenticação multifator](active-directory-identityprotection-flows.md#multi-factor-authentication-registration)  

- [Registro da autenticação multifator durante a entrada arriscada](active-directory-identityprotection-flows.md#multi-factor-authentication-registration-during-a-risky-sign-in).





**Para abrir o diálogo de configurações relacionadas**:

1. Na folha **Azure AD Identity Protection**, clique em **Configurações**.<br><br> ![Registro de MFA](./media/active-directory-identityprotection/401.png "Registro de MFA") <br>

2. Na seção **Multi-Factor Authentication**, clique em **Registro**. <br><br> ![Registro de MFA](./media/active-directory-identityprotection/402.png "Registro de MFA") <br>




## Consulte também

 - [Canal 9: Azure AD e Identity Show: visualização do Identity Protection](https://channel9.msdn.com/Series/Azure-AD-Identity/Azure-AD-and-Identity-Show-Identity-Protection-Preview)
 - [Tipos de eventos de risco detectados pelo Azure Active Directory Identity Protection](active-directory-identityprotection-risk-events-types.md)
 - [Vulnerabilidades detectadas pelo Azure Active Directory Identity Protection](active-directory-identityprotection-vulnerabilities.md)
 - [Notificações do Azure Active Directory Identity Protection](active-directory-identityprotection-notifications.md)
 - [Fluxos do Azure Active Directory Identity Protection](active-directory-identityprotection-flows.md)
 - [Guia estratégico do Azure Active Directory Identity Protection](active-directory-identityprotection-playbook.md)
 - [Glossário do Azure Active Directory Identity Protection](active-directory-identityprotection-glossary.md).

<!-----HONumber=AcomDC_0316_2016-->