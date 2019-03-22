---
title: Configure um compartilhamento de perfil de usuário para um grupo de host - Azure
description: Como configurar um contêiner de perfil FSLogix para um pool de host de área de trabalho Virtual do Windows (visualização).
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: how-to
ms.date: 03/21/2019
ms.author: helohr
ms.openlocfilehash: 9dfbda6e17cf954369fd6caa533ba9eef41fd451
ms.sourcegitcommit: 02d17ef9aff49423bef5b322a9315f7eab86d8ff
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/21/2019
ms.locfileid: "58336007"
---
# <a name="set-up-a-user-profile-share-for-a-host-pool"></a>Configure um compartilhamento de perfil de usuário para um pool de host

O serviço de área de trabalho Virtual do Windows (versão prévia) oferece FSLogix contêineres de perfil como a solução de perfil do usuário recomendada. Não recomendamos usar a solução de disco de perfil de usuário (UDP), e ele será preterido em versões futuras do Windows de área de trabalho Virtual.

Esta seção informa como configurar um compartilhamento de contêiner FSLogix perfil para um pool de host.

## <a name="create-a-new-virtual-machine-that-will-act-as-a-file-share"></a>Criar uma nova máquina virtual que atuará como um compartilhamento de arquivos

Ao criar a máquina virtual, certifique-se de colocá-lo sobre a rede virtual mesma que as máquinas de virtuais do pool de host ou em uma rede virtual que tenha conectividade com as máquinas de virtuais do pool de host. Você pode criar uma máquina virtual de várias maneiras:

- [Criar uma máquina virtual de uma imagem de galeria do Azure](https://docs.microsoft.com/azure/virtual-machines/windows/quick-create-portal#create-virtual-machine)
- [Criar uma máquina virtual de uma imagem gerenciada](https://docs.microsoft.com/azure/virtual-machines/windows/create-vm-generalized-managed)
- [Criar uma máquina virtual a partir de uma imagem não gerenciada](https://github.com/Azure/azure-quickstart-templates/tree/master/101-vm-from-user-image)

Depois de criar a máquina virtual, uni-lo ao domínio, fazendo o seguinte:

1. [Conectar-se à máquina virtual](https://docs.microsoft.com/azure/virtual-machines/windows/quick-create-portal#connect-to-virtual-machine) com as credenciais que você forneceu ao criar a máquina virtual.
2. Na máquina virtual, inicie **painel de controle** e selecione **sistema**.
3. Selecione **nome do computador**, selecione **alterar as configurações**e, em seguida, selecione **alteração...**
4. Selecione **domínio** e, em seguida, insira o domínio do Active Directory em uma rede virtual.
5. Autenticar com uma conta de domínio que tenha privilégios para ingressar computadores em domínio.

## <a name="prepare-the-virtual-machine-to-act-as-a-file-share-for-user-profiles"></a>Preparar a máquina virtual para atuar como um compartilhamento de arquivos para perfis de usuário

A seguir estão as instruções gerais sobre como preparar uma máquina virtual para atuar como um compartilhamento de arquivos para perfis de usuário:

1. Associar as máquinas de virtuais do host de sessão para um [grupo de segurança do Active Directory](https://docs.microsoft.com/windows/security/identity-protection/access-control/active-directory-security-groups). Esse grupo de segurança será usado para autenticar as máquinas de virtuais de hosts de sessão para a máquina virtual de compartilhamento de arquivo que acabou de criar.
2. [Conectar-se à máquina de virtual do compartilhamento de arquivo](https://docs.microsoft.com/azure/virtual-machines/windows/quick-create-portal#connect-to-virtual-machine).
3. Na máquina virtual do compartilhamento de arquivo, crie uma pasta na **unidade C** que será usado como o compartilhamento de perfil.
4. Clique com botão direito na nova pasta, selecione **propriedades**, selecione **Sharing**, em seguida, selecione **compartilhamento avançado...** .
5. Selecione **compartilhar esta pasta**, selecione **permissões...** , em seguida, selecione **adicionar...** .
6. Pesquise o grupo de segurança ao qual você adicionou as máquinas de virtuais do host de sessão e, em seguida, verifique se esse grupo tem **controle total**.
7. Depois de adicionar o grupo de segurança, clique com botão direito na pasta, selecione **propriedades**, selecione **compartilhamento**, em seguida, copie o **caminho de rede** usar para uso posterior.

Para obter recomendações sobre permissões, consulte o seguinte [FSLogix documentação](https://support.fslogix.com/index.php/forum-main/faqs/84-best-practices#120).

## <a name="configure-the-fslogix-profile-container"></a>Configurar o contêiner de perfil FSLogix

Para configurar as máquinas virtuais com o software FSLogix, faça o seguinte em cada computador registrado para o pool de host:

1. [Conectar-se à máquina virtual](https://docs.microsoft.com/azure/virtual-machines/windows/quick-create-portal#connect-to-virtual-machine) com as credenciais que você forneceu ao criar a máquina virtual.
2. Inicie um navegador da internet e navegue até o seguinte [link](https://go.microsoft.com/fwlink/?linkid=2084562) para baixar o agente FSLogix. Como parte da demonstração pública do Windows de área de trabalho Virtual, você obterá uma chave de licença para ativar o software FSLogix. A chave é o arquivo LicenseKey.txt incluído no arquivo. zip FSLogix agente.
3. Instale o agente FSLogix.
4. Navegue até **Program Files** > **FSLogix** > **aplicativos** para confirmar se o agente instalado.
5. No menu Iniciar, executar **RegEdit** como administrador. Navegue até **computador\\HKEY_LOCAL_MACHINE\\software\\FSLogix\\perfis**
6. Crie os seguintes valores:

| Nome                | Digite               | Dados/valor                        |
|---------------------|--------------------|-----------------------------------|
| Ativado             | DWORD              | 1                                 |
| VHDLocations        | Valor de cadeia de caracteres múltipla | "Caminho de rede para o compartilhamento de arquivos" |
| VolumeType          | Cadeia             |  VHDX                              |
| SizeInMBs           | DWORD              | "inteiro para o tamanho do perfil de"     |
| IsDynamic           | DWORD              | 1                                 |
| LockedRetryCount    | DWORD              | 1                                 |
| LockedRetryInterval | DWORD              | 0                                 |
