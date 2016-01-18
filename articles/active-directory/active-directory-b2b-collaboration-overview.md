<properties
   pageTitle="Colaboração B2B do Azure AD (Active Directory do Azure) | Microsoft Azure"
   description="Colaboração do Active Directory B2B do Azure permite que os parceiros de negócios acessem seus aplicativos corporativos, com cada um de seus usuários representado por uma conta exclusiva do AD do Azure."
   services="active-directory"
   documentationCenter=""
   authors="curtand"
   manager="stevenpo"
   editor=""/>

<tags
   ms.service="active-directory"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="identity"
   ms.date="01/05/2016"
   ms.author="curtand"/>

# Colaboração B2B do AD do Azure (Active Directory do Azure)

A colaboração B2B do AD do Azure permite habilitar o acesso aos seus aplicativos corporativos por meio de identidades gerenciadas por parceiros. Você pode criar relações entre empresas convidando e autorizando usuários de empresas parceiras para acessar os recursos. A complexidade é reduzida, pois cada empresa é federada uma única vez com o Active Directory do Azure e cada usuário é representado por uma única conta do AD do Azure. A segurança é aumentada porque o acesso é revogado quando usuários parceiros são desligados de suas organizações e acesso involuntário por meio da associação a diretórios internos é impedido. Para parceiros de negócios que ainda não têm o AD do Azure, a colaboração B2B tem uma experiência de inscrição simplificada para fornecer contas do AD do Azure aos parceiros de negócios.

-   Seus parceiros de negócios usam suas próprias credenciais de entrada, que libera você de gerenciar um diretório de parceiro externo e da necessidade de remover o acesso quando os usuários saem da organização do parceiro.

-   Você gerencia o acesso aos aplicativos independentemente do ciclo de vida da conta do parceiro de negócio. Por exemplo, isso significa que você pode revogar o acesso sem precisar pedir ao departamento de TI de seu parceiro de negócio para fazer qualquer coisa.

## Funcionalidades

Colaboração B2B simplifica o gerenciamento e aprimora a segurança de acesso do parceiro a recursos corporativos, incluindo aplicativos SaaS, como o Office 365, a equipe de vendas, serviços do Azure e todos os aplicativos móveis, de nuvem e locais, com reconhecimento de declaração. Colaboração B2B permite aos parceiros gerenciar suas próprias contas e as empresas podem aplicar políticas de segurança para acesso de parceiros.

No Active Directory B2B do Azure a colaboração é fácil de configurar com inscrição simplificada para parceiros de todos os portes, mesmo que eles não tenham seu próprio Active Directory do Azure por meio de um processo verificado de email. Também é fácil de manter sem diretórios externos ou por configuração de federação do parceiro.

O processo:

1. Colaboração do AD B2B do Azure permite que um administrador da empresa convide e autorize um conjunto de usuários externos ao carregar um arquivo CSV (Valores Separados por Vírgulas) de no máximo 2.000 linhas para o portal de colaboração B2B.

  ![Caixa de diálogo de upload do arquivo CSV](./media/active-directory-b2b-collaboration-overview/upload-csv.png)

2. O portal enviará convites por email aos usuários externos.

3. O usuário convidado entrará em uma conta existente de trabalho com a Microsoft (gerenciada no AD do Azure) ou obterá uma nova conta de trabalho no AD do Azure.

4. Depois de conectado, o usuário será redirecionado para o aplicativo que foi compartilhado com eles.

Atualmente, não há suporte a convites para endereços de email do consumidor (por exemplo, Gmail ou [*comcast.net*](http://comcast.net/)).

Para obter mais informações sobre como funciona a colaboração B2B, confira [este vídeo](http://aka.ms/aadshowb2b).

## Formato de arquivo CSV

O arquivo CSV segue o formato abaixo. Adicione todas as vírgulas necessárias mesmo se você não especificar uma ou mais opções.

**Email:** endereço de email para usuário convidado.<br/> **DisplayName:** nome de exibição para o usuário convidado (normalmente, nome e sobrenome).<br/> **InviteAppID:** a ID do aplicativo a ser usado para identidade visual do convite por email e páginas de aceitação.<br/> **InviteReplyURL:** URL para a qual direcionar um usuário convidado após a aceitação do convite. Deve ser uma URL específica da empresa (como [*contoso.my.salesforce.com*](http://contoso.my.salesforce.com/)). Se esse campo opcional não for especificado, a URL do Painel de Acesso da empresa responsável pelo convite é gerado (essa URL está no formato `https://account.activedirectory.windowsazure.com/applications/default.aspx?tenantId=<TenantID>`).<br/> **InviteAppResources:** AppIDs aos quais os aplicativos podem atribuir usuários. AppIDs são recuperáveis chamando `Get-MsolServicePrincipal | fl DisplayName, AppPrincipalId`<br/> **InviteGroupResources:** ObjectIDs para grupos aos quais adicionar o usuário. ObjectIDs são recuperáveis chamando a URL `Get-MsolGroup | fl DisplayName, ObjectId`<br/> **InviteContactUsUrl:** "Fale conosco" a ser incluída em convites de email, caso o usuário convidado queira entrar em contato com sua organização.<br/>

## Arquivo CSV de exemplo
Aqui está um exemplo de CSV que você pode modificar para suas finalidades. Salve-o com qualquer nome de arquivo de sua preferência, mas certifique-se de que ele tenha uma extensão de arquivo “. csv”.

```
Email,DisplayName,InviteAppID,InviteReplyUrl,InviteAppResources,InviteGroupResources,InviteContactUsUrl
wharp@contoso.com,Walter Harp,cd3ed3de-93ee-400b-8b19-b61ef44a0f29,http://azure.microsoft.com/services/active-directory/,,,http://azure.microsoft.com/services/active-directory/
jsmith@contoso.com,Jeff Smith,cd3ed3de-93ee-400b-8b19-b61ef44a0f29,http://azure.microsoft.com/services/active-directory/,,,http://azure.microsoft.com/services/active-directory/
bsmith@contoso.com,Ben Smith,cd3ed3de-93ee-400b-8b19-b61ef44a0f29,http://azure.microsoft.com/services/active-directory/,,,http://azure.microsoft.com/services/active-directory/
```
## Próximas etapas
Conferir nossos outros artigos sobre a colaboração B2B do Azure

- [O que é a colaboração AD B2B do Azure?](active-directory-b2b-what-is-azure-ad-b2b.md)
- [Como funciona](active-directory-b2b-how-it-works.md)
- [Passo a passo detalhado](active-directory-b2b-detailed-walkthrough.md)
- [Referência do formato de arquivo CSV](active-directory-b2b-references-csv-file-format.md)
- [Formato do token de usuário externo](active-directory-b2b-references-external-user-token-format.md)
- [Alterações de atributo do objeto de usuário externo](active-directory-b2b-references-external-user-object-attribute-changes.md)
- [Limitações atuais da visualização](active-directory-b2b-current-preview-limitations.md)

<!---HONumber=AcomDC_0107_2016-->