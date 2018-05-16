---
title: Como e por que os aplicativos são adicionados ao Azure Active Directory
description: O que significa para um aplicativo ser adicionado ao Microsoft Azure AD e como são adicionados?
services: active-directory
documentationcenter: ''
author: CelesteDG
manager: mtillman
editor: ''
ms.assetid: 3321d130-f2a8-4e38-b35e-0959693f3576
ms.service: active-directory
ms.component: develop
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 04/18/2018
ms.author: celested
ms.custom: aaddev
ms.openlocfilehash: c9ebfcba59e3f46fb30f4cd2402ec4ebb606f6d0
ms.sourcegitcommit: e14229bb94d61172046335972cfb1a708c8a97a5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/14/2018
---
# <a name="how-and-why-applications-are-added-to-azure-ad"></a>Como e por que os aplicativos são adicionados ao AD do Azure
Há duas representações de aplicativos no Azure Microsoft AD: 
* [Objetos de aplicativo](active-directory-application-objects.md#application-object) - Embora haja [exceções](#notes-and-exceptions), elas podem ser consideradas a definição de um aplicativo.
* [Entidades de serviço](active-directory-application-objects.md#service-principal-object) - Estes podem ser considerados uma instância de uma aplicação. Geralmente, as entidades de serviço fazem referência a um objeto de aplicativo e um objeto de aplicativo pode ser referenciado por várias entidades de serviço nos diretórios.

## <a name="what-are-application-objects-and-where-do-they-come-from"></a>O que são objetos de aplicativo e de onde originam-se?
[Objetos de aplicativo](active-directory-application-objects.md#application-object) (que podem ser gerenciados no Portal do Azure por meio de [Registros de aplicativo](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ApplicationsListBlade)) descrevem o aplicativo para o Microsoft Azure AD e podem ser considerados a definição do aplicativo, permitindo que o serviço saiba como emitir tokens para o aplicativo baseado nas configurações. O objeto de aplicativo somente existirá no diretório base, mesmo se for um aplicativo multilocatário que dá suporte a entidades de serviço em outros diretórios. O objeto de aplicativo pode incluir qualquer um dos itens a seguir (assim como informações adicionais não mencionadas aqui):
* Nome, logotipo e editor
* URLs de resposta
* Segredos (chaves simétricas e/ou assimétricas usadas para autenticar o aplicativo)
* Dependências de API (OAuth)
* APIs/recursos/escopos publicados (OAuth)
* Funções de aplicativo (RBAC)
* Metadados de SSO e configuração
* Configuração e metadados de provisionamento de usuário
* Configuração e metadados de proxy

Objetos de aplicativo podem ser criados através de vários caminhos, incluindo:
* Registros de aplicativo no Portal do Azure
* Criar um novo aplicativo usando o Visual Studio e configurando-o para usar a autenticação do Microsoft Azure AD
* Quando um administrador adiciona um aplicativo da galeria de aplicativos (isso também cria uma entidade de serviço)
* Usar a API do Microsoft Graph, Microsoft Azure AD Graph ou o PowerShell para criar um novo aplicativo
* Muitos outras, incluindo várias experiências de desenvolvedor no Azure e em experiências de explorador de APIs em centros de desenvolvedores

## <a name="what-are-service-principals-and-where-do-they-come-from"></a>Quais são as entidades de serviço e de onde originam-se?
[Entidades de serviço](active-directory-application-objects.md#service-principal-object) (que podem ser gerenciadas por meio de [Aplicativos Empresariais](https://portal.azure.com/#blade/Microsoft_AAD_IAM/StartboardApplicationsMenuBlade/AllApps/menuId/)) são as que realmente governam um aplicativo que conecta-se ao Microsoft Azure AD e podem ser consideradas a instância do aplicativo no diretório. Para qualquer aplicativo, é possível ter no máximo um objeto de aplicativo (que é registrado em um diretório "base") e um ou mais objetos de entidade de serviço que representam instâncias do aplicativo em todos os diretórios nos quais atuam. 

A entidade de serviço pode incluir:

* Uma referência de volta a um objeto de aplicativo por meio da propriedade da ID do aplicativo
* Registros de atribuições de função de aplicativo de usuário e grupo locais
* Registros de permissões de usuários e administradores locais concedidas ao aplicativo
  * Por exemplo: permissão para o aplicativo acessar o email de um usuário específico
* Registros de políticas locais, incluindo política de acesso condicional
* Registros de configurações locais alternativas para um aplicativo
  * Declara regras de transformação
  * Mapeamentos de atributos (provisionamento do usuário)
  * Funções de aplicativo específicas do diretório (se o aplicativo der suporte a funções personalizadas)
  * Nome ou logotipo específico do diretório

Como os objetos de aplicativo, as entidades de serviço também podem ser criadas por meio de vários caminhos, incluindo:

* Quando os usuários entram em um aplicativo de terceiros integrado ao Microsoft Azure AD
  * Durante a entrada, os usuários são solicitados a conceder permissão ao aplicativo para acessar o perfil e outras permissões. A primeira pessoa a dar o consentimento faz com que a entidade de serviço que representa o aplicativo seja adicionada ao diretório.
* Quando os usuários entram nos serviços online da Microsoft, como [Office 365](http://products.office.com/)
  * Quando você assina o Office 365 ou inicia uma avaliação, uma ou mais entidades de serviço são criadas no diretório que representa os vários serviços utilizados para fornecer toda a funcionalidade associada ao Office 365.
  * Alguns serviços do Office 365, como o SharePoint, criam entidades de serviço continuamente para permitir a comunicação segura entre componentes, incluindo fluxos de trabalho.
* Quando um administrador adiciona um aplicativo da galeria de aplicativos (isso também cria um objeto de aplicativo subjacente)
* Adicionar um aplicativo para usar o [Proxy de Aplicativo do Microsoft Azure AD](https://msdn.microsoft.com/library/azure/dn768219.aspx)
* Conectar um aplicativo para logon único usando SAML ou SSO (logon único de senha)
* Programaticamente via API do Graph do Microsoft Azure AD ou PowerShell

## <a name="how-are-application-objects-and-service-principals-related-to-each-other"></a>Como os objetos de aplicativo e as entidades de serviço estão relacionados entre si?
Um aplicativo tem um objeto de aplicativo no diretório base que é referenciado por uma ou mais entidades de serviço em cada um dos diretórios onde opera (incluindo o diretório base do aplicativo).
![Um diagrama ilustrando como objetos de aplicativo e entidades de serviço interagem entre si e instâncias do Microsoft Azure AD.][apps_service_principals_directory]

No diagrama anterior, a Microsoft mantém dois diretórios internamente (mostrados à esquerda) que usa para publicar aplicativos:

* Um para Microsoft Apps (diretório de serviços da Microsoft)
* Um para aplicativos de terceiros pré-integrados (diretório da galeria de aplicativos)

Editores/fornecedores de aplicativos que integram-se ao Microsoft Azure AD precisam ter um diretório de publicação (mostrado à direita como "Algum Diretório SaaS").

Os aplicativos que você adiciona (representados como **Aplicativo (seu)** no diagrama) incluem:

* Aplicativos que você desenvolveu (integrados ao Microsoft Azure AD)
* Aplicativos conectados por você para logon único
* Aplicativos que você publicou usando o proxy de aplicativo do Microsoft Azure AD

### <a name="notes-and-exceptions"></a>Notas e exceções
* Nem todas as entidades de serviço apontam para um objeto de aplicativo. Quando o Microsoft Azure AD foi originalmente criado, os serviços fornecidos aos aplicativos eram mais limitados e a entidade de serviço era suficiente para estabelecer uma identidade do aplicativo. A entidade de serviço original era mais próxima, em termos de formato, da conta de serviço do Active Directory do Windows Server. Por esse motivo, ainda é possível criar entidades de serviço por meio de diferentes caminhos, como usar o PowerShell do Microsoft Azure AD, sem primeiro criar um objeto de aplicativo. A API do Graph do Microsoft Azure AD exige um objeto de aplicativo antes de criar uma entidade de serviço.
* Nem todas as informações descritas acima estão expostas programaticamente. Os itens a seguir estão disponíveis apenas na interface do usuário:
  * Declara regras de transformação
  * Mapeamentos de atributos (provisionamento do usuário)
* Para obter informações mais detalhadas sobre a entidade de serviço e os objetos de aplicativo, consulte a documentação de referência da API REST do Microsoft Azure AD Graph:
  * [Aplicativo](https://msdn.microsoft.com/library/azure/ad/graph/api/entity-and-complex-type-reference#application-entity)
  * [Entidade de serviço](https://msdn.microsoft.com/library/azure/ad/graph/api/entity-and-complex-type-reference#serviceprincipal-entity)

## <a name="why-do-applications-integrate-with-azure-ad"></a>Por que os aplicativos integram-se ao Microsoft Azure AD?
Os aplicativos são adicionados ao Microsoft Azure AD para aproveitar um ou mais dos serviços fornecidos, incluindo:

* Autenticação e autorização de aplicativos
* Autenticação e autorização de usuário
* SSO usando federação ou senha
* Provisionamento e sincronização de usuário
* Controle de acesso baseado em função - Use o diretório para definir funções de aplicativo para executar verificações de autorização baseadas em funções em um aplicativo
* Serviços de autorização do OAuth - Usados pelo Office 365 e outros aplicativos da Microsoft para autorizar o acesso a APIs/recursos
* Publicação de aplicativo e proxy - Publique um aplicativo a partir de uma rede privada na Internet

## <a name="who-has-permission-to-add-applications-to-my-azure-ad-instance"></a>Quem tem permissão para adicionar aplicativos à minha instância do AD do Azure?
Embora existam algumas tarefas que apenas administradores globais podem realizar (por exemplo, adicionar aplicativos da galeria de aplicativos e configurar um aplicativo para usar o Proxy de Aplicativo), todos os usuários do diretório têm direitos para registrar objetos de aplicativo que estão desenvolvendo e discrição sobre quais aplicativos eles compartilham/fornecem acesso aos dados organizacionais por meio do consentimento. Se uma pessoa for o primeiro usuário no diretório a entrar em um aplicativo e conceder consentimento, isso criará uma entidade de serviço no locatário, caso contrário, as informações de concessão de consentimento serão armazenadas na entidade de serviço existente.

Permitir que usuários registrem e forneçam consentimento a aplicativos inicialmente pode parecer preocupante, mas lembre-se do seguinte:


* Aplicativos foram capazes de aproveitar o Windows Server Active Directory para autenticação de usuário por muitos anos sem exigir que o aplicativo fosse registrado ou gravado no diretório. Agora, a organização terá visibilidade aprimorada para saber exatamente quantos aplicativos estão usando o diretório e com que finalidade.
* Delegar essas responsabilidades aos usuários nega a necessidade de registro de aplicativo controlado pelo administrador e processo de publicação. Com o ADFS (Serviços de Federação do Active Directory), era provável que um administrador tivesse que adicionar um aplicativo como parte confiável em nome de seus desenvolvedores. Agora, os desenvolvedores podem usar o autoatendimento.
* Usuários que entram em aplicativos usando as contas da organização para fins comerciais é algo positivo. Se esses usuários deixarem a organização posteriormente, ele perderão automaticamente o acesso à conta no aplicativo que estavam utilizando.
* Ter um registro de quais dados foram compartilhados com qual o aplicativo é algo bom. Os dados estão mais transportáveis do que nunca e é útil ter um registro evidente de quem compartilhou quais dados com quais aplicativos.
* Proprietários de API que usam o Microsoft Azure AD para OAuth decidem exatamente quais permissões os usuários podem conceder aos aplicativos e quais permissões exigem o consentimento de um administrador. Somente administradores podem conceder escopos maiores e permissões mais significativas, enquanto o consentimento do usuário é direcionado aos dados e recursos próprios dos usuários.
* Quando um usuário adiciona ou permite que um aplicativo acesse seus dados, o evento poderá ser auditado de modo que você possa exibir os Relatórios de Auditoria no Portal do Azure para determinar como um aplicativo foi adicionado ao diretório.

Se você ainda quiser impedir os usuários no seu diretório de registrarem aplicativos e de entrarem em aplicativos sem a aprovação do administrador, há duas configurações que você poderá alterar para desativar esses recursos:

* Para impedir usuários de consentirem aplicativos em seu próprio nome:
  1. No Portal do Azure, vá para a seção [Configurações de Usuário](https://portal.azure.com/#blade/Microsoft_AAD_IAM/StartboardApplicationsMenuBlade/UserSettings/menuId/) em Aplicativos Empresariais.
  2. Altere **Os usuários podem consentir que os aplicativos acessem os dados da empresa em seu nome** para **Não**. 
     *Observe que, se você decidir desativar o consentimento do usuário, será necessário que um administrador forneça consentimento a qualquer novo aplicativo que um usuário precise utilizar.*
* Para impedir que os usuários registrem seus próprios aplicativos:
  1. No Portal do Azure, vá para a seção [Configurações de Usuário](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/UserSettings) em Azure Active Directory
  2. Altere **Os usuários podem registrar aplicativos** para **Não**.

> [!NOTE]
> A própria Microsoft usa a configuração padrão com usuários capazes de registrar aplicativos e dar consentimento a aplicativos em seu próprio nome.

<!--Image references-->
[apps_service_principals_directory]:../media/active-directory-how-applications-are-added/HowAppsAreAddedToAAD.jpg

