<properties
	pageTitle="Injetando dados personalizados em máquinas virtuais do Azure"
	description="Este tópico descreve como injetar dados personalizados em uma máquina virtual do Azure quando a instância é criada e como localizar os dados personalizados no Windows ou Linux."
	services="virtual-machines"
	documentationCenter=""
	authors="squillace"
	manager="timlt"
	editor="tysonn"/>

<tags
	ms.service="virtual-machines"
	ms.workload="infrastructure-services"
	ms.tgt_pltfrm="vm-windows"
	ms.devlang="na"
	ms.topic="article"
	ms.date="04/07/2015"
	ms.author="rasquill"/>


#Injetando dados personalizados em uma Máquina Virtual do Azure

Injetar um script ou de outros dados em uma Máquina Virtual do Azure quando provisionada é um cenário muito comum, independentemente do sistema operacional ser uma distribuição do Microsoft Windows ou do Linux. Este tópico descreve como:

- Injetar dados em uma Máquina Virtual do Azure quando provisionada

- Recuperá-los para Windows e Linux e

- Usar ferramentas especiais em alguns sistemas para detectar e identificar dados personalizados automaticamente.

> [AZURE.NOTE]Este tópico estende-se [nesta publicação do blog Azure](http://azure.microsoft.com/blog/2014/04/21/custom-data-and-cloud-init-on-windows-azure/) sobre este recurso e será mantido atualizado conforme apareçam mais funcionalidades.

## Injetando os dados personalizados na Máquina Virtual do Azure

No momento, esse recurso tem suporte apenas na [Interface de Linha de Comando do Azure](https://github.com/Azure/azure-sdk-tools-xplat). Embora você possa usar qualquer uma das opções para o comando `azure vm create`, a abordagem a seguir demonstra uma abordagem muito básica.

```
    PASSWORD='AcceptablePassword -- more than 8 chars, a cap, a num, a special'
    VMNAME=mycustomdataubuntu
    USERNAME=username
    VMIMAGE= An image chosen from among those listed by azure vm image list
    azure vm create $VMNAME $VMIMAGE $USERNAME $PASSWORD --location "West US" --json -d ./custom-data.txt -e 22
```


## Usando dados personalizados na Máquina Virtual

+ Se a Máquina Virtual do Azure for uma Máquina Virtual do Windows, o arquivo de dados personalizado será salvo em `%SYSTEMDRIVE%\AzureData\CustomData.bin` e, embora tenha sido codificado com base-64 para transferência do computador local para a nova Máquina Virtual, ele é decodificado automaticamente e pode ser aberto imediatamente.

   >[AZURE.NOTE]Se já existir, o arquivo será substituído. A segurança no diretório é definida como **System:Full Control** e **Administrators:Full Control**.

+ Se a Máquina Virtual do Azure for uma Máquina Virtual do Linux, o arquivo de dados personalizados estará nos dois lugares a seguir, mas como os dados serão codificados como base-64, você precisará decodificar os dados primeiro.

    + Em `/var/lib/waagent/ovf-env.xml`
    + Em `/var/lib/waagent/CustomData`



## Cloud-Init no Azure

Se a sua Máquina Virtual do Azure for de uma imagem do Ubuntu ou CoreOS, você poderá usar CustomData para enviar um cloud-config para cloud-init. Ou, se o arquivo de dados personalizado for um script, cloud-init poderá simplesmente executá-lo.

### Imagens de nuvem do Ubuntu

Na maioria das imagens do Linux do Azure você editaria "/etc/waagent.conf" para configurar o disco de recurso temporário e o arquivo de troca. Consulte o [Guia de usuário agente do Linux do Azure](virtual-machines-linux-agent-user-guide.md) para saber mais.

No entanto, nas imagens de nuvem do Ubuntu devemos usar cloud-init para configurar o disco de recurso (também conhecido como disco "efêmero") e partição de troca. Consulte a seguinte página no wiki do Ubuntu para obter mais detalhes:

 - [Wiki do Ubuntu: configurar partições de troca](http://go.microsoft.com/fwlink/?LinkID=532955&clcid=0x409)


<!--Every topic should have next steps and links to the next logical set of content to keep the customer engaged-->
## Próximas etapas: usando cloud-init

Para obter mais informações, consulte [Documentação de cloud-init para Ubuntu](https://help.ubuntu.com/community/CloudInit).

<!--Link references-->
[Referência da API REST de gerenciamento para adicionar serviço de função](http://msdn.microsoft.com/library/azure/jj157186.aspx)

[Interface de linha de comando do Azure](https://github.com/Azure/azure-sdk-tools-xplat)
 

<!---HONumber=July15_HO1-->