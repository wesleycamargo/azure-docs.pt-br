<properties title="virtual-machines-how-to-inject-custom-data" pageTitle="Injecting Custom Data into Azure Virtual Machines" description="This topic describes how to inject custom data into an Azure Virtual Machine when the instance is created and how to locate the custom data on either Windows or Linux." metaKeywords="Azure linux vm, linux vm, userdata vm, user data vm, custom data vm, windows custom data" services="virtual-machines" solutions="" documentationCenter="" authors="rasquill" manager="dongill" editor="tysonn" videoId="" scriptId="" />

<tags ms.service="virtual-machines" ms.workload="infrastructure-services" ms.tgt_pltfrm="vm-windows" ms.devlang="na" ms.topic="article" ms.date="01/01/1900" ms.author="rasquill"></tags>

# Injetando dados personalizados em uma Máquina Virtual do Azure

Injetar um script ou de outros dados em uma Máquina Virtual do Azure quando provisionada é um cenário muito comum, independentemente do sistema operacional ser uma distribuição do Microsoft Windows ou do Linux. Este tópico descreve como:

-   Injetar dados em uma Máquina Virtual do Azure quando provisionada

-   Recuperá-los para Windows e Linux e

-   Usar ferramentas especiais em alguns sistemas para detectar e identificar dados personalizados automaticamente.

> [WACOM.NOTE] Este tópico expande [esta postagem no blog do Azure][esta postagem no blog do Azure] sobre esse recurso e continuará sendo atualizado à medida que mais funcionalidades surgirem.

<!--Table of contents for topic, the words in brackets must match the heading wording exactly-->

Neste tópico:

-   [Injetando os dados personalizados na Máquina Virtual do Azure][Injetando os dados personalizados na Máquina Virtual do Azure]

-   [Usando dados personalizados na Máquina Virtual][Usando dados personalizados na Máquina Virtual]

-   [Próximas etapas][Próximas etapas]

## <span id="injectingCustomData"></span></a>Injetando os dados personalizados na Máquina Virtual do Azure

Esse recurso é compatível no momento apenas na [Interface de linha de comando de plataforma cruzada do Microsoft Azure][Interface de linha de comando de plataforma cruzada do Microsoft Azure]. Embora você possa usar qualquer uma das opções para o comando `azure vm create`, a abordagem a seguir demonstra uma abordagem muito básica.

        PASSWORD='AcceptablePassword -- more than 8 chars, a cap, a num, a special'
        VMNAME=mycustomdataubuntu
        USERNAME=username
        VMIMAGE= An image chosen from among those listed by azure vm image list
        azure vm create $VMNAME $VMIMAGE $USERNAME $PASSWORD --location "West US" --json -d ./custom-data.txt -e 22

## <span id="usingCustomData"></span></a>Usando dados personalizados na Máquina Virtual

-   Se a Máquina Virtual do Azure for uma Máquina Virtual do Windows, o arquivo de dados personalizado será salvo em `%SYSTEMDRIVE%\AzureData\CustomData.bin` e, embora tenha sido codificado com base-64 para transferência do computador local para a nova Máquina Virtual, ele é decodificado automaticamente e pode ser aberto imediatamente.

	> [WACOM.NOTE] Se já existir, o arquivo será substituído. A segurança no diretório é definida como **System:Full Control** e **Administrators:Full Control**.

-   Se a Máquina Virtual do Azure for uma Máquina Virtual do Linux, o arquivo de dados personalizados estará nos dois lugares a seguir, mas como os dados serão codificados como base-64, você precisará decodificar os dados primeiro.

    -   Em `/var/lib/waagent/ovf-env.xml`
    -   Em `/var/lib/waagent/CustomData`

<!--Every topic should have next steps and links to the next logical set of content to keep the customer engaged-->

## <span id="nextsteps"></span></a>Próximas etapas: Usando cloud-init

Se a Máquina Virtual do Azure for uma imagem do Ubuntu, será possível usar cloud-init para iniciar um script para usar os dados personalizados automaticamente (ou, se o arquivo de dados personalizados for um script, executá-lo). Para obter mais informações, consulte [Documentação de cloud-init para Ubuntu][Documentação de cloud-init para Ubuntu].

<!--Link references-->

[Referência da API REST de gerenciamento para adicionar serviço de função][Referência da API REST de gerenciamento para adicionar serviço de função]

[Interface de linha de comando de plataforma cruzada do Microsoft Azure][Interface de linha de comando de plataforma cruzada do Microsoft Azure]

  [esta postagem no blog do Azure]: http://azure.microsoft.com/blog/2014/04/21/custom-data-and-cloud-init-on-windows-azure/
  [Injetando os dados personalizados na Máquina Virtual do Azure]: #injectingCustomData
  [Usando dados personalizados na Máquina Virtual]: #usingCustomData
  [Próximas etapas]: #nextsteps
  [Interface de linha de comando de plataforma cruzada do Microsoft Azure]: https://github.com/Azure/azure-sdk-tools-xplat
  [Documentação de cloud-init para Ubuntu]: https://help.ubuntu.com/community/CloudInit
  [Referência da API REST de gerenciamento para adicionar serviço de função]: http://msdn.microsoft.com/library/azure/jj157186.aspx
