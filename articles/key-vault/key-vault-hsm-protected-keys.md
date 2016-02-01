<properties
	pageTitle="Como gerar e transferir chaves protegidas por HSM para o Cofre da Chave do Azure | Microsoft Azure"
	description="Use este artigo para ajudá-lo a planejar, gerar e transferir as suas próprias chaves de HSM protegido para usar com o Cofre da Chave do Azure."
	services="key-vault"
	documentationCenter=""
	authors="cabailey"
	manager="mbaldwin"
	tags="azure-resource-manager"/>

<tags
	ms.service="key-vault"
	ms.workload="identity"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="01/19/2016"
	ms.author="cabailey"/>
#Como gerar e transferir chaves de HSM protegido para o Cofre da Chave do Azure

##Introdução

Para garantia extra, ao usar o Cofre da Chave do Azure, você pode importar ou gerar chaves em módulos de segurança de hardware (HSM) que nunca extrapolam o limite do HSM. Normalmente, este cenário é conhecido como *Trazer a sua própria chave* ou BYOK. Os HSMs têm certificação FIPS 140-2 Nível 2. O Cofre da Chave do Azure usa a família Thales nShield de HSMs para proteger as suas chaves.

Use as informações neste tópico para ajudá-lo a planejar, gerar e transferir as suas próprias chaves de HSM protegido para usar com o Cofre da Chave do Azure.

Essa funcionalidade não está disponível para o Azure China.

>[AZURE.NOTE]Para obter mais informações sobre o Cofre da Chave do Azure, consulte [O que é o Cofre da Chave do Azure?](key-vault-whatis.md)
>
>Para obter um tutorial de Introdução, que inclui a criação de um Cofre da Chave para chaves de HSM protegido, consulte [Introdução ao Cofre da Chave do Azure](key-vault-get-started.md).

Para mais informações sobre como gerar e transferir uma chave de HSM protegido pela Internet:

- Gerar a chave por meio de uma estação de trabalho offline, o que reduz a superfície de ataque.

- A chave é criptografada com uma Chave de Troca de Chaves (KEK), que permanece criptografada até que seja transferida para os HSMs do Cofre da Chave do Azure. Apenas a versão criptografada da sua chave deixa a estação de trabalho original.

- O conjunto de ferramentas define as propriedades em sua chave de locatário que associa a sua chave ao universo de segurança do Cofre da Chave do Azure. Então, após os HSMs do Cofre da Chave do Azure receberem e descriptografarem a chave, somente esses HSMs poderão usá-la. A chave não pode ser exportada. Essa associação é exigida pelos HSMs de Thales.

- A Chave de Troca de Chaves (KEK) que é usada para criptografar a sua chave é gerada dentro dos HSMs do Cofre da Chave do Azure e não é exportável. Os HSMs exigem que não possa haver nenhuma versão clara da KEK fora dos HSMs. Além disso, o conjunto de ferramentas inclui o atestado de Thales de que a KEK não é exportável e foi gerada dentro de um HSM original que foi fabricado pela Thales.

- O conjunto de ferramentas inclui atestado de Thales de que o universo de segurança do Cofre da Chave do Azure também foi gerado em um HSM original fabricado pela Thales. Isso comprova que a Microsoft está usando hardware original.

- A Microsoft usa KEKs separadas, bem como universos de segurança em cada região geográfica, o que garante que a chave possa ser usada somente em data centers na região em que você a criptografou. Por exemplo, uma chave de um cliente europeu não pode ser usada em data centers na América do Norte ou na Ásia.

##Para obter mais informações sobre serviços HSMs da Thales e da Microsoft

A Thales e-Security é um provedor líder global em criptografia de dados e soluções de segurança cibernética para os serviços financeiros, de alta tecnologia, manufatura, governo e setores de tecnologia. Com um histórico de proteção corporativa e informações do governo com 40 anos de existência, as soluções Thales são usadas por quatro das cinco maiores empresas do setor de energia e aeroespacial, 22 países da OTAN e protege mais de 80 por cento das transações de pagamento em todo o mundo.

A Microsoft tem colaborado com a Thales para aprimorar os mais recentes HSMs. Essas melhorias permitem obter os benefícios típicos dos serviços hospedados sem abrir mão do controle sobre as chaves. Especificamente, essas melhorias permitem que a Microsoft gerencie os HSMs para que você não precise fazer isso. Como um serviço de nuvem, o Cofre da Chave do Azure escala verticalmente a curto prazo para atender aos picos de uso da sua organização. Ao mesmo tempo, a chave é protegida dentro dos HSMs da Microsoft: você mantém o controle sobre o ciclo de vida da chave, pois gera a chave e a transfere para HSMs da Microsoft.

##Implementando o Traga a sua própria chave (BYOK) para o Cofre da Chave do Azure

Use as seguintes informações e procedimentos, se você for gerar a sua própria chave de HSM protegido e, em seguida, transferi-la para o Cofre da Chave do Azure — cenário de Traga a sua própria chave (BYOK).


##Pré-requisitos para BYOK

Consulte a tabela a seguir para obter uma lista de pré-requisitos para o Traga a sua própria chave (BYOK) para o Cofre da Chave do Azure.

|Requisito|Mais informações|
|---|---|
|Uma assinatura do Azure|Para criar um Cofre da Chave do Azure, você precisa de uma assinatura do Azure: [Conecte-se para fazer a avaliação gratuita](../../../../pricing/free-trial)|
|Um Cofre da Chave do Azure que dá suporte a HSMs|Para obter mais informações sobre os recursos e as camadas de serviço para o Cofre da Chave do Azure, consulte o site [Preços do Cofre da Chave do Azure](../../../../pricing/details/key-vault/).|
|HSM da Thales, smartcards e software de suporte|Você deve ter acesso a um módulo de segurança de Hardware da Thales e conhecimento operacional básico dos HSMs da Thales. Consulte [Módulo de segurança de Hardware da Thales](https://www.thales-esecurity.com/msrms/buy) para obter a lista de modelos compatíveis ou para comprar um HSM, se você não tiver um.|
|O hardware e o software a seguir:<ol><li>Uma estação de trabalho x64 offline com um sistema operacional Windows mínimo do Windows 7 e o software Thales nShield que seja, pelo menos, da versão 11.50.<br/><br/>Se esta estação de trabalho executar o Windows 7, você deverá [instalar o Microsoft .NET Framework 4.5](http://download.microsoft.com/download/b/a/4/ba4a7e71-2906-4b2d-a0e1-80cf16844f5f/dotnetfx45_full_x86_x64.exe).</li><li>Uma estação de trabalho que está conectada à Internet e tem um sistema operacional Windows mínimo do Windows 7.</li><li>Uma unidade USB ou outro dispositivo de armazenamento portátil que tenha pelo menos 16 MB de espaço livre.</li></ol>|Por motivos de segurança, é recomendável que a primeira estação de trabalho não seja conectada a uma rede. No entanto, isso não é programaticamente aplicado.<br/><br/>Observe que nas instruções a seguir, essa estação de trabalho é chamada de estação de trabalho desconectada.</p></blockquote><br/>Além disso, se a chave de locatário destina-se a uma rede de produção, recomendamos que você use uma segunda estação de trabalho separada para baixar o conjunto de ferramentas e carregar a chave de locatário. Porém, para fins de teste, você pode usar a mesma estação de trabalho que o primeiro.<br/><br/>Observe que nas instruções a seguir, essa segunda estação de trabalho é chamada de estação de trabalho conectada à Internet.</p></blockquote><br/>|

##Gerar e transferir sua chave para o HSM do Cofre da Chave do Azure

Você usará as 5 etapas a seguir para gerar e transferir a sua chave para um HSM do Cofre da Chave do Azure:

- [Etapa 1: preparar sua estação de trabalho conectada à Internet](#step-1-prepare-your-internet-connected-workstation)
- [Etapa 2: preparar sua estação de trabalho desconectada](#step-2-prepare-your-disconnected-workstation)
- [Etapa 3: Gerar a sua chave](#step-3-generate-your-key)
- [Etapa 4: Preparar a sua chave para transferência](#step-4-prepare-your-key-for-transfer)
- [Etapa 5: Transferir a sua chave para o Cofre da Chave do Azure](#step-5-transfer-your-key-to-azure-key-vault)

## Etapa 1: preparar sua estação de trabalho conectada à Internet
Nessa primeira etapa, siga os procedimentos a seguir em sua estação de trabalho que está conectada à Internet.


###Etapa 1.1: Instalar o Azure PowerShell

Na estação de trabalho conectada à Internet, baixe e instale o módulo do Azure PowerShell que inclui os cmdlets para gerenciar o Cofre da Chave do Azure. Isso requer uma versão mínima do 0.8.13.

Para obter instruções de instalação, consulte [Como instalar e configurar o Azure PowerShell](../powershell-install-configure.md).

###Etapa 1.2: Obter a sua ID da assinatura do Azure

Inicie uma sessão do Azure PowerShell e entre em sua conta do Azure usando o seguinte comando:

		Add-AzureAccount
Na janela pop-up do navegador, insira o nome de usuário e a senha da sua conta do Azure. Em seguida, use o comando [Get-AzureSubscription](https://msdn.microsoft.com/library/azure/dn790366.aspx):

		Get-AzureSubscription
Na saída, localize a ID para a assinatura que você usará para o Cofre da Chave do Azure. Você precisará dessa ID da assinatura mais tarde.

Não feche a janela do Azure PowerShell.

###Etapa 1.3: Baixe o conjunto de ferramentas BYOK para o Cofre da Chave do Azure

Vá para Centro de Download da Microsoft e [baixe o conjunto de ferramentas BYOK do Cofre da Chave do Azure](http://www.microsoft.com/download/details.aspx?id=45345) para a sua região ou instância do Azure:

|Região geográfica ou instância do Azure|Nome do pacote|Hash de pacote SHA-256|
|---|---|---|
|América do Norte|KeyVault-BYOK-Tools-UnitedStates.zip|D9FDA9F5A34E1388CD6C9138E5B75B7051FB7D6B11F087AFE0553DC85CCF0E36|
|Europa|KeyVault-BYOK-Tools-Europe.zip|881DCA798305B8408C06BAE7B3EFBC1E9EA6113A8D6EC443464F3744896F32C3|
|Ásia|KeyVault-BYOK-Tools-AsiaPacific.zip|0C76967B3AC76687E4EA47EB96174EE6B25AB24E3114E28A90D9B93A2E6ABF6E|
|América Latina|KeyVault-BYOK-Tools-LatinAmerica.zip|B38015990D4D1E522B8367FF78E78E0234BF9592663470426088C44C3CAAAF48|
|Japão|KeyVault-BYOK-Tools-Japan.zip|DB512CD9472FDE2FD610522847DF05E4D7CD49A296EE4A2DD74D43626624A113|
|Austrália|KeyVault-BYOK-Tools-Australia.zip|8EBC69E58E809A67C036B50BB4F1130411AD87A7464E0D61A9E993C797915967|
|[Azure Government](../../../../features/gov/)|KeyVault-BYOK-Tools-USGovCloud.zip|4DE9B33990099E4197ED67D786316F628E5218FC1EB0C24DCAD8A1851FD345B8|

Para validar a integridade do seu conjunto de ferramentas BYOK baixado, na sua sessão do Azure PowerShell, use o cmdlet [Get-FileHash](https://technet.microsoft.com/library/dn520872.aspx).

	Get-FileHash KeyVault-BYOK-Tools-*.zip

O conjunto de ferramentas inclui o seguinte:

- Um pacote de Chave de Troca de Chave (KEK) que tem um nome que começa com **BYOK-KEK-pkg-.**
- Um pacote de Universo de Segurança que tem um nome que começa com **BYOK-SecurityWorld-pkg-.**
- Um script python chamado v**erifykeypackage.py.**
- Um arquivo executável de linha de comando chamado **KeyTransferRemote.exe** e DLLs associadas.
- Um pacote redistribuível do Visual C++, chamado **vcredist\_x64.exe.**

Copie o pacote para uma unidade USB ou outro armazenamento portátil.

##Etapa 2: preparar sua estação de trabalho desconectada

Para essa segunda etapa, siga os procedimentos a seguir na estação de trabalho que não está conectado a uma rede (Internet ou rede interna).


###Etapa 2.1: Preparar a estação de trabalho desconectada com HSM da Thales

Instale o software de suporte nCipher (Thales) em um computador Windows e, em seguida, anexe um HSM da Thales a esse computador.

Verifique se as ferramentas Thales estão no caminho (**%nfast\_home%\\bin** e **%nfast\_home%\\python\\bin**). Por exemplo, digite o seguinte:

		set PATH=%PATH%;”%nfast_home%\bin”;”%nfast_home%\python\bin”

Para obter mais informações, consulte o guia do usuário fornecido com o HSM da Thales.

###Etapa 2.2: Instalar o conjunto de ferramentas BYOK na estação de trabalho desconectada

Copie o pacote do conjunto de ferramentas BYOK da unidade USB ou outro armazenamento portátil e, em seguida, faça o seguinte:

1. Extraia os arquivos do pacote baixado em qualquer pasta.
2. Nessa pasta, execute o vcredist\_x64.exe.
3. Siga as instruções para instalar os componentes de tempo de execução do Visual C++ para o Visual Studio 2012.

##Etapa 3: Gerar a sua chave

Para a terceira etapa, execute os seguintes procedimentos na estação de trabalho desconectada.

###Etapa 3.1: Criar um Universo de segurança

Inicie um prompt de comando e execute o programa do novo universo da Thales.

	new-world.exe --initialize --cipher-suite=DLf1024s160mRijndael --module=1 --acs-quorum=2/3

Este programa cria um arquivo **Universo de Segurança** em %NFAST\_KMDATA%\\local\\world, que corresponde à pasta C:\\ProgramData\\nCipher\\Key Management Data\\local. É possível usar valores diferentes para o quorum, mas, no nosso exemplo, você será solicitado a inserir três cartões em branco e pins para cada um deles. Em seguida, os dois cartões darão acesso completo ao universo de segurança. Esses cartões se tornam o **Conjunto de Cartões do Administrador** para o novo universo de segurança.

Faremos o seguinte:

- Faça backup do arquivo do universo. Proteja o arquivo do universo, os Cartões do Administrador, juntamente com os seus pins, e certifique-se de que ninguém tenha acesso a mais de um cartão.

###Etapa 3.2: Validar o pacote baixado

Esta etapa é opcional, mas recomendada, para que você possa validar o seguinte:

- A Chave de Troca de Chaves que está incluída no conjunto de ferramentas foi gerada em um HSM original da Thales.
- O hash do Universo de segurança está incluído no conjunto de ferramentas que foi gerado em um HSM original da Thales.
- A Chave de Troca de Chaves é não exportável.

>[AZURE.NOTE]Para validar o pacote baixado, o HSM deve estar conectado, ligado e deve ter um Universo de segurança nele (como aquele que você acabou de criar).

Para validar o pacote baixado:

1.	Execute o script verifykeypackage.py, associando um dos seguintes, dependendo da sua região geográfica ou da instância do Azure:
	- Para a América do Norte:

			python verifykeypackage.py -k BYOK-KEK-pkg-NA-1 -w BYOK-SecurityWorld-pkg-NA-1
	- Para a Europa:

			python verifykeypackage.py -k BYOK-KEK-pkg-EU-1 -w BYOK-SecurityWorld-pkg-EU-1
	- Para a Ásia:

			python verifykeypackage.py -k BYOK-KEK-pkg-AP-1 -w BYOK-SecurityWorld-pkg-AP-1
	- Para a América Latina:

			python verifykeypackage.py -k BYOK-KEK-pkg-LATAM-1 -w BYOK-SecurityWorld-pkg-LATAM-1
	- Para o Japão:

			python verifykeypackage.py -k BYOK-KEK-pkg-JPN-1 -w BYOK-SecurityWorld-pkg-JPN-1
	- Para a Austrália:

			python verifykeypackage.py -k BYOK-KEK-pkg-AUS-1 -w BYOK-SecurityWorld-pkg-AUS-1
	- Para o [Azure Government](../../../../features/gov/), que usa a instância do governo dos EUA do Azure:

			python verifykeypackage.py -k BYOK-KEK-pkg-USGOV-1 -w BYOK-SecurityWorld-pkg-USGOV-1

	>[AZURE.TIP]O software Thales inclui python em %NFAST\_HOME%\\python\\bin

2.	Confirme que você vê o seguinte, que indica a validação bem-sucedida: **Resultado: SUCCESS**

Este script valida a cadeia do signatário até a chave-raiz da Thales. O hash dessa chave raiz é inserido no script e o seu valor deve ser **59178a47 de508c3f 291277ee 184f46c4 f1d9c639**. Você também pode confirmar esse valor separadamente visitando o [site da Thales](http://www.thalesesec.com/).

Agora você está pronto para criar uma nova chave.

###Etapa 3.3: Criar uma nova chave

Gere uma chave usando o programa **generatekey** da Thales.

Execute o seguinte comando para gerar a chave:

	generatekey --generate simple type=RSA size=2048 protect=module ident=contosokey plainname=contosokey nvram=no pubexp=

Quando você executar esse comando, use estas instruções:

- Substitua o valor de *contosokey* para a **ident** e **plainname** por qualquer valor de cadeia de caracteres. Para minimizar os custos administrativos e reduzir o risco de erros, recomendamos que você use o mesmo valor para ambos. O valor de **ident** deve conter somente números, traços, letras minúsculas.

- O pubexp é deixado em branco (padrão) neste exemplo, mas você pode especificar valores específicos. Para obter mais informações, consulte a Documentação da Thales.

Este comando cria um arquivo de Chave com Token na sua pasta %NFAST\_KMDATA%\\local com um nome iniciado por **key\_simple\_** seguido pela ident que foi especificada no comando. Por exemplo: **key\_simple\_contosokey**. Esse arquivo contém uma chave criptografada.

Faça backup deste arquivo de Chave com Token em um local seguro.

>[AZURE.IMPORTANT]Posteriormente, quando transferir a sua chave para o Cofre da Chave do Azure, a Microsoft não poderá exportar esta chave novamente para você, por isso é extremamente importante fazer backup da sua chave e da segurança do Universo de segurança. Entre em contato com a Thales para obter orientação e as práticas recomendadas para fazer backup da sua chave.

Agora você está pronto para transferir a sua chave para o Cofre da Chave do Azure.

##Etapa 4: Preparar a sua chave para transferência

Para esta quarta etapa, execute os seguintes procedimentos na estação de trabalho desconectada.

###Etapa 4.1: Criar uma cópia da chave com permissões reduzidas

Para reduzir as permissões em sua chave, em um prompt de comando, execute um dos seguintes, dependendo da sua região geográfica ou da instância do Azure:

- Para a América do Norte:

		KeyTransferRemote.exe -ModifyAcls -KeyAppName simple -KeyIdentifier contosokey -ExchangeKeyPackage BYOK-KEK-pkg-NA-1 -NewSecurityWorldPackage BYOK-SecurityWorld-pkg-NA-1
- Para a Europa:

		KeyTransferRemote.exe -ModifyAcls -KeyAppName simple -KeyIdentifier contosokey -ExchangeKeyPackage BYOK-KEK-pkg-EU-1 -NewSecurityWorldPackage BYOK-SecurityWorld-pkg-EU-1
- Para a Ásia:

		KeyTransferRemote.exe -ModifyAcls -KeyAppName simple -KeyIdentifier contosokey -ExchangeKeyPackage BYOK-KEK-pkg-AP-1 -NewSecurityWorldPackage BYOK-SecurityWorld-pkg-AP-1
- Para a América Latina:

		KeyTransferRemote.exe -ModifyAcls -KeyAppName simple -KeyIdentifier contosokey -ExchangeKeyPackage BYOK-KEK-pkg-LATAM-1 -NewSecurityWorldPackage BYOK-SecurityWorld-pkg-LATAM-1
- Para o Japão:

		KeyTransferRemote.exe -ModifyAcls -KeyAppName simple -KeyIdentifier contosokey -ExchangeKeyPackage BYOK-KEK-pkg-JPN-1 -NewSecurityWorldPackage BYOK-SecurityWorld-pkg-JPN-1
- Para a Austrália:

		KeyTransferRemote.exe -ModifyAcls -KeyAppName simple -KeyIdentifier contosokey -ExchangeKeyPackage BYOK-KEK-pkg-AUS-1 -NewSecurityWorldPackage BYOK-SecurityWorld-pkg-AUS-1
- Para o [Azure Government](../../../../features/gov/), que usa a instância do governo dos EUA do Azure:

		KeyTransferRemote.exe -ModifyAcls -KeyAppName simple -KeyIdentifier contosokey -ExchangeKeyPackage BYOK-KEK-pkg-USGOV-1 -NewSecurityWorldPackage BYOK-SecurityWorld-pkg-USGOV-1

Quando você executar esse comando, substitua *contosokey* pelo mesmo valor especificado na **Etapa 3.3: Criar uma nova chave** na etapa [Gerar a sua chave](#step-3-generate-your-key).

Você será solicitado a conectar seus cartões de admin do Universo de segurança.

Quando o comando for concluído, você verá **Resultado: SUCCESS** e a cópia da sua chave com permissões reduzidas estará no arquivo chamado key\_xferacId\_<contosokey>.

###Etapa 4.2: Inspecione a nova cópia da chave

Opcionalmente, execute os utilitários da Thales para confirmar as permissões mínimas na nova chave:

- aclprint.py:

		"%nfast_home%\bin\preload.exe" -m 1 -A xferacld -K contosokey "%nfast_home%\python\bin\python" "%nfast_home%\python\examples\aclprint.py"
- kmfile-dump.exe:

		"%nfast_home%\bin\kmfile-dump.exe" "%NFAST_KMDATA%\local\key_xferacld_contosokey"
Quando você executar este comando, substitua contosokey pelo mesmo valor especificado na **Etapa 3.3: Criar uma nova chave** na etapa [Gerar a sua chave](#step-3-generate-your-key).

###Etapa 4.3: Criptografar a chave usando a Chave de Troca de Chaves da Microsoft

Execute um dos comandos a seguir, dependendo da sua região geográfica ou da instância do Azure:

- Para a América do Norte:

		KeyTransferRemote.exe -Package -KeyIdentifier contosokey -ExchangeKeyPackage BYOK-KEK-pkg-NA-1 -NewSecurityWorldPackage BYOK-SecurityWorld-pkg-NA-1 -SubscriptionId SubscriptionID -KeyFriendlyName ContosoFirstHSMkey
- Para a Europa:

		KeyTransferRemote.exe -Package -KeyIdentifier contosokey -ExchangeKeyPackage BYOK-KEK-pkg-EU-1 -NewSecurityWorldPackage BYOK-SecurityWorld-pkg-EU-1 -SubscriptionId SubscriptionID -KeyFriendlyName ContosoFirstHSMkey
- Para a Ásia:

		KeyTransferRemote.exe -Package -KeyIdentifier contosokey -ExchangeKeyPackage BYOK-KEK-pkg-AP-1 -NewSecurityWorldPackage BYOK-SecurityWorld-pkg-AP-1 -SubscriptionId SubscriptionID -KeyFriendlyName ContosoFirstHSMkey
- Para a América Latina:

		KeyTransferRemote.exe -Package -KeyIdentifier contosokey -ExchangeKeyPackage BYOK-KEK-pkg-LATAM-1 -NewSecurityWorldPackage BYOK-SecurityWorld-pkg-LATAM-1 -SubscriptionId SubscriptionID -KeyFriendlyName ContosoFirstHSMkey
- Para o Japão:

		KeyTransferRemote.exe -Package -KeyIdentifier contosokey -ExchangeKeyPackage BYOK-KEK-pkg-JPN-1 -NewSecurityWorldPackage BYOK-SecurityWorld-pkg-JPN-1 -SubscriptionId SubscriptionID -KeyFriendlyName ContosoFirstHSMkey
- Para a Austrália:

		KeyTransferRemote.exe -Package -KeyIdentifier contosokey -ExchangeKeyPackage BYOK-KEK-pkg-AUS-1 -NewSecurityWorldPackage BYOK-SecurityWorld-pkg-AUS-1 -SubscriptionId SubscriptionID -KeyFriendlyName ContosoFirstHSMkey
- Para o [Azure Government](../../../../features/gov/), que usa a instância do governo dos EUA do Azure:

		KeyTransferRemote.exe -Package -KeyIdentifier contosokey -ExchangeKeyPackage BYOK-KEK-pkg-USGOV-1 -NewSecurityWorldPackage BYOK-SecurityWorld-pkg-USGOV-1 -SubscriptionId SubscriptionID -KeyFriendlyName ContosoFirstHSMkey

Quando você executar esse comando, use estas instruções:

- Substitua *contosokey* pelo identificador usado para gerar a chave na **Etapa 3.3: Criar uma nova chave** na etapa [Gerar a sua chave](#step-3-generate-your-key).

- Substitua *SubscriptionID* pela ID da assinatura do Azure que contém o seu Cofre da Chave. Esse valor foi recuperado anteriormente, na **Etapa 1.2: Obter a sua ID da assinatura do Azure** na etapa [Preparar a sua estação de trabalho conectada à Internet](#step-1-prepare-your-internet-connected-workstation).

- Substitua *ContosoFirstHSMKey* por um rótulo que será usado para o nome do arquivo de saída.

Quando ele for concluído com êxito, será exibido **Resultado: SUCCESS** e haverá um novo arquivo na pasta atual que tem o seguinte nome: TransferPackage-*ContosoFirstHSMkey*.byok

###Etapa 4.4: Copie o pacote de transferência de chave para a estação de trabalho conectada à Internet

Use uma unidade USB ou outro armazenamento portátil para copiar o arquivo de saída da etapa anterior (KeyTransferPackage-ContosoFirstHSMkey.byok) para sua estação de trabalho conectada à Internet.

##Etapa 5: Transferir a sua chave para o Cofre da Chave do Azure

Para essa etapa final, na estação de trabalho conectada à Internet, use o cmdlet [Add-AzureKeyVaultKey](https://msdn.microsoft.com/library/azure/dn868048.aspx) para carregar o pacote de transferência de chave que você copiou da estação de trabalho desconectada para o HSM do Cofre da Chave do Azure:

	Add-AzureKeyVaultKey -VaultName 'ContosoKeyVaultHSM' -Name 'ContosoFirstHSMkey' -KeyFilePath 'c:\TransferPackage-ContosoFirstHSMkey.byok' -Destination 'HSM'

Se o upload for bem-sucedido, você verá exibidas as propriedades da chave que você acabou de adicionar.


##Próximas etapas

Agora você pode usar essa chave de HSM protegido no Cofre da Chave. Para saber mais, consulte a seção **Se você deseja usar um módulo de segurança de hardware (HSM)** no tutorial [Introdução ao cofre da chave do Azure](key-vault-get-started.md).

<!---HONumber=AcomDC_0121_2016-->