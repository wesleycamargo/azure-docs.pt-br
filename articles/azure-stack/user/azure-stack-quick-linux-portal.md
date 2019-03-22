---
title: Azure Stack de início rápido – Portal criar VM
description: Rápido de pilha do Azure iniciar - criar uma VM do Linux usando o portal
services: azure-stack
cloud: azure-stack
author: mattbriggs
manager: femila
ms.service: azure-stack
ms.topic: quickstart
ms.date: 03/11/2019
ms.author: mabrigg
ms.reviewer: kivenkat
ms.custom: mvc
ms.lastreviewed: 12/03/2018
ms.openlocfilehash: 39298d34a0d29eb1e5a4234ffe0e0fcca844226e
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/18/2019
ms.locfileid: "57858177"
---
# <a name="quickstart-create-a-linux-server-virtual-machine-with-the-azure-stack-portal"></a>Início rápido: criar uma máquina virtual do servidor Linux com o portal do Azure Stack

*Aplica-se a: Integrados do Azure Stack, sistemas e o Kit de desenvolvimento do Azure Stack*

Você pode criar uma máquina de virtual do Ubuntu Server 16.04 LTS, usando o portal do Azure Stack. Siga as etapas neste artigo para criar e usar uma máquina virtual. Este artigo fornece as etapas para:

* Conecte-se à máquina virtual com um cliente remoto.
* Instale um servidor web NGINX.
* Limpe seus recursos.

> [!NOTE]  
> As imagens de tela neste artigo são atualizadas de acordo com as alterações introduzidas com a versão do Azure Stack 1808. 1808 adiciona suporte para usar *discos gerenciados* além dos discos não gerenciados. Se você usar uma versão anterior, algumas imagens para tarefas como a seleção de disco será diferentes do que é exibido neste artigo.  


## <a name="prerequisites"></a>Pré-requisitos

* **Uma imagem do Linux no marketplace do Azure Stack**

   Por padrão, o Azure Stack marketplace não têm uma imagem do Linux. Verifique se você tem o Azure Stack operador fornece o **Ubuntu Server 16.04 LTS** imagem em seu mercado. O operador pode usar as etapas descritas a [baixar itens do marketplace do Azure para o Azure Stack](../azure-stack-download-azure-marketplace-item.md) artigo.

* **Acesso a um cliente SSH**

   Se você estiver usando o Azure Stack desenvolvimento ASDK (Kit), você pode não ter acesso a um cliente SSH. Se você precisar de um cliente, há vários pacotes que incluem um cliente SSH. Por exemplo, PuTTY inclui um cliente SSH e o gerador de chave SSH (puttygen.exe). Para obter mais informações sobre pacotes disponíveis, leia o seguinte artigo do Azure: [Como usar chaves SSH com Windows no Azure](https://docs.microsoft.com/azure/virtual-machines/linux/ssh-from-windows#windows-packages-and-ssh-clients).

   Este início rápido usa o PuTTY para gerar as chaves SSH e conecte-se à máquina virtual Linux server. Para baixar e instalar o PuTTY, vá para [ https://www.putty.org/ ](https://www.putty.org).

## <a name="create-an-ssh-key-pair"></a>Criar um par de chaves SSH

Você precisa de um par de chaves SSH para concluir todas as etapas neste artigo. Se você tiver um par de chaves SSH existente, você pode ignorar esta etapa.

1. Navegue até a pasta de instalação do PuTTY (o local padrão é ```C:\Program Files\PuTTY```) e execute ```puttygen.exe```.
2. Na janela do PuTTY Key Generator, certifique-se a **tipo de chave para gerar** é definido como **RSA**e o **número de bits em uma chave gerada** é definido como **2048**. Quando você estiver pronto, clique em **gerar**.

   ![Configuração do puTTY Key Generator](media/azure-stack-quick-linux-portal/Putty01.PNG)

3. Para gerar uma chave, mova o cursor do mouse aleatoriamente na janela do PuTTY Key Generator.
4. Quando a geração de chave for concluída, clique em **Salvar chave pública** e, em seguida, clique em **Salvar chave privada** para salvar suas chaves em arquivos.

   ![Resultados do puTTY Key Generator](media/azure-stack-quick-linux-portal/Putty02.PNG)

## <a name="sign-in-to-the-azure-stack-portal"></a>Entrar no portal do Azure Stack

Entrar no portal do Azure Stack. O endereço do portal do Azure Stack dependendo de qual produto do Azure Stack você está se conectando:

* Para o Azure Stack desenvolvimento ASDK (Kit), vá para: https://portal.local.azurestack.external.
* Para um sistema integrado do Azure Stack, vá para a URL que o operador do Azure Stack fornecida.

## <a name="create-the-virtual-machine"></a>Criar a máquina virtual

1. Clique em **criar um recurso** no canto superior esquerdo do portal do Azure Stack.

2. Selecione **Computação** e, em seguida, selecione **Ubuntu Server 16.04 LTS**.
   
   ![Selecione o servidor Linux](media/azure-stack-quick-linux-portal/select.png)
1. Clique em **Criar**.

4. Digite as informações da máquina virtual. Para **Tipo de autenticação**, selecione **Chave pública SSH**. Cole a chave pública de SSH que você salvou e, em seguida, clique em **Okey**.

   > [!NOTE]
   > Certifique-se de que remover qualquer espaço em branco à esquerda ou direita para a chave.

   ![Noções básicas do painel – Configurar a máquina virtual](media/azure-stack-quick-linux-portal/linux-01.PNG)

5. Selecione **D1** para a máquina virtual.

   ![Painel de tamanho - escolher um tamanho de máquina virtual](media/azure-stack-quick-linux-portal/linux-02.PNG)

6. Sobre o **configurações** de página, faça as alterações desejadas para os padrões.
   
   - A partir da versão do Azure Stack 1808, você pode configurar **armazenamento** onde você pode optar por usar *discos gerenciados*. Antes da versão 1808 apenas discos não gerenciados podem ser usados.    
     ![Configurar o armazenamento para discos gerenciados](media/azure-stack-quick-linux-portal/linux-03.PNG)
    
     Quando as configurações estiverem prontas, selecione **Okey** para continuar.

7. Sobre o **resumo** , clique em **Okey** para iniciar a implantação de máquina virtual.  
   ![Implantar](media/azure-stack-quick-linux-portal/deploy.png)

## <a name="connect-to-the-virtual-machine"></a>Conectar-se à máquina virtual

1. Clique em **Connect** na página da máquina virtual. Você pode encontrar a cadeia de caracteres de conexão SSH que você precisa para se conectar à máquina virtual. 

2. Abra o PuTTY.

3. Na tela de configuração do PuTTY, você usará o **categoria** janela para rolar para cima ou para baixo. Role para baixo até **SSH**, expanda **SSH**e, em seguida, clique em **Auth**. Clique em **procurar** e selecione o arquivo de chave privada que você salvou.
   ![Conectar-se a máquina virtual](media/azure-stack-quick-linux-portal/putty03.PNG)

4. Role para cima na **categoria** janela e clique **sessão**.
5. No **nome do Host (ou endereço IP)** caixa, cole a cadeia de caracteres de conexão mostrada no portal do Azure Stack. Neste exemplo, a cadeia de caracteres é ```asadmin@192.168.102.34```.

   ![Cadeia de caracteres de conexão de configuração puTTY](media/azure-stack-quick-linux-portal/Putty04.PNG)

6. Clique em **abrir** para abrir uma sessão para a máquina virtual.

   ![Sessão do Linux](media/azure-stack-quick-linux-portal/Putty05.PNG)

## <a name="install-the-nginx-web-server"></a>Instalar o servidor web NGINX

Use os seguintes comandos de bash para atualizar fontes de pacote e instalar o pacote mais recente do NGINX na máquina virtual.

```bash
#!/bin/bash

# update package source
sudo apt-get -y update

# install NGINX
sudo apt-get -y install nginx
```

Quando terminar de instalar o NGINX, feche a sessão SSH e abra a página de visão geral da máquina virtual no portal do Azure Stack.

## <a name="open-port-80-for-web-traffic"></a>Abra a porta 80 para tráfego da Web

Um Grupo de Segurança de Rede (NSG) protege o tráfego de entrada e saída. Quando uma máquina virtual é criada no portal do Azure Stack, uma regra de entrada é criada na porta 22 para conexões SSH. Como essa máquina virtual hospeda um servidor web, uma regra NSG precisa ser criado para permitir o tráfego da web na porta 80.

1. Na máquina virtual **visão geral** página, clique no nome da **grupo de recursos**.
2. Selecione o **grupo de segurança de rede** para a máquina virtual. O NSG pode ser identificado usando a coluna **Tipo**.
3. No menu esquerdo, sob **as configurações**, clique em **regras de segurança de entrada**.
4. Clique em **Adicionar**.
5. Em **Nome**, digite **http**. Verifique se o **Intervalo de portas** está definido para 80 e a **Ação** está definida para **Permitir**.
6. Clique em **OK**

## <a name="view-the-nginx-welcome-page"></a>Exibir a página de boas-vindas do NGINX

Com o NGINX instalado e a porta 80 está aberta na sua máquina virtual, você pode acessar o servidor web usando o endereço IP público da máquina virtual. (O endereço IP público é mostrado na página de visão geral da máquina virtual).

Abra um navegador da web e navegue até ```http://<public IP address>```.

![Página de boas-vindas do NGINX web server](media/azure-stack-quick-linux-portal/linux-05.PNG)

## <a name="clean-up-resources"></a>Limpar recursos

Limpe todos os recursos que você não precisa mais. Para excluir a máquina virtual e seus recursos, selecione o grupo de recursos na página de máquina virtual e, em seguida, clique em **excluir**.

## <a name="next-steps"></a>Próximas etapas

Neste início rápido, você implantou uma máquina virtual de servidor Linux básica com um servidor web. Para saber mais sobre máquinas virtuais do Azure Stack, continue [considerações para máquinas virtuais no Azure Stack](azure-stack-vm-considerations.md).
