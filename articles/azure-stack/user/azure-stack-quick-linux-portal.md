---
title: "Azure início rápido de pilha - criar o Portal VM"
description: "Rápido de pilha do Azure iniciar - criar uma VM do Linux usando o portal"
services: azure-stack
cloud: azure-stack
author: vhorne
manager: byronr
ms.service: azure-stack
ms.topic: quickstart
ms.date: 09/25/2017
ms.author: victorh
ms.custom: mvc
ms.openlocfilehash: 5f815bafdcc7c05ec7f4149fb7c9df178f0f80e2
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="create-a-linux-virtual-machine-with-the-azure-stack-portal"></a>Criar uma máquina virtual Linux com o portal de pilha do Azure

*Aplica-se a: Azure pilha integrado sistemas e o Kit de desenvolvimento de pilha do Azure*

Máquinas de virtuais de pilha do Azure podem ser criadas por meio do portal de pilha do Azure. Esse método fornece uma interface de usuário baseada em navegador para criar e configurar uma máquina virtual e todos os recursos relacionados. Este guia de início rápido mostra como criar uma máquina virtual Linux rapidamente e instalar um servidor web.

## <a name="prerequisites"></a>Pré-requisitos

* **Uma imagem do Linux no mercado de pilha do Azure**

   Por padrão, o marketplace do Azure pilha não contêm uma imagem do Linux. Portanto, antes de criar uma máquina virtual Linux, certifique-se de que o operador de pilha do Azure baixou o **Ubuntu Server 16.04 LT** imagem usando as etapas descritas no [baixar itens do marketplace do Azure para o Azure Pilha](../azure-stack-download-azure-marketplace-item.md) tópico.

* **Acesso a um cliente SSH**

   Se você estiver usando o Kit de desenvolvimento de pilha do Azure (ASDK), você não pode ter acesso a um cliente SSH no seu ambiente. Se esse for o caso, você pode escolher entre vários pacotes que incluem um cliente SSH. Por exemplo, você pode instalar PuTTY que inclui um cliente SSH e um gerador de chave SSH (puttygen.exe). Para obter mais informações sobre as opções possíveis, consulte o seguinte relacionados ao artigo do Azure: [como chaves usar SSH com o Windows no Azure](https://docs.microsoft.com/en-us/azure/virtual-machines/linux/ssh-from-windows#windows-packages-and-ssh-clients).

   Este guia de início rápido usa PuTTY para gerar as chaves SSH e conectar à máquina virtual Linux. Para baixar e instalar o PuTTY, vá para [http://www.putty.org/](http://www.putty.org).

## <a name="create-an-ssh-key-pair"></a>Criar um par de chaves SSH

É necessário um par de chaves de SSH para concluir este guia de início rápido. Se você já tiver um par de chave SSH, essa etapa pode ser ignorada.

1. Navegue até a pasta de instalação PuTTY (o local padrão é ```C:\Program Files\PuTTY```) e execute ```puttygen.exe```.
2. Na janela do gerador de chave PuTTY, verifique se o **tipo de chave para gerar** é definido como **RSA**e o **número de bits em uma chave gerada** é definido como **2048**. Quando estiver pronto, clique em **gerar**.

   ![puttygen.exe](media/azure-stack-quick-linux-portal/Putty01.PNG)

3. Para concluir o processo de geração de chave, mova o cursor do mouse dentro da janela de gerador de chave PuTTY.
4. Quando a geração de chave for concluído, clique em **salva a chave pública** e **salva a chave privada** para salvar as chaves públicas e privadas em arquivos.

   ![Chaves puTTY](media/azure-stack-quick-linux-portal/Putty02.PNG)



## <a name="sign-in-to-the-azure-stack-portal"></a>Entre portal do Azure pilha

Entre portal do Azure pilha. O endereço do portal do Azure pilha depende de qual produto da pilha do Azure você está se conectando:

* Para o Azure pilha Development Kit (ASDK), acesse: https://portal.local.azurestack.external.
* Para um sistema de pilha do Azure integrado, vá para a URL que o operador de pilha do Azure fornecido.

## <a name="create-the-virtual-machine"></a>Criar a máquina virtual

1. Clique o **novo** botão localizado no canto superior esquerdo do portal do Azure pilha.

2. Selecione **Computação** e, em seguida, selecione **Ubuntu Server 16.04 LTS**.
3. Clique em **Criar**.

4. Digite as informações da máquina virtual. Para **Tipo de autenticação**, selecione **Chave pública SSH**. Quando você colar em sua chave pública de SSH (que você salvou um arquivo anteriormente), tome cuidado ao remover espaços em branco à esquerda ou à direita. Ao concluir, clique em **OK**.

   ![Noções básicas de máquina virtual](media/azure-stack-quick-linux-portal/linux-01.PNG)

5. Selecione **D1_V2** para a máquina virtual.

   ![Tamanho do computador](media/azure-stack-quick-linux-portal/linux-02.PNG)

6. Sobre o **configurações** página, mantenha os padrões e clique em **Okey**.

7. Sobre o **resumo** , clique em **Okey** para iniciar a implantação de máquina virtual.


## <a name="connect-to-the-virtual-machine"></a>Conectar-se à máquina virtual

1. Clique em **conectar** na página de máquina virtual. Isso exibe uma cadeia de caracteres de conexão SSH que pode ser usada para se conectar à máquina virtual.

   ![Conecte-se a máquina virtual](media/azure-stack-quick-linux-portal/linux-03.PNG)

2. Abra o PuTTY.
3. Sobre o **configuração PuTTY** tela, em **categoria**, expanda **SSH** e, em seguida, clique em **Auth**. Clique em **procurar** e selecione o arquivo de chave privada que você salvou anteriormente.

   ![Chave privada puTTY](media/azure-stack-quick-linux-portal/Putty03.PNG)
4. Em **categoria**, role para cima e clique em **sessão**.
5. No **nome de Host (ou endereço IP)** caixa, cole a cadeia de caracteres de conexão do portal do Azure pilha que você viu anteriormente. Neste exemplo, a cadeia de caracteres é ```asadmin@192.168.102.34```.
 
   ![Sessão puTTY](media/azure-stack-quick-linux-portal/Putty04.PNG)
6. Clique em **abrir** para abrir uma sessão para a máquina virtual.

   ![Sessão do Linux](media/azure-stack-quick-linux-portal/Putty05.PNG)

## <a name="install-nginx"></a>Instalar o NGINX

Use o seguinte script de bash origens do pacote de atualização e instalar o pacote NGINX mais recente na máquina virtual. 

```bash 
#!/bin/bash

# update package source
sudo apt-get -y update

# install NGINX
sudo apt-get -y install nginx
```

Quando terminar, saia da sessão SSH e retornar a página de visão geral de máquina virtual no portal do Azure pilha.


## <a name="open-port-80-for-web-traffic"></a>Abra a porta 80 para tráfego da Web 

Um Grupo de Segurança de Rede (NSG) protege o tráfego de entrada e saída. Quando uma máquina virtual é criada a partir do portal do Azure pilha, uma regra de entrada é criada na porta 22 para conexões SSH. Como esta máquina virtual hospeda um servidor web, uma regra NSG precisa ser criado para a porta 80.

1. Na máquina virtual **visão geral** página, clique no nome do **grupo de recursos**.
2. Selecione o **grupo de segurança de rede** para a máquina virtual. O NSG pode ser identificado usando a coluna **Tipo**. 
3. No menu esquerdo, sob **configurações**, clique em **regras de segurança de entrada**.
4. Clique em **Adicionar**.
5. Em **Nome**, digite **http**. Verifique se o **Intervalo de portas** está definido para 80 e a **Ação** está definida para **Permitir**. 
6. Clique em **OK**.


## <a name="view-the-nginx-welcome-page"></a>Exibir a página de boas-vindas do NGINX

Com NGINX instalado e a porta 80 aberta na sua máquina virtual, o servidor web agora pode ser acessado no endereço IP público da máquina virtual. O endereço IP público pode ser encontrado na página de visão geral da máquina virtual no portal do Azure pilha.

Abra um navegador da web e navegue até ```http://<public IP address>```.

![Site padrão NGINX](media/azure-stack-quick-linux-portal/linux-04.PNG)


## <a name="clean-up-resources"></a>Limpar recursos

Quando o grupo de recursos, a máquina virtual e todos os recursos relacionados não forem mais necessários, exclua-os. Para fazer isso, selecione o grupo de recursos da página de máquina virtual e clique em **excluir**.

## <a name="next-steps"></a>Próximas etapas

Esse início rápido, você tiver implantado uma máquina de virtual Linux simple, uma regra de grupo de segurança de rede e instalou um servidor web. Para saber mais sobre as máquinas virtuais de pilha do Azure, continuar [considerações para máquinas virtuais no Azure pilha](azure-stack-vm-considerations.md).

