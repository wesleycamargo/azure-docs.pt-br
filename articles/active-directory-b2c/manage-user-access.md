---
title: Gerenciar o acesso de usuários no Azure Active Directory B2C | Microsoft Docs
description: Saiba como identificar menores, coletar dados de data de nascimento e de país, e obter a aceitação dos termos de uso em seu aplicativo usando o Azure AD B2C.
services: active-directory-b2c
author: davidmu1
manager: mtillman
ms.service: active-directory
ms.workload: identity
ms.topic: article
ms.date: 05/04/2018
ms.author: davidmu
ms.component: B2C
ms.openlocfilehash: 52cf34d56f87d2543fb272ce99dbe011bc0ea037
ms.sourcegitcommit: 6116082991b98c8ee7a3ab0927cf588c3972eeaa
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/05/2018
ms.locfileid: "34711120"
---
# <a name="manage-user-access-in-azure-ad-b2c"></a>Gerenciar o acesso do usuário no Azure AD B2C

Este artigo fornece informações de como você pode gerenciar o acesso do usuário em seus aplicativos usando o AD (Azure Active Directory) B2C. O gerenciamento de acesso no seu aplicativo inclui:

- Identificação de menores e controle de acesso para usar o aplicativo
- Exigência do consentimento dos pais para que menores usem seus aplicativos
- Coleta de dados de data de nascimento e de país do usuário
- Captura de contrato de termos de uso e restrição de acesso

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

>[!Note] 
>Este artigo fornece informações que podem ser usadas para dar suporte às suas obrigações no GDPR. Se você estiver buscando informações gerais sobre o GDPR, confira a [Seção do GDPR do portal de Confiança do Serviço](https://servicetrust.microsoft.com/ViewPage/GDPRGetStarted).

## <a name="control-minor-access"></a>Controle de acesso de menor

Os aplicativos e as organizações podem optar por impedir que menores usem aplicativos e serviços que não sejam direcionados para essa audiência. Como alternativa, os aplicativos e as organizações podem optar por aceitar o uso por menores, gerenciar o consentimento dos pais e entregar experiências permitidas para menores, conforme a determinação das regras de negócios e o permitido pela lei. 

Se um usuário for identificado como menor, o fluxo de usuário no Azure AD B2C poderá ser definido como uma de três opções:

- **Retornar um id_token JWT assinado para o aplicativo** – o usuário é registrado no diretório e um token é retornado ao aplicativo. Em seguida, o aplicativo continua usando regras de negócio. Por exemplo, o aplicativo pode continuar com um processo de consentimento dos pais. Para fazer isso, escolha receber as declarações **ageGroup** e **consentProvidedForMinor** do aplicativo.
- **Enviar um token JSON não assinado para o aplicativo** – o Azure AD B2C notifica o aplicativo que o usuário é menor e fornece o status do consentimento dos pais do usuário. Em seguida, o aplicativo continua usando regras de negócio. Um token JSON não conclui uma autenticação bem-sucedida com o aplicativo. O aplicativo precisa processar o usuário não autenticado de acordo com as declarações incluídas no token JSON, que podem incluir **nome**, **email**, **ageGroup** e **consentProvidedForMinor**.
- **Bloquear o usuário** – se o usuário for menor e o consentimento dos pais não for fornecido.  Em seguida, o Azure AD B2C poderá apresentar uma tela ao usuário informando que ele está sendo bloqueado.  Nenhum token será emitido, o acesso será bloqueado e a conta de usuário não será criada durante um percurso de registro. Para implementar isso, você fornece uma página de conteúdo HTML/CSS adequada para informar o usuário e apresentar as opções apropriadas. Nenhuma ação adicional do aplicativo é necessária para novos registros.

## <a name="get-parental-consent"></a>Obter o consentimento dos pais

Dependendo do regulamento do aplicativo, pode ser necessário que o consentimento dos pais seja concedido por um usuário verificado como adulto.  O Azure AD B2C não oferece uma experiência para verificar a idade da pessoa e permitir que um adulto verificado dê o consentimento dos pais a um menor.  Essa experiência precisa ser fornecida pelo aplicativo ou por outro provedor de serviços.

Este é um exemplo de um fluxo de usuário para a coleta de consentimento dos pais:

1. Uma operação da [API do Graph do Azure Active Directory](https://msdn.microsoft.com/en-us/library/azure/ad/graph/api/api-catalog) identifica o usuário como menor e retorna os dados do usuário ao aplicativo na forma de um token JSON não assinado.
2. O aplicativo processa o token JSON e mostra uma tela para o menor, notificando que o consentimento dos pais é necessário e solicitando o consentimento de um pai online. 
3. O Azure AD B2C mostra uma jornada de entrada por meio da qual o usuário tem permissão de entrar normalmente e emite um token ao aplicativo definido para incluir **legalAgeGroupClassification = “minorWithParentalConsent”** O aplicativo coleta o endereço de email do pai e verifica se o pai é um adulto usando uma fonte confiável, como o órgão nacional de IDs, uma verificação de carteira de motorista ou uma prova de cartão de crédito. Em caso de sucesso, o aplicativo solicita que o menor entre usando o fluxo de usuário do Azure AD B2C. Se a autorização for negada (por exemplo, **legalAgeGroupClassification = “minorWithoutParentalConsent”**, o Azure AD B2C retornará um token JSON (não um logon) ao aplicativo para reiniciar o processo de consentimento. Opcionalmente, é possível personalizar o fluxo de usuário em que um menor ou um adulto pode recuperar o acesso à conta do menor enviando um código de registro para o endereço de email do menor ou para o endereço de email do adulto no registro.
4. O aplicativo oferece uma opção para o menor revogar o consentimento.
5. Quando o menor ou o adulto revoga o consentimento, a API do Graph do Azure AD pode ser usada para alterar **consetProvidedForMinor** para **negado**. Como alternativa, o aplicativo pode optar por excluir um menor cujo consentimento foi revogado. Opcionalmente, é possível personalizar o fluxo de usuário pelo qual o menor autenticado (ou o pai usando a conta do menor) pode revogar o consentimento. O Azure AD B2C registra **consentProvidedForMinor** como **negado**.

Para obter mais informações sobre o **legalAgeGroupClassification**, o **consentProvidedForMinor** e o **ageGroup**, confira [User Resource Type](https://developer.microsoft.com/en-us/graph/docs/api-reference/beta/resources/user) (Tipo de recurso do usuário). Para obter mais informações sobre atributos personalizados, confira [Usar atributos personalizados para coletar informações sobre seus consumidores](active-directory-b2c-reference-custom-attr.md). Ao abordar atributos estendidos usando a API do Graph do Azure AD, a versão longa do atributo precisa ser usada, como "extension_18b70cf9bb834edd8f38521c2583cd86_dateOfBirth": "2011-01-01T00:00:00Z"

## <a name="gather-date-of-birth-and-country-data"></a>Obter dados de data de nascimento e de país

Os aplicativos podem contar com o Azure AD B2C para coletar a DOB (data de nascimento) e o país de todos os usuários durante o registro. Se as informações de DOB ou de país ainda não existirem, elas poderão ser solicitadas ao usuário durante a próxima jornada de autenticação (entrada). Os usuários não poderão continuar sem fornecer as informações de DOB e de país. Com base na DOB e no país fornecidos, o Azure AD B2C determinará se a pessoa é considerada menor, de acordo com os padrões regulamentares desse país. 

Um fluxo de usuário personalizado pode reunir informações de DOB e de país e usar a transformação de declarações do Azure AD B2C para determinar o **ageGroup** e persistir o resultado (ou persistir as informações de país e de DOB diretamente) no diretório.

As etapas a seguir mostram a lógica que é usada para calcular o **ageGroup** usando a data de nascimento:

1. Tente localizar o país pelo código do país na lista. Se o país não for localizado, retorne ao **Padrão**.
2. Se o nó **MinorConsent** estiver presente no elemento de país:  <br>a. Calcule a data mínima em que o usuário precisaria ter nascido para ser considerado adulto. Exemplo: se a data de nascimento for 14/3/2015 e **MinorConsent** for 18, a data de nascimento mínima será 14/3/2000.
    <br>b. Compare a data de nascimento mínima com a data de nascimento real. Se a data de nascimento mínima for antes da data de nascimento do usuário, o cálculo retornará **Menor** como o cálculo de faixa etária.
3. Se o nó **MinorNoConsentRequired** estiver presente no elemento de país, repita as etapas 2a e 2b usando o valor de **MinorNoConsentRequired**. O resultado de 2b retornará **MinorNoConsentRequired** se a data de nascimento mínima for antes da data de nascimento do usuário. 
4. Se nenhum dos cálculos retornar true, o cálculo retornará **Adulto**.

Se um aplicativo tiver coletado dados de DOB e de país de forma confiável por outros métodos, o aplicativo poderá usar a API do Graph para atualizar o registro de usuário com essas informações. Por exemplo: 

- Se um usuário já tiver sido determinado como adulto, atualize o atributo de diretório **ageGroup** com o valor **Adulto**.
- Se um usuário já tiver sido determinado como menor, atualize o atributo de diretório **ageGroup** com o valor **Menor** e defina **consentProvidedForMinor** conforme o necessário.

Para obter mais informações sobre a coleta de dados de DOB, confira [Using age gating in Azure AD B2C](basic-age-gating.md) (Usando a restrição de idade no Azure AD B2C).

## <a name="capture-terms-of-use-agreement"></a>Capturar contrato de termos de uso

Ao desenvolver seu aplicativo, normalmente você captura a aceitação de termos de uso do usuário em seus aplicativos, com uma participação nula ou mínima do diretório de usuários.  No entanto, é possível usar um fluxo de usuário do Azure AD B2C para reunir o contrato de termos de uso, restringir o acesso sem aceitação e impor a aceitação das alterações futuras nos termos de uso com base na data da última aceitação e na data da versão mais recente dos termos de uso.

Os **Termos de Uso** também podem incluir "Consentimento para compartilhar dados com terceiros".  A aceitação positiva dessas condições de um usuário pode ser obtida tecnicamente em conjunto ou o usuário pode ser capaz de aceitar uma e não outra, dependendo dos regulamentos locais e das regras de negócio.

As etapas a seguir descrevem os recursos de gerenciamento de termos de uso:

1. Registrar a aceitação dos termos de uso e a data da aceitação do uso da API do Graph e de seus atributos estendidos. Isso pode ser feito usando os dois fluxos de usuário, interno e personalizado. É recomendável que você crie e use os atributos **extension_termsOfUseConsentDateTime** e **extension_termsOfUseConsentVersion**.
2. Criar uma caixa de seleção necessária intitulada "Aceitar termos de uso" e gravar o resultado durante a inscrição. Isso pode ser feito usando os dois fluxos de usuário, interno e personalizado.
3. O Azure AD B2C armazena o contrato de termos de uso e o consentimento. A API do Graph pode ser usada para consultar o status de qualquer usuário lendo o atributo de extensão usado para registrar a resposta, por exemplo, lendo **termsOfUseTestUpdateDateTime**. Isso pode ser feito usando os dois fluxos de usuário, interno e personalizado.
4. Exigir a aceitação dos termos de uso atualizados comparando a data da aceitação com a data da última versão dos termos de uso. Isso só pode ser feito usando um fluxo de usuário personalizado. Use o atributo estendido **extension_termsOfUseConsentDateTime** e compare o valor com a declaração de **termsOfUseTextUpdateDateTime**. Se a aceitação for antiga, force uma nova tela de aceitação autodeclaradas, caso contrário, bloqueie o acesso usando a lógica da política.
5. Exigir a aceitação dos termos de uso do atualizados comparando o número de versão da aceitação com o último número de versão aceito. Isso só pode ser feito usando um fluxo de usuário personalizado. Use o atributo estendido **extension_termsOfUseConsentDateTime** e compare o valor com a declaração de **extension_termsOfUseConsentVersion**. Se a aceitação for antiga, force uma nova aceitação autodeclarada, caso contrário, automático declarada tela, bloqueie o acesso usando a lógica da política.

A captura do consentimento para os termos de uso pode ser apresentada ao usuário nos seguintes cenários:

- Um novo usuário está se inscrevendo. Os termos de uso são exibidos, o resultado do consentimento é armazenado.
- Um usuário está entrando e já aceitou o consentimento anteriormente para os termos do contrato mais recentes ou ativos. Os termos de uso não são exibidos.
- Um usuário está entrando e ainda não aceitou o consentimento para os termos de uso mais recentes ou ativos. Os termos de uso são exibidos, o resultado do consentimento é armazenado.
- Um usuário está entrando e já aceitou o consentimento para os termos de uso mais antigos, que agora estão atualizados para uma versão mais recente. Os termos de uso são exibidos, o resultado do consentimento é armazenado.

A imagem a seguir mostra o fluxo de usuário recomendado:

![fluxo de aceitação do usuário](./media/manage-user-access/user-flow.png) 

Este é um exemplo de consentimento para termos de uso baseado em DateTime em uma declaração:

```
<ClaimsTransformations>
  <ClaimsTransformation Id="GetNewUserAgreeToTermsOfUseConsentDateTime" TransformationMethod="GetCurrentDateTime">
    <OutputClaims>
      <OutputClaim ClaimTypeReferenceId="extension_termsOfUseConsentDateTime" TransformationClaimType="currentDateTime" />
    </OutputClaims>
  </ClaimsTransformation>
  <ClaimsTransformation Id="IsTermsOfUseConsentRequired" TransformationMethod="IsTermsOfUseConsentRequired">
    <InputClaims>
      <InputClaim ClaimTypeReferenceId="extension_termsOfUseConsentDateTime" TransformationClaimType="termsOfUseConsentDateTime" />
    </InputClaims>
    <InputParameters>
      <InputParameter Id="termsOfUseTextUpdateDateTime" DataType="dateTime" Value="2098-01-30T23:03:45" />
    </InputParameters>
    <OutputClaims>
      <OutputClaim ClaimTypeReferenceId="termsOfUseConsentRequired" TransformationClaimType="result" />
    </OutputClaims>
  </ClaimsTransformation>
</ClaimsTransformations>
```

Este é um exemplo de consentimento para termos de uso baseado em Versão em uma declaração:

```
<ClaimsTransformations>
  <ClaimsTransformation Id="GetEmptyTermsOfUseConsentVersionForNewUser" TransformationMethod="CreateStringClaim">
    <InputParameters>
      <InputParameter Id="value" DataType="string" Value=""/>
    </InputParameters>
    <OutputClaims>
      <OutputClaim ClaimTypeReferenceId="extension_termsOfUseConsentVersion" TransformationClaimType="createdClaim" />
    </OutputClaims>
  </ClaimsTransformation>
  <ClaimsTransformation Id="GetNewUserAgreeToTermsOfUseConsentVersion" TransformationMethod="CreateStringClaim">
    <InputParameters>
      <InputParameter Id="value" DataType="string" Value="V1"/>
    </InputParameters>
    <OutputClaims>
      <OutputClaim ClaimTypeReferenceId="extension_termsOfUseConsentVersion" TransformationClaimType="createdClaim" />
    </OutputClaims>
  </ClaimsTransformation>
  <ClaimsTransformation Id="IsTermsOfUseConsentRequiredForVersion" TransformationMethod="CompareClaimToValue">
    <InputClaims>
      <InputClaim ClaimTypeReferenceId="extension_termsOfUseConsentVersion" TransformationClaimType="inputClaim1" />
    </InputClaims>
    <InputParameters>
      <InputParameter Id="compareTo" DataType="string" Value="V1" />
      <InputParameter Id="operator" DataType="string" Value="not equal" />
      <InputParameter Id="ignoreCase" DataType="string" Value="true" />
    </InputParameters>
    <OutputClaims>
      <OutputClaim ClaimTypeReferenceId="termsOfUseConsentRequired" TransformationClaimType="outputClaim" />
    </OutputClaims>
  </ClaimsTransformation>
</ClaimsTransformations> 
```

## <a name="next-steps"></a>Próximas etapas

- Saiba como excluir e exportar dados do usuário em [Manage user data](manage-user-data.md) (Gerenciar dados de usuário)
