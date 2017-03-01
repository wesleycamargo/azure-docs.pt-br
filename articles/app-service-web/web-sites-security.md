---
title: "Proteger um aplicativo no Serviço de Aplicativo do Azure"
description: "Saiba como proteger um aplicativo Web, um back-end de aplicativo móvel ou um aplicativo de API no Serviço de Aplicativo do Azure."
services: app-service
documentationcenter: 
author: cephalin
manager: erikre
editor: 
ms.assetid: 5ce560b4-42d7-4b20-935c-0445fd539e39
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: multiple
ms.topic: article
ms.date: 01/12/2016
ms.author: cephalin
translationtype: Human Translation
ms.sourcegitcommit: 5d51a5ef3387b4c00079547b0f44ffe1f96bd77c
ms.openlocfilehash: 641fe31fc4b160fe5f0628df717006fb540394b9
ms.lasthandoff: 02/17/2017


---
# <a name="secure-an-app-in-azure-app-service"></a>Proteger um aplicativo no Serviço de Aplicativo do Azure
Este artigo ajuda você a começar a proteger seu aplicativo Web, o back-end do aplicativo móvel ou o aplicativo de API no Serviço de Aplicativo do Azure. 

A segurança no Serviço de Aplicativo do Azure tem dois níveis: 

* **Segurança de plataforma e infraestrutura** - você confia no Azure para ter os serviços de que precisa para executar tarefas com segurança na nuvem.
* **Segurança de aplicativo** -você precisa projetar o aplicativo em si de modo seguro. Isso inclui como você se integra ao Active Directory do Azure, como gerencia certificados e como garante que pode se comunicar com segurança com diferentes serviços. 

#### <a name="infrastructure-and-platform-security"></a>Segurança de plataforma e infraestrutura
Como o Serviço de Aplicativo mantém as VMs, o armazenamento, as conexões de rede, as estruturas da Web, os recursos de gerenciamento e a integração do Azure e muito mais, ele é ativamente protegido e fortalecido, além de passar continuamente por rigorosas verificações de conformidade para garantir que:

* Seus aplicativos do Serviço de Aplicativo estejam isolados da Internet e de outros recursos do Azure dos clientes.
* A comunicação de segredos (por exemplo, cadeias de conexão) entre o aplicativo do Serviço de Aplicativo e outros recursos do Azure (por exemplo, Banco de Dados SQL) em um grupo de recursos permaneça dentro do Azure e não cruze nenhum limite de rede. Os segredos sejam sempre criptografados.
* Toda a comunicação entre seu aplicativo do Serviço de Aplicativo e recursos externos, como gerenciamento do PowerShell, interface de linha de comando, SDKs do Azure, APIs REST e conexões híbridas, seja corretamente criptografada.
* O gerenciamento de ameaças&24; horas por dia proteja recursos do Serviço de Aplicativo contra malware, DDoS (negação de serviços distribuído), MITM (man-in-the-middle) e outras ameaças. 

Para saber mais sobre segurança de plataforma e infraestrutura no Azure, confira [Centro de Confiabilidade do Azure](/support/trust-center/security/).

#### <a name="application-security"></a>Segurança de aplicativo
Enquanto o Azure é responsável por proteger a infraestrutura e a plataforma em que seu aplicativo é executado, é sua responsabilidade proteger o aplicativo em si. Em outras palavras, você precisa desenvolver, implantar e gerenciar o código e o conteúdo do aplicativo de maneira segura. Caso contrário, o código ou conteúdo do aplicativo pode ficar vulnerável a ameaças como:

* Injeção de SQL
* Sequestro de sessão
* Script entre sites
* MITM no nível de aplicativo
* DDoS no nível de aplicativo

Uma discussão completa das considerações sobre segurança para aplicativos Web está além do escopo deste documento. Como ponto de partida para mais diretrizes sobre como proteger seu aplicativo, confira [OWASP (Open Web Application Security Project)](https://www.owasp.org/index.php/Main_Page), mais especificamente os [10 principais projetos.](https://www.owasp.org/index.php/Category:OWASP_Top_Ten_Project), que lista as 10 principais falhas de segurança em aplicativos Web, conforme determinado pelos membros do OWASP.

## <a name="perform-penetration-testing-on-your-app"></a>Executar testes de penetração no aplicativo
Uma das maneiras mais fáceis de começar a fazer testes de vulnerabilidades no aplicativo do Serviço de Aplicativo é usar a [integração à Tinfoil Security](https://azure.microsoft.com/blog/web-vulnerability-scanning-for-azure-app-service-powered-by-tinfoil-security/) para executar a verificação de vulnerabilidades de um clique no aplicativo. Você pode exibir os resultados do teste em um relatório de fácil compreensão e aprender como corrigir cada vulnerabilidade com instruções passo a passo.

Se preferir realizar seus próprios testes de penetração ou se desejar usar outro pacote de scanners ou provedor, você deverá seguir o [processo de aprovação do teste de penetração do Azure](https://security-forms.azure.com/penetration-testing/terms) e obter aprovação prévia para realizar os testes de penetração desejados.

## <a name="a-namehttpsa-secure-communication-with-customers"></a><a name="https"></a> Comunicação segura com os clientes
Se você usar o nome de domínio **\*.azurewebsites.net** criado para o aplicativo do Serviço de Aplicativo, será possível usar imediatamente o protocolo HTTPS, já que um certificado SSL é fornecido para todos os nomes de domínio **\*.azurewebsites.net**. Se o site usar um [nome de domínio personalizado](web-sites-custom-domain-name.md), você poderá carregar um certificado SSL a fim de [habilitar o protocolo HTTPS](web-sites-configure-ssl-certificate.md) para o domínio personalizado.

Habilitar o [HTTPS](https://en.wikipedia.org/wiki/HTTPS) pode ajudar a proteger contra ataques MITM na comunicação entre o aplicativo e seus usuários.

## <a name="secure-data-tier"></a>Camada de dados segura
O Serviço de Aplicativo integra-se favoravelmente ao Banco de Dados SQL, de modo que todas as cadeias de conexão são criptografadas em todos os sentidos e são descriptografadas apenas na VM em que o aplicativo é executado *e* apenas quando o aplicativo é executado. Além disso, o Banco de Dados SQL do Azure inclui muitos recursos de segurança que ajudam a proteger os dados do aplicativo contra ameaças cibernéticas, incluindo [criptografia em repouso](https://msdn.microsoft.com/library/dn948096.aspx), [Always Encrypted](https://msdn.microsoft.com/library/mt163865.aspx), [Máscara de Dados Dinâmicos](../sql-database/sql-database-dynamic-data-masking-get-started.md) e [Detecção de Ameaças](../sql-database/sql-database-threat-detection.md). Se você tem dados confidenciais ou requisitos de conformidade, confira [Protegendo o Banco de Dados SQL](../sql-database/sql-database-security-overview.md) para saber mais sobre como proteger seus dados.

Se você usar um provedor de banco de dados de terceiros, como ClearDB, será preciso consultar a documentação do provedor diretamente no que se refere às práticas recomendadas de segurança.  

## <a name="a-namedevelopa-secure-development-and-deployment"></a><a name="develop"></a> Desenvolvimento e implantação seguros
### <a name="publishing-profiles-and-publish-settings"></a>Publicando configurações de publicação de perfis e publicação
Durante o desenvolvimento de aplicativos, a realização de tarefas de gerenciamento ou a automação de tarefas usando utilitários como **Visual Studio**, **Web Matrix**, **Azure PowerShell** ou a **CLI do Azure (Interface de linha de comando do Azure)**, é possível usar um arquivo de *configurações de publicação* ou um *perfil de publicação*. Ambos os tipos de arquivo autenticam você no Azure e devem ser protegidos para impedir o acesso não autorizado.

* Um arquivo de **configurações de publicação** contém
  
  * ID da assinatura do Azure
  * Um certificado de gerenciamento que permite realizar tarefas de gerenciamento para a assinatura *sem precisar fornecer um nome de conta ou uma senha*.
* Um arquivo de **perfil de publicação** contém
  
  * Informações para publicar no aplicativo

Se você usar um utilitário que utiliza um arquivo de configurações de publicação ou um arquivo de perfil de publicação, importe o arquivo que contém as configurações de publicação ou o perfil no utilitário e **exclua** o arquivo. Se você precisar manter o arquivo para compartilhar com outros que trabalhem no projeto, por exemplo, armazene-o em um local seguro, como um diretório *criptografado* com permissões restritas.

Além disso, você deve se certificar de que as credenciais importadas sejam seguras. Por exemplo, o **Azure PowerShell** e a **CLI do Azure (Interface de Linha de Comando do Azure)** armazenam informações importadas no seu **diretório base** (*~* em sistemas Linux ou OS X e em */usuários/seunomedeusuário* em sistemas Windows). Para ter segurança extra, convém **criptografar** esses locais usando as ferramentas de criptografia disponíveis para o sistema operacional.

### <a name="configuration-settings-and-connection-strings"></a>Definições de configuração e cadeias de conexão
É uma prática comum armazenar cadeias de conexão, credenciais de autenticação e outras informações confidenciais em arquivos de configuração. Infelizmente, esses cookies podem ser expostos no site ou pode haver check-in deles em um repositório público, expondo essas informações. Uma pesquisa simples no [GitHub](https://github.com), por exemplo, pode descobrir incontáveis arquivos de configuração com segredos expostos nos repositórios públicos.

A prática recomendada é manter essas informações fora dos arquivos de configuração do aplicativo. O Serviço de Aplicativo permite que você armazene informações de configuração como parte do ambiente de tempo de execução, como **configurações do aplicativo** e **cadeias de conexão**. Os valores são expostos ao aplicativo durante o tempo de execução por meio de *variáveis do ambiente* na maioria das linguagens de programação. Para aplicativos do .NET, esses valores são injetados na configuração do .NET durante o tempo de execução. Fora essas situações, essas definições de configuração permanecerão criptografadas, a menos que você as exiba ou as configure usando o [Portal do Azure](https://portal.azure.com) ou utilitários como o PowerShell ou a CLI do Azure. 

Armazenar informações de configuração no Serviço de Aplicativo permite que o administrador do aplicativo bloqueie informações confidenciais dos aplicativos de produção. Os desenvolvedores podem usar um conjunto separado de definições de configuração para desenvolvimento de aplicativo e as definições podem ser automaticamente substituídas pelas definições configuradas no Serviço de Aplicativo. Nem mesmo os desenvolvedores precisam conhecer os segredos configurados do aplicativo de produção. Para obter mais informações sobre como configurar definições de aplicativo e cadeias de conexão no Serviço de Aplicativo, confira [Configurando aplicativos Web](web-sites-configure.md).

### <a name="ftps"></a>Protocolo FTPS
O Serviço de Aplicativo do Azure fornece acesso FTP seguro ao sistema de arquivos para seu aplicativo por meio de **FTPS**. Isso permite acessar com segurança o código do aplicativo no aplicativo Web, bem como os logs de diagnóstico. É recomendável usar sempre FTPS em vez de FTP. 

O link FTPS para seu aplicativo pode ser encontrado seguindo estas etapas:

1. Abra o [Portal do Azure](https://portal.azure.com).
2. Selecione **Procurar Tudo**.
3. Na folha **Procurar**, selecione **Serviços de Aplicativo**.
4. Na folha **Serviços de Aplicativo** , selecione o aplicativo desejado.
5. Na folha do aplicativo, selecione **Todas as configurações**.
6. Na folha **Configurações**, selecione **Propriedades**.
7. Os links FTP e FTPS são fornecidos na folha **Configurações** . 

Para obter mais informações sobre o protocolo FTPS, consulte [Protocolo FTPS](http://en.wikipedia.org/wiki/File_Transfer_Protocol).

## <a name="next-steps"></a>Próximas etapas
Para obter mais informações sobre a segurança da plataforma Azure, informações sobre como relatar um **incidente de segurança ou um abuso**, ou informar à Microsoft que você realizará **testes de penetração** no site, confira a seção de segurança da [Central de Confiabilidade do Microsoft Azure](https://azure.microsoft.com/support/trust-center/security/).

Para saber mais sobre os arquivos **web.config** ou **applicationhost.config** em aplicativos do Serviço de Aplicativo, confira [Configuration options unlocked in Azure App Service web apps](https://azure.microsoft.com/blog/2014/01/28/more-to-explore-configuration-options-unlocked-in-windows-azure-web-sites/) (Opções de configuração desbloqueadas em aplicativos Web do Serviço de Aplicativo do Azure).

Para saber mais sobre como registrar em log informações de aplicativos do Serviço de Aplicativo, que podem ser úteis na detecção de ataques, confira [Habilitar o registro em log de diagnóstico](web-sites-enable-diagnostic-log.md).

> [!NOTE]
> Se você deseja começar com o Serviço de Aplicativo do Azure antes de se inscrever em uma conta do Azure, acesse [Experimentar o Serviço de Aplicativo](https://azure.microsoft.com/try/app-service/), em que você pode criar imediatamente um aplicativo inicial de curta duração no Serviço de Aplicativo. Nenhum cartão de crédito é exigido, sem compromissos.
> 
> 

## <a name="whats-changed"></a>O que mudou
* Para obter um guia sobre a alteração de Sites para o Serviço de Aplicativo, consulte: [Serviço de Aplicativo do Azure e seu impacto sobre os serviços do Azure existentes](http://go.microsoft.com/fwlink/?LinkId=529714)


