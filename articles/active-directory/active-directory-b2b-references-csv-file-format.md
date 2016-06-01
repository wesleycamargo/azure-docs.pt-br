<properties
   pageTitle="Formato de arquivo CSV para a visualização da colaboração B2B do Active Directory do Azure | Microsoft Azure"
   description="A B2B do Active Directory do Azure dá suporte a relações entre empresas, permitindo que os parceiros de negócios acessem de maneira seletiva seus aplicativos corporativos"
   services="active-directory"
   documentationCenter=""
   authors="viv-liu"
   manager="cliffdi"
   editor=""
   tags=""/>

<tags
   ms.service="active-directory"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="identity"
   ms.date="05/09/2016"
   ms.author="viviali"/>

# Visualização da colaboração B2B do AD do Azure: formato de arquivo CSV

A versão de visualização da colaboração B2B do AD do Azure requer um arquivo CSV especificando as informações do usuário do parceiro a serem carregadas por meio do portal do AD do Azure. O arquivo CSV deve conter os rótulos necessários abaixo e campos opcionais, conforme necessário. Modifique o arquivo CSV de exemplo (abaixo) sem alterar a ortografia dos rótulos na primeira linha.

>[AZURE.NOTE] A primeira linha de rótulos (como Email, DisplayName e assim por diante) é necessária para que o arquivo CSV seja analisado com êxito. A ortografia deve corresponder aos campos especificados abaixo. Esses rótulos identificam o conteúdo abaixo deles. Para campos opcionais que não são necessários, seus rótulos podem ser removidos do arquivo CSV. A coluna inteira pode ser deixada em branco.

## Campos obrigatórios: <br/>
**Email:** endereço de email do usuário convidado. <br/> **DisplayName:** nome de exibição para o usuário convidado (normalmente, nome e sobrenome).<br/>


## Campos opcionais: <br/>

**InvitationText:** personalize o texto do email de convite após a identidade visual do aplicativo e antes do link de resgate.<br/> **InvitedToApplications:** AppIDs de aplicativos corporativos para atribuir aos usuários. As AppIDs são recuperáveis no PowerShell por meio da chamada de `Get-MsolServicePrincipal | fl DisplayName, AppPrincipalId`<br/> **InvitedToGroups:** ObjectIDs dos grupos aos quais o usuário será adicionado. As ObjectIDs são recuperáveis no PowerShell por meio da chamada de `Get-MsolGroup | fl DisplayName, ObjectId`<br/> **InviteRedirectURL:** URL para direcionar um usuário convidado após a aceitação do convite. Deve ser uma URL específica da empresa (como [*contoso.my.salesforce.com*](http://contoso.my.salesforce.com/)). Se esse campo opcional não for especificado, o usuário convidado será direcionado para o Painel de Acesso do Aplicativo, em que ele poderá navegar para seus aplicativos corporativos escolhidos. A URL do Painel de Acesso do Aplicativo está no formato `https://account.activedirectory.windowsazure.com/applications/default.aspx?tenantId=<TenantID>`.<br/> **CcEmailAddress**: endereço de email para copiar o convite por email. Se o campo CcEmailAddress for usado, este convite não poderá ser usado para criação de locatário ou usuário verificado por email.<br/> **Language:** idioma do email de convite e da experiência de resgate, com “en” (inglês) como padrão quando não especificado. Os outros 10 códigos de idioma com suporte são:<br/>
1. de: alemão<br/>
2. es: espanhol<br/>
3. fr: francês<br/>
4. it: italiano<br/>
5. ja: japonês<br/>
6. ko: coreano<br/>
7. pt-BR: português (Brasil)<br/>
8. ru: russo<br/>
9. zh-HANS: chinês simplificado<br/>
10. zh-HANT: chinês tradicional<br/>

## Arquivo CSV de exemplo
Veja um CSV de exemplo que você pode modificar.

>[AZURE.NOTE] Copie e cole isto no Bloco de Notas e salve-o com uma extensão de arquivo “.csv”. Em seguida, edite-o no Excel. Ele será estruturado em uma tabela com rótulos na primeira linha.

> Adicione mais campos opcionais no Excel especificando o rótulo e populando a coluna abaixo dele.

```
Email,DisplayName,InvitationText,InviteRedirectUrl,InvitedToApplications,InvitedToGroups,CcEmailAddress,Language
wharp@contoso.com,Walter Harp,Hi Walter! I hope you’ve been doing well.,,cd3ed3de-93ee-400b-8b19-b61ef44a0f29,,you@yourcompany.com,en
jsmith@contoso.com,Jeff Smith,Hi Jeff! I hope you’ve been doing well.,,cd3ed3de-93ee-400b-8b19-b61ef44a0f29,,you@yourcompany.com,en
bsmith@contoso.com,Ben Smith,Hi Ben! I hope you’ve been doing well.,,cd3ed3de-93ee-400b-8b19-b61ef44a0f29,,you@yourcompany.com,en

```

## Artigos relacionados
Navegue por nossos outros artigos sobre colaboração B2B no Azure AD

- [O que é a colaboração B2B do AD do Azure?](active-directory-b2b-what-is-azure-ad-b2b.md)
- [Como funciona](active-directory-b2b-how-it-works.md)
- [Passo a passo detalhado](active-directory-b2b-detailed-walkthrough.md)
- [Formato do token de usuário externo](active-directory-b2b-references-external-user-token-format.md)
- [Alterações de atributo do objeto de usuário externo](active-directory-b2b-references-external-user-object-attribute-changes.md)
- [Limitações atuais da visualização](active-directory-b2b-current-preview-limitations.md)
- [Índice de artigos para Gerenciamento de Aplicativos no Active Directory do Azure](active-directory-apps-index.md)

<!---HONumber=AcomDC_0518_2016-->