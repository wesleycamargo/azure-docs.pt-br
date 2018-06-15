---
title: Azure início rápido de pilha - criar o Portal VM
description: Rápido de pilha do Azure iniciar - criar uma VM do Linux usando o portal
services: azure-stack
cloud: azure-stack
author: brenduns
manager: femila
ms.service: azure-stack
ms.topic: quickstart
ms.date: 04/24/2018
ms.author: brenduns
ms.reviewer: ''
ms.custom: mvc
ms.openlocfilehash: 2ea07f04d4c566c0add39d75cad3d3a4ed81c6c8
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/28/2018
ms.locfileid: "32152213"
---
# <a name="quickstart-create-a-linux-server-virtual-machine-with-the-azure-stack-portal"></a>Início rápido: criar uma máquina virtual do servidor Linux com o portal de pilha do Azure

*Aplica-se a: Azure pilha integrado sistemas e o Kit de desenvolvimento de pilha do Azure*

Você pode criar uma máquina virtual de Ubuntu Server 16.04 LTS usando o portal de pilha do Azure. Siga as etapas neste artigo para criar e usar uma máquina virtual. Este artigo fornece as etapas para:

* Conecte-se à máquina virtual com um cliente remoto.
* Instale um servidor de web NGINX.
* Limpe seus recursos.

## <a name="prerequisites"></a>Pré-requisitos

* **Uma imagem do Linux no mercado de pilha do Azure**

   Por padrão, o marketplace do Azure pilha não contêm uma imagem do Linux. Antes de criar uma máquina virtual do servidor Linux, certifique-se de que o operador de pilha do Azure fornece o **Ubuntu Server 16.04 LTS** imagem que você precisa. O operador pode usar as etapas descritas no [baixar itens do marketplace do Azure para o Azure pilha](../azure-stack-download-azure-marketplace-item.md) artigo.

* **Acesso a um cliente SSH**

   Se você estiver usando o Kit de desenvolvimento de pilha do Azure (ASDK), você não terá acesso a um cliente SSH. Se você precisar de um cliente, há vários pacotes que incluem um cliente SSH. Por exemplo, PuTTY inclui um cliente SSH e um gerador de chave SSH (puttygen.exe). Para obter mais informações sobre pacotes disponíveis, leia o seguinte artigo do Azure: [como chaves usar SSH com o Windows no Azure](https://docs.microsoft.com/azure/virtual-machines/linux/ssh-from-windows#windows-packages-and-ssh-clients).

   Este guia de início rápido usa PuTTY para gerar as chaves SSH e conectar à máquina virtual Linux server. Para baixar e instalar o PuTTY, vá para [ http://www.putty.org/ ](http://www.putty.org).

## <a name="create-an-ssh-key-pair"></a>Criar um par de chaves SSH

É necessário um par de chaves de SSH para concluir todas as etapas neste artigo. Se você tiver um par de chave SSH existente, você pode ignorar esta etapa.

1. Navegue até a pasta de instalação PuTTY (o local padrão é ```C:\Program Files\PuTTY```) e execute ```puttygen.exe```.
2. Na janela do gerador de chave PuTTY, verifique se o **tipo de chave para gerar** é definido como **RSA**e o **número de bits em uma chave gerada** é definido como **2048**. Quando você estiver pronto, clique em **gerar**.

   ![Configuração de gerador de chave puTTY](media/azure-stack-quick-linux-portal/Putty01.PNG)

3. Para gerar uma chave, mova o cursor do mouse aleatoriamente na janela do gerador de chave PuTTY.
4. Quando a geração de chave for concluído, clique em **salva a chave pública** e, em seguida, clique em **salva a chave privada** para salvar as chaves de arquivos.

   ![Resultados de gerador de chave puTTY](media/azure-stack-quick-linux-portal/Putty02.PNG)

## <a name="sign-in-to-the-azure-stack-portal"></a>Entre portal do Azure pilha

Entre portal do Azure pilha. O endereço do portal do Azure pilha depende de qual produto da pilha do Azure você está se conectando:

* Para o Azure pilha Development Kit (ASDK), acesse: https://portal.local.azurestack.external.
* Para um sistema de pilha do Azure integrado, vá para a URL que o operador de pilha do Azure fornecido.

## <a name="create-the-virtual-machine"></a>Criar a máquina virtual

1. Clique em **criar um recurso** no canto superior esquerdo do portal do Azure pilha.

2. Selecione **Computação** e, em seguida, selecione **Ubuntu Server 16.04 LTS**.
3. Clique em **Criar**.

4. Digite as informações da máquina virtual. Para **Tipo de autenticação**, selecione **Chave pública SSH**. Cole a chave pública de SSH que você salvou e clique em **Okey**.

   >[!NOTE]
 Certifique-se de que remover espaços em branco à esquerda ou à direita para a chave.

   ![Noções básicas do painel - Configure a máquina virtual](media/azure-stack-quick-linux-portal/linux-01.PNG)

5. Selecione **D1_V2** para a máquina virtual.

   ![Tamanho do painel - escolher um tamanho de máquina virtual](media/azure-stack-quick-linux-portal/linux-02.PNG)

6. Sobre o **configurações** página, mantenha os padrões e clique em **Okey**.

7. Sobre o **resumo** , clique em **Okey** para iniciar a implantação de máquina virtual.

## <a name="connect-to-the-virtual-machine"></a>Conectar-se à máquina virtual

1. Clique em **conectar** na página de máquina virtual. Isso exibe uma cadeia de caracteres de conexão SSH que você precisa se conectar à máquina virtual.

   ![Conecte-se a máquina virtual](media/azure-stack-quick-linux-portal/linux-03.PNG)

2. Abra o PuTTY.
3. No **configuração PuTTY** tela que você usará o **categoria** janela para rolar para cima ou para baixo. Role para baixo até **SSH**, expanda **SSH**e, em seguida, clique em **Auth**. Clique em **procurar** e selecione o arquivo de chave privada que você salvou.

   ![Selecione a chave privada PuTTY](media/azure-stack-quick-linux-portal/Putty03.PNG)

4. Rolar para cima no **categoria** janela e clique **sessão**.
5. No **nome de Host (ou endereço IP)** caixa, cole a cadeia de caracteres de conexão mostrada no portal da pilha do Azure. Neste exemplo, a cadeia de caracteres é ```asadmin@192.168.102.34```.

   ![Cadeia de caracteres de conexão de configuração puTTY](media/azure-stack-quick-linux-portal/Putty04.PNG)

6. Clique em **abrir** para abrir uma sessão para a máquina virtual.

   ![Sessão do Linux](media/azure-stack-quick-linux-portal/Putty05.PNG)

## <a name="install-the-nginx-web-server"></a>Instalar o servidor de web NGINX

Use os seguintes comandos de bash origens do pacote de atualização e instalar o pacote NGINX mais recente na máquina virtual.

```bash
#!/bin/bash

# update package source
sudo apt-get -y update

# install NGINX
sudo apt-get -y install nginx
```

Quando concluir a instalação NGINX, feche a sessão SSH e abra a página de visão geral da máquina virtual no portal do Azure pilha.

## <a name="open-port-80-for-web-traffic"></a>Abra a porta 80 para tráfego da Web

Um Grupo de Segurança de Rede (NSG) protege o tráfego de entrada e saída. Quando uma máquina virtual é criada no portal do Azure pilha, uma regra de entrada é criada na porta 22 para conexões SSH. Porque esta máquina virtual hospeda um servidor web, uma regra NSG precisa ser criado para permitir o tráfego da web na porta 80.

1. Na máquina virtual **visão geral** página, clique no nome do **grupo de recursos**.
2. Selecione o **grupo de segurança de rede** para a máquina virtual. O NSG pode ser identificado usando a coluna **Tipo**.
3. No menu esquerdo, sob **configurações**, clique em **regras de segurança de entrada**.
4. Clique em **Adicionar**.
5. Em **Nome**, digite **http**. Verifique se o **Intervalo de portas** está definido para 80 e a **Ação** está definida para **Permitir**.
6. Clique em **OK**

## <a name="view-the-nginx-welcome-page"></a>Exibir a página de boas-vindas do NGINX

Com NGINX instalado e a porta 80 aberta na sua máquina virtual, você pode acessar o servidor web usando o endereço IP público da máquina virtual. (O endereço IP público é mostrado na página de visão geral da máquina virtual).

Abra um navegador da web e navegue até ```http://<public IP address>```.

![Página de boas-vindas NGINX web server](media/azure-stack-quick-linux-portal/linux-04.PNG)

## <a name="clean-up-resources"></a>Limpar recursos

Limpe os recursos que você não precisa mais. Para excluir a máquina virtual e seus recursos, selecione o grupo de recursos na página de máquina virtual e, em seguida, clique em **excluir**.

## <a name="next-steps"></a>Próximas etapas

Esse início rápido, você implantado uma máquina de virtual de servidor Linux básico com um servidor web. Para saber mais sobre as máquinas virtuais de pilha do Azure, continuar [considerações para máquinas virtuais no Azure pilha](azure-stack-vm-considerations.md).
