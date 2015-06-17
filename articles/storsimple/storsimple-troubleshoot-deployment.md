<properties 
   pageTitle="Solucionar problemas de implantação do dispositivo StorSimple"
   description="Descreve como diagnosticar e corrigir os erros que ocorrem quando você implanta o StorSimple pela primeira vez."
   services="storsimple"
   documentationCenter="NA"
   authors="SharS"
   manager="adinah"
   editor="tysonn" /> 
<tags 
   ms.service="storsimple"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="TBD"
   ms.date="04/06/2015"
   ms.author="v-sharos" />

# Solucionar problemas de implantação do dispositivo StorSimple

## Visão geral

Este artigo oferece orientações úteis de solução de problemas para sua implantação do Microsoft Azure StorSimple. Descreve problemas comuns, possíveis causas e etapas recomendadas para ajudar você a resolver problemas que podem ocorrer durante a execução do StorSimple. Essas informações se aplicam ao dispositivo físico local StorSimple e ao dispositivo virtual StorSimple.

> [AZURE.NOTE]Problemas relacionados com a configuração de dispositivo que você pode enfrentar podem ocorrer na primeira implantação do dispositivo ou podem ocorrer mais tarde, quando o dispositivo estiver funcionando. Este artigo se concentra na solução de problemas da primeira implantação. Para solucionar problemas de um dispositivo operacional, vá para [Solucionar problemas de dispositivo operacional](storsimple-troubleshoot-operational-device.md).

Este artigo também descreve as ferramentas para solucionar problemas de implantações do StorSimple e fornece um exemplo passo a passo de solução de problemas.

## Problemas da primeira implantação

Se você tiver um problema ao implantar seu dispositivo pela primeira vez, considere o seguinte:

- Se você estiver solucionando problemas de um dispositivo físico, verifique se o hardware foi instalado e configurado como descrito em [Instalação de hardware do dispositivo](https://msdn.microsoft.com/library/azure/dn772375.aspx).
- Verifique os pré-requisitos da implantação. Verifique se você tem todas as informações descritas na [lista de verificação de implantação](lista de verificação storsimple-implantação-passo-a-passo.md#pré-instalação).
- Examine as notas de versão do StorSimple para ver se o problema é descrito. As notas de versão incluem soluções alternativas para problemas de instalação conhecidos. 

Durante a implantação do dispositivo, os problemas mais comuns enfrentados pelos usuários ocorrem quando eles executam o assistente de instalação e quando registram o dispositivo via Windows PowerShell para StorSimple. (Você usa o Windows PowerShell para StorSimple para registrar e configurar seu dispositivo StorSimple. Para obter mais informações sobre o registro do dispositivo, consulte [Registrar seu dispositivo](https://msdn.microsoft.com/library/azure/dn757742.aspx)).

As seções a seguir podem ajudar você a resolver problemas encontrados ao configurar o dispositivo StorSimple pela primeira vez.

## Processo do assistente de instalação inicial

As etapas a seguir resumem o processo do assistente de instalação. Para obter informações detalhadas sobre instalação, consulte o [Passo a passo da implantação do StorSimple](storsimple-deployment-walkthrough.md).

1. Execute o cmdlet [Invoke-HcsSetupWizard](https://technet.microsoft.com/library/dn688135.aspx) para iniciar o assistente de instalação, que orientará você pelo resto das etapas. 
2. Configure a rede: o assistente de instalação permite definir configurações de rede para a interface de rede DADOS 0 em seu dispositivo StorSimple. Essas configurações incluem o seguinte:
  - IP Virtual (VIP), máscara de sub-rede e gateway – o cmdlet [Set-HcsNetInterface](https://technet.microsoft.com/library/dn688161.aspx) é executado em segundo plano. Ele configura o endereço IP, a máscara de sub-rede e o gateway para a interface de rede DADOS 0 em seu dispositivo StorSimple.
  - Servidor DNS primário – o cmdlet [Set-HcsDnsClientServerAddress](https://technet.microsoft.com/library/dn688172.aspx) é executado em segundo plano. Ele define as configurações de DNS para sua solução StorSimple.
  - Servidor NTP – o cmdlet [Set-HcsNtpClientServerAddress](https://technet.microsoft.com/library/dn688138.aspx) é executado em segundo plano. Ele define as configurações do servidor NTP para sua solução StorSimple.
  - Proxy da Web opcional – o cmdlet [Set-HcsWebProxy](https://technet.microsoft.com/library/dn688154.aspx) é executado em segundo plano. Ele define e habilita a configuração do proxy da Web para sua solução StorSimple.
3. Configure as senhas: a próxima etapa será configurar o administrador do dispositivo e as senhas do Gerenciador de Instantâneos StorSimple.
  - A senha do administrador do dispositivo é usada para fazer logon no seu dispositivo. A senha do dispositivo padrão é *Senha1*.
  - A senha do Gerenciador de Instantâneos StorSimple é necessária quando você configura um dispositivo para usar o Gerenciador de Instantâneos StorSimple. Primeiro você precisa definir a senha no assistente de instalação e então pode defini-la e alterá-la do serviço Gerenciador do StorSimple. Essa senha autentica o dispositivo no Gerenciador de Instantâneos StorSimple.
 
    > [AZURE.IMPORTANT]As senhas são coletadas antes do registro, mas aplicadas somente depois que você registra com êxito o dispositivo. Se houver uma falha ao aplicar uma senha, você deverá fornecer a senha novamente até que as senhas necessárias (que atendem aos requisitos de complexidade) sejam coletadas.

4. Registre o dispositivo: a etapa final é registrar o dispositivo no serviço Gerenciador do StorSimple em execução no Microsoft Azure. O registro exige que você [obtenha a chave de registro](https://msdn.microsoft.com/library/azure/cd4dee49-6ae8-4ff0-b79b-74b2027cb694#sec03) do Portal de Gerenciamento do Azure e a forneça no assistente de instalação. Depois que o dispositivo for registrado com êxito, uma chave de criptografia de dados de serviço será fornecida para você. Mantenha essa chave de criptografia em um local seguro, pois ela será necessária para registrar todos os dispositivos subsequentes no serviço.

## Erros comuns durante a implantação de dispositivo

As tabelas a seguir listam os erros comuns que podem ser encontrados quando você:

- Define as configurações de rede necessárias.
- Defina as configurações de proxy da Web opcional.
- Configure o administrador do dispositivo e as senhas do Gerenciador de Instantâneos StorSimple. 
- Registre o dispositivo. 

### Erros que ocorrem quando você define as configurações de rede necessárias

| Nº| Mensagem de erro | Possíveis causas | Ação recomendada |
| ---| ------------- | --------------- | ------------------ |
| 1 | Invoke-HcsSetupWizard: esse comando só pode ser executado no controlador ativo. | A configuração estava sendo executada no controlador passivo.| Execute esse comando do controlador ativo. Para saber mais, consulte [Identificar um controlador ativo em seu dispositivo](https://msdn.microsoft.com/library/azure/dn790262.aspx).|
| 2 | Invoke-HcsSetupWizard: o dispositivo não está pronto. | Há problemas com a conectividade de rede em DADOS 0.| Verifique a conectividade de rede física em DADOS 0.|
| 3 | Invoke-HcsSetupWizard: há um conflito de um endereço IP com outro sistema na rede (exceção de HRESULT: 0x80070263). | O IP fornecido para DADOS 0 já estava sendo usado por outro sistema. | Forneça um novo IP que não esteja em uso.|
| 4 | Invoke-HcsSetupWizard: falha em um recurso de cluster. (Exceção de HRESULT:0x800713AE). | Duplique o VIP. O IP fornecido já está em uso.| Forneça um novo IP que não esteja em uso.|
| 5 | Invoke-HcsSetupWizard: endereço IPv4 inválido. | O endereço IP foi fornecido em um formato incorreto.| Verifique o formato e forneça seu endereço IP novamente. Para saber mais, consulte [Endereçamento Ipv4][1]. |
| 6 | Invoke-HcsSetupWizard: endereço IPv6 inválido. | O endereço IP foi fornecido em um formato incorreto.| Verifique o formato e forneça seu endereço IP novamente. Para saber mais, consulte [Endereçamento Ipv6][2].|
| 7 | Invoke-HcsSetupWizard: há mais pontos de extremidade disponíveis no mapeador de pontos de extremidade (exceção de HRESULT: 0x800706D9) | A funcionalidade de cluster não está funcionando. | [Contate o Suporte da Microsoft](https://msdn.microsoft.com/library/azure/dn757750.aspx) para as próximas etapas.

### Erros que ocorrem quando você define as configurações opcionais do proxy da Web

| Nº| Mensagem de erro | Possíveis causas | Ação recomendada |
| ---| ------------- | --------------- | ------------------ |
| 1 | Invoke-HcsSetupWizard: parâmetro inválido (exceção de HRESULT: 0x80070057) | Um dos parâmetros fornecidos para as configurações do proxy não é válido.| O URI não foi fornecido no formato correto. Use o seguinte formato: http://*<IP address or FQDN of the web proxy server>*:*<TCP port number>* |
| 2 | Invoke-HcsSetupWizard: servidor RPC não disponível (exceção de HRESULT: 0x800706ba) | A causa raiz é uma das seguintes:<ol><li>o cluster não está ativo.</li><li>O controlador passivo não consegue se comunicar com o controlador ativo e o comando é executado no controlador passivo.</li></ol> | Dependendo da causa raiz:<ol><li>[contate o Suporte da Microsoft](https://msdn.microsoft.com/library/azure/dn757750.aspx) para certificar-se de que o cluster esteja ativo</li><li>Execute o comando do controlador ativo. Se você quiser executar o comando do controlador passivo, você precisará garantir que o controlador passivo pode se comunicar com o controlador ativo. Você precisará [contatar o Suporte do Microsoft](https://msdn.microsoft.com/library/azure/dn757750.aspx) se essa conectividade for interrompida.</li></ol> |
| 3 | Invoke-HcsSetupWizard: falha na chamada RPC (exceção de HRESULT: 0x800706be) | O cluster está inoperante. | [Contate o Suporte da Microsoft](https://msdn.microsoft.com/library/azure/dn757750.aspx) para certificar-se de que o cluster esteja ativo.|
| 4 | Invoke-HcsSetupWizard: recurso de cluster não encontrado (exceção de HRESULT: 0x8007138f) | O recurso de cluster não foi encontrado. Isso poderá acontecer quando a instalação não tiver sido correta. | Talvez seja necessário redefinir o dispositivo para as configurações padrão de fábrica. [Contate o Suporte da Microsoft](https://msdn.microsoft.com/library/azure/dn757750.aspx) para criar um recurso de cluster.|
| 5 | Invoke-HcsSetupWizard: Cluster recurso não online (exceção de HRESULT: 0x8007138c)| Os recursos de cluster não estão online. | [Contate o Suporte da Microsoft](https://msdn.microsoft.com/library/azure/dn757750.aspx) para as próximas etapas.|

### Erros que ocorrem quando você configura as senhas do administrador do dispositivo e do Gerenciador de Instantâneos StorSimple.

A senha do administrador do dispositivo padrão é *Senha1*. Essa senha expira após o primeiro logon; portanto, você precisará usar o assistente de instalação para alterá-la. Você deve fornecer uma nova senha do administrador do dispositivo ao registrar o dispositivo pela primeira vez.

Se você usar o software Gerenciador de Instantâneos StorSimple em execução no host do Windows Server para gerenciar o dispositivo, também deverá fornecer uma senha do Gerenciador de Instantâneos StorSimple durante o registro inicial.

Verifique se suas senhas atendem aos seguintes requisitos:

- Sua senha do administrador do dispositivo deve ter entre 8 e 15 caracteres.
- Sua senha do Gerenciador de Instantâneos StorSimple deve ter 14 ou 15 caracteres.
- As senhas precisam conter 3 destes 4 tipos de caracteres: minúsculos, maiúsculos, numéricos e especiais. 
- Sua senha não pode ser igual às 24 últimas senhas.

Além disso, tenha em mente que as senhas expiram a cada ano e só poderão ser alteradas depois que você registrar com êxito o dispositivo. Se o registro falhar por algum motivo, as senhas não serão alteradas. Para obter mais informações sobre senhas do administrador do dispositivo e do Gerenciador de Instantâneos StorSimple, vá até [Senhas](https://msdn.microsoft.com/library/azure/00fb2b46-e29e-4c4d-90a8-1dbb7db3672e#sec_02).

Você pode encontrar um ou mais dos seguintes erros ao configurar as senhas do administrador do dispositivo e do Gerenciador de Instantâneos StorSimple.

| Nº| Mensagem de erro | Ação recomendada |
| ---| ------------- | ------------------ | 
| 1 | A senha excede o comprimento máximo. |Use uma senha que atenda a estes requisitos:<ul><li>sua senha do administrador do dispositivo deve ter entre 8 e 15 caracteres.</li><li>Sua senha do Gerenciador de Instantâneos StorSimple deve ter 14 ou 15 caracteres.</li></ul> | 
| 2 | A senha não atende ao comprimento necessário. | Use uma senha que atenda a estes requisitos:<ul><li>sua senha do administrador do dispositivo deve ter entre 8 e 15 caracteres.</li><li>Sua senha do Gerenciador de Instantâneos StorSimple deve ter 14 ou 15 caracteres.</lu></ul> |
| 3 | A senha deve conter caracteres minúsculos. | As senhas devem conter 3 destes 4 tipos de caracteres: minúsculos, maiúsculos, numéricos e especiais. Certifique-se de que sua senha atende a esses requisitos. |
| 4 | A senha deve conter caracteres numéricos. | As senhas devem conter 3 destes 4 tipos de caracteres: minúsculos, maiúsculos, numéricos e especiais. Certifique-se de que sua senha atende a esses requisitos. |
| 5 | A senha deve conter caracteres especiais. | As senhas devem conter 3 destes 4 tipos de caracteres: minúsculos, maiúsculos, numéricos e especiais. Certifique-se de que sua senha atende a esses requisitos. |
| 6 | As senhas devem conter 3 destes 4 tipos de caracteres: maiúsculos, minúsculos, numéricos e especiais. | Sua senha não contém os tipos de caracteres necessários. Certifique-se de que sua senha atende a esses requisitos. |
| 7 | O parâmetro não corresponde à confirmação. | Certifique-se de que sua senha atende a todos os requisitos e de que ela tenha sido inserida corretamente. |
| 8 | Sua senha não pode corresponder ao padrão. | A senha padrão é *Senha1*. Será necessário alterar essa senha depois de fazer logon pela primeira vez. |
| 9 | A senha que você inseriu não corresponde à senha do dispositivo. Redigite a senha. | Verifique a senha e digite-a novamente. |

As senhas são coletadas antes do registro do dispositivo, mas são aplicadas somente após o registro com êxito. O fluxo de trabalho de recuperação de senha requer que o dispositivo esteja registrado.

> [AZURE.IMPORTANT]Em geral, se uma tentativa de aplicar uma senha falhar, o software tentará coletar repetidamente a senha até obter êxito. Em casos raros, a senha não pode ser aplicada. Nessa situação, você pode registrar o dispositivo e continuar, mas as senhas não serão alteradas. Você não receberá nenhuma indicação de que a senha não foi alterada — a senha do administrador do dispositivo ou a senha do Gerenciador de Instantâneos StorSimple. Se essa situação ocorrer, recomendamos que você altere as duas senhas.

Você pode redefinir as senhas no Portal de Gerenciamento do serviço Gerenciador do StorSimple. Para obter mais informações, consulte:

- [Configurar a senha do administrador do dispositivo](https://msdn.microsoft.com/library/azure/02f1412f-e196-4a88-8eda-2113247ea47c#sec09)
- [Configurar a senha do Gerenciador de Instantâneos StorSimple](https://msdn.microsoft.com/library/azure/02f1412f-e196-4a88-8eda-2113247ea47c#sec08)

### Erros que ocorrem quando você registra o dispositivo

Use o serviço Gerenciador do StorSimple em execução no Microsoft Azure para registrar o dispositivo. Você pode encontrar um ou mais dos problemas a seguir durante o registro do dispositivo.

| Nº| Mensagem de erro | Possíveis causas | Ação recomendada |
| ---| ------------- | --------------- | ------------------ |
| 1 | Erro 350027: falha ao registrar o dispositivo no Gerenciador do StorSimple. | | Aguarde alguns minutos e repita a operação. Se o problema persistir, [contate o Suporte da Microsoft](https://msdn.microsoft.com/library/azure/dn757750.aspx).|
| 2 | Erro 350013: ocorreu um erro no registro do dispositivo. Isso pode ser causado por uma chave de registro do serviço incorreta. | | Registre o dispositivo novamente com a chave de registro do serviço correta. Para obter mais informações, consulte [Obter a chave de registro do serviço.](https://msdn.microsoft.com/library/azure/cd4dee49-6ae8-4ff0-b79b-74b2027cb694#sec03) |
| 3 | Erro 350063: a autenticação no serviço Gerenciador do StorSimple passou, mas o registro falhou. Repita a operação após algum tempo. | Esse erro indica que a autenticação no ACS passou, mas a chamada de registro feita ao serviço falhou. Isso pode ser resultado de uma falha de rede esporádica. | Se o problema persistir, [contate o Suporte da Microsoft](https://msdn.microsoft.com/library/azure/dn757750.aspx). |
| 4 | Erro 350049: o serviço não pôde ser acessado durante o registro. | Quando é feita a chamada ao serviço, uma exceção de Web é recebida. Em alguns casos, isso pode ser corrigido repetindo a operação mais tarde. | Verifique o endereço IP e o nome DNS e repita a operação. Se o problema persistir, [contate o Suporte da Microsoft](https://msdn.microsoft.com/library/azure/dn757750.aspx). | 
| 5 | Erro 350031: o dispositivo já foi registrado. | | Nenhuma ação é necessária. |
| 6 | Erro 350016: falha no registro do dispositivo. | |Verifique se a chave de registro está correta. |
| 7 | Invoke-HcsSetupWizard: ocorreu um erro ao registrar seu dispositivo; isso pode ter acontecido devido a endereço IP ou nome DNS incorreto. Verifique suas configurações de rede e tente novamente. Se o problema persistir, [contate o Suporte da Microsoft](https://msdn.microsoft.com/library/azure/dn757750.aspx).(Erro 350050) | Verifique se o dispositivo pode executar ping na rede externa. Se você não tiver conectividade com a rede externa, o registro poderá falhar com esse erro. Esse erro pode ser uma combinação de um ou mais dos seguintes:<ul><li>IP incorreto</li><li>Sub-rede incorreta</li><li>Gateway incorreto</li><li>Configurações de DNS incorretas</li></ul> | Consulte as etapas no [Exemplo passo a passo de solução de problemas](#step-by-step-storsimple-troubleshooting-example). |
| 8 | Invoke-HcsSetupWizard: a operação atual falhou devido a um erro de serviço interno [0x1FBE2]. Repita a operação após algum tempo. Se o problema persistir, contate o Suporte da Microsoft. | Esse é um erro genérico lançado para todos os erros de usuário invisíveis do serviço ou agente. O motivo mais comum pode a falha na autenticação do ACS. Uma possível causa da falha é que há problemas com a configuração do servidor NTP e hora do dispositivo não está definida corretamente. | Corrija a hora (se houver problemas) e, em seguida, repita a operação de registro. Se o problema persistir, [contate o Suporte da Microsoft](https://msdn.microsoft.com/library/azure/dn757750.aspx) para as próximas etapas. |
| 9 | Aviso: não foi possível ativar o dispositivo. As senhas do administrador do dispositivo e do Gerenciador de Instantâneos StorSimple não foram alteradas. | Se o registro falhar, as senhas do administrador do dispositivo e do Gerenciador de Instantâneos StorSimple não serão alteradas. |

## Ferramentas para solucionar problemas em implantações do StorSimple

O StorSimple inclui várias ferramentas que você pode usar para solucionar problemas de sua solução StorSimple. Estão incluídos:

- Pacotes de suporte e logs de dispositivo 
- Cmdlets projetados especificamente para a solução de problemas 

## Pacotes de suporte e logs de dispositivo disponíveis para solução de problemas

Um pacote de suporte contém todos os logs relevantes que podem ajudar a equipe do Suporte da Microsoft na solução de problemas do dispositivo. Você pode usar o Windows PowerShell para StorSimple para gerar um pacote de suporte criptografado que pode ser compartilhado com o pessoal de suporte.

### Para exibir os logs ou o conteúdo do pacote de suporte

1. Use o Windows PowerShell para StorSimple para gerar um pacote de suporte, como descrito em [Gerar um pacote de suporte](https://msdn.microsoft.com/library/azure/dn772348.aspx).

2. Baixe o [script de descriptografia](https://gallery.technet.microsoft.com/scriptcenter/Script-to-decrypt-a-a8d1ed65) localmente no computador cliente.

3. Use este [procedimento passo a passo](https://msdn.microsoft.com/library/azure/dn762117.aspx) para abrir e descriptografar o pacote de suporte.

4. Os logs do pacote de suporte descriptografado estão no formato etw/etvx. Você pode executar as etapas a seguir para exibir esses arquivos no Visualizador de Eventos do Windows:
  1. Execute o comando **eventvwr** no cliente Windows. Isso iniciará o Visualizador de Eventos.
  2. No painel **Ações**, clique em **Abrir Log Salvo** e aponte para os arquivos de log no formato de etvx/etw (o pacote de suporte). Agora você pode exibir o arquivo. Depois de abrir o arquivo, você poderá e clicar com o botão direito do mouse e salvar o arquivo como texto.
   
    > [AZURE.IMPORTANT]Você também pode usar o cmdlet **Get-WinEvent** para abrir esses arquivos no Windows PowerShell. Para obter mais informações, consulte [Get-WinEvent](https://technet.microsoft.com/library/hh849682.aspx) na documentação de referência do cmdlet do Windows PowerShell.

5. Ao abrir os logs no Visualizador de Eventos, procure os seguintes logs que contêm problemas relacionados à configuração do dispositivo:

  - hcs_pfconfig/Operational Log
  - hcs_pfconfig/Config

6. Nos arquivos de log, procure cadeias de caracteres relacionadas aos cmdlets chamados pelo assistente de instalação. Consulte [Processo do assistente de instalação inicial](#first-time-setup-wizard-process) para obter uma lista desses cmdlets. 

7. Se você não conseguir descobrir a causa do problema, poderá [contatar o Suporte da Microsoft](https://msdn.microsoft.com/library/azure/dn757750.aspx) para as próximas etapas. Use as etapas em [Iniciar uma sessão de suporte](https://msdn.microsoft.com/library/azure/dn757804.aspx) ao contatar o Suporte da Microsoft para obter assistência.

## Cmdlets disponíveis para solução de problemas

Use os seguintes cmdlets do Windows PowerShell para detectar erros de conectividade.

- Get-NetAdapter: use esse cmdlet para detectar a integridade das interfaces de rede. 

- Test-Connection: use esse cmdlet para verificar a conectividade de rede dentro e fora da rede.

- Test-HcsmConnection: use esse cmdlet para verificar a conectividade de um dispositivo registrado com êxito.

## Solucionar problemas com o cmdlet Get-NetAdapter

Quando você configura interfaces de rede para uma implantação no dispositivo pela primeira vez, o status do hardware não está disponível na interface do usuário do serviço Gerenciador do StorSimple porque o dispositivo ainda não está registrado no serviço. Além disso, a página Status de Hardware nem sempre pode refletir o estado do dispositivo, especialmente se houver problemas que afetem a sincronização do serviço. Nessas situações, você pode usar o cmdlet Get-NetAdapter para determinar a integridade e o status de suas interfaces de rede.

### Para ver uma lista de todos os adaptadores de rede no seu dispositivo

1. Inicie o Windows PowerShell para StorSimple e digite **Get-NetAdapter**. 

2. Use a saída do cmdlet Get-NetAdapter e as diretrizes a seguir para entender o status de sua interface de rede.
  - Se a interface estiver íntegra e habilitada, o status **ifIndex** será mostrado como **Ativa**.
  - Se a interface estiver íntegra, mas não estiver conectada fisicamente (por um cabo de rede), **ifIndex** será mostrado como **Desabilitado**.
  - Se a interface estiver íntegra e mas não habilitada, o status **ifIndex** será mostrado como **NãoPresente**.
  - Se a interface não existir, ele não aparecerá na lista. O serviço Gerenciador do StorSimple da interface do usuário ainda mostrará essa interface em estado de falha.

Para obter mais informações sobre como usar esse cmdlet, vá até [GetNetAdapter](https://technet.microsoft.com/library/jj130867.aspx) na referência do cmdlet do Windows PowerShell.

As seções a seguir mostram exemplos de saída do cmdlet Get-NetAdapter.

 Nesses exemplos, controlador 0 foi o controlador passivo e foi configurado da seguinte maneira:

- As interfaces de rede DADOS 0, DADOS 1, DADOS 2 e DADOS 3 existiam no dispositivo.
- As placas de interface de rede DADOS 4 e DADOS 5 não estavam presentes; portanto, não está listadas na saída.
- DADOS 0 estava habilitada.

O controlador 1 foi o controlador ativo e foi configurado da seguinte maneira:

- As interfaces de rede DADOS 0, DADOS 1, DADOS 2, DADOS 3, DADOS 4 e DADOS 5 existiam no dispositivo.
- DADOS 0 estava habilitada.

**Saída de exemplo – controlador 0**

A seguir, a saída do controlador 0 (o controlador passivo). DADOS 1, DADOS 2 e DADOS 3 não estão conectadas. DADOS 4 e DADOS 5 não estão listadas porque não estão presentes no dispositivo.

     Controller0>Get-NetAdapter
     Name                 InterfaceDescription                        ifIndex  Status
     ------               --------------------                        -------  ----------
     DATA3                Mellanox ConnectX-3 Ethernet Adapter #2     17       NotPresent
     DATA2                Mellanox ConnectX-3 Ethernet Adapter        14       NotPresent
     Ethernet 2           HCS VNIC                                    13       Up
     DATA1                Intel(R) 82574L Gigabit Network Co...#2     16       NotPresent
     DATA0                Intel(R) 82574L Gigabit Network Conn...     15       Up


**Saída de exemplo – controlador 1**

A seguir, a saída do controlador 1 (o controlador ativo). Somente a interface DADOS 0 no dispositivo está configurada e funcionando.

     Controller1>Get-NetAdapter
     Name                 InterfaceDescription                        ifIndex  Status
     ------               --------------------                        -------  ----------
     DATA3                Mellanox ConnectX-3 Ethernet Adapter        18       NotPresent
     DATA2                Mellanox ConnectX-3 Ethernet Adapter #2     19       NotPresent
     DATA1                Intel(R) 82574L Gigabit Network Co...#2     16       NotPresent
     DATA0                Intel(R) 82574L Gigabit Network Conn...     15       Up
     Ethernet 2           HCS VNIC                                    13       Up
     DATA5                Intel(R) Gigabit ET Dual Port Server...     14       NotPresent
     DATA4                Intel(R) Gigabit ET Dual Port Serv...#2     17       NotPresent

 
## Solucionar problemas com o cmdlet Test-Connection

Você pode usar o cmdlet Test-Connection para determinar se o seu dispositivo StorSimple pode se conectar à rede externa. Se todos os parâmetros de rede, incluindo o DNS, estiverem configurados corretamente no assistente de instalação, você poderá usar o cmdlet Test-Connection para executar o ping em um endereço conhecido fora da rede, como outlook.com.

Consulte a seguir os exemplos de saída do cmdlet Test-Connection.

> [AZURE.NOTE]No primeiro exemplo, o dispositivo está configurado com um DNS incorreto. No segundo exemplo, o DNS está correto.
 
**Saída de exemplo – DNS incorreto**

No exemplo a seguir, não há nenhuma saída para os endereços IPV4 e IPV6, o que indica que o DNS não foi resolvido. Isso significa que não há conectividade com a rede externa e um DNS correto precisa ser fornecido.

     Source        Destination     IPV4Address      IPV6Address
     ------        -----------     -----------      -----------
     HCSNODE0      outlook.com
     HCSNODE0      outlook.com
     HCSNODE0      outlook.com
     HCSNODE0      outlook.com

**Saída de exemplo – DNS correto**

No exemplo a seguir, o DNS retorna o endereço IPV4, o que indica se o DNS está configurado corretamente. Isso confirma que há conectividade com a rede externa.

     Source        Destination     IPV4Address      IPV6Address
     ------        -----------     -----------      -----------
     HCSNODE0      outlook.com     132.245.92.194
     HCSNODE0      outlook.com     132.245.92.194
     HCSNODE0      outlook.com     132.245.92.194
     HCSNODE0      outlook.com     132.245.92.194

## Solucionar problemas com o cmdlet Test-HcsmConnection

Use o cmdlet Test-HcsmConnection para um dispositivo que já está conectado ao serviço Gerenciador do StorSimple e registrado nele. Esse cmdlet ajuda a verificar a conectividade entre um dispositivo registrado e o serviço Gerenciador do StorSimple correspondente. Você pode executar esse comando no Windows PowerShell para StorSimple.

### Para executar o cmdlet Test-HcsmConnection

1. Certifique-se de que o dispositivo esteja registrado.

2. Verifique o status do dispositivo. Se o dispositivo estiver desativado, no modo de manutenção ou offline, você poderá ver os seguintes erros:

   - ErrorCode.CiSDeviceDecommissioned – indica que o dispositivo está desativado.
   - ErrorCode.DeviceNotReady – indica que o dispositivo está no modo de manutenção.
   - ErrorCode.DeviceNotReady – indica que o dispositivo não está online.

3. Verifique se o serviço Gerenciador do StorSimple está em execução (use o cmdlet [Get-ClusterResource](https://technet.microsoft.com/library/ee461004.aspx)). Se o serviço não estiver em execução, talvez você veja os seguintes erros:

   - ErrorCode.CiSApplianceAgentNotOnline
   - ErrorCode.CisPowershellScriptHcsError – indica que houve uma exceção ao executar Get-ClusterResource.

4. Verifique o token do ACS (Serviço de Controle de Acesso). Se ele lançar uma exceção da Web, isso poderá ser o resultado de um problema de gateway, de uma autenticação de proxy ausente, de um DNS incorreto ou de uma falha de autenticação. Você pode ver os seguintes erros:

   - ErrorCode.CiSApplianceGateway – indica uma exceção HttpStatusCode.BadGateway: o serviço de resolução de nome não conseguiu resolver o nome do host. 
   - ErrorCode.CiSApplianceProxy – indica uma exceção HttpStatusCode.ProxyAuthenticationRequired (código de status HTTP 407): o cliente não pôde autenticar com o servidor proxy. 
   - ErrorCode.CiSApplianceDNSError – indica uma exceção WebExceptionStatus.NameResolutionFailure: o serviço de resolução de nome não conseguiu resolver o nome do host. .
   - ErrorCode.CiSApplianceACSError – indica que o serviço retornou um erro de autenticação, mas há conectividade.
   
   Se isso não lançar uma exceção da Web, verifique o seguinte:

   - ErrorCode.CiSApplianceFailure – indica que o dispositivo falhou.

5. Verifique a conectividade do serviço de nuvem. Se o serviço lançar uma exceção da Web, talvez você veja os seguintes erros:

  - ErrorCode.CiSApplianceGateway – indica uma exceção HttpStatusCode.BadGateway: um servidor proxy intermediário recebeu uma solicitação incorreta de outro proxy ou do servidor original.
  - ErrorCode.CiSApplianceProxy – indica uma exceção HttpStatusCode.ProxyAuthenticationRequired (código de status HTTP 407): o cliente não pôde autenticar com o servidor proxy. 
  - ErrorCode.CiSApplianceDNSError – indica uma exceção WebExceptionStatus.NameResolutionFailure: o serviço de resolução de nome não conseguiu resolver o nome do host. .
  - ErrorCode.CiSApplianceACSError – indica que o serviço retornou um erro de autenticação, mas há conectividade.
  
  Se ele não lançar uma exceção da Web, verifique o seguinte: 
  - ErrorCode.CiSApplianceSaasServiceError – isso indica um problema com o serviço Gerenciador do StorSimple.
 
6. Verifique a conectividade do Barramento de Serviço do Azure. ErrorCode.CiSApplianceServiceBusError indica que o dispositivo não pode se conectar ao Barramento de Serviço.
 
Os arquivos de log CiSCommandletLog0Curr.errlog e CiSAgentsvc0Curr.errlog terão mais informações, como os detalhes da exceção.

Para obter mais informações sobre como usar o cmdlet, vá até [Test-HcsmConnection](https://technet.microsoft.com/library/dn715782.aspx) na documentação de referência do Windows PowerShell.

> [AZURE.IMPORTANT]Você pode executar esse cmdlet para o controlador passivo e o ativo.
 
Consulte os exemplos de saída do cmdlet Test-HcsmConnection a seguir.

**Exemplo de saída – dispositivo registrado com êxito**

O primeiro exemplo é de um dispositivo registrado com êxito no serviço Gerenciador do StorSimple e que não tem nenhum problema de conectividade.

     Controller1>Test-HcsmConnection -verbose
     Checking device state  ... Success.
     Device registered successfully
     Checking device authentication.  ... This operation will take few minutes to complete....
     Checking device authentication.  ... Success.
     Checking connectivity from device to StorSimple Manager service.  ... This operation will take few minutes to complete....
     Checking connectivity from device to StorSimple Manager service.  ... Success.
     Checking connectivity from StorSimple Manager service to StorSimple device. .... Success.
     Controller1>

**Saída de exemplo – dispositivo offline**

Esse exemplo é de um dispositivo com o status **offline** no Portal de Gerenciamento.

     Checking device state: Success 
     Device is registered successfully 
     Checking connectivity from device to SaaS.. Failure

O dispositivo não pôde se conectar usando a configuração de proxy da Web atual. Isso pode ser um problema com a configuração de proxy da Web ou um problema de conectividade de rede. Nesse caso, certifique-se de que as configurações de proxy da web estejam corretas e de que seus servidores de proxy da Web estão online e acessíveis.

## Exemplo passo a passo de solução de problemas do StorSimple

O exemplo a seguir mostra o passo a passo da solução de problemas de uma implantação do StorSimple. No cenário de exemplo, o registro de dispositivo falha com uma mensagem de erro indicando que as configurações de rede ou o nome DNS estão incorretas.

A mensagem de erro retornada é:

     Invoke-HcsSetupWizard: An error has occurred while registering the device. This could be due to incorrect IP address or DNS name. Please check your network settings and try again. If the problems persist, contact Microsoft Support.
     +CategoryInfo: Not specified
     +FullyQualifiedErrorID: CiSClientCommunicationErros, Microsoft.HCS.Management.PowerShell.Cmdlets.InvokeHcsSetupWizardCommand

O erro pode ter sido causado por qualquer uma das seguintes opções:

- Instalação de hardware incorreto
- Interfaces de rede com defeito
- Endereço IP, máscara de sub-rede, gateway, servidor DNS primário ou proxy da Web incorreto
- Chave de registro incorreta
- Configurações de firewall incorretas

### Para localizar e corrigir o problema de registro do dispositivo

1. Verifique a configuração do dispositivo: no controlador ativo, execute **Invoke-HcsSetupWizard**.

     > [AZURE.NOTE]O assistente de instalação deve ser executado no controlador ativo. Para verificar se você está conectado ao controlador ativo, examine a faixa apresentada no console serial. A faixa indica se você está conectado ao controlador 0 ou 1, e se o controlador está ativo ou passivo. Para obter mais informações, consulte [Identificar um controlador ativo em seu dispositivo](https://msdn.microsoft.com/library/azure/dn790262.aspx).
 
2. Certifique-se de que o dispositivo esteja conectado corretamente: verifique a cabeamento de rede no backplane do dispositivo. O cabeamento é específico para o modelo do dispositivo. Para obter mais informações, vá até [Cabear seu dispositivo 8100](https://msdn.microsoft.com/library/azure/dn757738.aspx) ou [Cabear seu dispositivo 8600](https://msdn.microsoft.com/library/azure/dn757762.aspx).

     > [AZURE.NOTE]Se você estiver usando portas de rede 10 GbE, precisará usar os adaptadores de QSFP-SFP e os cabos SFP fornecidos. Para obter mais informações, consulte a [lista de cabos, comutadores e transceptores recomendados pelo fornecedor OEM para portas Mellanox](http://www.mellanox.com/page/cables?mtag=cable_overview).
 
3. Verifique a integridade da interface de rede:

   - Use o cmdlet Get-NetAdapter para detectar a integridade das interfaces de rede para DADOS 0. 
   - Se o link não estiver funcionando, o status **ifindex** indicará que a interface está inativa. Dessa forma, você precisará verificar a conexão de rede da porta para o dispositivo e o comutador. Você também precisará eliminar os cabos com defeito. 
   - Se você suspeitar que a porta DADOS 0 no controlador ativo tenha falhado, poderá confirmar isso conectando-se à porta DADOS 0 no controlador 1. Para confirmar isso, desconecte o cabo de rede da parte traseira do dispositivo do controlador 0, conecte o cabo ao controlador 1 e, em seguida, execute o cmdlet Get-NetAdapter novamente. Se a porta DADOS 0 de um controlador falhar, [contate o Suporte da Microsoft](https://msdn.microsoft.com/library/azure/dn757750.aspx) para as próximas etapas. Talvez seja necessário substituir o controlador em seu sistema.
 
4. Verifique a conectividade com o comutador:
   - Certifique-se de que as interfaces de rede DADOS 0 no controlador 0 e no controlador 1 em seu compartimento primário estejam na mesma sub-rede. 
   - Verifique o hub ou o roteador. Normalmente, você deve conectar os dois controladores no mesmo hub ou roteador. 
   - Certifique-se de que os comutadores usados para a conexão tenham DADOS 0 para os dois controladores na mesma vLAN.
   
5. Elimine erros de usuário:

  - Execute novamente o assistente de instalação (execute **Invoke-HcsSetupWizard**) e insira os valores novamente para garantir que não haja erros. 
  - Verifique a chave de registro usada. A mesma chave de registro pode ser usada para conectar vários dispositivos a um serviço Gerenciador do StorSimple. Use o procedimento em [Obter a chave de registro de serviço](https://msdn.microsoft.com/library/azure/cd4dee49-6ae8-4ff0-b79b-74b2027cb694#sec03) para garantir que você esteja usando a chave de registro correta.

    > [AZURE.IMPORTANT]Se você tiver vários serviços em execução, precisará garantir que a chave de registro para o serviço adequado seja usada para registrar o dispositivo. Se você tiver registrado um dispositivo com o serviço Gerenciador do StorSimple errado, precisará [contatar o Suporte da Microsoft](https://msdn.microsoft.com/library/azure/dn757750.aspx) para as próximas etapas. Você precisa executar uma redefinição de fábrica do dispositivo (o que pode resultar na perda de dados) para então conectá-lo ao serviço pretendido.

6. Use o cmdlet Test-Connection para verificar se há conectividade com a rede externa. Para obter mais informações, vá até [Solucionar problemas com o cmdlet Test-Connection](#troubleshoot-with-the-test-connection-cmdlet).

7. Verifique se há interferência de firewall. Se você tiver verificado que as configurações de IP Virtual (VIP), sub-rede, gateway e DNS estão todos corretas e ainda assim ter problemas de conectividade, é possível que seu firewall esteja bloqueando a comunicação entre o dispositivo e a rede externa. Você precisa garantir que as portas 80 e 443 estejam disponíveis no seu dispositivo StorSimple para comunicação de saída. Para obter mais informações, consulte [Requisitos de rede para o dispositivo StorSimple](https://msdn.microsoft.com/library/azure/dn772371.aspx).

8. Examine os logs. Vá para [Pacotes de suporte e logs de dispositivo disponíveis para solução de problemas](#pacotes-suporte-e-logs-dispositivo-para-solução-de-problemas).

9. Se as etapas anteriores não resolverem o problema, [contate o Suporte da Microsoft](https://msdn.microsoft.com/library/azure/dn757750.aspx) para obter assistência. 

## Próximas etapas
[Solucionar problemas de um dispositivo operacional](../storsimple-troubleshoot-an-operational-device.md)

<!--Link references-->

[1]: https://technet.microsoft.com/library/dd379547(v=ws.10).aspx
[2]: https://technet.microsoft.com/library/dd392266(v=ws.10).aspx
<!--HONumber=52-->
 