<properties
   pageTitle="Visão geral sobre os diversos portais necessários para criar uma oferta de Marketplace | Microsoft Azure"
   description="Visão geral sobre os diversos portais necessários para criar uma oferta de Marketplace"
   services="marketplace-publishing"
   documentationCenter=""
   authors="HannibalSII"
   manager=""
   editor=""/>

<tags
   ms.service="marketplace"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="07/27/2016"
   ms.author="hascipio" />


# Portais que você precisará
Antes de iniciar o processo de publicação de uma oferta, vamos apresentar os diversos portais que serão necessários. Abaixo está o resumo sobre os portais - Centro de Desenvolvedores, Portal de Publicação do Azure e Portal do Azure - na ordem em que você vai interagir com eles.
## Central de Desenvolvedores
[http://dev.windows.com/registration?accountprogram=azure](http://dev.windows.com/registration?accountprogram=azure)
### Descrição
Criar sua conta do Centro de Desenvolvedores da Microsoft é uma tarefa única. Certifique-se de que a empresa ainda não tem uma conta do Centro de Desenvolvedores antes de tentar criar uma. Durante o processo, nós coletamos informações de conta bancária, informações fiscais e informações de endereço da empresa.

> [AZURE.NOTE] Se você estiver publicando apenas ofertas gratuitas (ou ofertas “traga sua própria licença”), nós não exigiremos informações de imposto e banco.

### Identidade/conta usada
De maneira ideal, isso é uma lista de distribuição ou um grupo de segurança (por exemplo, azurepublishing@*partnercompany*.com). A lista de distribuição ou um grupo de segurança **deve** ser registrado como uma conta da Microsoft.

> [AZURE.TIP] Nós recomendamos usar uma lista de distribuição ou um grupo de segurança, pois ele remove a dependência de qualquer pessoa, embora uma conta individual também possa ser usada.

## Portal de publicação
[https://publish.windowsazure.com](https://publish.windowsazure.com)

### Descrição
Isso é o portal que você usa para trabalhar na oferta e publicá-la (marketing, preços, publicação, certificação se aplicável etc.).

### Identidade/conta usada
O grupo de segurança ou a lista de distribuição acima deve ser usada pela primeira vez para entrar no portal de publicação. Posteriormente, outros usuários podem ser adicionados como coadministradores. Isso é como é mapeada para os dados de registro do Centro de Desenvolvedores.

## Portal do Azure
[https://portal.azure.com](https://portal.azure.com)
### Descrição
Isso é o portal onde você pode exibir suas ofertas preparadas e publicadas no Azure Marketplace (aplicável a máquinas virtuais, modelos de soluções e serviços de desenvolvedor baseados no Gerenciador de Recursos do Azure).
### Identidade/conta usada
Enquanto você estiver preparando uma oferta do portal de publicação, um ID de assinatura deve estar na lista branca. A mesma assinatura (há um nome de usuário e senha associados a ela) precisa ser usada para entrar neste portal para testar a oferta de preparação.

## Consulte também
- [Introdução: Como publicar uma oferta para o Azure Marketplace](marketplace-publishing-getting-started.md)

<!---HONumber=AcomDC_0803_2016-->