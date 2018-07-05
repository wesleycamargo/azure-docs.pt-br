---
title: Configurar uma identidade comum para a Máquina Virtual de Ciência de Dados – Azure | Microsoft Docs
description: Configure uma identidade comum em ambientes de DSVM da equipe da empresa.
keywords: aprendizado profundo, IA, ferramentas de ciência de dados, máquina virtual de ciência de dados, análise geoespacial, processo de ciência de dados da equipe
services: machine-learning
documentationcenter: ''
author: gopitk
manager: cgronlun
ms.assetid: ''
ms.service: machine-learning
ms.component: data-science-vm
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/08/2018
ms.author: gokuma
ms.openlocfilehash: d6235f3a425481a13e627d683bb4c3943b473b40
ms.sourcegitcommit: 638599eb548e41f341c54e14b29480ab02655db1
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/21/2018
ms.locfileid: "36311070"
---
# <a name="set-up-a-common-identity-on-the-data-science-virtual-machine"></a>Configurar uma identidade comum na Máquina Virtual de Ciência de Dados

Em uma VM (máquina virtual) do Azure, incluindo a DSVM (Máquina Virtual de Ciência de Dados), você cria contas de usuário locais enquanto provisiona a VM. Os usuários então se autenticam na VM usando essas credenciais. Se você tiver várias VMs às quais precisa acessar, essa abordagem poderá tornar o gerenciamento de credenciais difícil rapidamente. Contas de usuário comuns e gerenciamento usando um provedor de identidade baseado em padrões permitem que você use um único conjunto de credenciais para acessar vários recursos no Azure, incluindo várias DSVMs. 

O Active Directory é um provedor de identidade popular e tem suporte no Azure como serviço e no local. Você pode usar o Azure AD (Azure Active Directory) ou o Active Directory local para autenticar usuários em uma DSVM autônoma ou em um cluster de DSVMs em um conjunto de dimensionamento de máquina virtual do Azure. Você faz isso unindo as instâncias de DSVM a um domínio do Active Directory. 

Se você já tiver o Active Directory para gerenciar as identidades, poderá usá-lo como seu provedor de identidade comum. Se você não tiver o Active Directory, poderá executar uma instância gerenciada do Active Directory no Azure por meio de um serviço chamado [Azure AD DS](https://docs.microsoft.com/azure/active-directory-domain-services/) (Azure Active Directory Domain Services). 

A documentação do [Azure AD](https://docs.microsoft.com/azure/active-directory/) fornece [instruções detalhadas de gerenciamento](https://docs.microsoft.com/azure/active-directory/choose-hybrid-identity-solution#synchronized-identity), incluindo a conexão do Azure AD a seu diretório local, se você tiver um. 

Este artigo descreve as etapas para configurar um serviço de domínio do Active Directory totalmente gerenciado no Azure usando o Azure AD DS. Em seguida, você pode associar DSVMs ao domínio gerenciado do Active Directory para permitir que os usuários acessem um conjunto de DSVMs (e outros recursos do Azure) usando uma conta de usuário e credenciais comuns. 

## <a name="set-up-a-fully-managed-active-directory-domain-on-azure"></a>Configurar um domínio do Active Directory totalmente gerenciado no Azure

O Azure AD DS simplifica o gerenciamento de identidades, fornecendo um serviço totalmente gerenciado no Azure. Nesse domínio do Active Directory, você pode gerenciar usuários e grupos. Etapas para configurar contas de usuário e um domínio do Active Directory hospedado no Azure em seu diretório:

1. No portal do Azure, adicione o usuário ao Active Directory: 

   a. Entre no [Centro de administração do Azure Active Directory](https://aad.portal.azure.com) com uma conta que seja um administrador global do diretório.
    
   b. Selecione **Azure Active Directory** e, em seguida, **Usuários e grupos**.
    
   c. Em **Usuários e grupos**, selecione **Todos os usuários** e, em seguida, selecione **Novo usuário**.
   
      O painel **Usuário** é aberto.
      
      ![O painel "Usuário"](./media/add-user.png)
    
   d. Insira os detalhes do usuário, como **Nome** e **Nome de usuário**. A parte de nome de domínio do nome de usuário deve ser o nome de domínio padrão inicial "[nome de domínio].onmicrosoft.com" ou um [nome de domínio personalizado](../../active-directory/add-custom-domain.md) verificado e não federado, como "contoso.com".
    
   e. Copie ou anote a senha de usuário gerada para que você possa fornecê-la ao usuário depois que esse processo estiver concluído.
    
   f. Opcionalmente, você pode abrir e preencher as informações em **Perfil**, **Grupos** ou **Função do diretório** para o usuário. 
    
   g. Em **Usuário**, selecione **Criar**.
    
   h. Distribua com segurança a senha gerada para o novo usuário para que ele possa entrar.

2. Crie uma instância do Azure AD DS. Siga as instruções do artigo [Habilitar o Azure Active Directory Domain Services usando o Portal do Azure](https://docs.microsoft.com/azure/active-directory-domain-services/active-directory-ds-getting-started) (tarefas 1 a 5). É importante atualizar as senhas de usuários existentes no Active Directory para que a senha no Azure AD DS esteja sincronizada. Também é importante adicionar o DNS ao Azure AD DS, conforme descrito na tarefa 4 do artigo. 

3. Criar uma sub-rede de DSVM separada na rede virtual criada na tarefa 2 da etapa anterior.
4. Criar uma ou mais instâncias de VM de Ciência de Dados na sub-rede de DSVM. 
5. Siga as [instruções](https://docs.microsoft.com/azure/active-directory-domain-services/active-directory-ds-join-ubuntu-linux-vm ) para adicionar DSVM ao Active Directory. 
6. Monte um compartilhamento de Arquivos do Azure para hospedar seu diretório inicial ou de bloco de anotações para habilitar a montagem de seu espaço de trabalho em qualquer computador. (Se você precisa de rigorosas permissões de nível de arquivo, será necessário um NFS em execução em uma ou mais VMs).

   a. [Criar um compartilhamento de arquivos do Azure](../../storage/files/storage-how-to-create-file-share.md).
    
   b. Monte-o em DSVM no Linux. Quando você selecionar o botão **Conectar** para o compartilhamento de Arquivos do Azure em sua conta de armazenamento no Portal do Azure, o comando a ser executado no shell do Bash na DSVM do Linux será mostrado. O comando é assim:
   
   ```
   sudo mount -t cifs //[STORAGEACCT].file.core.windows.net/workspace [Your mount point] -o vers=3.0,username=[STORAGEACCT],password=[Access Key or SAS],dir_mode=0777,file_mode=0777,sec=ntlmssp
   ```
7. Presuma que você tenha montado o compartilhamento de Arquivos do Azure em /data/workspace, por exemplo. Agora crie diretórios para cada um dos usuários no compartilhamento: /data/workspace/user1, /data/workspace/user2 e assim por diante. Criar um diretório `notebooks` no espaço de trabalho de cada usuário. 
8. Criar links simbólicos para `notebooks` em `$HOME/userx/notebooks/remote`.   

Agora, você tem os usuários em sua instância do Active Directory hospedada no Azure. Usando as credenciais do Active Directory, os usuários podem fazer logon em qualquer DSVM (SSH ou JupyterHub) associado ao Azure AD DS. Como o espaço de trabalho do usuário está em um compartilhamento de Arquivos do Azure, os usuários terão acesso a seus blocos de anotações e outros trabalhos de qualquer DSVM ao usar o JupyterHub. 

Para o dimensionamento automático, você pode usar um conjunto de dimensionamento de máquinas virtuais para criar um pool de VMs que ingressaram no domínio dessa maneira e com o disco compartilhado montado. Os usuários podem fazer logon em qualquer computador disponível no conjunto de dimensionamento de máquinas virtuais e ter acesso ao disco compartilhado onde os seus blocos de anotações são salvos. 

## <a name="next-steps"></a>Próximas etapas

* [Armazenar com segurança as credenciais para acessar recursos de nuvem](dsvm-secure-access-keys.md)



