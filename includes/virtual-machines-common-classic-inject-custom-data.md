


Este tópico descreve como:

- Injetar dados em uma máquina virtual do Azure quando provisionada.

- Recuperá-los para Windows e Linux.

- Usar ferramentas especiais em alguns sistemas para detectar e identificar dados personalizados automaticamente.

> [AZURE.NOTE] Este artigo descreve como injetar dados personalizados usando uma VM criada com a API do Gerenciamento de Serviços do Azure. Para aprender a usar a API do Gerenciamento de Recursos do Azure, consulte [o modelo de exemplo](https://github.com/Azure/azure-quickstart-templates/tree/master/101-vm-customdata).

## Injetando dados personalizados na máquina virtual do Azure

No momento, esse recurso tem suporte apenas na [Interface de Linha de Comando do Azure](https://github.com/Azure/azure-xplat-cli). Embora você possa usar qualquer uma das opções para o comando `azure vm create`, a abordagem demonstrada é muito básica.

```
    PASSWORD='AcceptablePassword -- more than 8 chars, a cap, a num, a special'
    VMNAME=mycustomdataubuntu
    USERNAME=username
    VMIMAGE= An image chosen from among those listed by azure vm image list
    azure vm create $VMNAME $VMIMAGE $USERNAME $PASSWORD --location "West US" --json -d ./custom-data.txt -e 22
```


## Usando dados personalizados na máquina virtual

+ Se sua máquina virtual do Azure é uma máquina virtual baseada no Windows, o arquivo de dados personalizado é salvo em `%SYSTEMDRIVE%\AzureData\CustomData.bin`. Embora tenha sido codificado com base-64 para transferência do computador local para a nova máquina virtual, ele é decodificado automaticamente e pode ser aberto imediatamente.

   > [AZURE.NOTE] Se já existir, o arquivo será substituído. A segurança no diretório é definida como **System:Full Control** e **Administrators:Full Control**.

+ Se sua máquina virtual do Azure é uma máquina virtual baseada em Linux, o arquivo de dados personalizado estará localizado nos dois locais a seguir. Os dados serão codificados com base-64; portanto, você precisará decodificar os dados primeiro.

    + Em `/var/lib/waagent/ovf-env.xml`
    + Em `/var/lib/waagent/CustomData`



## Cloud-init no Azure

Se a sua máquina virtual do Azure for de uma imagem do Ubuntu ou CoreOS, você poderá usar CustomData para enviar um cloud-config para cloud-init. Ou, se o arquivo de dados personalizado for um script, cloud-init poderá simplesmente executá-lo.

### Imagens de nuvem do Ubuntu

Na maioria das imagens do Linux do Azure você editaria "/etc/waagent.conf" para configurar o disco de recurso temporário e o arquivo de troca. Consulte o [Guia de usuário agente do Linux do Azure](virtual-machines-linux-agent-user-guide.md) para saber mais.

No entanto, nas Imagens de Nuvem do Ubuntu, devemos usar cloud-init para configurar o disco de recurso (ou seja, o disco "efêmero") e a partição de troca. Consulte a seguinte página no wiki do Ubuntu para obter mais detalhes: [AzureSwapPartitions](https://wiki.ubuntu.com/AzureSwapPartitions).



<!--Every topic should have next steps and links to the next logical set of content to keep the customer engaged-->
## Próximas etapas: usando cloud-init

Para obter mais informações, consulte [Documentação de cloud-init para Ubuntu](https://help.ubuntu.com/community/CloudInit).

<!--Link references-->
[Referência da API REST de gerenciamento para adicionar serviço de função](http://msdn.microsoft.com/library/azure/jj157186.aspx)

[Interface de linha de comando do Azure](https://github.com/Azure/azure-xplat-cli)

<!---HONumber=AcomDC_0323_2016-->