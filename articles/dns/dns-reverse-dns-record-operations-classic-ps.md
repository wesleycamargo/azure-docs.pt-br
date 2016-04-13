<properties 
   pageTitle="Como gerenciar registros DNS reversos para seus serviços usando o PowerShell no modelo de implantação clássica | Microsoft Azure"
   description="Como gerenciar registros DNS reversos ou registros PTR para os serviços do Azure usando o PowerShell no modelo de implantação clássica."
   services="DNS"
   documentationCenter="na"
   authors="s-malone"
   manager="carmonm"
   editor=""
   tags="azure-service-management"
/>
<tags  
   ms.service="DNS"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="03/09/2016"
   ms.author="s-malone" />

# Como gerenciar registros DNS reversos para seus serviços (clássicos) usando o PowerShell

[AZURE.INCLUDE [dns-reverse-dns-record-operations-arm-selectors-include.md](../../includes/dns-reverse-dns-record-operations-arm-selectors-include.md)]
<BR>
[AZURE.INCLUDE [DNS-reverse-dns-record-operations-intro-include.md](../../includes/dns-reverse-dns-record-operations-intro-include.md)]
<BR>
[AZURE.INCLUDE [azure-arm-classic-important-include](../../includes/learn-about-deployment-models-classic-include.md)] [Resource Manager model](dns-reverse-dns-record-operations-ps.md).

## Validação de registros DNS reversos
Para garantir que um terceiro não crie registros DNS reversos que sejam mapeados para seus domínios DNS, o Azure permite apenas a criação de um registro DNS reverso, em que uma das seguintes opções é verdadeira:

- O FQDN do DNS reverso é o nome do Serviço de Nuvem para o qual foi especificado, ou qualquer nome do Serviço de Nuvem contido na mesma assinatura; por exemplo, o DNS reverso é “contosoapp1.cloudapp.net.”.
- O encaminhamento FQDN do DNS reverso é resolvido no nome ou IP do Serviço de Nuvem para o qual foi especificado, ou como qualquer nome ou IP do Serviço de Nuvem contido na mesma assinatura; por exemplo, o DNS reverso é “app1.contoso.com.”, que é um alias de CName para contosoapp1.cloudapp.net.

Verificações de validação são executadas somente quando a propriedade de DNS reverso de um Serviço de Nuvem é definida ou modificada. Uma nova validação periódica não é executada.

## Adicionar DNS reverso aos Serviços de Nuvem existentes
É possível adicionar um registro de DNS reverso a um Serviço de Nuvem existente usando o cmdlet "Set-AzureService":

	PS C:\> Set-AzureService –ServiceName “contosoapp1” –Description “App1 with Reverse DNS” –ReverseDnsFqdn “contosoapp1.cloudapp.net.”

## Criar um Serviço de Nuvem com DNS reverso  
É possível adicionar um novo Serviço de Nuvem com a propriedade de DNS reverso especificada usando o cmdlet “Set-AzureService”:

	PS C:\> New-AzureService –ServiceName “contosoapp1” –Location “West US” –Description “App1 with Reverse DNS” –ReverseDnsFqdn “contosoapp1.cloudapp.net.”

## Exibir o DNS reverso dos Serviços de Nuvem existentes
É possível exibir o valor configurado para um Serviço de Nuvem existente usando o cmdlet "Get-AzureService":

	PS C:\> Get-AzureService "contosoapp1"

## Remover o DNS reverso dos Serviços de Nuvem existentes
É possível remover uma propriedade de DNS reverso de um Serviço de Nuvem existente usando o cmdlet “Set-AzureService”. Isso é feito definindo o valor da propriedade do DNS reverso como em branco:

	PS C:\> Set-AzureService –ServiceName “contosoapp1” –Description “App1 with Reverse DNS” –ReverseDnsFqdn “”

[AZURE.INCLUDE [PERGUNTAS FREQUENTES](../../includes/dns-reverse-dns-record-operations-faq-asm-include.md)]

<!-----------HONumber=AcomDC_0330_2016-->