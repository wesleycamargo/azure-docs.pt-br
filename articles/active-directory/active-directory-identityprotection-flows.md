<properties
	pageTitle="Fluxos do Azure Active Directory Identity Protection | Microsoft Azure"
	description="Fornece uma visão geral da experiência do usuário quando o Identity Protection tiver atenuado ou corrigido um usuário ou quando a autenticação multifator for exigida por uma política."
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
	ms.date="03/07/2016"
	ms.author="markvi"/>

#Fluxos do Azure Active Directory Identity Protection

Com o Azure Active Directory Identity Protection, é possível:

- exigir o registro de usuários na autenticação multifator

- lidar com entradas arriscadas e usuários comprometidos

A resposta do sistema para esses problemas tem um impacto na experiência de entrada de um usuário, pois apenas entrar diretamente fornecendo um nome de usuário e uma senha não será mais possível. São necessárias etapas adicionais para fazer com que um usuário volte ao trabalho com segurança.

Este tópico fornece uma visão geral da experiência de entrada de um usuário em relação a todos os casos que podem ocorrer.

**Autenticação multifator**

- Registro de autenticação multifator



**Entrada em risco**

- Recuperação de entrada arriscada

- Entrada arriscada bloqueada

- Registro da autenticação multifator durante a entrada arriscada
 

**Usuário em risco**

- Recuperação de conta comprometida

- Conta comprometida bloqueada




## Registro de autenticação multifator

A melhor experiência do usuário para ambos, o fluxo de recuperação de conta comprometida e o fluxo de entrada arriscado, é quando o usuário pode recuperar-se automaticamente. Caso os usuários estejam registrados na autenticação multifator, eles já terão um número de telefone associado às suas contas, que pode ser usado para resolver os desafios de segurança. Não é necessário qualquer envolvimento do suporte técnico ou do administrador para recuperar-se do comprometimento da conta. Por isso, é altamente recomendado registra seus usuários para autenticação multifator.

Os administradores podem:

- definir uma política que exige que os usuários configurem suas contas para verificação de segurança adicional. 
- permitir que o registro de autenticação multifator seja ignorado por até 30 dias, caso desejem oferecer um período de carência antes de registrar-se.

**O registro na autenticação multifator tem três etapas:**

1. Na primeira etapa, o usuário recebe uma notificação sobre a necessidade de configurar a conta para a autenticação multifator.<br><br> ![Correção](./media/active-directory-identityprotection-flows/140.png "Correção") <br>


2. Para configurar a autenticação multifator, é necessário informar ao sistema como você deseja ser contatado.<br><br> ![Correção](./media/active-directory-identityprotection-flows/141.png "Correção") <br>
 
3. O sistema envia um desafio para você e você precisa respondê-lo.<br><br> ![Correção](./media/active-directory-identityprotection-flows/142.png "Correção") <br>

 



## Recuperação de entrada arriscada

Após um administrador configurar uma política para riscos de entrada, os usuários afetados são notificados ao tentarem entrar.

**O fluxo de entrada arriscada tem duas etapas:**

1. O usuário é informado de que algo incomum foi detectado em sua entrada, como a entrada de um novo local, dispositivo ou aplicativo.<br><br> ![Correção](./media/active-directory-identityprotection-flows/120.png "Correção") <br>

2. O usuário precisa provar sua identidade resolvendo um desafio de segurança. Se o usuário estiver registrado para autenticação multifator, ele precisará fazer uma viagem de ida e volta com um código de segurança para seu número de telefone. Como essa é apenas uma entrada arriscada e não uma conta comprometida, o usuário não precisará alterar a senha nesse fluxo.<br><br> ![Correção](./media/active-directory-identityprotection-flows/121.png "Correção") <br>



 
## Entrada arriscada bloqueada
Os administradores podem optar por definir uma política de Entrada Arriscada para bloquear os usuários ao entrar dependendo do nível de risco. Para ser desbloqueado, os usuários finais devem contatar um administrador ou o suporte técnico, ou então pode tentar entrar de um dispositivo ou local familiar. A autorrecuperação com uma resolução de autenticação multifator não é uma opção neste caso.<br><br> ![Correção](./media/active-directory-identityprotection-flows/130.png "Correção") <br>



## Registro da autenticação multifator durante a entrada arriscada

É importante que os usuários registrem-se para autenticação multifator para que estejam preparados e consigam resolver os desafios de segurança. Se um usuário não estiver registrado para a autenticação multifator, porém a política exige que ele esteja, ele pode receber a solicitação para registrar-se durante uma entrada arriscada. Isso significa que um invasor pode acabar recebendo uma solicitação para adicionar um número de telefone em vez do usuário válido.

Para evitar essa situação, exija que os usuários se registrem na autenticação multifator assim que possível, de modo que um número de telefone já seja associado à conta deles caso sejam comprometidos. Como alternativa, os administradores podem bloquear completamente usuários comprometido que não estão registrados para autenticação multifator.

**O registro na autenticação multifator durante uma entrada arriscada tem duas etapas:**

1. O usuário é notificado de que a conta foi colocada em risco.<br><br> ![Correção](./media/active-directory-identityprotection-flows/150.png "Correção") <br>

2. O processo de registro na autenticação multifator é iniciado.<br><br> ![Correção](./media/active-directory-identityprotection-flows/151.png "Correção") <br>

Para obter as próximas etapas, veja [Registro na autenticação multifator](#multi-factor-authentication-registration)




## Recuperação de conta comprometida

Após configurar uma política de segurança de risco do usuário, os usuários que atendem o usuário nível de risco especificado na política (sendo, portanto, considerados comprometidos) devem passar pelo fluxo de recuperação de comprometimento de usuário antes de poderem entrar.

**O fluxo de recuperação de comprometimento do usuário tem três etapas:**

1. O usuário é informado de que a segurança da conta está em risco devido à atividade suspeita ou credenciais vazadas.

<br> ![Correção](./media/active-directory-identityprotection-flows/101.png "Correção") <br>

2.	O usuário precisa provar sua identidade resolvendo um desafio de segurança. Se o usuário estiver registrado para autenticação multifator, ele pode recuperar-se automaticamente do comprometimento. Eles precisarão fazer uma viagem de ida e volta de um código de segurança para o número de telefone. 

<br> ![Correção](./media/active-directory-identityprotection-flows/110.png "Correção") <br>


3.	Por fim, o usuário será forçado a alterar a senha após outra pessoa ter tido acesso à sua conta. Veja as capturas de tela desta experiência abaixo.
 
<br> ![Correção](./media/active-directory-identityprotection-flows/111.png "Correção") <br>



## Conta comprometida bloqueada 

Para desbloquear um usuário que foi bloqueado por uma política de segurança de risco do usuário, este deve contatar um administrador ou o suporte técnico. Não é possível recuperar-se sozinho com uma solução de autenticação multifator neste caso.

<br> ![Correção](./media/active-directory-identityprotection-flows/104.png "Correção") <br>



 
## Redefinir senha

Caso os usuários comprometidos sejam impedidos de entrar, um administrador poderá gerar uma senha temporária para eles. Os usuários terão de alterar suas senhas durante a próxima entrada.

<br> ![Correção](./media/active-directory-identityprotection-flows/160.png "Correção") <br>


 




 

## Consulte também

- [Azure Active Directory Identity Protection](active-directory-identityprotection.md) 

<!---HONumber=AcomDC_0309_2016-->