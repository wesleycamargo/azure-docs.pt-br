---
title: Como instalar o HANA no SAP HANA no Azure (Instâncias Grandes) | Microsoft Docs
description: Instalar o HANA no SAP HANA no Azure (Instância Grande).
services: virtual-machines-linux
documentationcenter: ''
author: hermanndms
manager: jeconnoc
editor: ''
ms.service: virtual-machines-linux
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 09/10/2018
ms.author: rclaus
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 76a7ce99799b85d81aa6e127ebe1e57e2df3e59a
ms.sourcegitcommit: 2d961702f23e63ee63eddf52086e0c8573aec8dd
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/07/2018
ms.locfileid: "44167572"
---
# <a name="example-of-an-sap-hana-installation-on-hana-large-instances"></a>Exemplo de uma instalação do SAP HANA nas Instâncias Grandes do HANA

Esta seção ilustra como instalar o SAP HANA em uma unidade de Instância Grande do HANA. O estado inicial que temos tem esta aparência:

- Você forneceu à Microsoft todos os dados para implantar uma Instância Grande do SAP HANA.
- Você recebeu a Instância Grande do SAP HANA da Microsoft.
- Você criou uma VNet do Azure que está conectada à rede local.
- Você conectou o circuito do ExpressRoute das Instâncias Grandes do HANA com a VNet do Azure.
- Você instalou uma VM do Azure usada como uma jumpbox para as Instâncias Grandes do HANA.
- Você garantiu que pode se conectar na jumpbox com a unidade de Instância Grande do HANA e vice-versa.
- Você verificou se todos os pacotes e patches necessários estão instalados.
- Você leu as observações e a documentação da SAP sobre a instalação do HANA no sistema operacional que está sendo usado e verificou se há suporte para a versão do HANA de sua escolha na versão do sistema operacional.

O que é mostrado nas próximas sequências é o download dos pacotes de instalação do HANA na VM de jumpbox, nesse caso, executada em um sistema operacional Windows, a cópia dos pacotes para a unidade de Instância Grande do HANA e a sequência da instalação.

## <a name="download-of-the-sap-hana-installation-bits"></a>Baixar os bits de instalação do SAP HANA
Como as unidades de Instância Grande do HANA não têm conectividade direta com a Internet, não é possível baixar diretamente os pacotes de instalação da SAP na VM da Instância Grande do HANA. Para resolver a conectividade direta com a Internet ausente, você precisa da jumpbox. Baixe os pacotes na VM de jumpbox.

Para baixar os pacotes de instalação do HANA, você precisa ser um usuário S da SAP ou outro usuário, o que permite que você acesse o SAP Marketplace. Siga esta sequência de telas após o logon:

Acesse [SAP Service Marketplace](https://support.sap.com/en/index.html) > clique em Baixar Software > Instalações e Upgrade > Por Índice Alfabético > em H – SAP HANA Platform Edition > SAP HANA Platform Edition 2.0 > Instalação > Baixar os seguintes arquivos

![Baixar a instalação do HANA](./media/hana-installation/image16_download_hana.PNG)

No caso de demonstração, baixamos os pacotes de instalação do SAP HANA 2.0. Na VM de jumpbox do Azure, expanda os arquivos mortos de extração automática para o diretório, conforme mostrado abaixo.

![Extrair a instalação do HANA](./media/hana-installation/image17_extract_hana.PNG)

Conforme os arquivos mortos são extraídos, copie o diretório criado pela extração, no caso acima, 51052030, para a unidade de Instância grande do HANA no volume /hana/shared de um diretório criado.

> [!Important]
> Não copie os pacotes de instalação para a raiz ou para o LUN de inicialização, pois o espaço é limitado e precisa ser usado por outros processos também.


## <a name="install-sap-hana-on-the-hana-large-instance-unit"></a>Instalar o SAP HANA na unidade de Instância Grande do HANA
Para instalar o SAP HANA, você precisa fazer logon como a raiz do usuário. Somente a raiz tem permissões suficientes para instalar o SAP HANA.
A primeira coisa que você precisa fazer é definir permissões no diretório copiado em hana/shared. As permissões precisam ser definidas como

```
chmod –R 744 <Installation bits folder>
```

Se você deseja instalar o SAP HANA usando a instalação gráfica, o pacote gtk2 precisa ser instalado nas Instâncias Grandes do HANA. Verifique se ele é instalado com o comando

```
rpm –qa | grep gtk2
```

Nas próximas etapas, demonstramos a instalação do SAP HANA com a interface gráfica do usuário. Como a próxima etapa, vá para o diretório de instalação e navegue para o subdiretório HDB_LCM_LINUX_X86_64. Iniciar

```
./hdblcmgui 
```
fora desse diretório. Agora, você está sendo guiado por uma sequência de telas em que precisa fornecer os dados da instalação. No caso demonstrado, estamos instalando o servidor de banco de dados e os componentes de cliente do SAP HANA. Portanto, nossa seleção é “Banco de Dados do SAP HANA”, conforme mostrado abaixo

![Selecionar o HANA na instalação](./media/hana-installation/image18_hana_selection.PNG)

Na próxima tela, você escolhe a opção “Instalar Novo Sistema”

![Selecionar a nova instalação do HANA](./media/hana-installation/image19_select_new.PNG)

Após essa etapa, você precisa selecionar entre vários outros componentes que podem ser instalados adicionalmente ao servidor de banco de dados do SAP HANA.

![Selecionar os componentes adicionais do HANA](./media/hana-installation/image20_select_components.PNG)

Para fins desta documentação, escolhemos o HANA SAP Client e o SAP HANA Studio. Também instalamos uma instância escalável. Portanto, na próxima tela, você precisa escolher “Sistema de Host Único” 

![Selecionar a instalação escalável](./media/hana-installation/image21_single_host.PNG)

Na próxima tela, você precisa fornecer alguns dados

![Fornecer o SID do SAP HANA](./media/hana-installation/image22_provide_sid.PNG)

> [!Important]
> Como o SID (ID do Sistema) do HANA, você precisa fornecer o mesmo SID fornecido à Microsoft quando solicitou a implantação da Instância Grande do HANA. A escolha de um SID diferente causará falha na instalação, devido a problemas de permissão de acesso em diferentes volumes

Como o diretório de instalação, use o diretório /hana/shared. Na próxima etapa, você precisa fornecer os locais dos arquivos de dados e dos arquivos de log do HANA


![Fornecer o local do Log do HANA](./media/hana-installation/image23_provide_log.PNG)

> [!Note]
> Você deve definir como arquivos de dados e de log os volumes que já são fornecidos com os pontos de montagem que contêm o SID escolhido na seleção de tela anterior a esta tela. Se o SID for incompatível com aquele que você digitou, na tela anterior, volte e ajuste o SID para o valor existente nos pontos de montagem.

Na próxima etapa, examine o nome do host e, finalmente, corrija-o. 

![Examinar o nome do host](./media/hana-installation/image24_review_host_name.PNG)

Na próxima etapa, você também precisa recuperar os dados fornecidos à Microsoft quando solicitou a implantação da Instância Grande do HANA. 

![Fornecer um UID e GID do usuário do sistema](./media/hana-installation/image25_provide_guid.PNG)

> [!Important]
> Você precisa fornecer a mesma ID de Usuário do Sistema e ID do Grupo de Usuários fornecidas à Microsoft quando solicitou a implantação da unidade. Se você não fornecer as mesmas IDs, a instalação do SAP HANA na unidade de Instância Grande do HANA falhará.

Nas próximas duas telas, que não mostramos nesta documentação, você precisa fornecer a senha para o usuário do SISTEMA do banco de dados do SAP HANA e a senha do usuário sapadm, que é usada para o Agente de Host do SAP instalado como parte da instância de banco de dados do SAP HANA.

Depois de definir a senha, uma tela de confirmação é exibida. Verifique todos os dados listados e continue com a instalação. Você acessa uma tela de progresso que documenta o progresso da instalação, como a mostrada abaixo

![Verificar o progresso da instalação](./media/hana-installation/image27_show_progress.PNG)

Como a instalação for concluída, você deverá ter uma imagem como a seguir

![A instalação é concluída](./media/hana-installation/image28_install_finished.PNG)

Neste ponto, a instância do SAP HANA deve estar em execução e pronta para uso. Você deve conseguir se conectar a ele no SAP HANA Studio. Verifique também os últimos patches do SAP HANA e aplique-os.


**Próximas etapas**

- Veja [Alta disponibilidade e recuperação de desastre de SAP HANA em Instâncias Grandes do Azure](hana-overview-high-availability-disaster-recovery.md).

