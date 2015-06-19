<properties
   pageTitle="Implantando um modelo usando a CLI do Azure para Mac, Linux e Windows"
   description="Descreve as etapas básicas para implantar ou atualizar qualquer modelo."
   services="virtual-machines"
   documentationCenter=""
   authors="squillace"
   manager="timlt"
   editor=""/>

<tags
   ms.service="virtual-machines"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="infrastructure"
   ms.date="04/21/2015"
   ms.author="rasquill"/>

# Implantando um modelo usando a CLI do Azure para Mac, Linux e Windows

## Instalar curl, wget ou outra ferramenta de download
Este tópico usa **curl**. Use o gerenciador de pacotes do sistema operacional ou baixe-o [daqui](http://curl.haxx.se/download.html).

## Baixe o arquivo de parâmetros de modelo (ou o modelo, se necessário)

As etapas a seguir o habilitarão a implantar um modelo do Azure, mesmo que ele seja complexo. Este tópico usa o modelo que cria um servidor básico do Ubuntu com a extensão de VM customscript instalada como exemplo. Os arquivos também são incluídos no fim do tópico de referência.

### Baixar o arquivo azuredeploy-parameters.json

Baixe o arquivo azuredeploy-parameters.json se ele existir para o modelo que você deseja implantar.

    curl -O https://github.com/azure/azurermtemplates/raw/master/linux-virtual-machine-with-customdata/azuredeploy-parameters.json
    
## Inserir suas informações de implantação de grupo de recursos
    
Abra esse arquivo com seu editor favorito. Você verá que precisa especificar um valor para várias das chaves, particularmente **adminUsername**, **adminPassword** (lembre-se das regras de complexidade!) e o nome da conta de armazenamento e os nomes DNS desejados.
    
    {
      "newStorageAccountName": {
        "value": "uniquestorageaccountname"
      },
      "adminUsername": {
        "value": ""
      },
      "adminPassword": {
        "value": ""
      },
      "dnsNameForPublicIP": {
        "value": "uniquednsnameforpublicip"
      },
      "vmSourceImageName": {
        "value": "b39f27a8b8c64d52b05eac6a62ebad85__Ubuntu-14_04_2_LTS-amd64-server-20150309-pt-br-30GB"
      },
      "location": {
        "value": "West US"
      },
      "virtualNetworkName": {
        "value": "myVNET"
      },
      "vmSize": {
        "value": "Standard_A0"
      },
      "vmName": {
        "value": "myVM"
      },
      "publicIPAddressName": {
        "value": "myPublicIP"
      },
      "nicName": {
        "value": "myNic"
      }
    }
    
Adicione novos valores (oAzure criará novo recursos de armazenamento e DNS para você, se possível) ou use recursos que você já criou. O seguinte arquivo azuredeploy-parameters.json mostra um exemplo:




a url a seguir captura o arquivo de parâmetros do azuredeploy-parameters.json "vazio", que funcionará se usar o método interativo. Se for usada a abordagem do arquivo de parâmetros baixado e personalizado, será preciso usar a opção de arquivo de modelo <template-file> em vez disso. Também tenho scripts escritos que extraem seções individuais de qualquer parte desses arquivos, dependendo do que você deseja fazer. Convém mencionar que, para fazer análise de json, é possível usar jq: curl http://stedolan.github.io/jq/download/linux64/jq -o /usr/bin/jq


### Implantar seus arquivos de parâmetros e modelo


[AZURE.NOTE]Você pode observar que alguns modelos podem não ter nenhum arquivo azuredeploy-parameters.json correspondente.

parâmetros para definir ou pode já ser uma parte do próprio modelo, dependendo de quais modelos você está usando. Nesses casos, você pode

Se o modelo contiver seus parâmetros diretamente ou se você desejar extrair os dados de parâmetros por conta própria. Para obter mais informações sobre a estrutura de modelos, consulte [Linguagem de Modelo do Gerenciador de Recursos do Azure](https://msdn.microsoft.com/library/azure/dn835138.aspx).


https://github.com/azure/azurermtemplates/raw/master/linux-virtual-machine-with-customdata/azuredeploy.json (ou apenas o arquivo azuredeploy-parameters.json). Você pode extrair a seção de parâmetros do modelo. Nesse caso, você precisará salvá-la novamente no modelo em si OU como um arquivo azuredeploy-parameters.json separado. Você precisará obter os valores para colocá-los nos parâmetros.

## Modifique o arquivo azuredeploy-templates.json

Colete os valores necessários

<!--Every topic should have next steps and links to the next logical set of content to keep the customer engaged-->
## Próximas etapas

Vestibul precedente ipsum primis na faucibus orci luctus et ultrices posuere cubilia Curae; Nullam ultricies, ipsum vitae volutpat hendrerit, eros de pretium diam purus, vitae tincidunt nulla lorem sed turpis: [Link 3 para outro tópico da documentação de azure.microsoft.com](../storage-whatis-account.md).

<!--Image references-->
[5]: ./media/markdown-template-for-new-articles/octocats.png
[6]: ./media/markdown-template-for-new-articles/pretty49.png
[7]: ./media/markdown-template-for-new-articles/channel-9.png
[8]: ./media/markdown-template-for-new-articles/copytemplate.png

<!--HONumber=52-->
 