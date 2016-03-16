<properties
   pageTitle="Federação com o AD FS de um cliente | Microsoft Azure"
   description="Como federar com o AD FS de um cliente em um aplicativo multilocatário"
   services=""
   documentationCenter="na"
   authors="MikeWasson"
   manager="roshar"
   editor=""
   tags=""/>

<tags
   ms.service="guidance"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="02/16/2016"
   ms.author="mwasson"/>

# Federação com o AD FS de um cliente

Este artigo faz [parte de uma série]. Há também um [aplicativo de exemplo] completo que acompanha esta série.

Este artigo descreve como um aplicativo SaaS multilocatário pode oferecer suporte à autenticação por meio do AD FS (Serviços de Federação do Active Directory) para federar com o AD FS de um cliente.

## Visão geral

O Azure AD (Azure Active Directory) facilita a entrada de usuários a partir de locatários do Azure AD, incluindo clientes do Office365 e do Dynamics CRM Online. Mas e quanto aos clientes que usam o Active Directory local em uma intranet corporativa?

Uma opção seria esses clientes sincronizarem o AD local com o Azure AD, usando o [Azure AD Connect]. No entanto, talvez alguns clientes não consigam usar essa abordagem devido à política de TI corporativa ou por outros motivos. Nesse caso, outra opção é a federação por meio do AD FS (Serviços de Federação do Active Directory).

Para habilitar este cenário:

-	O cliente deve ter um farm do AD FS voltado para a Internet.
-	O provedor de SaaS implanta seu próprio farm do AD FS.
-	O cliente e o provedor de SaaS devem configurar a [confiança de federação]. Esse é um processo manual.

Há três funções principais na relação de confiança:

-	O AD FS do cliente é o [parceiro de conta], responsável por autenticar usuários do AD do cliente e criar tokens de segurança com declarações de usuário.
-	O AD FS do provedor de SaaS é o [parceiro de recurso], que confia no parceiro de conta e recebe as declarações de usuário.
-	O aplicativo é configurado como uma RP (terceira parte confiável) no AD FS do provedor de SaaS.

	![Relação de confiança de federação](media/guidance-multitenant-identity/federation-trust.png)

> [AZURE.NOTE] Neste artigo, supomos que o aplicativo use OpenID Connect como o protocolo de autenticação. Outra opção é usar o WS-Federation.

> Para o OpenID Connect, o provedor de SaaS deve usar o AD FS 4.0 em execução no Windows Server 2016, que atualmente está em versão Technical Preview. O AD FS 3.0 não oferece suporte para o OpenID Connect.

> O ASP.NET Core 1.0 não inclui suporte nativo para WS-Federation.

Para obter um exemplo de como usar o WS-Federation com o ASP.NET 4, confira https://github.com/Azure-Samples/active-directory-dotnet-webapp-wsfederation

## Fluxo de autenticação

1.	Quando o usuário clicar em "entrar", o aplicativo será redirecionado para um ponto de extremidade do OpenID Connect sobre o AD FS do provedor de SaaS.
2.	O usuário insere seu nome de usuário da organização ("`alice@corp.contoso.com`"). O AD FS usa a descoberta de realm inicial para redirecionar o AD FS do cliente, no qual o usuário insere suas credenciais.
3.	O AD FS do cliente envia declarações de usuário para o AD FS do provedor de SaaS, usando o WF-Federation (ou SAML).
4.	Fluxo de declarações do AD FS para o aplicativo, usando o OpenID Connect. Isso exige uma transição de protocolo do WS-Federation.

## Limitações

No momento da redação deste artigo, o aplicativo recebe um conjunto limitado de declarações no id\_token do OpenID. O AD FS 4.0 ainda está em visualização, portanto essa lista pode mudar. Mas, se o seu aplicativo exigir outras declarações, o que atualmente não é possível, é importante indicar isso antes de continuar.

Atualmente, as seguintes declarações são enviadas no id\_token:

Declaração | Descrição
------|-------------
aud | Público-alvo &mdash; o aplicativo para o qual as declarações foram emitidas.
authenticationinstant | [Instante de autenticação]
c\_hash | Valor de hash do código
exp | [Hora de expiração]
iat | [Emitido em]
iss | Emissor. O valor dessa declaração sempre será o AD FS do parceiro de recurso, não o AD FS do cliente. (Em outras palavras, essa declaração identificará o provedor de SaaS como o emissor, em vez do cliente).
name | Nome de usuário. Exemplo: `john@corp.fabrikam.com`.
nameidentifier | [Identificador de nome]
nonce | Nonce de sessão
upn | Nome UPN. Exemplo: john@corp.fabrikam.com

Especificamente, observe que a declaração "iss" não especifica o AD FS do cliente. Para conhecer o domínio do cliente, você precisará examinar o UPN. O restante deste artigo descreve como configurar a relação de confiança entre o RP (o aplicativo) e o parceiro de conta (o cliente).

## Implantação do AD FS

O provedor de SaaS pode implantar o AD FS no local ou em VMs do Azure. É importante seguir estas diretrizes para obter segurança e disponibilidade:

-	Implante pelo menos dois servidores do AD FS e dois servidores proxy do AD FS para atingir a melhor disponibilidade do serviço AD FS.
-	Os controladores de domínio e servidores do AD FS nunca devem ser expostos diretamente à Internet e devem estar em uma rede virtual com acesso direto a elas.
-	Os proxies do aplicativo Web (antes conhecidos como proxies do AD FS) devem ser usados para publicar servidores do AD FS na Internet.

Para configurar uma topologia semelhante no Azure é preciso usar redes virtuais, NSGs, VMs do azure e conjuntos de disponibilidade. Para obter mais detalhes, confira [Diretrizes para implantar o Active Directory do Windows Server em máquinas virtuais do Azure](MSDN).

## Configurar o aplicativo para usar a autenticação do OpenID Connect com o AD FS

O provedor de SaaS deve habilitar o OpenID Connect entre o aplicativo e do AD FS. Para fazer isso, adicione um grupo de aplicativos ao AD FS. Você pode encontrar instruções detalhadas nesta [postagem do blog], em " Setting up a Web App for OpenId Connect sign in AD FS". Em seguida, configure o middleware OpenID Connect. O ponto de extremidade de metadados é `https://domain/adfs/.well-known/openid-configuration`, no qual o domínio é o domínio do AD FS do provedor de SaaS.

Normalmente, você pode combinar esse a outros pontos de extremidade do OpenID Connect (por exemplo, AAD). Você precisará de dois botões de entrada diferentes, ou alguma outra maneira para diferenciá-los, para que o usuário seja enviado ao ponto de extremidade de autenticação correto.

## Configurar o parceiro de recursos do AD FS

O provedor de SaaS deve fazer o seguinte para cada cliente que deseja se conectar via ADFS:

1.	Adicionar uma relação de confiança do provedor de declarações.
2.	Adicionar regras de declarações.
3.	Habilitar a descoberta de realm inicial.

Veja as etapas com mais detalhes.

### Adicionar a relação de confiança do provedor de declarações

1.	No Gerenciador do Servidor, clique em **Ferramentas** e selecione **Gerenciamento do AD FS**.
2.	Na árvore de console, em **AD FS**, clique com o botão direito do mouse em **Relações de Confiança do Provedor de Declarações**. Selecione **Adicionar a Relação de Confiança do Provedor de Declarações**.
3.	Clique em **Iniciar** para iniciar o assistente.
4.	Selecione os opção "Importar dados sobre o provedor de declarações publicados online ou em uma rede local". Digite o URI do ponto de extremidade de metadados de federação do cliente. (Exemplo: `https://contoso.com/FederationMetadata/2007-06/FederationMetadata.xml`). Você precisará obter isso do cliente.
5.	Conclua o assistente usando as opções padrão.

### Editar regras de declarações

1.	Clique com o botão direito do mouse na relação de confiança de provedor de declarações recém-adicionada e selecione **Editar Regras de Declarações**.
2.	Clique em **Adicionar Regra**.
3.	Selecione "Passar por Filtro uma Declaração de Entrada" e clique em **Avançar**. ![Assistente para Adicionar Regra de Declaração de Transformação](media/guidance-multitenant-identity/edit-claims-rule.png)
4.	Insira um nome para a regra.
5.	Em "Tipo de declaração de entrada", selecione **UPN**.
6.	Selecione "Passar todos os valores de declaração". ![Assistente para Adicionar Regra de Declaração de Transformação](media/guidance-multitenant-identity/edit-claims-rule2.png)
7.	Clique em **Concluir**.
8.	Repita as etapas 2 a 7 e especifique o **Tipo de Declaração de Ancoragem** do tipo de declaração de entrada.
9.	Clique em **OK** para concluir o assistente.

### Habilitar a descoberta de realm inicial
Execute o seguinte script do PowerShell:

```
Set-ADFSClaimsProviderTrust -TargetName "name" -OrganizationalAccountSuffix @("suffix")
```

onde "name" é o nome amigável da relação de confiança do provedor de declarações e "suffix" é o sufixo UPN do AD do cliente (por exemplo, "corp.fabrikam.com").

Com essa configuração, os usuários finais podem digitar sua conta institucional e o AD FS selecionará automaticamente o provedor de declarações correspondente. Confira [Personalizando as páginas de entrada do AD FS], na seção "Configurar o Provedor de Identidade para usar determinados sufixos de email".

## Configuração do parceiro de conta do AD FS

Os cliente deve fazer o seguinte:

1.	Adicionar uma relação de confiança da RP (terceira parte confiável)
2.	Adiciona regras de declarações.

### Adicionar a relação de confiança da RP

1.	No Gerenciador do Servidor, clique em **Ferramentas** e selecione **Gerenciamento do AD FS**.
2.	Na árvore de console, em **AD FS**, clique com o botão direito do mouse em **Relações de Confiança da Terceira Parte Confiável**. Selecione **Adicionar Relação de Confiança de Terceira Parte Confiável**.
3.	Selecione **Reconhecimento de Declarações** e clique em **Iniciar**.
4.	Na página **Selecionar Fonte de Dados**, selecione os opção "Importar dados sobre o provedor de declarações publicados online ou em uma rede local". Insira o URI do ponto de extremidade de metadados de federação do cliente. ![Assistente para Adicionar Relação de Confiança de Terceira Parte Confiável](media/guidance-multitenant-identity/add-rp-trust.png)
5.	Na página **Especificar Nome para Exibição**, insira qualquer nome.
6.	Na página **Escolher Política de Controle de Acesso**, escolha uma política. Você pode permitir todos na organização ou escolher um grupo de segurança específico. ![Assistente para Adicionar Relação de Confiança de Terceira Parte Confiável](media/guidance-multitenant-identity/add-rp-trust2.png)
7.	Clique em **Avançar** para concluir o assistente.

### Adicionar regras de declarações

1.	Clique com o botão direito do mouse na terceira parte confiável recém-adicionada e selecione **Editar Política de Emissão de Declaração**.
2.	Clique em **Adicionar Regra**.
3.	Selecione "Enviar Atributos do LDAP como Declarações" e clique em **Avançar**. ![Assistente para Adicionar Regra de Declaração de Transformação](media/guidance-multitenant-identity/add-claims-rules.png)
4.	Insira um nome para a regra, como “UPN”.
5.	Em **Repositório de atributos**, escolha **Active Directory**.
6.	Na seção **Mapeamento de atributos LDAP**:
  -	Em **Atributo LDAP**, escolha **Nome UPN**.
  - Em **Tipo de Declaração de Saída**, escolha **UPN**. ![Assistente para Adicionar Regra de Declaração de Transformação](media/guidance-multitenant-identity/add-claims-rules2.png)
7.	Clique em **Concluir**.
8.	Clique em **Adicionar regra** novamente.
9.	Selecione “Enviar Declarações Usando uma Regra Personalizada” e clique em **Avançar**.
10.	Insira um nome para a regra, como “Declaração de Ancoragem”.
11.	Em **Regra personalizada**, insira o seguinte:

	```
    EXISTS([Type == "http://schemas.microsoft.com/ws/2014/01/identity/claims/anchorclaimtype"])=>
      issue (Type = "http://schemas.microsoft.com/ws/2014/01/identity/claims/anchorclaimtype",
             Value = "http://schemas.xmlsoap.org/ws/2005/05/identity/claims/upn");
	```

    Essa regra mapeia a declaração de UPN para a declaração de Ancoragem.

12.	Clique em **Concluir**.
13.	Clique em **OK** para concluir o assistente.

<!-- Links -->
[parte de uma série]: guidance-multitenant-identity.md
[Azure AD Connect]: ../active-directory/active-directory-aadconnect.md
[confiança de federação]: https://technet.microsoft.com/library/cc738707.aspx
[parceiro de conta]: https://technet.microsoft.com/library/cc758463(v=ws.10).aspx
[parceiro de recurso]: https://technet.microsoft.com/library/cc758463(v=ws.10).aspx
[Instante de autenticação]: https://msdn.microsoft.com/library/system.security.claims.claimtypes.authenticationinstant%28v=vs.110%29.aspx
[Hora de expiração]: http://tools.ietf.org/html/draft-ietf-oauth-json-web-token-25#section-4.1.4
[Emitido em]: http://tools.ietf.org/html/draft-ietf-oauth-json-web-token-25#section-4.1.6
[Identificador de nome]: https://technet.microsoft.com/library/ee913589.aspx
[Guidelines for Deploying Windows Server Active Directory on Azure Virtual Machines]: https://msdn.microsoft.com/library/azure/jj156090.aspx
[postagem do blog]: http://www.cloudidentity.com/blog/2015/08/21/OPENID-CONNECT-WEB-SIGN-ON-WITH-ADFS-IN-WINDOWS-SERVER-2016-TP3/
[Personalizando as páginas de entrada do AD FS]: https://technet.microsoft.com/library/dn280950.aspx
[aplicativo de exemplo]: https://github.com/Azure-Samples/guidance-identity-management-for-multitenant-apps

<!---HONumber=AcomDC_0302_2016-->