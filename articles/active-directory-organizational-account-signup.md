<properties umbracoNaviHide="0" pageTitle="How To Configure Cloud Services" metaKeywords="Azure cloud services, cloud service, configure cloud service" description="Learn how to configure Azure cloud services." linkid="manage-windows-how-to-guide-storage-accounts" urlDisplayName="How to: storage accounts" headerExpose="" footerExpose="" disqusComments="1" title="Sign up for Azure as an organization" authors="terrylan" manager="terrylan" />

<tags ms.service="active-directory" ms.workload="identity" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="01/01/1900" ms.author="terrylan" />

# Inscrever-se no Azure como uma organização

[WACOM.INCLUDE [disclaimer](../includes/disclaimer.md)]

Até recentemente, era possível se inscrever para uma nova assinatura do Azure usando apenas a sua Conta da Microsoft (Windows Live ID). Agora, o Azure oferece suporte aos dois seguintes métodos de conta para inscrição:

-   **Contas da Microsoft** (criadas por você para o uso pessoal) - fornecem acesso a todos os produtos voltados para o consumidor da Microsoft e serviços de nuvem, como Outlook (Hotmail), Messenger, SkyDrive, MSN, Xbox Live ou Office Live. Se inscrever no Outlook cria automaticamente uma conta da Microsoft com um endereço @Outlook.com. Após a criação, a conta da Microsoft pode ser usada para acessar serviços de nuvem relacionados ao consumidor da Microsoft e/ou ao Azure. [Saiba mais][Saiba mais]

-   **Contas organizacionais** (emitidas por uma administração para uso comercial/acadêmico) - fornecem acesso a todos os serviços de nuvem em nível de empresas pequenas e de médio porte da Microsoft, como o Azure, Windows Intune ou Office 365. Quando você se inscreve em qualquer um desses serviços como uma organização, um locatário baseado na nuvem é provisionado automaticamente no Active Directory do Windows Azure para representar sua organização. [Saiba mais][1]

    Quando esse locatário é criado, um administrador pode emitir contas organizacionais para cada um de seus funcionários/alunos e atribuir licenças a essas contas com base nas assinaturas do serviço de nuvem aos quais eles precisam de acesso, como o Azure.

    *Deseja inscrever-se no Azure como uma organização?* **Inscreva-se agora mesmo**

## O que é o Active Directory do Azure?

Da mesma forma como o Active Directory é um serviço disponibilizado para os clientes através do sistema operacional Windows Server para gerenciamento do diretório no local, o Active Directory do Azure (AD do Azure) é um serviço disponibilizado através do Azure para que você possa gerenciar o diretório de nuvem da sua organização. [Saiba mais][2]

Como esse é diretório de nuvem da sua organização, você decide quem são os seus usuários, quais informações são mantidas na nuvem, quem pode usar essas informações ou gerenciá-las e quais aplicativos ou serviços podem acessar essas informações. Ao usar o AD do Azure, é responsabilidade da Microsoft manter o Active Directory em funcionamento na nuvem com alta escala, alta disponibilidade e recuperação de desastres integrada, respeitando completamente os requisitos para privacidade e segurança das informações da organização.

### Integração com o Active Directory local

Se sua organização já usa o Active Directory no local, você poderá usar os recursos de integração do diretório do AD do Azure, como a sincronização do diretório e o logon único, para estender ainda mais o alcance de suas identidades existentes no local para a nuvem para oferecer uma experiência aprimorada para o administrador e o usuário final. [Saiba mais][3]

  [Saiba mais]: http://windows.microsoft.com/pt-br/windows-live/sign-in-what-is-microsoft-account
  [1]: http://technet.microsoft.com/pt-BR/library/jj573650
  [2]: http://technet.microsoft.com/pt-BR/library/hh967619
  [3]: http://technet.microsoft.com/pt-BR/library/jj573653
