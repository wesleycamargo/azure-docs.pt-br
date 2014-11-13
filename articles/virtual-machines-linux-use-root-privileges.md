<properties urlDisplayName="Use root privileges" pageTitle="Usar privil&eacute;gios de raiz nas m&aacute;quinas virtuais Linux do Azure" metaKeywords="" description="Saiba como usar privil&eacute;gios de raiz em uma m&aacute;quina virtual Linux do Azure." metaCanonical="" services="virtual-machines" documentationCenter="" title="Utilizando privil&eacute;gios de raiz nas m&aacute;quinas virtuais Linux do Azure" authors="timlt" solutions="" manager="timlt" editor="" />

<tags ms.service="virtual-machines" ms.workload="infrastructure-services" ms.tgt_pltfrm="vm-linux" ms.devlang="na" ms.topic="article" ms.date="01/01/1900" ms.author="timlt" />

# Utilizando privilégios de raiz nas máquinas virtuais Linux do Azure

Os usuários podem executar comandos com privilégios elevados em uma máquina virtual do Linux usando o `sudo`comando. No entanto, a experiência pode variar dependendo de como o sistema foi fornecido.

1.  **Chave SSH e senha ou apenas** a máquina virtual foi configurada com um dos certificados (`.CER` arquivo), bem como uma senha, ou apenas um nome de usuário e uma senha. Neste caso `sudo` solicitará a senha do usuário antes de executar o comando.

2.  **Somente a chave SSH** a máquina virtual foi configurada com um certificado (`.cer` ou `.pem` arquivo), mas nenhuma senha. Neste caso `sudo` **não** solicitará a senha do usuário antes de executar o comando.

## SSH chave e a senha ou a senha apenas

Faça logon na máquina virtual Linux usando autenticação de senha ou chave SSH, em seguida, executar comandos usando `sudo`, por exemplo:

    # sudo <command>
    [sudo] password for azureuser:

Nesse caso, o usuário será solicitado para uma senha. Depois de inserir a senha, `sudo` executará o comando com raiz`root` privilégios.

## SSH chave somente

Faça logon na máquina virtual Linux usando autenticação de chave SSH, em seguida, execute comandos usando `sudo`, por exemplo:

    # sudo <command>

Nesse caso, o usuário **não** será solicitado para uma senha. Depois de pressionar `<enter>`,`sudo` executará o comando com `root` privilégios.

