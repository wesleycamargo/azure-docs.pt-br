<properties
   pageTitle="Passo a passo detalhado usando a visualização de colaboração B2B do Active Directory do Azure| Microsoft Azure"
   description="A colaboração B2B do Active Directory do Azure dá suporte a relações entre empresas, permitindo que os parceiros de negócios acessem de maneira seletiva seus aplicativos corporativos"
   services="active-directory"
   documentationCenter=""
   authors="viv-liu"
   manager="cliffdi"
   editor=""
   tags=""/>

<tags
   ms.service="active-directory"
   ms.devlang="NA"
   ms.topic="get-started-article"
   ms.tgt_pltfrm="NA"
   ms.workload="identity"
   ms.date="05/09/2016"
   ms.author="viviali"/>

# Visualização da colaboração B2B do AD do Azure: passo a passo detalhado

Este passo a passo descreve como usar a colaboração B2B do AD do Azure. Como administradores de TI da Contoso, queremos compartilhar aplicativos com funcionários de três empresas parceiras. Nenhuma das empresas parceiras precisa ter o AD do Azure.

- Alice da Simple Partners Org
- Carlos, da Medium Partner Org, precisa ter acesso a um conjunto de aplicativos
- Brenda, da Complex Partner Org, precisa ter acesso a um conjunto de aplicativos e à associação em grupos na Contoso

Após o envio de convites a usuários de parceiros, podemos configurá-los no AD do Azure para conceder acesso aos aplicativos e associação a grupos por meio do portal do Azure. Vamos começar pela adição de Alice.

## Adicionando Alice ao diretório da Contoso
1. Crie um arquivo .csv com os cabeçalhos, conforme mostrado, preenchendo apenas o **Email**, **DisplayName** e **InviteContactUsUrl** de Alice. **DisplayName** é o nome que aparece no convite e também o nome que aparece no diretório Azure AD da Contoso. **InviteContactUsUrl** é uma maneira de Alice entrar em contato com a Contoso. No exemplo a seguir, InviteContactUsUrl especifica o perfil do LinkedIn da Contoso. É importante esclarecer os rótulos na primeira linha do arquivo. csv exatamente conforme especificados na [referência de formato de arquivo CSV](active-directory-b2b-references-csv-file-format.md). ![Exemplo de arquivo CSV para Alice](./media/active-directory-b2b-detailed-walkthrough/AliceCSV.png)

2. No portal do Azure, adicionar um usuário no diretório da Contoso (Active Directory > Contoso > Usuários > Adicionar Usuário). Na lista suspensa "Tipo de Usuário", selecione "Usuários em empresas parceiras". Carregue o arquivo .csv. Feche o arquivo .csv antes do carregamento. ![Carregamento do arquivo CSV para Alice](./media/active-directory-b2b-detailed-walkthrough/AliceUpload.png)

3. Alice agora é representada como um Usuário Externo no diretório do Azure AD da Contoso. ![Alice está listada no AD do Azure](./media/active-directory-b2b-detailed-walkthrough/AliceInAD.png)

4. Alice recebe o email a seguir. ![Email de convite para Alice](./media/active-directory-b2b-detailed-walkthrough/AliceEmail.png)

5. Alice clica no link e é solicitada a aceitar o convite e fazer logon usando suas credenciais corporativas. Se Alice não estiver no diretório do Azure AD, ela precisará se inscrever. ![Inscrição após o convite para Alice](./media/active-directory-b2b-detailed-walkthrough/AliceSignUp.png)

6. Alice é redirecionada para o Painel de Acesso do Aplicativo, vazio até que ela tenha acesso aos aplicativos. ![Painel de acesso para Alice](./media/active-directory-b2b-detailed-walkthrough/AliceAccessPanel.png)

Esse procedimento permite a forma mais simples de colaboração B2B. Como uma usuária no diretório do Azure AD da Contoso, Alice pode ter acesso a aplicativos e grupos por meio do portal do Azure. Agora vamos adicionar Carlos, que precisa de acesso aos aplicativos Moodle e Salesforce.

## Adicionando Carlos ao diretório da Contoso e concedendo acesso aos aplicativos
1. Use o Windows PowerShell com o módulo do AD do Azure instalado para encontrar as IDs de aplicativo Moodle e Salesforce. As IDs podem ser recuperadas usando o cmdlet: `Get-MsolServicePrincipal | fl DisplayName, AppPrincipalId` Isso abre uma lista de todos os aplicativos disponíveis na Contoso e suas AppPrincialIds. ![Recuperar IDs para Carlos](./media/active-directory-b2b-detailed-walkthrough/BobPowerShell.png)

2. Crie o arquivo .csv contendo Email, DisplayName, **InviteAppID**, **InviteAppResources** e InviteContactUsUrl de Carlos. Preencha **InviteAppResources** com a AppPrincipalIds do Moodle e da Salesforce encontradas no PowerShell, separadas por um espaço. Preencha **InviteAppId** com o AppPrincipalId do Moodle para identificar o email e entrar em páginas com um logotipo do Moodle. ![Exemplo de arquivo CSV para Carlos](./media/active-directory-b2b-detailed-walkthrough/BobCSV.png)

3. Carregue o arquivo .csv por meio do Portal do Azure assim como foi feito para Alice. Carlos agora é representado como um usuário externo no diretório do Azure AD da Contoso.

4. Carlos recebe o email a seguir. ![Email de convite para Carlos](./media/active-directory-b2b-detailed-walkthrough/BobEmail.png)

5. Carlos clica no link e é solicitado a aceitar o convite. Após conectar, ele é direcionado para o Painel de Acesso e já pode usar o Moodle e o Salesforce. ![Painel de acesso para Carlos](./media/active-directory-b2b-detailed-walkthrough/BobAccessPanel.png)

Adicionaremos Brenda em seguida, que precisa de acesso aos aplicativos e da associação a grupos no diretório da Contoso.

## Adicionando Brenda ao diretório da Contoso, concedendo acesso aos aplicativos e permitindo a associação a grupo

1. Use o Windows PowerShell com o módulo do AD do Azure instalado para encontrar as IDs do aplicativo e as IDs de grupo da Contoso.
 - Recupere AppPrincipalId usando o cmdlet `Get-MsolServicePrincipal | fl DisplayName, AppPrincipalId`, assim como foi feito para Carlos
 - Recuperar ObjectId para grupos usando o cmdlet `Get-MsolGroup | fl DisplayName, ObjectId`. Isso abre uma lista de todos os grupos na Contoso e suas ObjectIds. IDs de grupo também podem ser recuperadas como a ID de objeto na guia Propriedades do grupo no portal do Azure. ![Recuperar IDs e grupos para Brenda](./media/active-directory-b2b-detailed-walkthrough/CarolPowerShell.png)

2. Crie o arquivo .csv, preenchendo Email, DisplayName, InviteAppID, InviteAppResources, **InviteGroupResources** e InviteContactUsUrl de Brenda. **InviteGroupResources** é preenchida pelas ObjectIds dos grupos MeuGrupo1 e Externos, separados por um espaço. ![Exemplo de arquivo CSV para Brenda](./media/active-directory-b2b-detailed-walkthrough/CarolCSV.png)

3. Carregue o arquivo .csv por meio do portal do Azure.

4. Brenda é uma usuária no diretório da Contoso e também é um membro dos grupos MeuGroupo1 e Externos, como visto no portal do Azure. ![Brenda está listada em um grupo no AD do Azure](./media/active-directory-b2b-detailed-walkthrough/CarolGroup.png)

5. Brenda recebe um email contendo um link para aceitar o convite. Após a entrada, ela será redirecionada para o Painel de Acesso do Aplicativo a fim de ter acesso ao Moodle e ao Salesforce.

Isso é tudo que há para adicionar usuários de empresas parceiras em colaboração B2B do AD do Azure. Este passo a passo mostrou como adicionar os usuários Brenda, Alice e Carlos ao diretório Contoso usando três arquivos. csv separados. Esse processo pode ficar mais fácil com a condensação dos arquivos. csv separados em um único arquivo. ![Exemplo de arquivo CSV para Alice, Carlos e Brenda](./media/active-directory-b2b-detailed-walkthrough/CombinedCSV.png)

## Artigos relacionados
Procure nossos outros artigos sobre a colaboração B2B do AD do Azure:

- [O que é a colaboração B2B do AD do Azure?](active-directory-b2b-what-is-azure-ad-b2b.md)
- [Como funciona](active-directory-b2b-how-it-works.md)
- [Referência do formato de arquivo CSV](active-directory-b2b-references-csv-file-format.md)
- [Formato do token de usuário externo](active-directory-b2b-references-external-user-token-format.md)
- [Alterações de atributo do objeto de usuário externo](active-directory-b2b-references-external-user-object-attribute-changes.md)
- [Limitações atuais da visualização](active-directory-b2b-current-preview-limitations.md)
- [Índice de artigos para Gerenciamento de Aplicativos no Active Directory do Azure](active-directory-apps-index.md)

<!---HONumber=AcomDC_0831_2016-->