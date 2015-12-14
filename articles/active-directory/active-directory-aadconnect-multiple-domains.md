<properties
	pageTitle="Vários domínios do Azure AD Connect"
	description="Este documento descreve a instalação e a configuração de vários domínios de nível superior com o O365 e o Azure AD."
	services="active-directory"
	documentationCenter=""
	authors="billmath"
	manager="stevenpo"
	editor="curtand"/>

<tags
	ms.service="active-directory"
	ms.workload="identity"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="12/02/2015"
	ms.author="billmath"/>

#Suporte para vários domínios

Muitos de vocês perguntaram como é possível configurar vários domínios e subdomínios de nível superior do Office 365 ou Azure AD com federação. Embora a maior parte disso possa ser feito de maneira bastante simples, devido a algumas coisas que fazemos nos bastidores, há algumas dicas e truques que você deve saber para evitar os problemas a seguir

- Mensagens de erro ao tentar configurar domínios adicionais para federação
- Os usuários em subdomínios não conseguem fazer logon depois que vários domínios de nível superior foram configurados para federação

## Vários domínios de nível superior
Explicarei a instalação de uma organização de exemplo chamada contoso.com que deseja ter um domínio adicional chamado fabrikam.com.

Digamos que, em meu sistema local, eu tenha o AD FS configurado com o nome de serviço de federação fs.jenfield.com.

Na primeira vez que eu me inscrever no Office 365 ou Azure AD, posso optar por configurar contoso.com como o meu primeiro domínio de logon. Posso fazer isso por meio do Azure AD Connect ou do Powershell do Azure AD usando New-MsolFederatedDomain.

Feito isso, vamos examinar os valores padrão das duas das novas propriedades de configuração nova do domínio do Azure AD (elas podem ser consultadas por meio de Get-MsolDomainFederationSettings):

| Nome da Propriedade | Valor | Descrição|
| ----- | ----- | -----|
|IssuerURI | http://fs.jenfield.com/adfs/services/trust| Embora isso se pareça com uma URL, essa propriedade é realmente apenas um nome para o sistema de autenticação local e, portanto, o caminho não precisa ser resolvido em nada. Por padrão, o Azure AD define isso como o valor do identificador do serviço de federação em minha configuração local do AD FS.
|PassiveClientSignInUrl|https://fs.jenfield.com/adfs/ls/|This é o local para o qual as solicitações de conexão passiva serão enviadas e ele é resolvido em meu sistema real do AD FS. Na verdade, existem várias propriedades de “* Url”, mas precisamos apenas examinar um exemplo para ilustrar a diferença entre essa propriedade e um URI, como o IssuerURI.

Agora, imagine que adiciono meu segundo domínio fabrikam.com. Além disso, posso fazer isso executando o assistente do Azure AD Connect uma segunda vez ou por meio do PowerShell.

Se eu tentar adicionar o segundo domínio como federado usando o PowerShell do Azure AD, receberei um erro.

Isso ocorre devido a uma restrição no Azure AD, em que o IssuerURI não pode ter o mesmo valor para mais de um domínio. Para superar essa limitação, você deve usar um IssuerURI diferente para o novo domínio. De fato, isso é o que o parâmetro SupportMultipleDomain faz. Quando usado com os cmdlets para configurar a federação (New-, Convert-, and Update-MsolFederatedDomain), esse parâmetro fará com que o Azure AD configure o IssuerURI com base no nome do domínio que deve ser exclusivo entre os locatários no Azure AD; portanto, ele deve ser exclusivo. Há também uma alteração às regras de declaração, mas falarei sobre isso daqui a pouco.

Portanto, no Powershell, se eu adicionar fabrikam.com usando o parâmetro SupportMultipleDomain,

    PS C:\>New-MsolFederatedDomain -DomainName fabrikam.com –SupportMultipleDomain

Obterei a seguinte configuração no Azure AD:

- NomeDomínio: fabrikam.com
- IssuerURI: http://fabrikam.com/adfs/services/trust 
- PassiveClientSignInUrl: https://fs.jenfield.com/adfs/ls/ 

Observe que, enquanto o IssuerURI foi definido como um valor com base no meu domínio e, portanto, exclusivo, os valores da URL do ponto de extremidade ainda estão configurados para apontar para o serviço de federação em fs.jenfield.com, exatamente como estão configurados para o domínio original contoso.com. Portanto, todos os domínios continuarão apontando para o mesmo sistema do AD FS.

A outra coisa que SupportMultipleDomain faz é garantir que o sistema do AD FS incluirá o valor de Issuer correto nos tokens emitidos para o Azure AD. Ele faz isso usando a parte do domínio do UPN dos usuários e configurando isso como o domínio no issuerURI, ou seja, https://{upn suffix}/adfs/services/trust. Dessa forma, durante a autenticação no Azure AD ou Office 365, o elemento Issuer no token do usuário é usado para localizar o domínio no Azure AD. Se uma correspondência não for encontrada, a autenticação falhará.

Por exemplo, se a UPN de um usuário for johndoe@fabrikam.com, o elemento Emissor nas emissões de token do AD FS será definido como http://fabrikam.com/adfs/services/trust. Isso corresponderá à configuração do Azure AD, e a autenticação será bem-sucedida.

Veja abaixo a regra de declaração personalizada que implementa essa lógica:

    c:[Type == "http://schemas.xmlsoap.org/claims/UPN"] => issue(Type =   "http://schemas.microsoft.com/ws/2008/06/identity/claims/issuerid", Value = regexreplace(c.Value, ".+@(?<domain>.+)", "http://${domain}/adfs/services/trust/"));

Agora em minha configuração, registrei contoso.com primeiro sem a opção supportMultipleDomains e com o valor padrão de IssuerURI. Quando adiciono fabrikam.com, na verdade, preciso garantir que contoso.com também está configurado para usar a opção SupportMultipleDomains, já que a atualização da regra de declaração não enviará mais o IssuerURI padrão e a autenticação falhará devido a um erro de correspondência de IssuerURI. Não se preocupe; nós o avisaremos sobre isso antes de permitir que você use a opção supportMultipleDomain em um domínio diferente.

Para corrigir isso, precisamos atualizar a configuração do domínio contoso.com também. O assistente do Azure AD Connect é um ótimo em descobrir quando o que descrevemos acima precisa ser feito e em fazer a coisa certa quando você estiver adicionando um segundo domínio. Na primeira vez, se você já estiver na configuração SupportMultipleDomain, não substituiremos você.

No PowerShell, você precisa fornecer a opção SupportMultipleDomain manualmente.

Veja abaixo para obter todas as etapas detalhadas para fazer a transição de um domínio único para vários domínios.

Feito isso, teremos então a configuração para dois domínios no Azure AD:

- NomeDomínio: contoso.com
- IssuerURI: http://contoso.com/adfs/services/trust 
- PassiveClientSignInUrl: https://fs.jenfield.com/adfs/ls/ 
- NomeDomínio: fabrikam.com
- IssuerURI: http://fabrikam.com/adfs/services/trust 
- PassiveClientSignInUrl: https://fs.jenfield.com/adfs/ls/ 

O logon federado para os usuários dos domínios contoso.com e fabrikam.com agora funcionará. Resta apenas um problema: o logon para os usuários em subdomínios.

##Subdomínios
Digamos que eu adicione meu subdomínio sub.contoso.com ao Azure AD. Devido à maneira como o Azure AD gerencia domínios, o subdomínio herdará as configurações do domínio pai, neste caso contoso.com. Isso significa que o IssuerURI para user@sub.contoso.com precisará ser http://contoso.com/adfs/services/trust. No entanto, a regra padrão implementada acima para

O Azure AD gerará um token com um emissor como http://sub.contoso.com/adfs/services/trust, que não corresponderá ao valor obrigatório do domínio e a autenticação falhará. Felizmente, temos uma solução alternativa para isso também, mas ela não é bem incorporada em nossas ferramentas. Você precisa atualizar a confiança da terceira parte confiável do AD FS do Microsoft Online manualmente.

Você precisa configurar a regra de declaração personalizada para que ela ignore todos os subdomínios do sufixo UPN do usuário ao construir o valor de Issuer personalizado. Você pode encontrar as etapas exatas para fazer isso nas etapas abaixo.

Portanto, em resumo, você pode ter vários domínios com nomes diferentes, bem como subdomínios, todos federados ao servidor do AD FS; bastam algumas etapas adicionais para garantir que os valores de Issuer estejam definidos corretamente para todos os usuários.

<!---HONumber=AcomDC_1203_2015-->