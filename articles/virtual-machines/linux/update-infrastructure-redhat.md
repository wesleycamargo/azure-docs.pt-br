---
title: "Infraestrutura de Atualização do Red Hat (RHUI) | Microsoft Docs"
description: "Saiba mais sobre a Infraestrutura de Atualização do Red Hat (RHUI) para as instâncias sob demanda do Red Hat Enterprise Linux no Microsoft Azure"
services: virtual-machines-linux
documentationcenter: 
author: BorisB2015
manager: timlt
editor: 
ms.assetid: f495f1b4-ae24-46b9-8d26-c617ce3daf3a
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 09/10/2017
ms.author: borisb
ms.translationtype: HT
ms.sourcegitcommit: d24c6777cc6922d5d0d9519e720962e1026b1096
ms.openlocfilehash: 43d9095bcbd490abc6d01c214a17b8586f92ed1e
ms.contentlocale: pt-br
ms.lasthandoff: 09/14/2017

---
# <a name="red-hat-update-infrastructure-rhui-for-on-demand-red-hat-enterprise-linux-vms-in-azure"></a>Infraestrutura de Atualização do Red Hat (RHUI) para as VMs do Red Hat Enterprise Linux sob demanda no Azure
 A [Infraestrutura de Atualização do Red Hat](https://access.redhat.com/products/red-hat-update-infrastructure) permite aos provedores de nuvem (por exemplo, o Azure) espelhar o conteúdo do repositório hospedado pelo Red Hat, criar repositórios personalizados com conteúdo específico ao Azure e disponibilizá-lo para as VMs do usuário final.

As imagens PAYG (Pagas conforme o uso) do RHEL (Red Hat Enterprise Linux) vêm pré-configuradas para acessar o RHUI do Azure. Você não precisa fazer nenhuma configuração adicional: execute `sudo yum update` depois da instância do RHEL estar pronta para obter as atualizações mais recentes. Este serviço é incluído como parte das taxas de software PAYG do RHEL.

## <a name="important-information-about-azure-rhui"></a>Informações importantes sobre o RHUI do Azure

1. Atualmente, o RHUI dá suporte apenas à última versão secundária em determinada família do RHEL (RHEL6 ou RHEL7). A instância VM do RHEL conectada ao RHUI faz o upgrade para a última versão secundária quando você executa `sudo yum update`. 

    Por exemplo, se você provisionar uma VM com base na imagem PAYG do RHEL 7.2 e executar `sudo yum update`, acabará com uma VM do RHEL 7.4 (última versão secundária atual da família do RHEL7).

    Para evitar esse comportamento, você precisa criar sua própria imagem, conforme descrito no artigo [Criar e carregar um máquina virtual baseada em Red Hat para o Azure](redhat-create-upload-vhd.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) e conectá-la a outra infraestrutura de atualização ([diretamente aos servidores de distribuição de conteúdo do Red Hat](https://access.redhat.com/solutions/253273) ou ao servidor do Red Hat Satellite).

2. O acesso ao RHUI hospedado pelo Azure é incluído no preço de imagem PAYG do RHEL. Cancelar o registro de uma VM RHEL PAYG do RHUI hospedado no Azure não converte a máquina virtual em uma VM do tipo BYOL (traga sua própria licença). Se você registrar a mesma VM com outra fonte de atualizações, poderá incorrer em encargos duplos _indiretos_: a primeira vez para a taxa de software RHEL do Azure e a segunda para assinaturas do Red Hat que foram adquiridas anteriormente. Se você precisar usar consistentemente uma infraestrutura de atualização diferente do RHUI hospedado pelo Azure, considere a possibilidade de criar e implantar suas próprias imagens (do tipo BYOL), conforme descrito em [Criar e carregar uma máquina virtual baseada no Red Hat para o Azure](redhat-create-upload-vhd.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).

3. Duas classes de imagens PAYG do RHEL no Azure (RHEL for SAP HANA e RHEL for SAP Business Applications) estão conectadas a canais dedicados do RHUI que permanecem na versão secundária específica do RHEL, conforme necessário para a certificação da SAP. 

4. O acesso ao RHUI hospedado pelo Azure é limitado às VMs nos [Intervalos de IP do Data Center do Microsoft Azure](https://www.microsoft.com/download/details.aspx?id=41653). Se você estiver encaminhando por proxy todo o tráfego de VM por meio da infraestrutura de rede local, talvez precise configurar rotas definidas pelo usuário para as VMs PAYG do RHEL para acessar o RHUI do Azure.

### <a name="the-ips-for-the-rhui-content-delivery-servers"></a>Os IPs para os servidores de distribuição de conteúdo do RHUI
O RHUI está disponível em todas as regiões onde as imagens do RHEL sob demanda estão disponíveis. Atualmente, inclui todas as regiões públicas listadas na página [Painel de status do Azure](https://azure.microsoft.com/status/) e as regiões da Alemanha e do Governo dos EUA do Azure. 

Se você estiver usando a configuração de rede para restringir o acesso de VMs PAYG do RHEL, verifique se os seguintes IPs são permitidos para `yum update` funcionar dependendo do ambiente em que você está. 

```
# Azure Global
13.91.47.76
40.85.190.91
52.187.75.218
52.174.163.213

# Azure US Government
13.72.186.193

# Azure Germany
51.5.243.77
51.4.228.145
```

## <a name="rhui-azure-infrastructure-update"></a>Atualização da infraestrutura RHUI do Azure

Em setembro de 2016, implantamos um RHUI atualizado do Azure e, em abril de 2017, desligamos o RHUI antigo do Azure. Se você usa as imagens PAYG do RHEL (ou seus instantâneos) de setembro de 2016 ou posterior, você está se conectando automaticamente ao novo RHUI do Azure. No entanto, se tiver instantâneos/VMs mais antigas, sua configuração precisará ser atualizada manualmente para acessar o RHUI do Azure, conforme descrito abaixo.

Os novos servidores do RHUI do Azure são implantados com o [Gerenciador de Tráfego do Azure](https://azure.microsoft.com/services/traffic-manager/) para que um único ponto de extremidade (rhui-1.microsoft.com) possa ser usado por qualquer VM, independentemente da região. 

### <a name="troubleshooting-connection-problems-to-azure-rhui"></a>Solução de problemas de conexão com o RHUI do Azure
Se estiver tendo problemas para se conectar ao RHUI do Azure em sua VM PAYG RHEL do Azure, siga estas etapas
1. Inspecionar a configuração da VM para o ponto de extremidade do RHUI do Azure

    Verifique se o arquivo `/etc/yum.repos.d/rh-cloud.repo` contém a referência a `rhui-[1-3].microsoft.com` na baseurl da seção `[rhui-microsoft-azure-rhel*]` do arquivo. Se esse é o caso, significa que você está usando o novo RHUI do Azure.

    No entanto, será necessária uma atualização da configuração se ele estiver apontando para um local com o padrão a seguir: `mirrorlist.*cds[1-4].cloudapp.net`. Se você estiver usando o instantâneo de VM antigo, atualize-o para que ele aponte para o novo RHUI do Azure.

2. O acesso ao RHUI hospedado no Azure é limitado às VMs nos [intervalos de IP do Datacenter do Microsoft Azure] (https://www.microsoft.com/download/details.aspx?id=41653).
 
3. Se estiver usando a nova configuração, verifique se a VM se conecta do intervalo de IP do Azure e, se ainda não puder se conectar ao RHUI do Azure, registre um caso de suporte na Microsoft ou no Red Hat.

### <a name="manual-update-procedure-to-use-the-azure-rhui-servers"></a>Procedimento de atualização manual para usar os servidores do RHUI do Azure
Esse procedimento é fornecido apenas para referência: as imagens PAYG do RHEL já tem a configuração correta para se conectarem ao RHUI do Azure.

Baixar (via curl) a assinatura de chave pública

```bash
curl -o RPM-GPG-KEY-microsoft-azure-release https://download.microsoft.com/download/9/D/9/9d945f05-541d-494f-9977-289b3ce8e774/microsoft-sign-public.asc 
```

Verificar a chave baixada

```bash
gpg --list-packets --verbose < RPM-GPG-KEY-microsoft-azure-release
```

Verificar o arquivo de saída, verificar `keyid` e `user ID packet`:

```bash
Version: GnuPG v1.4.7 (GNU/Linux)
:public key packet:
        version 4, algo 1, created 1446074508, expires 0
        pkey[0]: [2048 bits]
        pkey[1]: [17 bits]
        keyid: EB3E94ADBE1229CF
:user ID packet: "Microsoft (Release signing) <gpgsecurity@microsoft.com>"
:signature packet: algo 1, keyid EB3E94ADBE1229CF
        version 4, created 1446074508, md5len 0, sigclass 0x13
        digest algo 2, begin of digest 1a 9b
        hashed subpkt 2 len 4 (sig created 2015-10-28)
        hashed subpkt 27 len 1 (key flags: 03)
        hashed subpkt 11 len 5 (pref-sym-algos: 9 8 7 3 2)
        hashed subpkt 21 len 3 (pref-hash-algos: 2 8 3)
        hashed subpkt 22 len 2 (pref-zip-algos: 2 1)
        hashed subpkt 30 len 1 (features: 01)
        hashed subpkt 23 len 1 (key server preferences: 80)
        subpkt 16 len 8 (issuer key ID EB3E94ADBE1229CF)
        data: [2047 bits]
```

Instalar a chave pública

```bash
sudo install -o root -g root -m 644 RPM-GPG-KEY-microsoft-azure-release /etc/pki/rpm-gpg
sudo rpm --import /etc/pki/rpm-gpg/RPM-GPG-KEY-microsoft-azure-release
```

Baixar, verificar e instalar o RPM do cliente. 

> [!NOTE]
> As versões de pacote mudam e se você estiver se conectando manualmente ao RHUI do Azure, talvez encontre a última versão do pacote do cliente para cada família do RHEL provisionando a última imagem da galeria.
> 

Download: para RHEL 6

```bash
curl -o azureclient.rpm https://rhui-1.microsoft.com/pulp/repos/microsoft-azure-rhel6/rhui-azure-rhel6-2.1-32.noarch.rpm 
```

Para RHEL 7

```bash
curl -o azureclient.rpm https://rhui-1.microsoft.com/pulp/repos/microsoft-azure-rhel7/rhui-azure-rhel7-2.1-19.noarch.rpm  
```

Verificar:

```bash
rpm -Kv azureclient.rpm
```

Verificar na saída se a assinatura do pacote está OK

```bash
azureclient.rpm:
    Header V3 RSA/SHA256 Signature, key ID be1229cf: OK
    Header SHA1 digest: OK (927a3b548146c95a3f6c1a5d5ae52258a8859ab3)
    V3 RSA/SHA256 Signature, key ID be1229cf: OK
    MD5 digest: OK (c04ff605f82f4be8c96020bf5c23b86c)
```

Instalar o RPM

```bash
sudo rpm -U azureclient.rpm
```

Após a conclusão, verifique se você pode acessar o RHUI do Azure por meio da VM.

## <a name="next-steps"></a>Próximas etapas
Para criar uma VM do Red Hat Enterprise Linux por meio da imagem Pré-Paga do Azure Marketplace e aproveitar o RHUI hospedado pelo Azure, acesse o [Azure Marketplace](https://azure.microsoft.com/marketplace/partners/redhat/). 
