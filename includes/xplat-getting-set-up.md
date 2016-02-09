<properties services="virtual-machines" title="Setting up Azure CLI for service management" authors="squillace" solutions="" manager="timlt" editor="tysonn" />

<tags
   ms.service="virtual-machine"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="linux"
   ms.workload="infrastructure"
   ms.date="04/13/2015"
   ms.author="rasquill" />

## Usando a CLI do Azure

As etapas a seguir o ajudarão a usar a CLI do Azure facilmente com a versão mais recente e com a assinatura correta. Se você precisar instalar a CLI do Azure e conectá-lo primeiro em sua conta, confira [Interface de Linha de Comando do Azure (CLI do Azure)](xplat-cli-install.md).

### Etapa 1: atualizar a versão da CLI do Azure

Para usar a CLI do Azure para comandos imperativos com modo de gerenciamento de serviço, você deve ter uma versão recente, se possível. Para verificar a sua versão, digite `azure --version`. Você deve ver algo como:

    $ azure --version
    0.8.17 (node: 0.10.25)

Se quiser atualizar a sua versão da CLI do Azure, confira [CLI do Azure](https://github.com/Azure/azure-xplat-cli).

### Etapa 2: definir a conta e assinatura do Azure

Depois de conectar a CLI do Azure com a conta que você deseja usar, você pode ter mais de uma assinatura. Se você tiver, deverá rever as assinaturas disponíveis para a sua conta, digitando `azure account list` e então selecionando a assinatura que quer usar digitando `azure account set <subscription id or name> true`, em que _nome ou id de assinatura_ é a id da assinatura ou o nome da assinatura com que você gostaria de trabalhar na sessão atual. Você verá algo semelhante ao que se segue:

    $ azure account set "Visual Studio Ultimate with MSDN" true
    info:    Executing command account set
    info:    Setting subscription to "Visual Studio Ultimate with MSDN" with id "0e220bf6-5caa-4e9f-8383-51f16b6c109f".
    info:    Changes saved
    info:    account set command OK

> [AZURE.NOTE] Se você ainda não tiver uma conta do Azure, mas tem uma assinatura para a assinatura do MSDN, você pode obter créditos gratuitos do Azure ativando os seus [benefícios de assinante do MSDN aqui](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/) – ou você pode usar a conta gratuita. Ambos funcionarão para o acesso ao Azure.

<!---HONumber=AcomDC_0128_2016-->