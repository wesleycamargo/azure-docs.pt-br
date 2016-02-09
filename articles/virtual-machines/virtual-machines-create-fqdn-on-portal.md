<properties
   pageTitle="Criar o FQDN para uma VM no portal do Azure | Microsoft Azure"
   description="Saiba como criar um nome de domínio totalmente qualificado, ou FQDN, para uma máquina virtual baseada no Gerenciador de Recursos no portal do Azure."
   services="virtual-machines"
   documentationCenter=""
   authors="dsk-2015"
   manager="timlt"
   editor="tysonn"
   tags="azure-resource-manager"/>

<tags
   ms.service="virtual-machines"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="01/19/2016"
   ms.author="dkshir"/>

# Criar um nome de domínio totalmente qualificado no portal do Azure

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-rm-include.md)]modelo de implantação clássico.


Quando você cria uma máquina virtual no [portal do Azure](https://portal.azure.com) usando o modelo de implantação do **Gerenciador de Recursos**, o portal cria um recurso de IP público para a máquina virtual. Você pode usar esse endereço IP para acessar remotamente a máquina virtual. Embora o portal não crie um [nome de domínio totalmente qualificado](https://en.wikipedia.org/wiki/Fully_qualified_domain_name) ou FQDN, por padrão, é extremamente fácil criar um depois que a máquina virtual é criada. Este artigo apresenta as etapas para criar um nome DNS ou FQDN.

No artigo, presumimos que você se conectou à sua assinatura no portal e criou uma máquina virtual com as imagens disponíveis, usando o **Gerenciador de Recursos**. Siga estas etapas assim que a máquina virtual começar a ser executada.

1.  Exiba as configurações da máquina virtual no portal e clique no endereço IP Público.

    ![localizar recurso ip](media/virtual-machines-create-fqdn-on-portal/locatePublicIP.PNG)

2.  Observe que o nome DNS para o IP público está em branco. Clique em **Todas as configurações** para a folha de IP público.

    ![ip de configurações](media/virtual-machines-create-fqdn-on-portal/settingsIP.PNG)

3.  Abra a guia **Configuração** nas Configurações de IP Público. Digite a etiqueta de nome DNS desejada e **Salve** essa configuração.

    ![inserir rótulo do nome dns](media/virtual-machines-create-fqdn-on-portal/dnsNameLabel.PNG)

    O recurso IP Público agora mostrará essa nova etiqueta DNS em sua folha.

4.  Feche as folhas de IP Público e volte até a folha da máquina virtual no portal. Verifique se o nome DNS/FQDN aparece ao lado do endereço IP para o recurso IP Público.

    ![O FQDN é criado](media/virtual-machines-create-fqdn-on-portal/fqdnCreated.PNG)


    Agora, você pode se conectar remotamente à máquina virtual usando esse nome DNS. Por exemplo, use o `SSH adminuser@testdnslabel.centralus.cloudapp.azure.com` ao se conectar a uma máquina virtual do Linux que tem o nome de domínio totalmente qualificado de `testdnslabel.centralus.cloudapp.azure.com` e o nome de usuário de `adminuser`.

<!---HONumber=AcomDC_0128_2016-->