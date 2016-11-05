


Este tópico descreve como:

* Injetar dados em uma VM (máquina virtual) do Azure quando está sendo provisionada.
* Recuperá-los para Windows e Linux.
* Usar ferramentas especiais em alguns sistemas para detectar e identificar dados personalizados automaticamente.

> [!NOTE]
> Este artigo descreve como injetar dados personalizados usando uma VM criada com a API do Gerenciamento de Serviços do Azure. Para aprender a usar a API do Gerenciamento de Recursos do Azure, consulte [o modelo de exemplo](https://github.com/Azure/azure-quickstart-templates/tree/master/101-vm-customdata).
> 
> 

## Injetando dados personalizados na máquina virtual do Azure
No momento, esse recurso tem suporte apenas na [Interface de Linha de Comando do Azure](https://github.com/Azure/azure-xplat-cli). Vamos criar um arquivo `custom-data.txt` que contém os dados para então inseri-los na VM durante o provisionamento. Embora você possa usar qualquer uma das opções para o comando `azure vm create`, a abordagem demonstrada é muito básica:

```
    azure vm create <vmname> <vmimage> <username> <password> \  
    --location "West US" --ssh 22 \  
    --custom-data ./custom-data.txt  
```


## Usando dados personalizados na máquina virtual
* Se sua VM do Azure for do Windows, o arquivo de dados personalizado estará salvo em `%SYSTEMDRIVE%\AzureData\CustomData.bin`. Embora tenha sido codificado com base64 para transferência do computador local para a nova VM, ele é decodificado automaticamente e pode ser aberto imediatamente.
  
  > [!NOTE]
  > Se já existir, o arquivo será substituído. A segurança no diretório é definida como **System:Full Control** e **Administrators:Full Control**.
  > 
  > 
* Se sua VM do Azure for uma VM do Linux, o arquivo de dados personalizado se encontrará em um dos dois locais a seguir dependendo da sua distribuição. Os dados serão codificados com base64, portanto, você precisará decodificar os dados primeiro:
  
  * `/var/lib/waagent/ovf-env.xml`
  * `/var/lib/waagent/CustomData`
  * `/var/lib/cloud/instance/user-data.txt` 

## Cloud-init no Azure
Se a sua VM do Azure for de uma imagem do Ubuntu ou CoreOS, você poderá usar o CustomData para enviar um cloud-config para cloud-init. Ou, se o arquivo de dados personalizado for um script, cloud-init poderá simplesmente executá-lo.

### Imagens de nuvem do Ubuntu
Na maioria das imagens do Linux do Azure você editaria "/etc/waagent.conf" para configurar o disco de recurso temporário e o arquivo de troca. Consulte o [Guia de usuário agente do Linux do Azure](../articles/virtual-machines/virtual-machines-linux-agent-user-guide.md) para saber mais.

No entanto, nas Imagens de Nuvem do Ubuntu, devemos usar cloud-init para configurar o disco de recurso (ou seja, o disco "efêmero") e a partição de troca. Consulte a seguinte página no wiki do Ubuntu para obter mais detalhes: [AzureSwapPartitions](https://wiki.ubuntu.com/AzureSwapPartitions).

<!--Every topic should have next steps and links to the next logical set of content to keep the customer engaged-->
## Próximas etapas: usando cloud-init
Para obter mais informações, consulte [Documentação de cloud-init para Ubuntu](https://help.ubuntu.com/community/CloudInit).

<!--Link references-->
[Referência da API REST de gerenciamento para adicionar serviço de função](http://msdn.microsoft.com/library/azure/jj157186.aspx)

[Interface de linha de comando do Azure](https://github.com/Azure/azure-xplat-cli)

<!---HONumber=AcomDC_0427_2016-->