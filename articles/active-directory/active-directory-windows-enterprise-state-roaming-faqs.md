<properties
	pageTitle="Configurações e perguntas frequentes sobre o roaming de dados | Microsoft Azure"
	description="Responde a algumas dúvidas que os administradores de TI podem ter sobre as configurações e a sincronização de dados do aplicativo."
	services="active-directory"
    keywords="configurações do enterprise state roaming, nuvem do windows, perguntas frequentes sobre o enterprise state roaming"
	documentationCenter=""
	authors="femila"
	manager="stevenpo"
	editor="curtand"/>

<tags
	ms.service="active-directory"  
	ms.workload="identity"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="02/03/2016"
	ms.author="femila"/>

# Configurações e perguntas frequentes sobre o roaming de dados

Este tópico responde a algumas dúvidas que os administradores de TI podem ter sobre as configurações e a sincronização de dados do aplicativo.

## Quais dados são movidos?
**Configurações do Windows**: as configurações internas do PC do sistema operacional Windows. Em geral, essas são as configurações que personalizam o PC do usuário e incluem as seguintes grandes categorias:

- **Tema**: tema da área de trabalho, configurações da barra de tarefas, etc. 
- **Configurações do Internet Explorer**: guias abertas recentemente, favoritos, etc.
- **Configurações do navegador Edge**: favoritos, lista de leitura
- **Senhas**: senhas da Internet, perfis de Wi-Fi, etc. 
- **Preferências de idioma**: layouts de teclado, idioma do sistema, data e hora, etc. 
- **Facilidade de acesso**: tema de alto contraste, Narrador, Lupa, etc. 
- **Outras configurações do Windows**: configurações do prompt de comando, lista de aplicativos, etc. 

**Dados de aplicativo**: os aplicativos Universais do Windows podem gravar dados de configurações em uma pasta de "roaming" e todos os dados gravados nessa pasta serão automaticamente sincronizados. Cabe ao desenvolvedor de aplicativos individuais criar um aplicativo para tirar proveito desse recurso. Para obter mais detalhes sobre como desenvolver um aplicativo Universal do Windows que usa roaming, confira a [API de armazenamento appdata](https://msdn.microsoft.com/library/windows/apps/mt299098.aspx) e o [blog do desenvolvedor sobre roaming appdata do Windows 8](http://blogs.msdn.com/b/windowsappdev/archive/2012/07/17/roaming-your-app-data.aspx).

## Qual conta é usada para a sincronização de configurações?
No Windows 8 e no Windows 8.1, a sincronização de configurações sempre usou as contas de consumidor da Microsoft. Os usuários corporativos tinham a capacidade de conectar uma conta da Microsoft à conta de domínio do Active Directory para obter acesso à sincronização de configurações. No Windows 10, essa funcionalidade "conta da Microsoft conectada" está sendo substituída por uma estrutura de conta principal/secundária.
 
A conta principal é definida como a conta usada para fazer logon no Windows e pode ser uma conta da Microsoft, uma conta do AD do Azure (Active Directory do Azure), uma conta do Active Directory local ou uma conta local. Além da conta principal, os usuários do Windows 10 podem adicionar uma ou mais contas de nuvem secundária ao dispositivo. Essas contas secundárias geralmente são uma conta da Microsoft, uma conta do Active Directory do Azure ou outra conta, como do Gmail ou do Facebook. Essas contas secundárias fornecem acesso a serviços adicionais, como o logon único e a Windows Store, mas elas não são capazes de alimentar a sincronização de configurações.

No Windows 10, apenas a conta principal do dispositivo pode ser usada para a sincronização de configurações (confira Como atualizar a partir da sincronização de configurações da conta da Microsoft no Windows 8 para a sincronização de configurações do AD do Azure no Windows 10? para obter a exceção).

Os dados nunca são misturados entre as diferentes contas de usuário no dispositivo. Há duas regras de sincronização de configurações: as configurações do Windows sempre serão movidas com a conta principal. Os dados de aplicativo serão marcados com a conta usada para adquirir o aplicativo. Somente os aplicativos marcados com a conta principal serão sincronizados. A marcação de propriedade do aplicativo é determinada quando um aplicativo é instalado por meio da Windows Store ou quando o aplicativo for carregado por meio do gerenciamento de dispositivos móveis (MDM).

Se o proprietário de um aplicativo não puder ser identificado, o aplicativo será movido com a conta principal. Se um dispositivo for atualizado a partir do Windows 8 ou do Windows 8.1 para o Windows 10, todos os aplicativos serão marcados como adquiridos pela conta da Microsoft porque, em geral, a maioria dos aplicativos foi adquirida por meio da Windows Store e não havia qualquer suporte da Windows Store para contas do AD do Azure antes do Windows 10. Se um aplicativo for instalado por meio de uma licença offline, o aplicativo será marcado usando a conta principal no dispositivo.

>[AZURE.NOTE]  
A capacidade de conectar uma Conta da Microsoft a uma conta de domínio e de sincronizar todos os dados do usuário para a Conta da Microsoft (ou seja, o roaming da Conta da Microsoft por meio da funcionalidade "Conta da Microsoft conectada e Active Directory") foi removida dos dispositivos Windows 10 ingressados em um ambiente do Active Directory/AD do Azure conectado.
 
## Como eu atualizo a partir da sincronização das configurações da conta da Microsoft no Windows 8 para a sincronização das configurações do AD do Azure no Windows 10?
Um usuário que tiver ingressado no domínio do Active Directory executando o Windows 8 ou o Windows 8.1 com uma conta da Microsoft conectada sincronizará as configurações por meio da conta da Microsoft do usuário. Após a atualização para o Windows 10, os usuários ingressados no domínio continuarão a sincronizar as configurações de usuário por meio da conta da Microsoft, desde que o domínio do Active Directory não se conecte ao AD do Azure. Se o domínio do Active Directory local se conectar ao AD do Azure, o dispositivo do usuário começará a tentar sincronizar configurações usando a conta conectada do AD do Azure. Se o administrador do AD do Azure não habilitar o Enterprise State Roaming, os usuários com uma conta conectada do AD do Azure interromperão a sincronização de configurações. Após habilitar a sincronização de configurações por meio do AD do Azure, os usuários do Windows 10 começarão imediatamente a sincronizar as configurações do Windows usando o AD do Azure se o usuário entrar com uma identidade do AD do Azure.

Os usuários que armazenaram dados pessoais no dispositivo corporativo devem estar cientes de que o sistema operacional Windows e os dados do aplicativo começarão a ser sincronizados para o AD do Azure. Isso tem as seguintes implicações: - os usuários da conta pessoal da Microsoft terão suas configurações lentamente “separadas” das configurações nas contas organizacionais do AD do Azure, já que a conta da Microsoft e a sincronização de configurações do AD do Azure estão usando contas separadas. - os dados pessoais, como as senhas de Wi-Fi, as credenciais da Web e os favoritos e as guias do Internet Explorer anteriormente sincronizados por meio de uma conta da Microsoft conectada serão sincronizados por meio do AD do Azure.


## Como funciona a conta da Microsoft e a interoperabilidade do Enterprise State Roaming do AD do Azure? 
Nas versões de 2015 do Windows 10, o Enterprise State Roaming só tem suporte para uma única conta de cada vez. Se o usuário entrar no Windows usando a conta organizacional do AD do Azure, todos os dados serão sincronizados por meio do AD do Azure. Se o usuário entrar no Windows usando uma conta pessoal da Microsoft, todos os dados serão sincronizados por meio da conta da Microsoft. Os dados de aplicativo serão movidos usando a conta de entrada principal no dispositivo caso a licença do aplicativo seja de propriedade da conta principal. Os aplicativos que pertencerem a qualquer conta secundária não terão os dados sincronizados no Windows 10.

## As configurações das contas do AD do Azure de vários locatários são sincronizadas?
Quando houver várias contas do AD do Azure de diferentes locatários do AD do Azure no mesmo dispositivo, você deverá atualizar o registro do dispositivo para se comunicar com o serviço Azure Rights Management (RMS) para cada locatário do AD do Azure.

1. Primeiro, é necessário o GUID de cada locatário do AD do Azure. Abra o portal clássico do Azure e escolha um locatário do AD do Azure. O GUID do locatário é a URL na barra de endereços do navegador, como a seguir: `https://manage.windowsazure.com/YourAccount.onmicrosoft.com#Workspaces/ActiveDirectoryExtension/Directory/Tenant GUID/directoryQuickStart`
2. Depois que você tiver o GUID, deverá adicionar a seguinte chave do registro: **HKEY\_LOCAL\_MACHINE\\SOFTWARE\\Microsoft\\Windows\\SettingSync\\WinMSIPC<tenant ID GUID>** Na chave <**tenant ID GUID**>, crie um novo Valor de Cadeia de Caracteres Múltipla (REG-MULTI-SZ) chamado **AllowedRMSServerUrl**s e, para seus dados, especifique as URLs de ponto de distribuição de licenciamento dos outros locatários do Azure que o dispositivo acessa. 
3. Você pode encontrar as URLs de ponto de distribuição de licenciamento ao executar o cmdlet **Get-AadrmConfiguration**. Se os valores de **LicensingIntranetDistributionPointUrl ** e de **LicenseingExtranetDistributionPointUrl** forem diferentes, especifique ambos valores. Se os valores forem iguais, especifique o valor apenas uma vez.


## Quais são as opções para as configurações de roaming para aplicativos Clássicos do Windows?
O roaming só funciona para aplicativos Universais do Windows. Há duas opções disponíveis para habilitar o roaming em um aplicativo Clássico do Windows:

- Usando o Project Centennial, você pode facilmente converter um aplicativo Clássico do Windows em um aplicativo Universal do Windows. A partir daqui, alterações mínimas de código serão solicitadas pelo desenvolvedor do aplicativo para poder tirar proveito do roaming de dados de aplicativo do AD do Azure. Este é o caminho recomendado para habilitar o roaming de dados de aplicativo do Win32. 
- Usando o gerador de modelos [User Experience Virtualization (UE-V)](https://technet.microsoft.com/library/dn458947.aspx), você poderá criar um modelo de configurações personalizadas e aplicá-lo ao seu aplicativo sempre que ele for iniciado. Esta opção não exige que o desenvolvedor do aplicativo altere o código do aplicativo mas, nas versões de 2015 do Windows 10, a UE-V está limitada ao roaming do Active Directory local para os clientes que adquiriram o Microsoft Desktop Optimization Package. No futuro, a Microsoft poderá investigar maneiras de tornar a UE-V ainda mais integrada ao Windows e de estender a UE-V para mover as configurações por meio da nuvem do AD do Azure. 

## Posso armazenar configurações e dados sincronizados no local?
O Enterprise State Roaming foi projetado para armazenar dados de configurações na nuvem do Azure. A UE-V oferece uma solução de roaming local para o Windows 10.

## Quem gerencia os dados que estão sendo movidos?
O administrador de locatários gerenciará os dados e eles serão armazenados em um datacenter do Azure. Todos os dados de usuário são criptografados em trânsito e em repouso na nuvem usando o Azure Rights Management (Azure RMS). Isso é uma melhoria em comparação à sincronização de configurações baseada em conta da Microsoft, onde apenas determinados dados confidenciais, como credenciais de usuários, são criptografados antes de saírem do dispositivo.

A Microsoft está comprometida em proteger os dados do cliente. Os dados de configurações do usuário de uma empresa são automaticamente criptografados pelo Azure RMS sempre que deixam um dispositivo Windows 10 para que outro usuário não possa ler esses dados. Se sua organização tiver uma assinatura paga do Azure RMS, você poderá usar outros recursos do Azure RMS, como rastrear e revogar documentos automaticamente, proteger emails com informações confidenciais e gerenciar suas próprias chaves (a solução "traga sua própria chave", também conhecida como BYOK). Para saber mais sobre esses recursos e sobre o funcionamento do Azure RMS, confira [O que é o Azure Rights Management](https://technet.microsoft.com/jj585026.aspx)?

## Posso gerenciar a sincronização para um aplicativo ou uma configuração específica?
No Windows 10, não há configurações do MDM ou da Política de Grupo para desabilitar o roaming de um aplicativo individual. Os administradores de locatários podem desabilitar a sincronização de dados de aplicativos para todos os aplicativos em um dispositivo gerenciado, mas não há controles mais refinados por aplicativo ou ao nível do aplicativo.

## O que um usuário individual pode fazer para habilitar/desabilitar o roaming?
No aplicativo Configurações, vá para Contas -> Sincronizar suas configurações. Nesta página, você pode ver qual conta está sendo usada para mover configurações e pode habilitar ou desabilitar grupos individuais de configurações a serem movidos.
 
##Qual é a recomendação atual da Microsoft para habilitar o roaming no Windows 10? 
A Microsoft tem algumas soluções de roaming de configurações diferentes disponíveis, incluindo Perfis de Usuário de Roaming, UE-V e Roaming do AD do Azure. Se sua organização não estiver pronta ou confortável para movimentar dados para a nuvem, a Microsoft recomenda que você use a UE-V como sua principal tecnologia de roaming. Se sua organização exigir suporte de roaming para aplicativos Clássicos do Windows, mas estiver ansiosa para mudar para a nuvem, a Microsoft recomenda que você use a sincronização de configurações da empresa e a UE-V. Embora a UE-V e a sincronização de configurações da empresa sejam tecnologias muito semelhantes, não são mutuamente exclusivas e hoje complementam uma à outra para garantir que sua organização ofereça os serviços de roaming que seus usuários necessitam. Ao usar a sincronização de configurações da empresa e a UE-V, serão aplicadas as seguintes regras:

- O Enterprise State Roaming é o principal agente de roaming no dispositivo. A UE-V está sendo usada para suplementar a "lacuna do Win32". 
- O roaming da UE-V para as configurações do Windows e os dados de aplicativo modernos UWP devem ser desabilitados porque já são cobertos pelo Enterprise State Roaming. 

## O que acontece quando minha organização adquire o Azure RMS depois de usar o roaming por um tempo? 
Se sua organização já estiver usando o roaming no Windows 10 com a assinatura gratuita do Azure RMS, comprar uma assinatura paga do Azure RMS não terá qualquer impacto na funcionalidade do recurso de roaming e seu administrador de TI não precisará fazer qualquer alteração de configuração.

## Problemas conhecidos

- O logon por cartão inteligente ou por cartão inteligente virtual no Windows faz com que a sincronização de configurações pare de funcionar. Se você tentar fazer logon em seu dispositivo usando um cartão inteligente ou um cartão inteligente Virtual, a sincronização vai parar de funcionar. Atualizações futuras do Windows 10 poderão resolver esse problema.
- Há um problema com a sincronização de **Favoritos** no Internet Explorer. A sincronização de favoritos do IE está temporariamente desabilitada para impedir o surgimento de outros problemas. Atualizações futuras do Windows 10 resolverão esse problema.


## Tópicos relacionados
- [Visão geral do enterprise state roaming](active-directory-windows-enterprise-state-roaming-overview.md)
- [Habilitar o enterprise state roaming no Active Directory do Azure](active-directory-windows-enterprise-state-roaming-enable.md)
- [Política de grupo e as configurações do MDM para a sincronização de configurações](active-directory-windows-enterprise-state-roaming-group-policy-settings.md)
- [Referência de configurações de roaming do Windows 10](active-directory-windows-enterprise-state-roaming-windows-settings-reference.md)

<!---HONumber=AcomDC_0204_2016-->