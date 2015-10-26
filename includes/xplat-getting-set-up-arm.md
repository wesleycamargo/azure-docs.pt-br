<properties services="virtual-machines" title="Using Azure CLI with Azure Resource Manager" authors="squillace" solutions="" manager="timlt" editor="tysonn" />

<tags
   ms.service="virtual-machine"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="linux"
   ms.workload="infrastructure"
   ms.date="04/13/2015"
   ms.author="rasquill" />

## Usando a CLI do Azure com o Gerenciador de Recursos do Azure (ARM)

Antes de usar a CLI do Azure com modelos e comandos do Gerenciador de Recursos para implantar recursos do Azure e cargas de trabalho usando grupos de recursos, será necessário ter uma conta com o Azure (obviamente). Se você não tiver uma conta, você pode obter uma [avaliação gratuita do Azure aqui](http://azure.microsoft.com/pricing/free-trial/).

> [AZURE.NOTE]Se você ainda não tiver uma conta do Azure, mas tem uma assinatura para a assinatura do MSDN, você pode obter créditos gratuitos do Azure ativando os seus [benefícios de assinante do MSDN aqui](http://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/) – ou você pode usar a conta gratuita. Ambos funcionarão para o acesso ao Azure.

### Etapa 1: verificar a versão da CLI do Azure

Para usar a CLI do Azure para comandos imperativos e modelos do ARM, é necessário ter pelo menos a versão 0.8.17. Para verificar a sua versão, digite `azure --version`. Você deve ver algo como:

    $ azure --version
    0.8.17 (node: 0.10.25)

Se precisar atualizar a sua versão da CLI do Azure, confira [CLI do Azure](https://github.com/Azure/azure-xplat-cli).

### Etapa 2: Verifique se você estiver usando uma identidade de trabalho ou de escola com o Azure

Você só pode usar o modo de comando ARM, se você estiver usando um [locatário do Azure Active Directory](https://msdn.microsoft.com/library/azure/jj573650.aspx#BKMK_WhatIsAnAzureADTenant) ou um [Nome Principal do Serviço](https://msdn.microsoft.com/library/azure/dn132633.aspx). (Esses também são chamados de *IDs organizacionais*.)

Para ver se você tiver um, faça logon digitando `azure login` e use o seu nome de usuário do trabalho ou de escola e a senha, quando solicitado. Se você tiver um, você verá algo semelhante ao seguinte:

    $ azure login
    info:    Executing command login
    warn:    Please note that currently you can login only via Microsoft organizational account or service principal. For instructions on how to set them up, please read http://aka.ms/Dhf67j.
    Username: ahmet@contoso.onmicrosoft.com
    Password: *********
    |info:    Added subscription Visual Studio Ultimate with MSDN
    info:    Setting subscription Visual Studio Ultimate with MSDN as default
    info:    Added subscription Azure Free Trial
    +
    info:    login command OK

Se você não vir isso, você deve criar um novo locatário (ou entidade de serviço) com sua identidade de conta da Microsoft. (Isso é geralmente o caso com as assinaturas do MSDN pessoais ou assinaturas de avaliação gratuitas.) Para criar uma ID de trabalho ou de escola a partir de sua conta do Azure criada com uma ID Microsoft, consulte [Associar um Diretório do AD do Azure com uma nova Assinatura do Azure](https://msdn.microsoft.com/library/azure/jj573650.aspx#BKMK_WhatIsAnAzureADTenant). Se você achar que você já deve ter uma ID organizacional, você precisará falar com a pessoa que criou a conta para você.

### Etapa 3: escolha a sua assinatura do Azure

Se você tiver apenas uma assinatura na sua conta do Azure, a CLI do Azure se associa a essa assinatura por padrão. Se você tiver mais de uma assinatura, você precisa selecionar a assinatura que deseja usar, digitando `azure account set <subscription id or name> true` onde _nome ou id de assinatura_ é a id da assinatura ou o nome da assinatura com que você gostaria de trabalhar na sessão atual.

Você verá algo semelhante ao que se segue:

    $ azure account set "Azure Free Trial" true
    info:    Executing command account set
    info:    Setting subscription to "Azure Free Trial" with id "2lskd82-434-4730-9df9-akd83lsk92sa".
    info:    Changes saved
    info:    account set command OK

### Etapa 4: colocar a CLI do Azure no modo de ARM

Para usar o modo de Gerenciamento de Recursos do Azure (ARM) com a CLI do Azure, digite `azure config mode arm`. Você verá algo semelhante ao que se segue:

    $ azure config mode arm
    info:    New mode is arm

> [AZURE.NOTE]Você pode alternar novamente para usar os comandos de gerenciamento de serviço do Azure digitando `azure config mode asm`.

<!---HONumber=Oct15_HO3-->