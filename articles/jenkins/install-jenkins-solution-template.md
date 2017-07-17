---
title: Criar seu primeiro Jenkins mestre em uma VM Linux (Ubuntu) no Azure
description: "Aproveite o modelo de solução para implantar o Jenkins."
services: app-service\web
documentationcenter: 
author: mlearned
manager: douge
editor: 
ms.assetid: 8bacfe3e-7f0b-4394-959a-a88618cb31e1
ms.service: multiple
ms.workload: web
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: hero-article
ms.date: 6/7/2017
ms.author: mlearned
ms.custom: mvc
ms.translationtype: Human Translation
ms.sourcegitcommit: 1500c02fa1e6876b47e3896c40c7f3356f8f1eed
ms.openlocfilehash: 748f1994d0ee5b6c6a988bca8e5b694b29c0b8c5
ms.contentlocale: pt-br
ms.lasthandoff: 06/30/2017

---

<a id="create-your-first-jenkins-master-on-a-linux-ubuntu-vm-on-azure" class="xliff"></a>

# Criar seu primeiro Jenkins mestre em uma VM Linux (Ubuntu) no Azure

Este guia de início rápido mostra como instalar a versão do Jenkins estável mais recente em uma VM Linux (Ubuntu 14.04 LTS) junto com as ferramentas e os plug-ins configurados para funcionar com o Azure. As ferramentas incluem:
<ul>
<li>Git para controle do código-fonte</li>
<li>Plug-in de credencial do Azure para se conectar com segurança</li>
<li>Plugin de agentes de VM do Azure para build elástico, teste e integração contínua</li>
<li>Plugin do Armazenamento do Azure para armazenar os artefatos</li>
<li>CLI do Azure para implantar aplicativos usando scripts</li>
</ul>

Neste tutorial, você aprenderá a:

> [!div class="checklist"]
> * Crie uma conta gratuita do Azure.
> * Crie um mestre Jenkins em uma VM do Azure com um modelo de solução. 
> * Execute a configuração inicial para o Jenkins.
> * Instale os plug-ins sugeridos.

Se você não tiver uma assinatura do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

<a id="create-the-vm-in-azure-by-deploying-the-solution-template-for-jenkins" class="xliff"></a>

## Criar a VM no Azure implantando o modelo de solução para Jenkins

Os modelos de início rápido do Azure permitem a implantação rápida e confiável de tecnologias complexas no Azure.  O Azure Resource Manager permite que você provisione seus aplicativos usando um [modelo declarativo](https://azure.microsoft.com/en-us/resources/templates/?term=jenkins) Em um modelo único, você pode implantar vários serviços, juntamente com suas dependências. Use o mesmo modelo para implantar repetidamente seu aplicativo durante cada estágio do ciclo de vida do aplicativo.

Exiba as informações de [planos e preços](https://azuremarketplace.microsoft.com/en-us/marketplace/apps/bitnami.jenkins?tab=PlansAndPrice) para este modelo a fim de entender as opções de custo.

Vá para [A imagem do marketplace para o Jenkins](https://azuremarketplace.microsoft.com/en-us/marketplace/apps/azure-oss.jenkins?tab=Overview), clique em **OBTER AGORA**  

No portal do Azure, clique em **Criar**.  Este modelo requer o uso do Gerenciador de Recursos e, por isso, a lista suspensa de modelos está desabilitada.
   
![Diálogo do portal do Azure](./media/install-jenkins-solution-template/ap-create.png)

Na guia **Definir configurações básicas**:

![Definir as configurações básicas](./media/install-jenkins-solution-template/ap-basic.png)

* Forneça um nome para sua instância Jenkins.
* Selecione um tipo de disco VM.  Para cargas de trabalho de produção, escolha VM e SSD maiores para melhorar o desempenho.  Você pode ler mais sobre os tipos de disco do Azure [aqui.](https://docs.microsoft.com/en-us/azure/storage/storage-premium-storage)
* Nome de usuário: deve atender aos requisitos de tamanho e não deve incluir palavras reservadas ou caracteres sem suporte. Não são permitidos nomes como "admin".  Para obter mais informações, consulte [aqui](https://docs.microsoft.com/en-us/azure/virtual-machines/windows/faq) os requisitos de senha e nome de usuário.
* Tipo de autenticação: crie uma instância protegida por uma senha ou [chave pública SSH](https://docs.microsoft.com/en-us/azure/virtual-machines/linux/ssh-from-windows). Se você usar uma senha, ela deve atender a 3 dos seguintes requisitos: um caractere em letra minúscula, um em maiúscula, um número e um caractere especial.
* Manter o tipo de versão do Jenkins como **LTS**
* Selecione uma assinatura.
* Crie um Grupo de recursos ou use um grupo existente.
* Selecione um local.

Na guia **Configurar opções adicionais**:

![Configurar opções adicionais](./media/install-jenkins-solution-template/ap-addtional.png)

* Forneça um rótulo de nome de domínio para identificar exclusivamente o mestre Jenkins.

Clique em **OK** para ir para a próxima etapa. 

Quando a validação for bem-sucedida, clique em **OK** para baixar o modelo e os parâmetros. 

Em seguida, selecione **Comprar** para provisionar todos os recursos.

<a id="setup-ssh-port-forwarding" class="xliff"></a>

## Configurar o encaminhamento de porta SSH

Por padrão, a instância Jenkins está usando o protocolo HTTP e escuta na porta 8080. Os usuários não devem autenticar por meio de protocolos não seguros.
    
Configure o encaminhamento de porta para exibir a IU do Jenkins no seu computador local.

<a id="if-you-are-using-windows" class="xliff"></a>

### Se você estiver usando o Windows:

Instale o PuTTY e execute esse comando se você usa senha para proteger o Jenkins:
```
putty.exe -ssh -L 8080:localhost:8080 <username>@<Domain name label>.<location>.cloudapp.azure.com
```
* Para fazer logon, digite a senha.

![Para fazer logon, digite a senha.](./media/install-jenkins-solution-template/jenkins-pwd.png)

Se você usa SSH, execute este comando:
```
putty -i <private key file including path> -L 8080:localhost:8080 <username>@<Domain name label>.<location>.cloudapp.azure.com
```

<a id="if-you-are-using-linux-or-mac" class="xliff"></a>

### Se você estiver usando Linux ou Mac:

Se você usa senha para proteger o mestre Jenkins, execute este comando:
```
ssh -L 8080:localhost:8080 <username>@<Domain name label>.<location>.cloudapp.azure.com
```
* Para fazer logon, digite a senha.

Se você usa SSH, execute este comando:
```
ssh -i <private key file including path> -L 8080:localhost:8080 <username>@<Domain name label>.<location>.cloudapp.azure.com
```

<a id="connect-to-jenkins" class="xliff"></a>

## Conectar-se ao Jenkins
Depois de iniciar o túnel, navegue para http://localhost:8080/ em seu computador local.

Desbloqueie o painel Jenkins pela primeira vez com a senha de administrador inicial.

![Desbloquear Jenkins](./media/install-jenkins-solution-template/jenkins-unlock.png)

Para obter um token, use SSH para a VM e execute `sudo cat /var/lib/jenkins/secrets/initialAdminPassword`.

![Desbloquear Jenkins](./media/install-jenkins-solution-template/jenkins-ssh.png)

Você será solicitado a instalar os plug-ins sugeridos.

Em seguida, crie um usuário de administrador para o mestre Jenkins.

Sua instância Jenkins agora está pronta para uso! Você pode acessar uma exibição somente leitura acessando http://\<nome DNS público da instância que você acabou de criar\>.

![O Jenkins está pronto!](./media/install-jenkins-solution-template/jenkins-welcome.png)

<a id="next-steps" class="xliff"></a>

## Próximas etapas

Neste tutorial, você:

> [!div class="checklist"]
> * Criou um mestre Jenkins com o modelo de solução.
> * Executou a configuração inicial do Jenkins.
> * Instalou plug-ins.

Siga este link para ver como usar agentes de VM do Azure para a integração contínua com o Jenkins.

> [!div class="nextstepaction"]
> [Máquinas virtuais do Azure como agentes do Jenkins](jenkins-azure-vm-agents.md)

