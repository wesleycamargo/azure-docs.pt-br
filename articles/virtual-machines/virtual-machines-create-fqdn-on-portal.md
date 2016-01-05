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
   ms.date="08/21/2015"
   ms.author="dkshir"/>

# Criar um nome de domínio totalmente qualificado no portal do Azure

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-rm-include.md)]modelo de implantação clássico.


Quando você cria uma máquina virtual no [portal do Azure](https://portal.azure.com) usando o modelo de implantação do **Gerenciador de Recursos**, o portal cria um recurso de IP público para a máquina virtual. Você pode usar esse endereço IP para acessar remotamente a máquina virtual. No entanto, o portal não cria um [nome de domínio totalmente qualificado](https://en.wikipedia.org/wiki/Fully_qualified_domain_name) ou FQDN, por padrão. Uma vez que é mais fácil se lembrar e usar um FQDN em vez de um endereço IP, este artigo demonstra como você pode adicionar um à sua máquina virtual.

No artigo, presumimos que você se conectou à sua assinatura no portal e criou uma máquina virtual com as imagens disponíveis, usando o **Gerenciador de Recursos**. Siga estas etapas assim que a máquina virtual começar a ser executada.

1.  Exiba as configurações da máquina virtual no portal e clique no endereço IP Público.

    ![localizar recurso ip](media/virtual-machines-create-fqdn-on-portal/locatePublicIP.PNG)

2.  **Desassocie** o IP Público da máquina virtual. Observe que ele ainda não mostra um nome de domínio. Depois que clicar no botão **Sim**, pode levar alguns segundos para que a desassociação seja concluída.

    ![desassociar recurso ip](media/virtual-machines-create-fqdn-on-portal/dissociateIP.PNG)

    Associaremos esse IP Público à máquina virtual depois das etapas a seguir. Se o IP Público for um _IP Público Dinâmico_, você perderá o endereço IPV4 e um novo será atribuído depois que o FQDN for configurado.

3.  Depois que o botão **Desassociar** ficar esmaecido, clique na seção **Todas as configurações** do IP Público e abra a guia **Configuração**. Insira o rótulo do nome DNS desejado. **Salve** esta configuração.

    ![inserir rótulo do nome dns](media/virtual-machines-create-fqdn-on-portal/dnsNameLabel.PNG)

4.  Volte para a folha da máquina virtual no portal e clique em **Todas as configurações** da máquina virtual. Abra a guia **Interfaces de rede** e clique no recurso Interface de rede associado a essa máquina virtual. Isso abrirá a folha **Interface de rede** no portal.

    ![abrir interface de rede](media/virtual-machines-create-fqdn-on-portal/openNetworkInterface.PNG)

5.  É importante lembrar que o campo **Endereço IP Público** da Interface de rede está em branco. Clique na seção **Todas as configurações** dessa Interface de rede e abra a guia **Endereços IP**. Na folha **Endereços IP**, clique em **Habilitado** para o campo **Endereço IP Público**. Selecione a guia **Definir configurações necessárias do endereço IP** e selecione o IP padrão que você desassociou anteriormente. Clique em **Salvar**. Adicionar o recurso IP de volta pode levar alguns segundos.

    ![configurar recurso IP](media/virtual-machines-create-fqdn-on-portal/configureIP.PNG)

6.  Feche todas as outras folhas e volte para a folha **Máquina Virtual**. Clique no recurso IP Público nas configurações. É importante lembrar que a folha IP Público agora mostra o FQDN desejado como o **Nome DNS**.

    ![O FQDN é criado](media/virtual-machines-create-fqdn-on-portal/fqdnCreated.PNG)

    Agora, você pode se conectar remotamente à máquina virtual usando esse nome DNS. Por exemplo, use o `SSH adminuser@testdnslabel.eastus.cloudapp.azure.com` ao se conectar a uma máquina virtual do Linux que tem o nome de domínio totalmente qualificado de `testdnslabel.eastus.cloudapp.azure.com` e o nome de usuário de `adminuser`.

<!---HONumber=AcomDC_1203_2015-->