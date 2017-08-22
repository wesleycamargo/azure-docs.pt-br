---
title: "Alterar a cobrança do Azure RemoteApp | Microsoft Docs"
description: Saiba como parar de ser cobrado pelo Azure RemoteApp.
services: remoteapp
documentationcenter: 
author: msmbaldwin
manager: mbaldwin
ms.assetid: 8f94da9a-7848-4ddc-b7b7-d9c280ccf4d7
ms.service: remoteapp
ms.workload: compute
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/11/2017
ms.author: mbaldwin
ms.translationtype: HT
ms.sourcegitcommit: cddb80997d29267db6873373e0a8609d54dd1576
ms.openlocfilehash: 32fc673eeef01e80c73375bf264206beea8cfbe5
ms.contentlocale: pt-br
ms.lasthandoff: 07/18/2017

---
# <a name="migrate-from-azure-remoteapp-to-mycloudit"></a>Migrar do Azure RemoteApp para o MyCloudIT 

**Atualmente, você usa o Microsoft Azure RemoteApp?**: o MyCloudIT criou uma ferramenta automatizada para migrar suas coleções do ARA (Azure RemoteApp) para a plataforma de gerenciamento MyCloudIT, ao mesmo tempo que continuam sendo executadas no Microsoft Azure.

**Aproveite o portal do Azure Resource Manager**: a migração concluída para a plataforma MyCloudIT permite o acesso instantâneo ao novo portal do Azure Resource Manager do Azure. Esse portal contém todas as novas funcionalidades e inovações oferecidas pelo Microsoft Azure, incluindo o acesso a tamanhos de Máquina Virtual para garantir que a implantação seja criada para dar suporte às necessidades de sua empresa.

**Testar em paralelo para garantir a solução certa para suas necessidades**: a ferramenta de migração do MyCloudIT foi criada para iniciar o processo de migração e testar em paralelo, enquanto os usuários atuais do ARA continuam usando o ARA.  Os usuários permanecerão no ARA até que a migração e o teste sejam concluídos.  A ferramenta de migração foi criada para manipular a coleção típica do ARA.  Se você acredita que tem um cenário exclusivo não padrão, contate nossa equipe pelo email [sales@conexlink.com](mailto:sales@conexlink.com), para que possamos fornecer um plano adaptado para ajudar na migração.

**Funcionalidades de Área de Trabalho como Serviço**: observe que o MyCloudIT oferece não apenas as funcionalidades do RemoteApp com as quais você está acostumado, mas também Funcionalidades completas de Área de Trabalho como Serviço, pelo mesmo custo por mês sem requisitos mínimos de usuário.

## <a name="what-we-will-do-for-you"></a>O que faremos por você

O MyCloudIT automatiza a migração do modelo do Azure RemoteApp do portal clássico do Azure de sua assinatura para o Portal do Azure Resource Manager de sua assinatura com nossa ferramenta de migração automatizada.  

> [!NOTE]
> O modelo do Azure RemoteApp deve permanecer na mesma Região do Azure da implantação original do Azure RemoteApp.  Se precisar alterar Regiões do Azure ou assinaturas do Azure durante a migração, contate nossa equipe para obter mais diretrizes pelo email [sales@conexlink.com](mailto:sales@conexlink.com).

Leia abaixo para obter informações detalhadas sobre o processo de migração automatizada com a ferramenta de migração do MyCloudIT:

1. A ferramenta de migração examina suas assinaturas atuais em busca de todas as implantações existentes do ARA.  
2. Selecione uma coleção do ARA por vez para a migração.  Caso você tenha várias coleções, execute nossa ferramenta várias vezes.
3. Você tem a opção de copiar os UPDs (Discos de Perfil do Usuário) para a nova implantação, de modo que você possa recuperar dados herdados ou mapear os UPDs manualmente para a nova implantação. Se você optar por copiar os UPDs, nós os salvaremos e incluiremos um arquivo de texto que mapeia o nome do UPD para o nome de cada usuário.  Os UPDs serão copiados para um compartilhamento no servidor RDSMGMT `F:\Shares\LegacyUPD` e serão expostos por meio do compartilhamento `\\RDSmgmt\LegacyUPD`. 
4. A migração não exigirá nenhum tempo de inatividade para a implantação atual do ARA.  Porém, se forem feitas alterações nos UPDs (do ARA) após a cópia, essas alterações não serão refletidas nos UPDs armazenados no portal do Azure Resource Manager. 
5. Se você tiver VMs adicionais, como Controladores de Domínio e Servidores de Arquivos na Rede Virtual Clássica do Azure, estabeleceremos o emparelhamento VNET entre a Rede Virtual Clássica existente do Azure e a nova Rede Virtual que criamos para você, no novo Portal do Azure Resource Manager.
6. Nossa solução automatizada apenas estabelecerá o emparelhamento VNET entre a Rede Virtual Clássica existente do Azure e a nova Rede Virtual se a implantação existente do ARA for uma Implantação Híbrida; em outras palavras, se você estiver autenticando com um Controlador de Domínio do Active Directory do Windows Server na Rede Virtual Clássica existente. Se não estabelecermos o emparelhamento VNET para sua coleção, mas você precisar dele, contate nossa equipe pelo email [sales@conexlink.com](mailto:sales@conexlink.com) e ficaremos felizes em configurar o emparelhamento VNET sem custo adicional.
7. Nossa solução automatizada garantirá que a configuração do DNS do Azure é atualizada com as novas configurações da Rede Virtual, a fim de garantir que a nova implantação possa se conectar ao Controlador de Domínio existente na VNET clássica.
8. Nossa solução automatizada garantirá que não há nenhum conflito de endereço IP enquanto criamos essa nova Rede Virtual e estabelecemos o emparelhamento VNET para implantações que têm um Servidor existente do Windows Server Active Directory.
9. Se você estiver usando apenas o Azure AD para autenticação, o MyCloudIT criará um novo Domínio do Active Directory do e usará o Azure AD Connect para sincronizar usuários entre a instância existente do Azure AD e o novo Domínio do Active Directory do Windows Server criado pelo MyCloudIT.
10. Se você estiver usando um Domínio do Active Directory do Windows Server para autenticar usuários do ARA, nossa solução automatizada conectará a nova implantação existente do MyCloudIT ao Controlador de Domínio do Active Directory do Windows Server por meio do emparelhamento VNET.
11. Se você estiver usando o Azure Active Directory Domain Services para autenticação, poderemos migrá-lo, mas contate nossa equipe para que possamos criar um plano de migração personalizado para você.  Envie um email para [sales@conexlink.com](mailto:sales@conexlink.com). 
12. Depois que a coleção a ser migrada for confirmada, aguarde e observe enquanto nossa solução automatizada migra sua coleção do ARA e os Discos de Perfil do Usuário (opcional) para a nova solução de Aplicativos Remotos controlada pelo MyCloudIT.
13. Depois que a implantação for concluída, publicaremos novamente os mesmos aplicativos que foram publicados no ARA e, após a implantação, você poderá publicar mais aplicativos.

## <a name="post-migration-benefits"></a>Benefícios pós-migração

1. Fornecemos o console de gerenciamento que permite gerenciar o ciclo de vida completo de sua implantação de Aplicativos Remotos.
2. Você poderá gerenciar suas Máquinas Virtuais em nosso portal.  Inicie/pare e redimensione VMs individuais, se necessário.
3. O console de gerenciamento fornece a capacidade de criar e gerenciar usuários/grupos em nosso portal de gerenciamento.
4. O console de gerenciamento fornece a capacidade de sincronizar usuários com o Office 365 para criar uma experiência de logon semelhante.
5. O console de gerenciamento fornece a capacidade de criar Coleções adicionais de Aplicativo Remoto e Área de Trabalho sem custos de usuário duplicados nem requisitos mínimos de usuário. 
6. O console de gerenciamento fornece a capacidade de publicar novos aplicativos de Aplicativos Remotos.
7. O console de gerenciamento fornece a capacidade de agendar a inicialização e o desligamento da implantação de Aplicativos Remotos, caso você apenas precise da solução durante horários específicos.
8. O console de gerenciamento fornece a capacidade de automatizar a instalação e a configuração do agente de Backup do Azure, que fornece um histórico de retenção de documentos para dados do cliente.
9. O console de gerenciamento fornece acesso a métricas de desempenho da implantação.  Isso possibilita identificar possíveis afunilamentos de desempenho sem a instalação de ferramentas de gerenciamento de desempenho adicionais.
10. Se você tiver vários hosts da sessão, poderá habilitar o dimensionamento automático para que apenas os hosts da sessão que precisam estar em execução sejam executados.
11. O MyCloudIT fornece acesso ao servidor de gateway RDWeb por meio de um nome de domínio do MyCloudIT.  Também fornecemos a capacidade de remapear a URL para uma URL personalizada para a identidade visual do usuário final.

## <a name="prerequisites-for-migration"></a>Pré-requisitos da migração

1. Você deve ter acesso à assinatura do Azure que hospeda sua solução atual do Azure RemoteApp.
2. Você deve conceder permissões ao nosso portal dentro da assinatura para migrar seu modelo e para criar/modificar sua nova implantação do MyCloudIT.
3. Observe que, devido a uma limitação do Azure RemoteApp, cada coleção pode ser migrada apenas três vezes.  Se você precisar migrar uma coleção mais de três vezes, gere um tíquete para o Azure, a fim de aumentar a contagem de exportação ou contate nossa equipe e ajudaremos na solicitação do ARA para aumentar a contagem de exportação.

## <a name="mycloudit-billing"></a>Cobrança do MyCloudIT

Consulte [Preços do MyCloudIT para soluções do RemoteApp](https://mcitdocuments.blob.core.windows.net/terms/MyCloudIT_Pricing_Overview.pdf) (PDF) para obter informações sobre como prever e gerenciar os custos gerais do Azure.

Caso ainda tenha dúvidas, contate nossa equipe pelo email [sales@conexlink.com](mailto:sales@conexlink.com) ou assista ao vídeo de demonstração completo [Azure RemoteApp Migration Tool – MyCloudIT](https://www.youtube.com/watch?v=YQ_1F-JeeLM&t=482s) (Ferramenta de Migração do Azure RemoteApp – MyCloudIT). 


