---
title: Migrar aplicativos locais do AD FS para o Azure | Microsoft Docs
description: "Este documento destina-se a ajudar as organizações a entender como migrar aplicativos locais para o Azure AD, com foco em aplicativos SaaS federados."
services: active-directory
author: billmath
manager: mtillman
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 01/29/2018
ms.author: billmath
ms.openlocfilehash: ec0731534da2543d48bedc575bf882b790fa136b
ms.sourcegitcommit: 9d317dabf4a5cca13308c50a10349af0e72e1b7e
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/01/2018
---
# <a name="migrate-ad-fs-on-premises-apps-to-azure"></a>Migrar aplicativos locais do AD FS para o Azure 

Este documento destina-se a ajudar as organizações a entender como migrar aplicativos locais para o Azure AD.  Ele tem foco em aplicativos SaaS federados.  Este documento não fornece orientação passo a passo.  Ele fornece orientações conceituais para ajudá-lo a realizar a migração entendendo como as configurações locais se convertem para o Azure AD. Ele também aborda o que os cenários mais comuns exigem.

## <a name="introduction"></a>Introdução

Se você tem um diretório local que contém contas de usuário, é provável que tenha pelo menos um ou dois aplicativos.  E esses aplicativos são configurados para que os usuários acessem entrando com essas identidades.

E se você é como a maioria das organizações, provavelmente está a caminho da adoção de aplicativos e identidades de nuvem.  Talvez você já esteja usando o Office 365 e o Azure AD Connect.  Talvez tenha instalado aplicativos SaaS baseados em nuvem para algumas cargas de trabalho principais, mas não todas.  

Muitas organizações têm aplicativos SaaS ou LoB (linha de negócios) personalizados federados diretamente a um serviço de logon local, como o AD FS (Serviços de Federação do Active Directory), junto com o Office 365 e aplicativos baseados no Azure AD.  Este guia de migração descreve por que e como migrar aplicativos locais para o Azure AD.

>[!NOTE]
>Este guia fornece informações detalhadas sobre a configuração e a migração do aplicativo SaaS, com informações de alto nível sobre aplicativos LoB personalizados.  Há planos de diretrizes mais detalhadas para aplicativos LoB personalizados para o futuro.

Figura 1: aplicativos conectados diretamente no local ![local](media/migrate-adfs-apps-to-azure/migrate1.png)

Figura 2: Aplicativos federados por meio do Azure AD ![Azure](media/migrate-adfs-apps-to-azure/migrate2.png)

## <a name="why-migrate-apps-to-azure-ad"></a>Por que migrar aplicativos para o Azure AD?

Para uma organização que já usa o AD FS, o Ping ou outro provedor de autenticação local, a migração de aplicativos para o Azure AD permite os seguintes benefícios:

**Acesso mais seguro**
- Configure controles de acesso por aplicativo granulares, incluindo MFA (Autenticação Multifator), usando o [Acesso Condicional do Azure AD](https://docs.microsoft.com/azure/active-directory/active-directory-conditional-access-azure-portal).  As políticas podem ser aplicadas a aplicativos SaaS e personalizados da mesma forma que você deve estar fazendo atualmente para o Office 365
- Para detectar ameaças e proteger o logon com base em aprendizado de máquina e heurística que identificam o tráfego de risco, aproveite os recursos do Azure AD internos e continuamente em evolução com [Azure AD Identity Protection](https://docs.microsoft.com/azure/active-directory/active-directory-identityprotection)

**Colaboração B2B do AD do Azure**
- Depois que o logon para aplicativos SaaS for baseado no Azure AD, você poderá dar a parceiros acesso aos recursos de nuvem com a [colaboração B2B do Azure AD](https://docs.microsoft.com/azure/active-directory/active-directory-b2b-what-is-azure-ad-b2b).

**Experiência de administração mais fácil e recursos adicionais do Azure AD**
- O Azure AD como provedor de identidade para aplicativos SaaS dá suporte a recursos adicionais, como certificados de autenticação de token por aplicativo, [datas de validade do certificado configuráveis](https://docs.microsoft.com/azure/active-directory/active-directory-sso-certs), e [provisionamento automatizado](https://docs.microsoft.com/azure/active-directory/active-directory-saas-app-provisioning) de contas de usuário (em aplicativos principais da galeria) com base em identidades do Azure AD

**Manter os benefícios de um provedor de identidade local**
- Ao obter os benefícios do Azure AD, você pode continuar usando sua solução local para autenticação, para que benefícios como soluções MFA (Autenticação Multifator), registros em log e auditoria locais continuem em vigor 

**Começar a desativar o provedor de identidade local**
- Para organizações que desejam desativar o produto de autenticação local, a migração de aplicativos para o Azure AD permite uma transição mais fácil removendo parte do trabalho 

## <a name="mapping-types-of-apps-on-premises-to-types-of-apps-in-azure-ad"></a>Mapeando tipos de aplicativos locais para tipos de aplicativos no Azure AD
A maioria dos aplicativos se enquadram em algumas categorias com base no tipo de logon que usam.  Essas categorias determinam como o aplicativo é representado no Azure AD.

Resumindo, os aplicativos SAML 2.0 podem ser integrados ao Azure AD por meio da galeria de aplicativos do Azure AD ou como aplicativos que não fazem parte da galeria.  Aplicativos que usam o OAuth 2.0 ou o OpenID Connect podem ser integrados ao Azure AD da mesma forma que os "registros de aplicativo".  Continue lendo para obter mais detalhes.

### <a name="federated-saas-apps-vs-custom-lob-apps"></a>Aplicativos SaaS federados vs aplicativos LoB personalizados
Os aplicativos federados incluem aplicativos que se enquadram nas categorias listadas.

- Aplicativos SaaS 
    - Se os usuários fazem logon em aplicativos SaaS, como o Salesforce, o ServiceNow ou o Workday, e a integração está sendo feita com um provedor de identidade local como o AD FS ou o Ping, você está usando o logon federado para aplicativos SaaS.
    - Os aplicativos geralmente usam o protocolo SAML 2.0 para logon federado.
    - Os aplicativos que se enquadram nessa categoria podem ser integrados com o Azure AD como aplicativos empresariais, da galeria ou como aplicativos que não são da galeria.
- Aplicativos de LoB personalizados
    - Isso se refere a aplicativos não SaaS desenvolvidos internamente pela sua organização ou disponibilizados como um produto de pacote padrão instalado em seu data center.  Isso inclui aplicativos do SharePoint e aplicativos criados no WIF (Windows Identity Foundation).
    - Os aplicativos podem usar SAML 2.0, WS-Federation, OAuth ou OpenID Connect para logon federado
    - Aplicativos personalizados que usam Oauth 2.0, OpenID Connect ou WS-Federation podem ser integrados ao Azure AD como registros de aplicativo, e aplicativos personalizados que usam SAML 2.0 ou WS-Federation podem ser integrados como aplicativos fora da galeria no âmbito dos aplicativos empresariais

### <a name="non-federated-apps"></a>Aplicativos não federados
Além disso, os aplicativos não federados podem ser integrados ao Azure AD usando o Proxy de Aplicativo do Azure AD e as funcionalidades relacionadas.  Para saber mais sobre os recursos, siga os links:
- Aplicativos que usam WIA (autenticação integrada do Windows) diretamente para o Active Directory
    - Esses aplicativos podem ser integrados ao Azure AD por meio do [Proxy de Aplicativo do Azure AD](https://docs.microsoft.com/azure/active-directory/application-proxy-publish-azure-portal)
- Aplicativos que se integram com seu provedor de logon único por meio de um agente e usam cabeçalhos de autorização
    - Os aplicativos que usam um agente instalado para logon e autorização baseada em cabeçalho podem ser configurados para o Azure AD com base em logon usando o Proxy de Aplicativo do Azure AD com [Acesso Ping para o Azure AD](https://blogs.technet.microsoft.com/enterprisemobility/2017/06/15/ping-access-for-azure-ad-is-now-generally-available-ga/)

## <a name="translating-on-premises-federated-apps-to-azure-ad"></a>Convertendo aplicativos federados locais para o Azure AD 
Felizmente, o AD FS e o Azure AD funcionam de maneira semelhante e, portanto, os conceitos de configuração de confiança, URLS de entrada e saída e identificadores se aplicam em ambos os casos.  No entanto, há algumas pequenas diferenças que devem ser entendidas à medida que você faz a transição.

Na tabela, mapeamos várias ideias principais compartilhadas pelo AD FS, pelo Azure AD e por aplicativos SaaS para ajudá-lo a fazer a conversão. 

### <a name="representing-the-app-in-azure-ad-or-ad-fs"></a>Representando o aplicativo no Azure AD ou no AD FS
A migração começa avaliando como o aplicativo é configurado no local e mapeando essa configuração para o Azure AD.  A seguir está um mapeamento de elementos de configuração de terceira parte confiável do AD FS para os elementos correspondentes no Azure AD.  
- Termo do AD FS: terceira parte confiável ou relação de confiança de terceira parte confiável
- Termo do Azure AD: aplicativo empresarial ou registro de aplicativo (dependendo do tipo de aplicativo)

|Elemento de configuração do aplicativo|DESCRIÇÃO|Na configuração do AD FS|Local correspondente na configuração do Azure AD|Elemento do Token SAML|
|-----|-----|-----|-----|-----|
|URL de logon do aplicativo|URL da página de entrada do aplicativo. Aqui é onde o usuário deve ir para iniciar a entrada em um aplicativo em um fluxo SAML iniciado por SP.|N/D|No Azure AD, a URL de logon está configurada no portal do Azure, nas Propriedades de Logon único do aplicativo, como a URL de logon.</br></br>(talvez você precise clicar em Mostrar configurações de URL avançadas para ver a URL de logon)||
|URL de resposta do aplicativo|URL do aplicativo da perspectiva do IdP.  É para ela que o usuário e o token serão enviados quando o usuário se conectar ao IDP.</br></br>  Isso às vezes é chamado de ponto de extremidade de consumo da declaração SAML.|Encontrado na Relação de Confiança de Terceira Parte Confiável para o aplicativo.  Clique com botão direito do mouse na terceira parte confiável e escolha "Propriedades" -> guia "Pontos de Extremidade".|No Azure AD, a URL de resposta está configurada no portal do Azure, nas Propriedades de Logon único do aplicativo, como a URL de Resposta.</br></br>(talvez você precise clicar em Mostrar configurações de URL avançadas para ver a URL de Resposta)|Mapeia para o elemento de destino no token SAML.</br></br>  Valor de exemplo: https://contoso.my.salesforce.com|
|URL de logoff do aplicativo|URL para onde as solicitações de "limpeza de saída" são enviadas quando um usuário sai de um aplicativo, para fazer logoff em todos os outros aplicativos aos quais o IDP conectou o usuário.|Encontrado no Gerenciamento do AD FS em: Relações de Confiança de Terceira Parte Confiável.  Clique com botão direito do mouse no RP e escolha "Propriedades" -> guia "Pontos de Extremidade"|N/D: o Azure AD não dá suporte a "logoff único," que é a desconexão de todos os aplicativos.  Ele simplesmente desconecta o usuário do Azure AD.|ND|
|Identificador do aplicativo|O identificador do aplicativo da perspectiva do IdP O valor da URL de logon é frequentemente usado como o Identificador (mas nem sempre)</br></br>  Às vezes, o aplicativo o chama de "ID da Entidade".|No AD FS, esse é o ID de Terceira Parte Confiável: clique com o botão direito do mouse na relação de confiança de terceira parte confiável e escolha "Propriedades" -> Clique na guia "Identificadores"|No Azure AD, o identificador é configurado no portal do Azure, nas Propriedades de Logon único do aplicativo, como o Identificador em Domínio e URLs (talvez você precise clicar na caixa de seleção "Mostrar configurações de URL avançadas")|Corresponde ao elemento Público do token SAML|
|Metadados de federação do aplicativo|Local dos metadados de federação do aplicativo.  Usado pelo IdP para atualização automática de definições de configuração específicas, como pontos de extremidade ou certificados de criptografia.|A URL de metadados de federação do aplicativo é encontrado na Relação de Confiança de Terceira Parte Confiável do AD FS para o aplicativo.  Clique com o botão direito do mouse na relação de confiança, escolha Propriedades e clique na guia Monitoramento.|N/D: o Azure AD não dá suporte ao consumo de metadados de federação do aplicativo diretamente|ND|
|Identificador de usuário/NameID|Atributo usado para indicar exclusivamente a identidade do usuário do Azure AD ou do AD FS para seu aplicativo.</br></br>  Normalmente, o nome UPN ou o endereço de email do usuário.|No AD FS, isso é encontrado como uma regra de declaração na terceira parte confiável.  Na maioria dos casos, é a regra de declaração que emite uma declaração com um tipo que termina com "nameidentifier"|No Azure AD, o identificador de usuário pode ser encontrado no portal do Azure, nas Propriedades de Logon único do aplicativo, no cabeçalho Atributos de Usuário.</br></br>Por padrão, o UPN é usado.|Comunicado do IDP para o aplicativo como o elemento "NameID" no token SAML.|
|Outras Declarações a serem enviadas para o aplicativo|Além do Identificador de Usuário/NameID, outras informações de declaração são normalmente enviadas do IDP para o aplicativo, por exemplo, nome, sobrenome, endereço de email e grupos dos quais o usuário é membro|No AD FS, isso é encontrado como outras regras de declaração na terceira parte confiável.|No Azure AD, encontrado no portal do Azure, em Propriedades de Logon único do aplicativo, no cabeçalho Atributos de usuário, clique em Exibir e editar todos os outros atributos de usuário.|| 

### <a name="representing-azure-ad-as-an-identity-provider-idp-in-a-saas-app"></a>Representando o Azure AD como um IdP (provedor de identidade) em um aplicativo SaaS
Como parte da migração, o aplicativo precisa ser configurado para apontar para o Azure AD (diferentemente do provedor de identidade local).  Esta seção se concentra principalmente em aplicativos SaaS que usam o protocolo SAML e não em aplicativos LOB/personalizados. No entanto, os conceitos descritos se estenderiam a aplicativos LOB/personalizados. 

Em um alto nível, há algumas coisas importantes em apontar um aplicativo SaaS para o Azure AD
- Emissor do provedor de identidade: https&#58;//sts.windows.net/{tenant-id}/
- URL de Logon do Provedor de Identidade: https&#58;//login.microsoftonline.com/{tenant-id}/saml2
- Identity Provider Logout URL: https&#58;//login.microsoftonline.com/{tenant-id}/saml2 
- Local dos metadados de federação: https&#58;//login.windows.net/{tenant-id} <tenant-id>/federationmetadata/2007-06/federationmetadata.xml?appid={<application-id} 

em que {tenant-id} é substituído por seu ID de locatário, encontrado no portal do Azure em Active Directory -> Propriedades como "ID de diretório", e {aplication-id} é substituído pelo ID do aplicativo encontrado em Propriedades do aplicativo como "ID do aplicativo"

A tabela descreve detalhadamente os principais elementos de configuração de IdP para definir configurações de SSO no aplicativo e seus valores ou locais no AD FS e no Azure AD.  O quadro de referência da tabela é o aplicativo SaaS, que precisa saber para onde enviar solicitações de autenticação e como validar os tokens recebidos.

|Elemento de configuração|DESCRIÇÃO|AD FS|AD do Azure|
|---|---|---|---|
|IdP </br>sign-on </br>URL|URL de logon do IdP da perspectiva do aplicativo (para onde o usuário é redirecionado para o logon).|A URL de logon do AD FS é o nome do serviço de federação do AD FS seguido por “/adfs/ls/”, por exemplo: https&#58;//fs.contoso.com/adfs/ls/|O valor correspondente do Azure AD segue o padrão, em que {tenant-id} é substituído pelo ID de locatário encontrada no portal do Azure em Azure Active Directory -> Propriedades como "ID de Diretório"</br></br>Para aplicativos que usam o protocolo SAML-P: https&#58;//login.microsoftonline.com</br>/{tenant-id}/saml2 </br></br>Para aplicativos que usam o protocolo WS-Federation https&#58;//login.microsoftonline.com</br>/{tenant-id}/wsfed|
|IdP </br>Sair </br>URL|URL de logoff do IdP da perspectiva do aplicativo (para onde o usuário é redirecionado ao escolher "sair" do aplicativo).|Para o AD FS, a URL de logoff é a mesma que a URL de logon, ou a mesma URL com “wa=wsignout1.0” acrescentado, por exemplo, https&#58;//fs.contoso.com/adfs/ls /?wa=wsignout1.0|O valor correspondente para o Azure AD depende de o aplicativo ser capaz de dar suporte a logoff SAML 2.0.</br></br>Se o aplicativo dá suporte a logout SAML, o valor segue o padrão, em que o valor {tenant-id} é substituído pelo ID de locatário encontrado no portal do Azure em Azure Active Directory -> Propriedades como "ID de Diretório". https&#58;//login.microsoftonline.com</br>/{tenant-id}/saml2</br></br>Se o aplicativo não dá suporte a logoff SAML: https&#58;//login.microsoftonline.com</br>/common /wsfederation?wa=wsignout1.0|
|A criptografia do token </br>Assinando </br>Certificado|Certificado cuja chave privada o IDP usa para assinar tokens emitidos.  Verifica se o token veio do mesmo IDP em que o aplicativo está configurado para confiar.|O certificado de autenticação de token do AD FS é encontrado no Gerenciamento do AD FS, em Certificados.|No Azure AD, o certificado de assinatura de token pode ser encontrado no portal do Azure, em Propriedades de Logon único do aplicativo, no cabeçalho Certificado de Autenticação SAML, onde você pode baixar o certificado para carregá-lo no aplicativo.</br></br>  Se o aplicativo tiver mais de um certificado, todos os certificados poderão ser encontrados no arquivo xml de metadados de federação.|
|Identificador/ </br>“Emissor”|Identificador do IdP da perspectiva do aplicativo (às vezes chamado de "Emissor" ou "ID do Emissor")</br></br>No token SAML, o valor é exibido como o elemento "Emissor"|O identificador para o AD FS normalmente é o identificador do serviço de federação no Gerenciamento do AD FS em: Serviço -> Editar Propriedades do Serviço de Federação.  Por exemplo: http&#58;//fs.contoso.com/adfs/services/trust|O valor correspondente do Azure AD segue o padrão, em que o valor {tenant-id} é substituído pelo ID de locatário encontrada no portal do Azure em Azure Active Directory -> Propriedades como "ID de Diretório"  https&#58;//sts.windows.net/{tenant-id}/|
|IdP </br>Federação </br>Metadados|Local dos metadados de federação disponíveis publicamente do IDP.  (Os metadados de federação são usados por alguns aplicativos como uma alternativa à configuração de URLs, identificador e certificado de autenticação de token pelo administrador individualmente)|Localize a URL dos metadados de federação do AD FS no Gerenciamento do AD FS em Serviço -> Pontos de Extremidade -> Metadados-> Tipo: Metadados de Federação, por exemplo: https&#58;//fs.contoso.com/ FederationMetadata/2007-06/</br>FederationMetadata.xml|O valor correspondente do Azure AD segue o padrão https&#58;//login.microsoftonline.com</br>/{TenantDomainName}/FederationMetadata/2007-06/</br>FederationMetadata.xml, em que o valor {TenantDomainName} é substituído pelo nome do locatário no formato "contoso.onmicrosoft.com" </br></br>[Saiba mais](https://docs.microsoft.com/azure/active-directory/develop/active-directory-federation-metadata) sobre metadados de federação no Azure AD.

## <a name="migrating-saas-apps"></a>Migrando aplicativos SaaS
A migração de aplicativos SaaS do AD FS ou de outro provedor de identidade para o Azure AD é um processo manual atualmente. Para obter orientações específicas para o aplicativo, [confira a lista de tutoriais sobre a integração de aplicativos SaaS encontrados na galeria](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list).

Os tutoriais de integração presumem que você está fazendo uma integração de campo verde.  Há alguns conceitos-chave específicos para a migração que você deve conhecer na hora de planejar, avaliar, configurar e transferir seus aplicativos.  
- Embora alguns aplicativos possam ser migrados facilmente, aplicativos com requisitos mais complexos, como declarações personalizadas, podem exigir configuração adicional no Azure AD e/ou no Azure AD Connect
- Em cenários mais comuns, somente a declaração NameId e outras declarações de identificador de usuário comuns são necessárias para um aplicativo; para determinar se outras declarações adicionais são necessárias, examine as declarações que você está emitindo do AD FS ou do provedor de identidade de terceiros
- Depois de determinar que declarações adicionais são necessárias, você precisará disponibilizá-las no Azure AD.  Você precisa verificar a configuração de sincronização do Azure AD Connect para fazer com que um atributo necessário, por exemplo, samAccountName, seja sincronizado no Azure AD
- Depois que os atributos estão disponíveis no Azure AD, adicione regras de emissão de declarações no Azure AD para incluir os atributos como declarações em tokens emitidos.  Feito dentro das propriedades de Logon único do aplicativo no Azure AD.

### <a name="assessing-what-can-be-migrated"></a>Avaliar o que pode ser migrado
Os aplicativos SAML 2.0 podem ser integrados ao Azure AD por meio da galeria de aplicativos do Azure AD ou como aplicativos que não fazem parte da galeria.  

Há algumas configurações que exigem etapas adicionais no Azure AD, e algumas que não têm suporte atualmente.  Para determinar o que pode ser movido, examine a configuração atual de cada um dos seus aplicativos; especificamente, o seguinte:
- Regras de declaração configuradas (regras de transformação de emissão)
- Atributo e formato NameID do SAML
- Versões de token SAML emitidas
- Outras configurações, como regras de autorização de emissão ou políticas de controle de acesso e regras de autenticação multifator (autenticação adicional)

#### <a name="what-can-be-migrated-today"></a>O que pode ser migrado hoje
Aplicativos que podem ser migrados facilmente no momento incluem aplicativos SAML 2.0 que usam o conjunto padrão de elementos de configuração e declarações.  Esses aplicativos podem consistir em
- nome UPN
- endereço de email
- Nome
- Sobrenome
- Atributo alternativo, como NameID do SAML, incluindo o atributo de email, o prefixo de email, employeeid, os atributos de extensão 1 a 15 ou SamAccountName local do Azure AD (consulte [Editando a declaração NameIdentifier)](./develop/active-directory-saml-claims-customization.md)
- Declarações personalizadas (consulte o documento [aqui](active-directory-claims-mapping.md) e [aqui](./develop/active-directory-saml-claims-customization.md) para obter informações sobre mapeamentos de declarações com suporte)

Além dos elementos de nameID e declarações personalizadas, outras configurações que exigem etapas de configuração adicional no Azure AD como parte da migração incluem:
- Regras de autorização personalizada ou MFA no AD FS (configuradas usando o recurso [Acesso condicional do Azure AD](active-directory-conditional-access-azure-portal.md))
- Aplicativos com vários pontos de extremidade SAML podem ser configurados no Azure AD usando o PowerShell (esse recurso não está disponível no portal)
- Aplicativos de WS-Federation, como aplicativos do SharePoint que exigem tokens SAML versão 1.1 devem ser configurados manualmente usando o PowerShell

#### <a name="appsconfigurations-not-supported-in-azure-ad-today"></a>Aplicativos/configurações sem suporte no Azure AD atualmente
Os aplicativos que exigem os recursos a seguir não podem ser migrados atualmente.  Escreva na seção de comentários se tiver aplicativos que exigem esses recursos.
- Recursos de protocolo
    - Suporte para SLO (Logoff Único SAML) de todos os aplicativos conectados
    - Suporte para padrão WS-Trust ActAs
    - Resolução do artefato SAML 
    - Verificação de assinatura de solicitações SAML assinadas (observe que são aceitas solicitações assinadas, mas a assinatura não é verificada)
 - Recursos de token 
     - Criptografia de Token SAML 
     - Tokens SAML versão 1.1 em respostas do protocolo SAML 
- Declarações em recursos de token
    - Emitir nomes de grupos locais como declarações
    - Declarações de armazenamentos diferentes do Azure AD
    - Regras de transformação de emissão de declarações complexas (consulte este documento e este documento para saber mais sobre mapeamentos de declarações com suporte)
    - Emitir extensões de diretório como declarações
    - Especificação personalizada do formato NameID
    - Emissão de atributos com vários valores

>[!NOTE]
>O Azure AD está sempre evoluindo para adicionar recursos nessa área. Vamos manter este documento atualizado regularmente. 

### <a name="configuring-azure-ad"></a>Configurando o Azure AD    
#### <a name="configure-single-sign-on-sso-settings-for-the-saas-app"></a>Definir configurações de SSO (logon único) para o aplicativo SaaS

No Azure AD, a configuração de logon SAML conforme exigido pelo seu aplicativo é feita dentro das propriedades de logon único do aplicativo em Atributos de usuário, conforme mostrado:

![](media/migrate-adfs-apps-to-azure/migrate3.png)
- Clique em ' Exibir e editar todos os outros atributos de usuário' para ver os atributos a serem enviados como declarações no token de segurança

![](media/migrate-adfs-apps-to-azure/migrate4.png)
- Clique em uma linha de atributo específica para editá-la ou clique em 'Adicionar atributo' para um novo atributo. 
![](media/migrate-adfs-apps-to-azure/migrate5.png)

#### <a name="assign-users-to-the-app"></a>Atribuir usuários ao aplicativo
Para seus usuários no Azure AD poderem fazer logon em determinado aplicativo SaaS, eles precisam receber acesso do Azure AD.

Para atribuir usuários no portal do Azure AD, navegue até a tela do aplicativo SaaS no portal e clique em "Usuários e grupos" na barra lateral. Para adicionar um usuário ou um grupo, clique em "Adicionar usuário" na parte superior da tela. 

![](media/migrate-adfs-apps-to-azure/migrate6.png) 

![](media/migrate-adfs-apps-to-azure/migrate7.png)Para verificar o acesso, o usuário deverá ver o aplicativo SaaS em seu [painel de acesso](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction) ao fazer logon, que pode ser encontrado em http://myapps.microsoft.com. Por exemplo, o usuário recebeu acesso ao Salesforce e ao ServiceNow com êxito.

![](media/migrate-adfs-apps-to-azure/migrate8.png)

### <a name="configuring-the-saas-app"></a>Configurando o aplicativo SaaS
O processo de transferência da federação local para o Azure AD depende de o aplicativo com que você está trabalhando dar suporte a vários IdPs (provedores de identidade).  Estas são algumas perguntas comuns sobre o suporte a vários IdP:

   **P: o que significa para um aplicativo dar suporte a vários IdPs?**
    
   R: aplicativos SaaS que dão suporte a vários IdPs permitem que você insira todas as informações sobre o novo IdP (no nosso caso, o Azure AD) antes de confirmar a alteração da experiência de logon.  Quando a configuração estiver concluída, você poderá alternar a configuração de autenticação do aplicativo para apontar para o Azure AD.

   P: por que é importante se o aplicativo SaaS dá suporte a vários IdPs?

   R: se não há suporte para vários IdPs, o administrador precisa reservar um curto período de tempo como interrupção de serviço ou manutenção para poder configurar o Azure AD como um novo IdP para o aplicativo. Durante essa interrupção, os usuários devem ser notificados de que não poderão entrar em suas contas.

   Se um aplicativo der suporte a vários IdPs, a configuração do IdP adicional poderá ser feita com antecedência e o administrador poderá simplesmente alternar o IdP na transferência para o Azure.

   Além disso, se o aplicativo dá suporte a vários IdPs e você escolhe vários IdPs para processar a autenticação simultaneamente na entrada, o usuário recebe uma opção do IdP para autenticar na sua página de entrada.

#### <a name="example-multiple-idp-support"></a>Exemplo: suporte a vários IdPs
Por exemplo, no Salesforce, a configuração de IDP pode ser encontrada em Configurações -> Configurações da Empresa -> Meu Domínio -> Configuração de Autenticação.

![](media/migrate-adfs-apps-to-azure/migrate9.png)

Por causa da configuração criada anteriormente em identidade -> Configurações de logon único, você deve conseguir alterar seu IdP para a configuração de autenticação do AD FS para o Azure AD, por exemplo. 

![](media/migrate-adfs-apps-to-azure/migrate10.png)

### <a name="optional-configure-user-provisioning-in-azure-ad"></a>Opcional: configurar provisionamento de usuários no Azure AD
Opcionalmente, se você deseja que o Azure AD trate diretamente do provisionamento do usuário para determinado aplicativo SaaS, consulte este documento sobre como gerenciar o provisionamento de contas de usuário para aplicativos empresariais no Azure AD.

## <a name="next-steps"></a>Próximas etapas

- [Gerenciando aplicativos com o Azure Active Directory](active-directory-enable-sso-scenario.md)
- [Gerenciar o acesso aos aplicativos](active-directory-managing-access-to-apps.md)
- [Azure AD Connect: federação](active-directory-aadconnectfed-whatis.md)