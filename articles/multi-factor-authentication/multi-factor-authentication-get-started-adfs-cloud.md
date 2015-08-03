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

![Nuvem](./media/multi-factor-authentication-get-started-adfs-cloud/adfs1.png)

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

## IPs confiáveis para usuários federados
IPs confiáveis permitem aos administradores ignorar a autenticação multifator para endereço IP específico ou para usuários federados que têm as solicitações originadas em seu próprios intranet. As seções a seguir descrevem como configurar IPs confiáveis do Azure Multi-Factor Authentication com usuários federados e desviar a autenticação de multifatores quando uma solicitação se originar de dentro de uma intranet de usuários federados. Isso é conseguido por meio da configuração do AD FS para usar uma passagem ou filtrar um modelo de declaração de entrada com o tipo de declaração Dentro da rede corporativa. Este exemplo usa o Office 365 para a relação de confiança com terceira parte confiável.

### Configurar as regras de declarações do AD FS

A primeira coisa que precisamos fazer é configurar as declarações do AD FS. Estamos criando duas regras declarações: uma para o tipo de declaração Dentro da rede corporativa e um adicional para manter nossos usuários conectados.<ol>

<li>Abra o gerenciamento do AD FS.</li>
<li>À esquerda, selecione a relação de confiança com terceira parte confiável.</li>
<li>No meio, clique com o botão direito do mouse na plataforma de identidade Microsoft Office 365 e selecione **Editar Regras de Declaração...**</li>

![Nuvem](./media/multi-factor-authentication-get-started-adfs-cloud/trustedip1.png)

<li>Em Regras de Transformação de Emissão, clique em **Adicionar Regra**.</li>

![Nuvem](./media/multi-factor-authentication-get-started-adfs-cloud/trustedip2.png)

<li>No Assistente Adicionar Regra de Declaração de Transformação, selecione Passar ou filtrar uma Declaração de Entrada na lista e clique em Avançar.</li>

![Nuvem](./media/multi-factor-authentication-get-started-adfs-cloud/trustedip3.png)

<li>Na caixa ao lado do nome da regra de declaração, nomeie a regra. Por exemplo: InsideCorpNet.</li>
<li>Na lista suspensa, ao lado do tipo de declaração de entrada, selecione Dentro da rede corporativa.</li>

![Nuvem](./media/multi-factor-authentication-get-started-adfs-cloud/trustedip4.png)

<li>Clique em Concluir.</li>
<li>Em Regras de Transformação de Emissão, clique em **Adicionar Regra**.</li>
<li>No Assistente Adicionar Regra de Declaração de Transformação, selecione Enviar Declarações Usando uma Regra Personalizada da lista suspensa e clique em Avançar.</li>
<li>Na caixa abaixo do nome da regra de declaração: insira Manter Usuários Conectados.</li>
<li>Na caixa de regra Personalizar, insira: c:[Type == "http://schemas.microsoft.com/2014/03/psso"] => issue(claim = c);
</li>

![Nuvem](./media/multi-factor-authentication-get-started-adfs-cloud/trustedip5.png)

<li>Clique em **Concluir**.</li>
<li>Clique em **Aplicar**.</li>
<li>Clique em **OK**.</li>

<li>Feche o gerenciamento do AD FS.</li>

### Configurar IPs confiáveis do Azure Multi-Factor Authentication com usuários federados
Agora que as declarações estão prontas, podemos configurar IPs confiáveis.<ol>

<li>Entre no Portal de Gerenciamento do Azure.</li>
<li>À esquerda, clique no Active Directory.</li>
<li>Em Diretório, clique no diretório em que deseja configurar IPs confiáveis.</li>
<li>No Diretório que você selecionou, clique em Configurar.</li>
<li>Na seção autenticação multifator, clique em Gerenciar configurações de serviço.</li>
<li>Na página Configurações de Serviço, em IPs Confiáveis, selecione **Para solicitações de usuários federados originárias da minha intranet.**</li>

![Nuvem](./media/multi-factor-authentication-get-started-adfs-cloud/trustedip6.png)

<li>Clique em Salvar.</li>
<li>Depois que as atualizações forem aplicadas, clique em Fechar.</li>

É isso! Neste ponto, os usuários federados do Office 365 devem somente ter que usar MFA quando uma declaração for originada fora da intranet corporativa.

<!---HONumber=July15_HO4-->