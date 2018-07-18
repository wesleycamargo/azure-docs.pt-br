---
title: Personalização de idioma no Azure AD B2C | Microsoft Docs
description: Saiba como personalizar a experiência de idioma.
services: active-directory-b2c
documentationcenter: ''
author: davidmu1
manager: mtillman
editor: ''
ms.service: active-directory-b2c
ms.workload: identity
ms.topic: article
ms.date: 02/26/2018
ms.author: davidmu
ms.openlocfilehash: 3d0f1f2ffd02873df2e2e7eab9894d9c3421b0f7
ms.sourcegitcommit: 20d103fb8658b29b48115782fe01f76239b240aa
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/03/2018
---
# <a name="language-customization-in-azure-active-directory-b2c"></a>Personalização de idioma no Azure Active Directory B2C

>[!NOTE]
>Esse recurso está em uma versão prévia.
>

A personalização de idioma no Azure Active Directory B2C (Azure AD B2C) permite que sua política seja compatível com diferentes idiomas para atender às necessidades do cliente.  A Microsoft fornece as traduções para [36 idiomas](#supported-languages), mas você também pode fornecer suas próprias traduções para qualquer idioma. Mesmo que sua experiência seja fornecida apenas para um único idioma, você pode personalizar qualquer texto nas páginas.  

## <a name="how-language-customization-works"></a>Como funciona a personalização de idioma
Você usa a personalização de idioma para selecionar em quais idiomas o percurso do usuário estará disponível. Depois que o recurso estiver habilitado, você poderá fornecer o parâmetro da cadeia de caracteres de consulta `ui_locales`, a partir do seu aplicativo. Quando você chama o Azure AD B2C, sua página é traduzida para a localidade que você indicou. Esse o tipo de configuração fornece-lhe total controle sobre os idiomas em seu percurso do usuário e ignora as configurações de idioma do navegador do cliente. 

Talvez você não precise desse nível de controle sobre quais idiomas seu cliente vê. Se você não fornecer um parâmetro `ui_locales` a experiência do cliente será determinada pelas configurações do navegador.  Você ainda pode controlar para quais idiomas seu percurso do usuário está convertido adicionando-os como um idioma com suporte. Se o navegador de um cliente estiver configurado para mostrar um idioma que você não deseja fornecer suporte, o idioma que você selecionou como padrão nas culturas com suporte será mostrado.

- **Idiomas especificados por ui_locales**: após habilitar a personalização do idioma, o percurso do usuário será traduzido para o idioma especificado aqui.
- **Idioma solicitado pelo navegador**: se nenhum parâmetro `ui_locales` foi especificado, o percurso do usuário será traduzido para o idioma solicitado pelo navegador, *se houver suporte para o idioma*.
- **Idioma padrão da política**: se o navegador não especificar um idioma ou especificar um que não seja tenha suporte, o percurso do usuário será traduzido para o idioma padrão da política.

>[!NOTE]
>Se você estiver usando atributos de usuário personalizados, precisará fornecer suas próprias traduções. Para obter mais informações, consulte [Personalizar as cadeias de caracteres](#customize-your-strings).
>

## <a name="support-requested-languages-for-uilocales"></a>Solicitação de suporte de idiomas para ui_locales 
Políticas criadas antes da disponibilidade geral de personalização de idioma precisarão, primeiro, habilitar esse recurso. Políticas criadas após a personalização de idioma são habilitadas por padrão. 

Ao habilitar a personalização de idioma em uma política você poderá controlar o idioma do percurso do usuário, adicionando o parâmetro `ui_locales`.
1. [Vá para a página de recursos do B2C no Portal do Azure](https://docs.microsoft.com/azure/active-directory-b2c/active-directory-b2c-app-registration#navigate-to-b2c-settings).
2. Navegue até uma política que você deseja habilitar para traduções.
3. Selecione **Personalização de idioma**.  
4. Selecione **Habilitar personalização de idioma**.
5. Leia as informações na caixa de diálogo e selecione **Sim**.

## <a name="select-which-languages-in-your-user-journey-are-enabled"></a>Selecione quais idiomas em seu percurso do usuário estão habilitados 
Habilite um conjunto de idiomas para que o percurso do usuário seja traduzido quando o parâmetro `ui_locales` não for fornecido.
1. Certifique-se de que a política tenha a personalização de idioma habilitada a partir das instruções anteriores.
2. Na página **Editar política**, selecione **Personalização de idioma**.
3. Selecione um idioma que você deseja fornecer suporte.
4. No painel de propriedades, altere **Habilitado** para **Sim**.  
5. Selecione **Salvar** na parte superior do painel de propriedades.

>[!NOTE]
>Se um parâmetro `ui_locales` não for fornecido, a página será traduzida para o idioma do navegador do cliente somente se estiver habilitada.
>

## <a name="customize-your-strings"></a>Como personalizar suas cadeias de caracteres
A personalização de idioma permite que você personalize qualquer cadeia de caracteres no percurso do usuário.
1. Verifique se a política tenha a personalização de idioma habilitada nas instruções anteriores.
2. Na página **Editar política**, selecione **Personalização de idioma**.
3. Selecione o idioma que você deseja personalizar.
4. Selecione a página que você deseja editar.
5. Selecione **Baixar Padrões** (ou **Baixar substituições**, se esse idioma já foi editado anteriormente). 

Essas etapas fornecem um arquivo JSON que você pode usar para começar a editar suas cadeias de caracteres.

### <a name="change-any-string-on-the-page"></a>Alterar qualquer cadeia de caractere na página
1. Abra o arquivo JSON baixado das instruções anteriores em um editor de JSON.
2. Localize o elemento que você deseja alterar.  Você pode localizar `StringId` para a cadeia de caracteres que está procurando ou procurar o atributo `Value` que deseja alterar.
3. Atualize o atributo `Value` com o que você deseja exibir.
4. Para cada cadeia de caracteres que você deseja alterar, altere `Override` para `true`.
5. Salve o arquivo e carregue suas alterações. (É possível localizar o controle de upload no mesmo local de onde baixou o arquivo JSON.) 

>[!IMPORTANT]
>Se você precisar substituir uma cadeia de caracteres, certifique-se de que o valor `Override` esteja definido para `true`.  Se o valor não mudou, a entrada será ignorada. 
>

### <a name="change-extension-attributes"></a>Alterar atributos de extensão
Se você quiser alterar a cadeia de caracteres de um atributo de usuário personalizado ou se deseja adicionar um ao JSON, ele estará no seguinte formato:
```JSON
{
  "LocalizedStrings": [
    {
      "ElementType": "ClaimType",
      "ElementId": "extension_<ExtensionAttribute>",
      "StringId": "DisplayName",
      "Override": true,
      "Value": "<ExtensionAttributeValue>"
    }
    [...]
}
```

Substitua `<ExtensionAttribute>` pelo nome do seu atributo de usuário personalizado.  

Substitua `<ExtensionAttributeValue>` por uma cadeia de caracteres nova a ser exibida.

### <a name="provide-a-list-of-values-by-using-localizedcollections"></a>Fornecer uma lista de valores usando LocalizedCollections
Se você quiser fornecer uma lista configurada de valores para respostas, será necessário criar um atributo `LocalizedCollections`.  `LocalizedCollections` é uma matriz de pares `Name` e `Value`. Para adicionar `LocalizedCollections`, use o formato a seguir:

```JSON
{
  "LocalizedStrings": [...],
  "LocalizedCollections": [{
      "ElementType":"ClaimType", 
      "ElementId":"<UserAttribute>",
      "TargetCollection":"Restriction",
      "Override": true,
      "Items":[
           {
                "Name":"<Response1>",
                "Value":"<Value1>"
           },
           {
                "Name":"<Response2>",
                "Value":"<Value2>"
           }
     ]
  }]
}
```

* `ElementId` é o atributo do usuário ao qual esse atributo `LocalizedCollections` é uma resposta.
* `Name` é o valor mostrado para o usuário.
* `Value` é o que é retornado na declaração quando essa opção é selecionada.

### <a name="upload-your-changes"></a>Carregamento das suas alterações
1. Após concluir as alterações no arquivo JSON, retorne para o locatário B2C.
2. Na página **Editar política**, selecione **Personalização de idioma**.
3. Selecione o idioma para o qual você deseja traduzir.
4. Selecione a página onde você deseja fornecer traduções.
5. Selecione o ícone da pasta e selecione o arquivo JSON para upload.
 
As alterações são salvas na política automaticamente.

## <a name="customize-the-page-ui-by-using-language-customization"></a>Personalizar a interface do usuário da página usando personalização de idioma

Há duas maneiras de localizar o conteúdo HTML. Uma maneira é ativar a [personalização de idioma](active-directory-b2c-reference-language-customization.md). Habilitar esse recurso permite que o Azure AD B2C encaminhe o parâmetro do Open ID Connect, `ui-locales`, para seu ponto de extremidade.  O servidor de conteúdo pode usar esse parâmetro para fornecer páginas HTML personalizadas que são específicas a um idioma.

Como alternativa, é possível extrair conteúdo de lugares diferentes com base na localidade que está sendo usada. No ponto de extremidade habilitado para CORS, é possível configurar uma estrutura de pastas para hospedar conteúdo para idiomas específicos. Você chamará adequadamente se usar o valor curinga `{Culture:RFC5646}`.  Por exemplo, suponha que essa é a sua URI de página personalizada:

```
https://wingtiptoysb2c.blob.core.windows.net/{Culture:RFC5646}/wingtip/unified.html
```
Você pode carregar a página em `fr`. Quando a página efetua pulls de conteúdo HTML e CSS, ela efetua pulls de:
```
https://wingtiptoysb2c.blob.core.windows.net/fr/wingtip/unified.html
```

## <a name="add-custom-locales"></a>Adicionar localidades personalizadas

Também é possível adicionar idiomas para os quais a Microsoft atualmente não oferece traduções. Você precisará fornecer as traduções para todas as cadeias de caracteres da política.

1. Na página **Editar política**, selecione **Personalização de idioma**.
2. Selecione **Adicionar idioma personalizado** a partir da parte superior da página.
3. No painel de contexto aberto, identifique o idioma para o qual você está fornecendo traduções, inserindo um código de localidade válido.
4. Para cada página, você poderá baixar um conjunto de substituições para o idioma inglês e trabalhar nas traduções.
5. Após concluir os arquivos JSON, você poderá enviá-los para cada página.
6. Selecione **Habilitar** e a política agora poderá mostrar esse idioma aos usuários.
7. Salve o idioma.

## <a name="additional-information"></a>Informações adicionais

### <a name="page-ui-customization-labels-as-overrides"></a>Rótulos de personalização da interface do usuário da página como substituições
Ao habilitar a personalização de idioma, as edições anteriores para rótulos usando a personalização da interface do usuário da página são persistidas em um arquivo JSON para inglês (en). Você pode continuar a alterar os rótulos e outras cadeias de caracteres, carregando recursos de idiomas na personalização de idioma.
### <a name="up-to-date-translations"></a>Atualizar traduções
A Microsoft está comprometida em fornecer as traduções mais atualizadas para seu uso. A Microsoft aprimora continuamente as traduções e as mantém em conformidade para você. A Microsoft identificará bugs e alterações na terminologia global e fará atualizações que funcionarão perfeitamente no percurso do usuário.
### <a name="support-for-right-to-left-languages"></a>Suporte para idiomas da direita para a esquerda
A Microsoft atualmente não fornece suporte para idiomas escritos da direita para a esquerda. Se você precisar desse recurso, vote para ele nos [Comentários do Azure](https://feedback.azure.com/forums/169401-azure-active-directory/suggestions/19393000-provide-language-support-for-right-to-left-languag).
### <a name="social-identity-provider-translations"></a>Traduções de provedor de identidade social
A Microsoft fornece o parâmetro OIDC `ui_locales` para logons sociais. Mas alguns provedores de identidade social, incluindo o Facebook e o Google, não os consideram. 
### <a name="browser-behavior"></a>Comportamento do navegador
Ambos Chrome e o Firefox solicitam o idioma definido. Se for um idioma com suporte, será exibido antes do padrão. Atualmente, o Edge não solicita um idioma e exibe diretamente o idioma padrão.

### <a name="supported-languages"></a>Idiomas com suporte

| Linguagem              | Código de idioma |
|-----------------------|---------------|
| Bangla                | bn            |
| Tcheco                 | cs            |
| Dinamarquês                | da            |
| Alemão                | de            |
| Grego                 | el            |
| Inglês               | en            |
| Espanhol               | es            |
| Finlandês               | fi            |
| Francês                | fr            |
| Guzerate              | gu            |
| Híndi                 | hi            |
| Croata              | hr            |
| Húngaro             | hu            |
| Italiano               | it            |
| Japonês              | ja            |
| Kannada               | kn            |
| Coreano                | ko            |
| Malaiala             | ml            |
| Marati               | mr            |
| Malaio                 | ms            |
| Norueguês Bokmal      | nb            |
| Holandês                 | nl            |
| Punjabi               | pa            |
| Polonês                | pl            |
| Português - Brasil   | pt-br         |
| Português - Portugal | pt-pt         |
| Romeno              | ro            |
| Russo               | ru            |
| Eslovaco                | sk            |
| Sueco               | sv            |
| Tâmil                 | ta            |
| Télugo                | te            |
| Tailandês                  | th            |
| Turco               | tr            |
| Chinês - Simplificado  | zh-hans       |
| Chinês - Tradicional | zh-hant       |
