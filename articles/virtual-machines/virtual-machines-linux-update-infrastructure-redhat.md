<properties
   pageTitle="Infraestrutura de Atualização do Red Hat (RHUI) | Microsoft Azure"
   description="Saiba mais sobre a Infraestrutura de Atualização do Red Hat (RHUI) para as instâncias sob demanda do Red Hat Enterprise Linux no Microsoft Azure"
   services="virtual-machines-linux"
   documentationCenter=""
   authors="BorisB2015"
   manager="timlt"
   editor=""/>

<tags
   ms.service="virtual-machines-linux"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="vm-linux"
   ms.workload="infrastructure-services"
   ms.date="09/22/2016"
   ms.author="borisb"/>

# Infraestrutura de Atualização do Red Hat (RHUI) para as VMs do Red Hat Enterprise Linux sob demanda no Azure

As máquinas virtuais criadas a partir das imagens disponíveis do Red Hat Enterprise Linux (RHEL) sob demanda no Azure Marketplace são registradas para acessar a Infraestrutura de Atualização do Red Hat (RHUI) implantada no Azure. As instâncias do RHEL sob demanda têm acesso a um repositório yum regional e são capazes de receber atualizações incrementais.

A lista de repositórios yum, que é gerenciada pelo RHUI, é configurada na sua instância RHEL durante o provisionamento. Você não precisa fazer nenhuma configuração adicional: execute `yum update` depois da instância do RHEL estar pronta para obter as atualizações mais recentes.

> [AZURE.NOTE] A infraestrutura do RHUI do Azure foi atualizada recentemente (setembro de 2016) e requer alterações na configuração de suas instâncias existentes do RHEL para acesso ininterrupto ao RHUI do Azure. Consulte a seção de atualização da infraestrutura RHUI do Azure para obter detalhes.


## Atualização da infraestrutura RHUI do Azure
A partir de setembro de 2016, o Azure tem um novo conjunto de servidores do RHUI (Red Hat Update Infrastructure). Esses servidores são implantados com o [Gerenciador de Tráfego do Azure](https://azure.microsoft.com/services/traffic-manager/) para que um único ponto de extremidade (rhui-1.micrsoft.com) possa ser usado por qualquer VM, independentemente da região. Eles também usam um certificado SSL que é encadeado a uma autoridade de certificação bem conhecida (raiz Baltimore). Fazer essa atualização automática seria perigoso para alguns clientes que têm ACLs ou tabelas de roteamento personalizadas para os servidores de atualização RHUI, portanto, esta atualização é opcional. Etapas manuais para integração para esses novos servidores estão disponíveis nesta página, e um script completo para integração de forma automática (no momento da verificação das etapas individuais). As novas imagens de RHEL PAYG no Azure Marketplace (versões de setembro de 2016 ou posteriores) vão apontar automaticamente para os novos servidores do RHUI do Azure e não exigem nenhuma ação adicional.

### A nova linha do tempo de integração da infraestrutura RHUI do Azure

| Data | Observação |
| --- | --- |
|22 de setembro de 2016 | Servidores RHUI e instruções de instalação disponíveis para uso. VMs implantadas usando as novas (setembro de 2016) imagens do marketplace RHEL PAYG usarão automaticamente os novos servidores RHUI, mas as VMs existentes são opcionais
|1º de novembro de 2016 | Imagens de VM herdadas do RHEL PAYG (que usam os servidores antigos do RHUI do Azure) serão removidas da galeria do Azure Marketplace
|16 de janeiro de 2017 | Os servidores antigos do RHUI do Azure serão descomissionados. Atualizar todas as suas VMs de RHEL PAYG afetadas até essa data para manter o acesso ao RHUI do Azure

### Os IPs para os novos servidores RHUI são

```
13.91.45.82
52.187.72.244
52.174.166.207
40.112.59.164
```

### Procedimento de atualização manual para usar os novos servidores do RHUI do Azure

Baixar (via curl) a assinatura de chave pública

```
curl -o RPM-GPG-KEY-microsoft-azure-release https://download.microsoft.com/download/9/D/9/9d945f05-541d-494f-9977-289b3ce8e774/microsoft-sign-public.asc 
```

Verificar a chave baixada

```
gpg --list-packets --verbose < RPM-GPG-KEY-microsoft-azure-release
```

Verificar o arquivo de saída, verificar `keyid` e `user ID packet`:

```
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

```
sudo install -o root -g root -m 644 RPM-GPG-KEY-microsoft-azure-release /etc/pki/rpm-gpg
sudo rpm --import /etc/pki/rpm-gpg/RPM-GPG-KEY-microsoft-azure-release
```

Baixar, verificar e instalar o cliente RPM

Download: para RHEL 6

```
curl -o azureclient.rpm https://rhui-1.microsoft.com/pulp/repos/microsoft-azure-rhel6/rhui-azure-rhel6-2.0-2.noarch.rpm 
```

Para RHEL 7

```
curl -o azureclient.rpm https://rhui-1.microsoft.com/pulp/repos/microsoft-azure-rhel7/rhui-azure-rhel7-2.0-2.noarch.rpm  
```

Verificar:

```
rpm -Kv azureclient.rpm
```

Verificar na saída se a assinatura do pacote está OK

```
azureclient.rpm:
    Header V3 RSA/SHA256 Signature, key ID be1229cf: OK
    Header SHA1 digest: OK (927a3b548146c95a3f6c1a5d5ae52258a8859ab3)
    V3 RSA/SHA256 Signature, key ID be1229cf: OK
    MD5 digest: OK (c04ff605f82f4be8c96020bf5c23b86c)
```

Instalar o RPM

```
sudo rpm -U azureclient.rpm
```

Após a conclusão, verifique se você pode acessar o RHUI do Azure por meio da VM

### Script tudo-em-um para automatizar a tarefas acima
Use o script a seguir conforme necessário para automatizar a tarefa de atualização de VMs afetadas para os novos servidores RHUI do Azure.

```
# Download key
curl -o RPM-GPG-KEY-microsoft-azure-release https://download.microsoft.com/download/9/D/9/9d945f05-541d-494f-9977-289b3ce8e774/microsoft-sign-public.asc 

# Validate key
if ! gpg --list-packets --verbose < RPM-GPG-KEY-microsoft-azure-release | grep -q "keyid: EB3E94ADBE1229CF"; then
    echo "Keyfile azure.asc NOT valid. Exiting."
    exit 1
fi

# Install Key
sudo install -o root -g root -m 644 RPM-GPG-KEY-microsoft-azure-release /etc/pki/rpm-gpg
sudo rpm --import /etc/pki/rpm-gpg/RPM-GPG-KEY-microsoft-azure-release

# Download RPM package
if grep -q "release 7" /etc/redhat-release; then
    ver=7
elif  grep -q "release 6" /etc/redhat-release; then
    ver=6
else
    echo "Version not supported, exiting"
    exit 1
fi

url=https://rhui-1.microsoft.com/pulp/repos/microsoft-azure-rhel$ver/rhui-azure-rhel$ver-2.0-2.noarch.rpm
curl -o azureclient.rpm "$url"

# Verify package
if ! rpm -Kv azureclient.rpm | grep -q "key ID be1229cf: OK"; then
    echo "RPM failed validation ($url)"
    exit 1
fi

# Install package
sudo rpm -U azureclient.rpm
```

## Visão geral de RHUI
A [Red Hat Update Infrastructure](https://access.redhat.com/products/red-hat-update-infrastructure) oferece uma solução altamente escalonável para gerenciar o conteúdo do repositório yum para instâncias de nuvem do Red Hat Enterprise Linux hospedadas por provedores de nuvem certificados pela Red Hat. Com base no projeto Pulp upstream, o RHUI permite que provedores de nuvem espelhem o conteúdo do repositório hospedado pelo Red Hat localmente, criem repositórios personalizados com seu próprio conteúdo e disponibilizem esses repositórios para um grande grupo de usuários finais por meio de um sistema de entrega de conteúdo com balanceamento de carga.

## Regiões em que o RHUI está disponível
O RHUI está disponível em todas as regiões onde as imagens do RHEL sob demanda estão disponíveis. Atualmente, inclui todas as regiões públicas listadas na página [Painel de status do Azure](https://azure.microsoft.com/status/). O acesso do RHUI para as VMs provisionadas a partir das imagens do RHEL sob demanda está incluído em seu preço. A disponibilidade de nuvem regional/nacional será atualizada quando expandirmos a disponibilidade do RHEL sob demanda no futuro.

> [AZURE.NOTE] O acesso ao RHUI hospedado pelo Azure é limitado às VMs nos [Intervalos de IP do Data Center do Microsoft Azure](https://www.microsoft.com/download/details.aspx?id=41653).

## Obter atualizações de outro repositório de atualizações

Se você precisar obter atualizações de um repositório de atualizações diferente (em vez do RHUI hospedado pelo Azure), precisará cancelar o registro de suas instâncias do RHUI e registrá-las novamente na infraestrutura de atualização desejada (como Red Hat Satellite ou CDN do Portal do Cliente do Red Hat). Você precisará das devidas assinaturas do Red Hat para esses serviços e o registro para o [Acesso em Nuvem da Red Hat no Azure](https://access.redhat.com/ecosystem/partners/ccsp/microsoft-azure).

Para cancelar o registro do RHUI e registrar novamente na infraestrutura de atualização, siga as etapas abaixo.

1.	Edite /etc/yum.repos.d/rh-cloud.repo e altere todos os `enabled=1` para `enabled=0`. Por exemplo:

        # sed -i 's/enabled=1/enabled=0/g' /etc/yum.repos.d/rh-cloud.repo

2.	Edite /etc/yum/pluginconf.d/rhnplugin.conf e altere `enabled=0` para `enabled=1`.
3.	Então, registre-se na infraestrutura desejada, como o Portal do Cliente do Red Hat. Siga o guia de solução do Red Hat em [como registrar e assinar um sistema para o Portal do Cliente da Red Hat](https://access.redhat.com/solutions/253273).

> [AZURE.NOTE] O acesso ao RHUI hospedado pelo Azure é incluído no preço de imagem Pré-Pago (PAYG) do RHEL. Cancelar o registro de uma VM PAYG do RHEL a partir do RHUI hospedado pelo Azure não converte a máquina virtual na VM do tipo Traga Sua Própria Licença (BYOL), portanto, você pode ser cobrado duas vezes se registrar a mesma VM em outra fonte de atualizações.
> 
> Se você precisar usar consistentemente uma infraestrutura de atualização diferente do RHUI hospedado pelo Azure, considere criar e implantar suas próprias imagens (do tipo BYOL), como descrito no artigo [Criar e carregar uma máquina virtual baseada no Red Hat para o Azure](virtual-machines-linux-redhat-create-upload-vhd.md).

## Próximas etapas
Para criar uma VM do Red Hat Enterprise Linux por meio da imagem Pré-Paga do Azure Marketplace e aproveitar o RHUI hospedado pelo Azure, acesse o [Azure Marketplace](https://azure.microsoft.com/marketplace/partners/redhat/). Você poderá usar `yum update` em sua instância do RHEL sem nenhuma configuração adicional.

<!---HONumber=AcomDC_0928_2016-->