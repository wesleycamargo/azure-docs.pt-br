<properties
    pageTitle="Criar uma VM do Linux usando o Portal do Azure | Microsoft Azure"
    description="Criar uma VM do Linux usando o Portal do Azure."
    services="virtual-machines-linux"
    documentationCenter=""
    authors="vlivech"
    manager="timlt"
    editor=""
    tags="azure-resource-manager"
/>

<tags
    ms.service="virtual-machines-linux"
    ms.workload="infrastructure-services"
    ms.tgt_pltfrm="vm-linux"
    ms.devlang="na"
    ms.topic="hero-article"
    ms.date="04/05/2016"
    ms.author="v-livech"
/>

# Criar uma VM do Linux usando o Portal do Azure

Este artigo mostra como usar o [Portal do Azure](https://portal.azure.com/) para criar uma VM Linux imediatamente, sem precisar instalar nada. Os únicos requisitos são [uma conta do Azure](https://azure.microsoft.com/pricing/free-trial/) e [arquivos de chave públicos e privados SSH](virtual-machines-linux-mac-create-ssh-keys.md).


1. Conectado ao Portal do Azure com sua identidade de conta do Azure, clique em **+ Novo** no canto superior esquerdo:

    ![tela1](../media/virtual-machines-linux-quick-create-portal/screen1.png)

2. Clique em **Máquinas Virtuais** no **Marketplace**, em **Ubuntu Server 14.04 LTS** da lista de imagens **Aplicativos em Destaque**, e você verá a tela abaixo:

    ![tela2](../media/virtual-machines-linux-quick-create-portal/screen2.png)

3. Verifique na parte inferior se o modelo de implantação é `Resource Manager` e clique em **Criar**.

4. Na página **Noções Básicas**, insira:
    - um nome para a máquina virtual
    - um nome de usuário para o usuário administrador
    - o Tipo de Autenticação definido como **chave pública SSH**
    - sua Chave pública SSH como uma cadeia de caracteres (por padrão, de seu diretório `~/.ssh/`)
    - um nome de grupo de recursos (para criar um novo grupo de implantação) ou selecione um grupo existente

    e clique em **OK** para continuar e escolher o tamanho da VM. Ela deve ter a seguinte aparência:

    ![tela3](../media/virtual-machines-linux-quick-create-portal/screen3.png)

5. Escolha o tamanho **DS1**, que instala o Ubuntu em uma SSD Premium, e clique em **Selecionar** para definir as configurações.

    ![tela4](../media/virtual-machines-linux-quick-create-portal/screen4.png)

6. Em **Configurações**, mantenha os valores Armazenamento e Rede como padrão e clique em **OK** para exibir o resumo.

    ![tela5](../media/virtual-machines-linux-quick-create-portal/screen5.png)

7. Confirme as configurações para sua nova VM Ubuntu e clique em **OK**.

    ![tela6](../media/virtual-machines-linux-quick-create-portal/screen6.png)

8. Abra o painel do Portal e, em **Interfaces de rede**, escolha sua NIC

    ![tela7](../media/virtual-machines-linux-quick-create-portal/screen7.png)

9. Abra o menu Endereços IP Públicos nas configurações de NIC

    ![tela8](../media/virtual-machines-linux-quick-create-portal/screen8.png)

10. SSH para o IP público usando sua chave pública SSH

```
user@slackware$ ssh -i ~/.ssh/azure_id_rsa ubuntu@13.91.99.206
```

## Próximas etapas

Se desejar, [adicione um disco](virtual-machines-linux-add-disk.md).

<!---HONumber=AcomDC_0413_2016-->