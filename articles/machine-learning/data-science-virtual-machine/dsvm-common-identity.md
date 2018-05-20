---
title: Configurar identidade comum para Máquina Virtual de Ciência de Dados – Azure | Microsoft Docs
description: Configure uma identidade comum em ambientes de DSVM da equipe da empresa.
keywords: aprendizado profundo, IA, ferramentas de ciência de dados, máquina virtual de ciência de dados, análise geoespacial, processo de ciência de dados da equipe
services: machine-learning
documentationcenter: ''
author: gopitk
manager: cgronlun
ms.assetid: ''
ms.service: machine-learning
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/08/2018
ms.author: gokuma
ms.openlocfilehash: 431d3079c0d942c15ccbc8352261ccfe1f5f6e47
ms.sourcegitcommit: 909469bf17211be40ea24a981c3e0331ea182996
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/10/2018
---
# <a name="setup-common-identity-on-the-data-science-vm"></a>Configurar uma identidade comum na VM de Ciência de Dados

Por padrão, na VM do Azure incluindo a VM de Ciência de Dados (DSVM), contas de usuário local são criadas durante o provisionamento da VM e os usuários autenticam-se na VM com essas credenciais. Se você tiver várias VMs às quais precisa acessar, essa abordagem pode tornar o gerenciamento de credenciais difícil rapidamente. Contas de usuário comuns e gerenciamento usando um provedor de identidade baseado em padrões permitem que você use um único conjunto de credenciais para acessar vários recursos no Azure, incluindo várias DSVMs. 

O Active Directory (AD) é um provedor de identidade popular e tem suporte no Azure como serviço e também no local. Você pode aproveitar o AD ou Azure AD para autenticar usuários em uma Máquina Virtual de Ciência de Dados (DSVM) autônoma ou em um cluster de DSVM em um conjunto de dimensionamento de máquinas virtuais do Azure. Isso é feito ao unir as instâncias de DSVM a um domínio do AD. Se você já tiver um Active Directory para gerenciar as identidades, poderá usá-lo como seu provedor de identidade comum. Caso não tenha um AD, você pode executar um AD gerenciado no Azure por meio de um serviço chamado de [Azure Active Directory Domain Services](https://docs.microsoft.com/azure/active-directory-domain-services/)(Azure AD DS). 

A documentação do [Microsoft Azure Active Directory (Azure AD)](https://docs.microsoft.com/azure/active-directory/) fornece [instruções](https://docs.microsoft.com/azure/active-directory/choose-hybrid-identity-solution#synchronized-identity) detalhadas para o Active Directory gerenciado, incluindo a conexão do Azure AD a seu diretório local, caso haja. 

O restante deste artigo descreve as etapas para configurar um serviço de domínio do AD totalmente gerenciado no Azure usando o Azure AD DS e para unir as DSVMs ao domínio do AD gerenciado para permitir aos usuários acesso a um pool de DSVMs (e outros recursos do Azure) usando uma conta de usuário e credenciais comuns. 

##  <a name="set-up-a-fully-managed-active-directory-domain-on-azure"></a>Configurar um domínio do Active Directory totalmente gerenciado no Azure

O Azure AD DS simplifica o gerenciamento de identidades, fornecendo um serviço totalmente gerenciado no Azure. Nesse domínio do Active Directory, os usuários e grupos são gerenciados.  Etapas para configurar contas de usuário e um domínio do AD hospedado no Azure em seu diretório:

1. Adicionar usuário(s) ao Active Directory no portal 

    a. Entre no [Centro de administração do Azure Active Directory](https://aad.portal.azure.com) com uma conta que seja um administrador global do diretório.
    
    b. Selecione **Azure Active Directory** e, em seguida, **Usuários e grupos**.
    
    c. Em **Usuários e grupos**, selecione **Todos os usuários** e, em seguida, selecione **Novo usuário**.
   
   ![Selecionando o comando Adicionar](./media/add-user.png)
    
    d. Insira os detalhes do usuário, como **Nome** e **Nome de usuário**. A parte de nome de domínio do nome de usuário deve ser o nome de domínio padrão inicial "[nome de domínio].onmicrosoft.com" ou um [nome de domínio personalizado](../../active-directory/add-custom-domain.md) verificado e não federado, como "contoso.com".
    
    e. Copie ou anote a senha de usuário gerada para que você possa fornecê-la ao usuário depois que esse processo estiver concluído.
    
    f. Opcionalmente, você pode abrir e preencher as informações em **Perfil**, **Grupos** ou **Função do diretório** para o usuário. 
    
    g. Em **Usuário**, selecione **Criar**.
    
    h. Distribua com segurança a senha gerada para o novo usuário para que ele possa entrar.

2.  Criar os Azure Active Directory Domain Services

    Para obter mais informações, siga as instruções do artigo “[Habilitar o Azure Active Directory Domain Services usando o Portal do Azure](https://docs.microsoft.com/azure/active-directory-domain-services/active-directory-ds-getting-started)” (Tarefas 1 a 5). É importante que as senhas de usuários existentes no Active Directory sejam atualizadas para que a senha no Azure AD DS esteja sincronizada. Também é importante adicionar o DNS ao Azure AD DS, conforme listado na Tarefa nº 4 do artigo acima. 

3.  Criar uma sub-rede de DSVM separada na rede virtual criada na Tarefa nº 2 da etapa anterior
4.  Criar uma ou mais instâncias de VM de Ciência de Dados na sub-rede de DSVM 
5.  Siga as [instruções](https://docs.microsoft.com/azure/active-directory-domain-services/active-directory-ds-join-ubuntu-linux-vm ) para adicionar DSVM ao AD. 
6.  Em seguida, monte Arquivos do Azure compartilhados para hospedar seu diretório inicial ou de bloco de anotações para habilitar a montagem de seu espaço de trabalho em qualquer computador. (Se você precisa de rigorosas permissões de nível de arquivo, será necessário um NFS em execução em uma ou mais VMs)

    a. [Criar um Compartilhamento de Arquivos do Azure](../../storage/files/storage-how-to-create-file-share.md)
    
    b. Monte-o em DSVM no Linux. Quando você clicar no botão "Conectar" para os Arquivos do Azure em sua conta de armazenamento no Portal do Azure, o comando a ser executado no shell do bash na DSVM do Linux será mostrado. O comando terá esta aparência:
```
sudo mount -t cifs //[STORAGEACCT].file.core.windows.net/workspace [Your mount point] -o vers=3.0,username=[STORAGEACCT],password=[Access Key or SAS],dir_mode=0777,file_mode=0777,sec=ntlmssp
```
7.  Digamos que você montou seus Arquivos do Azure em /data/workspace. Agora crie diretórios para cada um dos seus usuários no compartilhamento. /data/workspace/user1, /data/workspace/user2 e assim por diante. Criar um diretório ```notebooks``` no espaço de trabalho de cada usuário. 
8. Criar links simbólicos para o ```notebooks``` em ```$HOME/userx/notebooks/remote```.   

Agora, você tem os usuários no Active Directory hospedado no Azure e pode fazer logon em qualquer DSVM (SSH, Jupyterhub) associada ao Azure AD DS usando as credenciais do AD. Como o espaço de trabalho do usuário está em arquivos compartilhados do Azure, o usuário terá acesso a seus blocos de anotações e outros trabalhos de qualquer DSVM ao usar Jupyterhub. 

Para o dimensionamento automático, você pode usar o conjunto de dimensionamento de máquinas virtuais para criar um pool de VMs que ingressaram no domínio dessa maneira e com o disco compartilhado montado. Os usuários podem fazer logon em qualquer computador disponível no conjunto de dimensionamento de máquinas virtuais e ter acesso ao disco compartilhado onde os seus blocos de anotações são salvos. 

## <a name="next-steps"></a>Próximas etapas

* [Armazenar com segurança as credenciais para acessar recursos de nuvem](dsvm-secure-access-keys.md)



