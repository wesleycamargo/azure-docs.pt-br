<properties 
	pageTitle="Protegendo os recursos de nuvem usando o Azure Multi-Factor Authentication e o AD FS" 
	description="Esta é a página do Azure Multi-Factor Authentication que descreve como começar a usar o Azure MFA e o AD FS na nuvem." 
	services="multi-factor-authentication" 
	documentationCenter="" 
	authors="billmath" 
	manager="terrylan" 
	editor="bryanla"/>

<tags 
	ms.service="multi-factor-authentication" 
	ms.workload="identity" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="06/02/2015" 
	ms.author="billmath"/>

# Protegendo os recursos de nuvem usando o Azure Multi-Factor Authentication e o AD FS

Se sua organização for federada com o Active Directory do Azure e você tiver recursos que são acessados pelo AD do Azure, será possível usar o Azure Multi-Factor Authentication ou os Serviços de Federação do Active Directory para proteger esses recursos. Use os procedimentos a seguir para proteger os recursos do Active Directory do Azure com o Azure Multi-Factor Authentication ou os Serviços de Federação do Active Directory.

## Para proteger recursos do AD do Azure usando o AD FS, siga este procedimento: 



1. Use as etapas descritas em [ativar autenticação multifator](multi-factor-authentication-get-started-cloud/#turn-on-multi-factor-authentication-for-users) para que os usuários habilitem uma conta.
2. Use o procedimento a seguir para configurar uma regra de declaração:

<center>![Nuvem](./media/multi-factor-authentication-get-started-adfs-cloud/adfs1.png)</center>

- 	Inicie o Console de gerenciamento do AD FS.
- 	Navegue até Terceira Parte Confiável e clique com o botão direito do mouse em Terceira Parte Confiável. Selecione Editar Regras de Declaração...
- 	Clique em Adicionar Regra...
- 	No menu suspenso, selecione Enviar Declarações Usando uma Regra Personalizada e clique em Avançar.
- 	Insira um nome para a regra de declaração.
- 	Em Regra personalizada: adicione o seguinte:


		=> issue(Type = "http://schemas.microsoft.com/claims/authnmethodsreferences", Value = "http://schemas.microsoft.com/claims/multipleauthn");

	Declaração correspondente:

		<saml:Attribute AttributeName="authnmethodsreferences" AttributeNamespace="http://schemas.microsoft.com/claims">
		<saml:AttributeValue>http://schemas.microsoft.com/claims/multipleauthn</saml:AttributeValue>
		</saml:Attribute>
- Clique em OK. Clique em Concluir. Feche o Console de gerenciamento do AD FS.

Os usuários podem concluir a conexão usando o método local (como o cartão inteligente).


 

<!---HONumber=July15_HO2-->