<properties
	pageTitle="Visualização do Active Directory B2C do Azure: atributos personalizados | Microsoft Azure"
	description="Como usar atributos personalizados no Active Directory B2C do Azure para coletar informações sobre seus consumidores"
	services="active-directory-b2c"
	documentationCenter=""
	authors="swkrish"
	manager="msmbaldwin"
	editor="curtand"/>

<tags
	ms.service="active-directory-b2c"
	ms.workload="identity"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="09/22/2015"
	ms.author="swkrish"/>

#  Visualização do Active Directory B2C do Azure: usar Atributos Personalizados para coletar informações sobre seus Consumidores

[AZURE.INCLUDE [active-directory-b2c-preview-note](../../includes/active-directory-b2c-preview-note.md)]

O diretório do Active Directory (AD) B2C do Azure vem com um conjunto interno de atributos, por exemplo: Nome, Sobrenome, Cidade, CEP, etc. No entanto, todos os aplicativos voltados para o consumidor têm requisitos exclusivos sobre quais informações (atributos) gostariam de coletar de seus consumidores. O Azure AD B2C permite que você amplie seu diretório (especificamente a extensão do conjunto de atributos armazenados em cada conta de consumidor). Você pode criar atributos personalizados no [portal de visualização do Azure](https://portal.azure.com/) e usá-los em suas políticas de inscrição, conforme mostrado abaixo. Você também pode ler e gravar esses atributos usando o Graph API do Azure AD conforme mostrado [aqui](active-directory-b2c-devquickstarts-graph-dotnet.md).

> [AZURE.NOTE]Os atributos personalizados usam [Extensões de esquema de diretório do Graph API do Azure AD](https://msdn.microsoft.com/library/azure/dn720459.aspx) nos bastidores.

## Como criar um Atributo Personalizado

1. [Navegue até a folha dos recursos B2C no portal de visualização do Azure](active-directory-b2c-app-registration.md#navigate-to-the-b2c-features-blade).
2. Clique em **Atributos de usuário**.
3. Clique em **+Adicionar** na parte superior da folha.
4. Forneça um **Nome** para o atributo personalizado (por exemplo, "ShoeSize") e, opcionalmente, uma **Descrição**. Clique em **Criar**. Pronto!

    > [AZURE.NOTE]Apenas a "String" **Tipo de Dados** está disponível no momento. Vamos adicionar mais tipos de dados (DateTime, Integer, etc.) no futuro.

O atributo personalizado agora está disponível na lista de **Atributos de usuário** e para uso em suas políticas de inscrição.

## Como usar um atributo personalizado em sua política de inscrição

1. Navegue até a folha dos recursos do B2C no [portal de visualização do Azure](htts://portal.azure.com/). Leia [aqui](active-directory-b2c-app-registration.md#navigate-to-the-b2c-features-blade) sobre como fazer isso.
2. Clique em **Políticas de inscrição**.
3. Abra a política de inscrição (por exemplo, "B2C\_1\_SiUp") clicando nela. Clique em **Editar** na parte superior da folha.
4. Clique em **Atributos de inscrição** e selecione o atributo personalizado (por exemplo, "ShoeSize"). Clique em **OK**.
5. Clique em **Declarações de aplicativo** e selecione o atributo personalizado. Clique em **OK**. 
6. Clique em **Salvar** na parte superior da folha. Pronto!

Você pode usar o recurso "Executar agora" da política para verificar a experiência do consumidor. Agora você deve ver "ShoeSize" na lista de atributos que estão sendo coletados durante a inscrição do consumidor e vê-lo no token enviado de volta ao seu aplicativo.

<!---HONumber=Sept15_HO4-->