<properties 
	pageTitle="Atualizando o PhoneFactor Agent para o Servidor Azure Multi-Factor Authentication" 
	description="Este documento descreve como começar a usar o Servidor Azure MFA e como atualizar do PhoneFactor Agent mais antigo." 
	services="multi-factor-authentication" 
	documentationCenter="" 
	authors="billmath" 
	manager="terrylan" 
	editor="bryanla"/>

<tags 
	ms.service="multi-factor-authentication" 
	ms.workload="identity" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="06/02/2015" 
	ms.author="billmath"/>

# Atualizando o PhoneFactor Agent para o Servidor Azure Multi-Factor Authentication

A atualização do PhoneFactorAgent v5.x ou mais antigo para o Servidor Azure Multi-Factor Authentication exige que o PhoneFactor Agent e componentes afiliados sejam desinstalados antes que o Servidor Azure Multi-Factor Authentication e seus componentes afiliados possam ser instalados.

## Para atualizar o PhoneFactor Agent para o Servidor Azure Multi-Factor Authentication
<ol>
<li>Primeiro, faça backup do arquivo de dados do PhoneFactor. O local de instalação padrão é C:\\Program Files\\PhoneFactor\\Data\\Phonefactor.pfdata.


<li>Se o Portal do Usuário estiver instalado:</li>
<ol>
<li>Navegue até a pasta de instalação e faça backup do arquivo web.config O local de instalação padrão é C:\inetpub\wwwroot\PhoneFactor.</li>


<li>Se você adicionou temas personalizados ao portal, faça backup de sua pasta personalizada abaixo do diretório C:\inetpub\wwwroot\PhoneFactor\App_Themes.</li>


<li>Desinstale o Portal do Usuário com o PhoneFactor Agent (disponível apenas se instalado no mesmo servidor que o PhoneFactor Agent) ou com Programa e Recursos do Windows.</li></ol>




<li>Se o Serviço Web de Aplicativos Móveis estiver instalado: <ol> <li>Vá para a pasta de instalação e faça backup do arquivo web.config. O local de instalação padrão é C:\\inetpub\\wwwroot\\PhoneFactorPhoneAppWebService.</li> <li>Desinstale o Serviço Web de Aplicativos Móveis com Programas e Recursos do Windows.</li></ol>

<li>Se o SDK de serviço da Web estiver instalado, desinstale-a com o PhoneFactor Agent ou com Programa e Recursos do Windows.

<li>Desinstale o PhoneFactor Agent com Programas e Recursos do Windows.

<li>Instale o Servidor Multi-Factor Authentication. Observe que o caminho de instalação é obtido do registro da instalação anterior do PhoneFactor Agent, de modo que ele deve ser instalado no mesmo local (por exemplo, C:\\Program Files\\PhoneFactor). As novas instalações terão um padrão diferente (por exemplo, C:\\Program Files\\Servidor do Multi-Factor Authentication). O arquivo de dados deixado pelo PhoneFactor Agent anterior deve ser atualizado durante a instalação, para que seus usuários e configurações ainda estejam lá após instalar o novo Servidor do Multi-Factor Authentication.

<li>Se solicitado, ative o Servidor do Multi-Factor Authentication e certifique-se de que ele esteja atribuído ao grupo de replicação correto.

<li>Se o SDK de Serviço Web já foi instalado, instale o novo SDK de Serviço Web por meio da interface do usuário do Servidor Multi-Factor Authentication. Observe que o nome do diretório virtual padrão agora é "MultiFactorAuthWebServiceSdk" em vez de "PhoneFactorWebServiceSdk". Se quiser usar o nome anterior, você deve alterar o nome do diretório virtual durante a instalação. Caso contrário, se você permitir que a instalação use o novo nome padrão, será preciso alterar a URL em todos os aplicativos que referenciem o SDK de Serviço Web, como o Portal do Usuário e o Serviço Web de Aplicativos Móveis, para apontar para o local correto.

<li>Se o Portal do Usuário já foi instalado no Servidor do PhoneFactor Agent, instale o novo Portal de Usuário do Multi-Factor Authentication pela interface do usuário do Servidor Multi-Factor Authentication. Observe que o nome do diretório virtual padrão agora é “MultiFactorAuth” em vez de “PhoneFactor”. Se quiser usar o nome anterior, você deve alterar o nome do diretório virtual durante a instalação. Caso contrário, se você permitir que a instalação use o novo nome padrão, clique no ícone do Portal do usuário no Servidor Multi-Factor Authentication e atualize a URL do Portal do Usuário na guia Configurações.

<li>Se o Portal do Usuário e/ou o Serviço Web de Aplicativos Móveis foi instalado anteriormente em um servidor diferente do PhoneFactor Agent: <ol> <li>Vá para o local de instalação (por exemplo, C:\\Program Files\\PhoneFactor) e copie o instalador ou instaladores apropriados em outro servidor. Há instaladores de 32 bits e 64 bits para o Portal do Usuário e o Serviço Web de Aplicativos Móveis. Eles são chamados MultiFactorAuthenticationUserPortalSetupXX.msi e MultiFactorAuthenticationMobileAppWebServiceSetupXX.msi respectivamente.</li> <li>Para instalar o Portal do Usuário no servidor Web, abra um prompt de comando como administrador e execute o MultiFactorAuthenticationUserPortalSetupXX.msi. Observe que o nome do diretório virtual padrão agora é “MultiFactorAuth” em vez de “PhoneFactor”. Se quiser usar o nome anterior, você deve alterar o nome do diretório virtual durante a instalação. Caso contrário, se você permitir que a instalação use o novo nome padrão, clique no ícone do Portal do usuário no Servidor Multi-Factor Authentication e atualize a URL do Portal do Usuário na guia Configurações. Usuários existentes precisarão ser informados sobre a nova URL.</li> <li>Vá para o local de instalação do Portal do Usuário (por exemplo, C:\\inetpub\\wwwroot\\MultiFactorAuth) e edite o arquivo web.config. Copie os valores nas seções appSettings e applicationSettings do arquivo web.config original que foi salvo em backup antes da atualização para o novo arquivo web.config. Se o novo nome do diretório virtual padrão foi mantido ao instalar o SDK do Serviço Web, altere a URL na seção applicationSettings para apontar para o local correto. Se quaisquer outros padrões tiverem sido alterados no arquivo web.config anterior, aplique as mesmas alterações no novo arquivo web.config.</li> <li>Para instalar o Portal do Usuário do Serviço Web no servidor Web, abra um prompt de comando como administrador e execute o MultiFactorAuthenticationMobileAppWebServiceSetupXX.msi. Observe que o nome do diretório virtual padrão agora é "MultiFactorAuthMobileAppWebService" em vez de "PhoneFactorPhoneAppWebService". Se quiser usar o nome anterior, você deve alterar o nome do diretório virtual durante a instalação. Convém escolher um nome mais curto para facilitar para os usuários finais a digitação em seus dispositivos móveis. Caso contrário, se você permitir que a instalação use o novo nome padrão, clique no ícone do Portal do Usuário no Servidor Multi-Factor Authentication e atualize a URL do Serviço Web de Aplicativos Móveis.</li> <li>Vá para o local de instalação do Serviço Web de Aplicativos Móveis (por exemplo, C:\\inetpub\\wwwroot\\MultiFactorAuthMobileAppWebService) e edite o arquivo web.config. Copie os valores nas seções appSettings e applicationSettings do arquivo web.config original que foi salvo em backup antes da atualização para o novo arquivo web.config. Se o novo nome do diretório virtual padrão foi mantido ao instalar o SDK do Serviço Web, altere a URL na seção applicationSettings para apontar para o local correto. Se quaisquer outros padrões tiverem sido alterados no arquivo web.config anterior, aplique as mesmas alterações ao novo arquivo web.config.</li></ol>


 


 

<!---HONumber=July15_HO2-->