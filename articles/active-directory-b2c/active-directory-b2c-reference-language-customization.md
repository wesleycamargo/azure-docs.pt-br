---
title: "Usar personalização de idioma - Azure Active Directory B2C| Microsoft Docs"
description: 
services: active-directory-b2c
documentationcenter: 
author: sama
ms.assetid: eec4d418-453f-4755-8b30-5ed997841b56
ms.service: active-directory-b2c
ms.workload: identity
ms.tgt_pltfrm: na
ms.topic: article
ms.devlang: na
ms.date: 02/26/2018
ms.author: sama
ms.openlocfilehash: 332c6b4ffd841a2c7aef9db5c8ba9e843790f4d6
ms.sourcegitcommit: 782d5955e1bec50a17d9366a8e2bf583559dca9e
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/02/2018
---
# <a name="azure-active-directory-b2c-using-language-customization"></a>Azure Active Directory B2C: usar personalização de idioma

>[!NOTE]
>Esse recurso está em uma versão prévia.
>

A personalização de idioma permite que sua política seja compatível com diferentes idiomas para atender às necessidades do cliente.  A Microsoft fornece traduções para 36 idiomas (consulte [Informações adicionais](#additional-information)), mas você também pode fornecer suas próprias traduções para qualquer idioma.  Mesmo que sua experiência seja fornecida apenas para um único idioma, você pode personalizar qualquer texto nas páginas.  

## <a name="how-does-language-customization-work"></a>Como funciona a personalização de idioma?
A personalização de idiomas permite que você selecione em quais idiomas o seu percurso do usuário está disponível.  Quando o recurso está habilitado, você pode fornecer o parâmetro de cadeia de caracteres de consulta, ui_locales, do seu aplicativo.  Quando você chama o Azure AD B2C, nós traduzimos a página para a localidade que você indicou.  Esse o tipo de configuração fornece-lhe total controle sobre os idiomas em seu percurso do usuário e ignora as configurações de idioma do navegador do cliente. Como alternativa, você talvez não precise desse nível de controle sobre quais idiomas o seu cliente vê.  Se você não fornecer um parâmetro ui_locales, a experiência do cliente será determinada pelas configurações do navegador dele.  Você ainda pode controlar para quais idiomas seu percurso do usuário está convertido adicionando-os como um idioma com suporte.  Se o navegador do cliente está definido para mostrar um idioma que você não deseja oferecer suporte, o idioma selecionado como padrão da cultura com suporte é mostrado.

1. **Idiomas especificados por local de IU** – depois que você habilita a personalização de idiomas, seu percurso do usuário é convertido para o idioma especificado aqui
2. **Idioma solicitado pelo navegador** - se nenhuma localidade de IU foi especificada, ele traduzirá para o idioma solicitado pelo navegador, **se ele estiver incluído nos idiomas com suporte**
3. **Idioma padrão de política** – se o navegador não especificar um idioma ou especificar um que não tenha suporte, ele traduzirá para o idioma padrão da política

>[!NOTE]
>Se você estiver usando atributos personalizados de usuário, você precisa fornecer suas próprias traduções. Confira "[Como personalizar suas cadeias de caracteres](#customize-your-strings)" para obter mais detalhes.
>

## <a name="support-uilocales-requested-languages"></a>Suporte aos idiomas ui_locales solicitados 
Políticas que foram criadas antes da liberação geral de disponibilidade da personalização de idioma precisarão habilitar esse recurso primeiro.  Políticas criadas depois, terão a personalização de idioma habilitada por padrão.  Ao habilitar a "Personalização de idiomas" em uma política, agora você pode controlar o idioma do percurso do usuário adicionando o parâmetro ui_locales.
1. [Siga estas etapas para navegar para a página de recursos do B2C no Portal do Azure.](https://docs.microsoft.com/azure/active-directory-b2c/active-directory-b2c-app-registration#navigate-to-b2c-settings)
2. Navegue até uma política que você deseja habilitar para traduções.
3. Clique em **Personalização de idiomas**.  
4. Clique em **Habilitar personalização do idioma** na parte superior.
5. Leia a caixa de diálogo e clique em "Sim".

## <a name="select-which-languages-in-your-user-journey-are-enabled"></a>Selecione quais idiomas em seu percurso do usuário estão habilitados 
Permita que um conjunto de idiomas para seu percurso do usuário seja traduzido quando o parâmetro ui_locales não for fornecido.
1. Verifique se a sua política tem "Personalização de idiomas" habilitada nas instruções anteriores.
2. Na página **Editar Política**, selecione **Personalização de Idioma**.
3. Selecione um idioma que você deseja dar suporte.
4. No painel de propriedades, alterne **Habilitado** para Sim.  
5. Clique em **Salvar** na parte superior do painel de propriedades.

>[!NOTE]
>Se um parâmetro ui_locales não for fornecido, a página será traduzida para o idioma do navegador do cliente somente se estiver habilitada
>

## <a name="customize-your-strings"></a>Como personalizar suas cadeias de caracteres
A "personalização de idiomas" permite que você personalize qualquer cadeia de caracteres em seu percurso do usuário.
1. Verifique se a sua política tem "Personalização de idiomas" habilitada nas instruções anteriores.
2. Na página **Editar Política**, selecione **Personalização de Idioma**.
3. Selecione o idioma que deseja personalizar.
4. Selecione a página que você deseja editar.
5. Clique em **Baixar Padrões** (ou **Baixar substituições**, se esse idioma já foi editado anteriormente). 

Essas etapas fornecem um arquivo JSON que você pode usar para começar a editar suas cadeias de caracteres.

### <a name="changing-any-string-on-the-page"></a>Alteração de qualquer cadeia de caracteres na página
1. Abra o arquivo JSON baixado das instruções anteriores em um editor de JSON.
2. Localize o elemento que você deseja alterar.  Você pode encontrar o `StringId` da cadeia de caracteres que você está procurando, ou procurar o `Value` que você deseja alterar.
3. Atualize o atributo `Value` com o que você deseja exibir.
4. Para cada cadeia de caracteres que deseja alterar, lembre-se de alternar `Override` para **Verdadeiro**.
5. Salve o arquivo e faça o upload de suas alterações (é possível localizar o controle de upload no mesmo local onde você baixou o arquivo JSON). 

>[!IMPORTANT]
>Se você precisar substituir uma cadeia de caracteres, certifique-se de que o valor `Override` esteja definido para `true`.  Se o valor não mudou, a entrada será ignorada. 
>

### <a name="changing-extension-attributes"></a>Alteração dos atributos de extensão
Se você deseja alterar a cadeia de caracteres para um atributo do usuário personalizado, ou adicionar uma ao JSON, faça isto no seguinte formato:
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

### <a name="using-localizedcollections"></a>Uso do LocalizedCollections
Se você deseja fornecer uma lista de conjunto de valores de respostas, você precisa criar um `LocalizedCollections`.  Um `LocalizedCollections` é uma matriz de pares de `Name` e `Value`.  O `Name` é o que é exibido e o `Value` é o que é retornado na declaração.  Para adicionar um `LocalizedCollections`, ele tem o seguinte formato:

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

* `ElementId` é o atributo do usuário, para o qual `LocalizedCollections` é uma resposta
* `Name` é o valor mostrado ao usuário
* `Value` é retornado na declaração quando essa opção está selecionada

### <a name="upload-your-changes"></a>Carregamento das suas alterações
1. Depois de concluir as alterações para o arquivo JSON, navegue de volta para seu locatário B2C.
2. Na página **Editar Política**, selecione **Personalização de Idioma**.
3. Selecione o idioma que deseja fornecer as traduções.
4. Selecione a página que deseja fornecer as traduções.
5. Clique no ícone da pasta e selecione o arquivo JSON para upload.
6. Isso foi alterado e salvo para sua política automaticamente.

## <a name="using-page-ui-customization-with-language-customization"></a>Usar personalização da interface do usuário da ágina com personalização de idioma

Há duas maneiras de localizar o conteúdo HTML.  Ativando ['Personalização de idioma'](active-directory-b2c-reference-language-customization.md).  Habilitar esse recurso permite que o Azure AD B2C encaminhe o parâmetro do Open ID Connect, `ui-locales`, para seu ponto de extremidade.  O servidor de conteúdo pode usar esse parâmetro para fornecer páginas HTML personalizadas que são específicas a um idioma.

Alternativamente, podemos extrair conteúdo de diferentes locais com base na localidade que está sendo usada.  No ponto de extremidade habilitado para CORS, é possível configurar uma estrutura de pastas para hospedar conteúdo para idiomas específicos e chamaremos coreto, se você colocar o valor de curinga, `{Culture:RFC5646}`.  Por exemplo, se tiver isso como URI da página personalizada:

```
https://wingtiptoysb2c.blob.core.windows.net/{Culture:RFC5646}/wingtip/unified.html
```
É possível carregar a página em `fr` e, quando estiver efetuando pull do conteúdo html e css, ele efetuará pull a partir de:
```
https://wingtiptoysb2c.blob.core.windows.net/fr/wingtip/unified.html
```

## <a name="custom-locales"></a>Localidades personalizadas

Também é possível adicionar idiomas para os quais a Microsoft atualmente não oferece traduções.  Além disso, será necessário fornecer as traduções para todas as cadeias de caracteres na política.

1. Na página **Editar Política**, selecione **Personalização de Idioma**.
2. Selecione **Adicionar idioma personalizado** a partir da parte superior da página.
3. No painel de contexto é aberto, identifique qual idioma você está fornecendo traduções ao inserir um código de localidade válido.
4. Para cada página, você poderá baixar um conjunto de substituições para o idioma inglês e trabalhar nas traduções.
5. Após concluir os arquivos JSON, será possível enviá-los para cada página.
6. Selecione **Habilitar** e a política agora poderá mostrar esse idioma ao usuário.
7. Lembre-se de salvar o idioma após habilitá-lo.

## <a name="additional-information"></a>Informações adicionais

### <a name="page-ui-customization-labels-are-persisted-as-your-first-set-of-overrides-once-language-customization-is-enabled"></a>Os rótulos de personalização da interface do usuário da página são persistidos como o primeiro conjunto de substituições, uma vez que a "Personalização de idioma" está habilitada
Ao habilitar a "personalização de idioma", as edições anteriores para rótulos usando a personalização da interface do usuário da página são persistidas em um arquivo JSON para inglês (en).  Você pode continuar a alterar os rótulos e outras cadeias de caracteres, carregando recursos de idiomas na "personalização de idiomas".
### <a name="microsoft-is-committed-to-provide-the-most-up-to-date-translations-for-your-use"></a>A Microsoft está comprometida em fornecer as traduções mais atualizadas
Vamos aprimorar continuamente as traduções e mantê-las em conformidade para você.  Identificaremos as alterações na terminologia global e faremos as atualizações que funcionarão diretamente no seu percurso do usuário.
### <a name="support-for-right-to-left-languages"></a>Suporte para idiomas da direita para a esquerda
Atualmente, não há suporte para idiomas escritos da direita para a esquerda, se você precisar desse recurso, vote para ele nos [Comentários do Azure](https://feedback.azure.com/forums/169401-azure-active-directory/suggestions/19393000-provide-language-support-for-right-to-left-languag).
### <a name="social-identity-provider-translations"></a>Traduções de provedor de identidade social
Fornecemos o parâmetro OIDC ui_locales para logons sociais, mas eles não são respeitados por alguns provedores de identidade social, incluindo o Facebook e o Google. 
### <a name="browser-behavior"></a>Comportamento do navegador
Ambos o Chrome e o Firefox solicitam para o idioma definido e, caso seja um idioma com suporte, ele será exibido antes do padrão.  Atualmente, o Edge não solicita um idioma e exibe diretamente o idioma padrão.

### <a name="what-languages-are-supported"></a>Quais são os idiomas com suporte?

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
