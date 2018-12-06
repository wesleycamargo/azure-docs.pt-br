---
title: Implantar o IBM Db2 pureScale no Azure
description: Aprenda como implementar uma [arquitetura de exemplo](ibm-db2-purescale-azure.md) usada recentemente para migrar uma empresa de seu ambiente IBM Db2 em execução no z / OS para o IBM Db2 pureScale no Azure.
services: virtual-machines-linux
documentationcenter: ''
author: njray
manager: edprice
editor: edprice
tags: ''
ms.assetid: ''
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.topic: article
ms.date: 11/09/2018
ms.author: njray
ms.openlocfilehash: 24bdc9867af869437cc0e440a80e5bf4813abbae
ms.sourcegitcommit: 8314421d78cd83b2e7d86f128bde94857134d8e1
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/19/2018
ms.locfileid: "51977896"
---
# <a name="deploy-ibm-db2-purescale-on-azure"></a>Implantar o IBM Db2 pureScale no Azure

Este artigo descreve como implementar uma [arquitetura de exemplo](ibm-db2-purescale-azure.md) usada recentemente para migrar uma empresa de seu ambiente IBM Db2 em execução no z / OS para o IBM Db2 pureScale no Azure.

Para seguir as etapas usadas durante a migração, consulte os scripts de instalação no repositório [Db2onAzure](http://aka.ms/db2onazure) no GitHub. Esses scripts baseiam-se a arquitetura usada para uma carga de trabalho OLTP (processamento) típico, o tamanho médio de transações online.

## <a name="get-started"></a>Introdução

Para implementar essa arquitetura, faça o download e execute o script deploy.sh localizado no repositório [Db2onAzure](http://aka.ms/db2onazure) no GitHub.

O repositório também inclui scripts que você pode usar para configurar um painel do Grafana que pode ser usado para consultar o Prometheus, o sistema de monitoramento e alerta de código aberto incluído no Db2.

> [!NOTE]
> O script deploy.sh no cliente cria chaves SSH privadas e as passa para o modelo de implantação por HTTPS. Para maior segurança, recomendamos o uso do [Azure Key Vault](https://docs.microsoft.com/azure/key-vault/key-vault-overview) para armazenar segredos, chaves e senhas.

## <a name="how-the-deployment-script-works"></a>Como funciona o script de implantação

O script deploy.sh cria e configura os recursos do Azure usados nessa arquitetura. O script solicita as assinaturas e máquinas virtuais do Azure usadas no ambiente de destino e executa as seguintes operações:

-   Configura o grupo de recursos, a rede virtual e as sub-redes no Azure para a instalação.

-   Configura os NSGs e SSH para o ambiente.

-   Configura várias NICs nas máquinas virtuais GlusterFS e Db2 pureScale.

-   Cria as máquinas virtuais de armazenamento GlusterFS.

-   Cria a máquina virtual de jumpbox.

-   Cria as máquinas virtuais do Db2 pureScale.

-   Cria a máquina virtual testemunha que o Db2 pureScale pinga.

-   Cria uma máquina virtual do Windows para ser usada no teste, mas não instala nada nela.

Em seguida, os scripts de implantação configuram a rede de área de armazenamento virtual iSCSI (vSAN) para armazenamento compartilhado no Azure. Neste exemplo, o iSCSI se conecta ao GlusterFS. Essa solução também oferece a opção de instalar os destinos iSCSI como um único nó do Windows. (O iSCSI fornece uma interface de armazenamento de bloco compartilhado sobre TCP / IP que permite que o procedimento de configuração Db2 pureScale use uma interface de dispositivo para se conectar ao armazenamento compartilhado.) Para obter informações básicas sobre o GlusterFS, consulte o tópico [Arquitetura: tipos de volumes](https://docs.gluster.org/en/latest/Quick-Start-Guide/Architecture/) em Introdução ao GlusterFS.

Os scripts de implantação executam estas etapas gerais:

1.  Configure um cluster de armazenamento compartilhado no Azure. O GlusterFS é usado para configurar o cluster de armazenamento compartilhado. Isso envolve pelo menos dois nós do Linux. Para detalhes de configuração, veja [Configurando o Red Hat Gluster Storage no Microsoft Azure](https://access.redhat.com/documentation/en-us/red_hat_gluster_storage/3.1/html/deployment_guide_for_public_cloud/chap-documentation-deployment_guide_for_public_cloud-azure-setting_up_rhgs_azure) na documentação do Red Hat Gluster.

2.  Configure uma interface iSCSI Direct nos servidores Linux de destino para o GlusterFS. Para detalhes de configuração, consulte [ GlusterFS iSCSI ](https://docs.gluster.org/en/latest/Administrator%20Guide/GlusterFS%20iSCSI/) no Guia de Administração do GlusterFS.

3.  Configure o Iniciador iSCSI nas máquinas virtuais Linux que acessarão o cluster GlusterFS usando o iSCSI Target. Para detalhes de configuração, veja [Como Configurar um Alvo e Iniciador iSCSI no Linux](https://www.rootusers.com/how-to-configure-an-iscsi-target-and-initiator-in-linux/) na documentação do RootUsers.

4.  GlusterFS é instalado como a camada de armazenamento para a interface de iSCSI.

Depois de criar o dispositivo iSCSI, a etapa final é instalar o Db2 pureScale. Como parte da configuração do Db2 pureScale, o [IBM Spectrum Scale](https://www.ibm.com/support/knowledgecenter/SSEPGG_11.1.0/com.ibm.db2.luw.qb.server.doc/doc/t0057167.html) (anteriormente conhecido como GPFS) é compilado e instalado no cluster do GlusterFS. Esse sistema de arquivos em cluster permite que o Db2 pureScale compartilhe dados entre as várias máquinas virtuais que executam o mecanismo Db2 pureScale. Para obter mais informações, consulte a documentação do [IBM Spectrum Scale](https://www.ibm.com/support/knowledgecenter/en/STXKQY_4.2.0/ibmspectrumscale42_welcome.html) no site da IBM.

## <a name="db2-purescale-response-file"></a>Arquivo de resposta do Db2 pureScale

O repositório do GitHub inclui o Db2server.rsp, um arquivo de resposta (.rsp) que permite gerar um script automatizado para a instalação do Db2 pureScale. A tabela a seguir lista as opções do pureScale do Db2 que o arquivo de resposta usa para a configuração. Você pode personalizar o arquivo de resposta conforme necessário para seu ambiente de instalação.

> [!NOTE]
> Um arquivo de resposta de amostra, Db2server.rsp, está incluído no repositório [Db2onAzure](http://aka.ms/db2onazure) no GitHub. Se você usar esse arquivo, deverá editá-lo antes que ele funcione em seu ambiente.

**Opções do arquivo de resposta Db2 pureScale**

| Nome da tela               | Campo                                        | Valor                                                                                                 |
|---------------------------|----------------------------------------------|-------------------------------------------------------------------------------------------------------|
| Bem-Vindo                   |                                              | Nova instalação                                                                                           |
| Escolha um produto          |                                              | Versão do Db2 11.1.3.3. Edições de servidor com o Db2 pureScale                                              |
| Configuração             | Diretório                                    | /data1/opt/ibm/db2/V11.1                                                                              |
|                           | Selecione o tipo de instalação                 | Típico                                                                                               |
|                           | Eu concordo com os termos da IBM                     | Verificado                                                                                               |
| Proprietário da instância            | Usuário Existente Por Instância, Nome do Usuário        | Db2sdin1                                                                                              |
| Usuário Isolado               | Usuário existente, nome de usuário                     | Db2sdfe1                                                                                              |
| Sistema de arquivos do cluster       | Caminho de dispositivo de partição de disco de compartilhado            | /dev/dm-2                                                                                             |
|                           | Ponto de montagem                                  | /Db2sd\_1804a                                                                                         |
|                           | Disco de dados compartilhado                         | /dev/dm-1                                                                                             |
|                           | Ponto de montagem (dados)                           | /Db2fs/datafs1                                                                                        |
|                           | Cluster de disco compartilhado                          | /dev/dm-0                                                                                             |
|                           | Ponto de montagem (Log)                            | /Db2fs/logfs1                                                                                         |
|                           | Db2 desempate de serviços de Cluster. Caminho de dispositivo | /dev/dm-3                                                                                             |
| Lista de hosts                 | d1 [eth1], d2 [eth1], cf1 [eth1], cf2 [eth1] |                                                                                                       |
|                           | CF primário preferencial                         | cf1                                                                                                   |
|                           | CF secundário preferencial                       | cf2                                                                                                   |
| Arquivo de resposta e o resumo | Primeira opção                                 | Instale o Db2 Server Edition com o recurso IBM Db2 pureScale e salve minhas configurações em um arquivo de resposta |
|                           | Nome do arquivo de resposta                           | /root/DB2server.rsp                                                                                   |

### <a name="notes-about-this-deployment"></a>Observações sobre essa implantação

-   Os valores para / dev-dm0, / dev-dm1, / dev-dm2 e / dev-dm3 podem ser alterados após uma reinicialização na máquina virtual em que a configuração ocorre (d0 no script automatizado). Para encontrar os valores corretos, você pode emitir o seguinte comando antes de concluir o arquivo de resposta no servidor em que a instalação será executada:

```
   [root\@d0 rhel]\# ls -als /dev/mapper
   total 0
   0 drwxr-xr-x 2 root root 140 May 30 11:07 .
   0 drwxr-xr-x 19 root root 4060 May 30 11:31 ..
   0 crw------- 1 root root 10, 236 May 30 11:04 control
   0 lrwxrwxrwx 1 root root 7 May 30 11:07 db2data1 -\> ../dm-1
   0 lrwxrwxrwx 1 root root 7 May 30 11:07 db2log1 -\> ../dm-0
   0 lrwxrwxrwx 1 root root 7 May 30 11:26 db2shared -\> ../dm-2
   0 lrwxrwxrwx 1 root root 7 May 30 11:08 db2tieb -\> ../dm-3
```

-   Os scripts de configuração usam aliases para os discos iSCSI para que os nomes reais possam ser encontrados facilmente.

-   Quando o script de configuração é executado em d0, os valores de **/ dev / dm- \*** podem ser diferentes em d1, cf0 e cf1. A configuração do Db2 pureScale não se importa.

## <a name="troubleshooting-and-known-issues"></a>Solução de problemas e problemas conhecidos

O repositório do GitHub inclui uma Base de Conhecimento mantida pelos autores. Ele lista possíveis problemas que você pode encontrar e resoluções que você pode tentar. Por exemplo, problemas conhecidos podem ocorrer quando:

-   Tentando alcançar o endereço IP do gateway.

-   Compilando a GPL.

-   O handshake de segurança entre hosts falha.

-   O instalador do Db2 detecta um sistema de arquivos existente.

-   Instalando manualmente o GPFS.

-   Instalando o Db2 pureScale quando o GPFS já está criado.

-   Removendo o Db2 pureScale e o GPFS.

Para obter mais informações sobre esses e outros problemas conhecidos, consulte o arquivo kb.md no repositório [Db2onAzure](http://aka.ms/Db2onAzure).

## <a name="next-steps"></a>Próximas etapas

-   [GlusterFS iSCSI](https://docs.gluster.org/en/latest/Administrator%20Guide/GlusterFS%20iSCSI/)

-   [Criando usuários necessários para uma instalação do Recurso Db2 pureScale](https://www.ibm.com/support/knowledgecenter/en/SSEPGG_11.1.0/com.ibm.db2.luw.qb.server.doc/doc/t0055374.html?pos=2)

-   [Db2icrt - criar instância de comando](https://www.ibm.com/support/knowledgecenter/en/SSEPGG_11.1.0/com.ibm.db2.luw.admin.cmd.doc/doc/r0002057.html)

-   [Db2 pureScale solução de dados de Clusters](http://www.ibmbigdatahub.com/blog/db2-purescale-clustered-database-solution-part-1)

-   [IBM Data Studio](https://www.ibm.com/developerworks/downloads/im/data/index.html/)

-   [Aliança de modernização de plataforma: IBM Db2 no Azure](https://www.platformmodernization.org/pages/ibmdb2azure.aspx)

-   [Guia de deslocamento e comparação de precisão de Data Center Virtual do Azure](https://azure.microsoft.com/resources/azure-virtual-datacenter-lift-and-shift-guide/)
