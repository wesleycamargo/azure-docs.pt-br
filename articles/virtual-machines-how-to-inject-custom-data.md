<properties title="virtual-machines-how-to-inject-custom-data" pageTitle="Injetando dados personalizados em máquinas virtuais do Azure" description="Este tópico descreve como injetar dados personalizados em uma máquina virtual do Azure quando a instância é criada e como localizar os dados personalizados no Windows ou Linux." metaKeywords="Azure linux vm, linux vm, userdata vm, user data vm, custom data vm, windows custom data" services="virtual-machines" solutions="" documentationCenter="" authors="rasquill" manager="timlt" editor="tysonn" videoId="" scriptId="" />

<tags ms.service="virtual-machines" ms.workload="infrastructure-services" ms.tgt_pltfrm="vm-windows" ms.devlang="na" ms.topic="article" ms.date="10/1/2014" ms.author="rasquill" />



#Injetando dados personalizados em uma Máquina Virtual do Azure 

Injetar um script ou de outros dados em uma Máquina Virtual do Azure quando provisionada é um cenário muito comum, independentemente do sistema operacional ser uma distribuição do Microsoft Windows ou do Linux. Este tópico descreve como:

- Injetar dados em uma Máquina Virtual do Azure quando provisionada

- Recuperá-los para Windows e Linux e 

- Usar ferramentas especiais em alguns sistemas para detectar e identificar dados personalizados automaticamente.

> [WACOM.NOTE] Este tópico se expande sobre [esta postagem no blog do Azure](http://azure.microsoft.com/blog/2014/04/21/custom-data-and-cloud-init-on-windows-azure/) sobre esse recurso e será mantido atualizado conforme mais funcionalidades surgirem.

<!--Table of contents for topic, the words in brackets must match the heading wording exactly-->
Neste tópico:

+ [Injetando os dados personalizados na Máquina Virtual do Azure](#injectingCustomData)

+ [Usando dados personalizados na Máquina Virtual](#usingCustomData)

+ [Próximas etapas](#nextsteps)


## <a id="injectingCustomData"></a>Injetando os dados personalizados na Máquina Virtual do Azure

Esse recurso é compatível no momento apenas na [Interface de linha de comando de plataforma cruzada do Microsoft Azure](https://github.com/Azure/azure-sdk-tools-xplat). Embora você possa usar qualquer uma das opções para o comando `azure vm create`, a abordagem a seguir demonstra uma abordagem muito básica. 

```
    PASSWORD='AcceptablePassword -- more than 8 chars, a cap, a num, a special'
    VMNAME=mycustomdataubuntu
    USERNAME=username
    VMIMAGE= An image chosen from among those listed by azure vm image list
    azure vm create $VMNAME $VMIMAGE $USERNAME $PASSWORD --location "West US" --json -d ./custom-data.txt -e 22
```


## <a id="usingCustomData"></a>Usando dados personalizados na Máquina Virtual
 
+ Se a Máquina Virtual do Azure for uma Máquina Virtual do Windows, o arquivo de dados personalizado será salvo em `%SYSTEMDRIVE%\AzureData\CustomData.bin` e, embora tenha sido codificado com base-64 para transferência do computador local para a nova Máquina Virtual, ele é decodificado automaticamente e pode ser aberto imediatamente. 

   > [WACOM.NOTE] Se já existir, o arquivo será substituído. A segurança no diretório é definida como **System:Full Control** e **Administrators:Full Control**.

+ Se a Máquina Virtual do Azure for uma Máquina Virtual do Linux, o arquivo de dados personalizados estará nos dois lugares a seguir, mas como os dados serão codificados como base-64, você precisará decodificar os dados primeiro.

    + Em `/var/lib/waagent/ovf-env.xml`
    + Em `/var/lib/waagent/CustomData` 

<!--Every topic should have next steps and links to the next logical set of content to keep the customer engaged-->
## <a id="nextsteps"></a>Próximas etapas: Usando cloud-init

Se a Máquina Virtual do Azure for uma imagem do Ubuntu, será possível usar cloud-init para iniciar um script para usar os dados personalizados automaticamente (ou, se o arquivo de dados personalizados for um script, executá-lo). Para obter mais informações, consulte [Documentação de cloud-init para Ubuntu](https://help.ubuntu.com/community/CloudInit).

<!--Link references-->
[Referência da API REST de gerenciamento para adicionar serviço de função](http://msdn.microsoft.com/library/azure/jj157186.aspx)

[Interface de linha de comando de plataforma cruzada do Microsoft Azure](https://github.com/Azure/azure-sdk-tools-xplat)


<!--HONumber=35_1-->
