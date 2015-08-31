<properties 
   pageTitle="Requisitos do sistema do StorSimple | Microsoft Azure" 
   description="Descreve os requisitos de sistema e práticas recomendadas para software, alta disponibilidade e rede para uma solução do Azure StorSimple." 
   services="storsimple" 
   documentationCenter="NA" 
   authors="alkohli" 
   manager="carolz" 
   editor=""/>

<tags
   ms.service="storsimple"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="TBD" 
   ms.date="08/17/2015"
   ms.author="alkohli"/>

# Requisitos do sistema do StorSimple

## Visão geral

Bem-vindo ao Microsoft Azure StorSimple. Este artigo descreve os requisitos do sistema importantes e as práticas recomendadas para seu dispositivo StorSimple e para os clientes de armazenamento acessarem o dispositivo. Esses requisitos incluem:

- **Requisitos de software para clientes de armazenamento** - descreve os sistemas operacionais com suporte e quaisquer requisitos adicionais para esses sistemas operacionais.
- **Requisitos de alta disponibilidade para o StorSimple** - descreve os requisitos de alta disponibilidade e as práticas recomendadas para o dispositivo StorSimple e o computador host. 
- **Requisitos de rede para o dispositivo StorSimple** - fornece informações sobre as portas que precisam ser abertas no firewall para permitir o tráfego iSCSI, de nuvem ou de gerenciamento.

Recomendamos a revisão cuidadosa das informações a seguir antes de implantar o sistema Azure StorSimple. Consulte-as como necessário durante a implantação e operação seguinte.

## Requisitos de software para clientes de armazenamento 

Os requisitos de software a seguir são para os clientes de armazenamento que acessam seu dispositivo StorSimple.

| Sistemas operacionais com suporte | Versão necessária | Detalhes/observações |
| --------------------------- | ---------------- | ------------- |
| Windows Server | 2008R2 SP1, 2012, 2012R2 |<ul><li>Os volumes iSCSI do StorSimple recebem suporte para uso somente nos seguintes tipos de disco do Windows:<ul><li>Volume simples em disco básico</li><li>Volume simples e espelhado em disco dinâmico</li></ul>O uso do Gerenciador de Instantâneos do StorSimple no Windows Server é necessário para backup/restauração de discos dinâmicos espelhados e para quaisquer backups consistentes com o aplicativo.</li><li>O Gerenciador de Instantâneos StorSimple só recebe suporte no Windows Server 2008 R2 SP1 (64 bits), Windows 2012 R2 e Windows Server 2012. <ul><li>Se você estiver usando o Windows Server 2012, instale o .NET 3.5 – 4.5 antes de instalar o Gerenciador de Instantâneos do StorSimple.</li><li>Se você estiver usando o Windows Server 2008 R2 SP1, instale o Windows Management Framework 3.0 antes de instalar o Gerenciador de Instantâneos StorSimple.</li></ul></li><li>Provisionamento dinâmico do Windows Server 2012 e recursos ODX receberão suporte se você estiver usando um volume iSCSI do StorSimple.<ul><li>O StorSimple pode criar apenas volumes de provisionamento dinâmico. Ele não pode criar volumes parcial ou totalmente provisionados.</li><li>Reformatar um volume de provisionamento dinâmico pode levar muito tempo. Recomendamos a exclusão do volume e criação de um novo em vez de reformatar.</li><li>Se você ainda preferir reformatar um volume:<ul><li>Execute o comando a seguir antes de reformatar a fim de evitar atrasos por recuperação de espaço: <br>`fsutil behavior set disabledeletenotify 1`</br></li><li>Após a conclusão da formatação, use o seguinte comando para reativar a recuperação de espaço:<br>`fsutil behavior set disabledeletenotify 0`</br></li><li>Aplique o hotfix do Windows Server 2012, conforme descrito em KB2870270 em seu computador com Windows Server.</li></ul></li></ul><li>O Adaptador do StorSimple para SharePoint só tem suporte no SharePoint 2010 e no SharePoint 2013. O RBS exige o SQL Server Enterprise Edition, versão 2008 R2 ou 2012.</li></ul>|
| VMWare | Testado com VMware vSphere 5.1 como cliente iSCSI | O recurso de bloco VAAI é testado com o VMware vSphere v.5.1 em dispositivos StorSimple. 
| Linux RHEL/CentOS | Suporte para clientes Linux iSCSI com o iniciador Open-iSCSI versões 5 e 6 | Suporte somente com o iniciador open-iSCSI. |
| Linux | SUSE Linux 11 | |
 >[AZURE.NOTE]Com base nos testes limitados realizados internamente, o IBM AIX não recebe suporte com o StorSimple no momento.

## Requisitos de alta disponibilidade para o StorSimple

A plataforma de hardware incluída com a solução StorSimple possui recursos de disponibilidade e confiabilidade de nível empresarial que fornecem uma base para uma infraestrutura de armazenamento altamente disponível e tolerante a falhas em seu datacenter. No entanto, há requisitos e práticas recomendadas que você deve obedecer para ajudar a garantir a disponibilidade de sua solução do Azure StorSimple. Antes de implantar o Azure StorSimple, examine cuidadosamente os requisitos e práticas recomendadas a seguir para o dispositivo StorSimple e computadores host conectados.

### Requisitos de alta disponibilidade e procedimentos para seu dispositivo StorSimple

Examine as seguintes informações com atenção para garantir a alta disponibilidade do seu dispositivo StorSimple.

#### Módulos de energia e resfriamento (PCMs)

Os dispositivos StorSimple incluem PCMs redundantes e intercambiáveis. Cada PCM tem capacidade suficiente para atender ao chassi inteiro. Para garantir a alta disponibilidade, os dois PCMs devem estar instalados.

- Conecte seus PCMs a fontes de alimentação diferentes a fim de fornecer disponibilidade de uma fonte de alimentação falhar.

- Se um PCM falhar, solicite uma substituição imediatamente.

- Remova um PCM com falha somente quando tiver a reposição e estiver pronto para instalá-lo.

- Não remova os dois PCMs simultaneamente. O módulo PCM inclui a bateria. A remoção dos dois PCMs resultará em um desligamento sem proteção por bateria.

#### Módulos do controlador

Os dispositivos StorSimple incluem módulos de controlador redundantes e intercambiáveis. Os módulos do controlador operam de modo ativo/passivo. A qualquer momento, um módulo do controlador está ativo e fornecendo o serviço, enquanto o outro módulo do controlador está passivo. O módulo do controlador passivo está ligado e se tornará operacional se o módulo do controlador ativo falhar ou for removido. Cada módulo do controlador tem capacidade suficiente para atender ao chassi inteiro. Os dois módulos do controlador devem ser instalados para garantir a alta disponibilidade.

- Certifique-se de que os dois módulos do controlador estejam sempre instalados.

- Se um módulo do controlador falhar, solicite uma substituição imediatamente.

- Remova um módulo de controlador com falha somente quando tiver a reposição e estiver pronto para instalá-lo. A remoção de um módulo para períodos prolongados afetará o fluxo de ar e, portanto, o resfriamento do sistema.

- Certifique-se de que as conexões de rede para ambos os módulos de controlador sejam idênticas, e as interfaces da rede conectada tenham uma configuração de rede idêntica.

- Se um módulo de controlador falhar ou precisar de substituição, certifique-se de que o outro módulo de controlador esteja em um estado ativo antes de substituir o módulo de controlador com falha.

- Não remova os dois módulos de controlador ao mesmo tempo.

- Se estiver ocorrendo um failover do controlador, não desligue o módulo do controlador em espera ou o remova do chassi.

- Após o failover do controlador, aguarde pelo menos cinco minutos antes de remover um dos módulos de controlador.

#### Interfaces de rede

Cada módulo de controlador do dispositivo StorSimple tem quatro interfaces de rede Ethernet de 1 Gigabit e duas de 10 Gigabits.

- Certifique-se de que as conexões de rede para os dois módulos de controlador sejam idênticas, e que as interfaces de rede as quais as interfaces do módulo de controlador estão conectadas tenham uma configuração de rede idêntica.

- Quando possível, implante as conexões de rede entre opções diferentes para garantir a disponibilidade do serviço no caso de falhas do dispositivo de rede.

- Ao desconectar a única, ou a última, interface habilitada para iSCSI (com IPs atribuídos), desabilite primeiro a interface e, em seguida, desconecte os cabos. Se a interface for desconectada primeiro, o controlador ativo passará por failover para o controlador passivo. Se as interfaces correspondentes do controlador passivo também forem desconectadas, os dois controladores serão reiniciados várias vezes antes da definição de um controlador.

- Conecte pelo menos duas interfaces DATA com a rede de cada controlador de módulo.

- Se você habilitou as duas interfaces de 10 GbE, implante-as em opções diferentes.

- Quando possível, use o MPIO em servidores para garantir que os servidores possam tolerar um link, rede ou falha de interface.

Para saber mais sobre como colocar o dispositivo em rede para proporcionar alta disponibilidade e desempenho, acesse [Instalar o dispositivo StorSimple 8100](storsimple-8100-hardware-installation.md#cable-your-storsimple-8100-device) ou [Instalar o dispositivo StorSimple 8600](storsimple-8600-hardware-installation.md#cable-your-storsimple-8600-device).

#### SSDs e HDDs

Dispositivos StorSimple incluem discos de estado sólido (SSDs) e unidades de disco rígido (HDDs) protegidos com espaços espelhados, e uma espera ativa é fornecida para as HDDs. O uso de espaços espelhados garante que o dispositivo seja capaz de tolerar a falha de um ou mais SSDs ou HDDs.

- Certifique-se de que todos os módulos SSD e HDD estejam instalados.

- Se um SSD ou HDD falhar, solicite uma substituição imediatamente.

- Se um SSD ou HDD falhar ou exigir substituição, remova somente o SSD ou HDD que exige a substituição.

- Não remova mais de um SSD ou HDD do sistema a qualquer momento.

- Durante a substituição, monitore o **Status de Hardware** na página **Manutenção** das unidades no SSDs e HDDs. Um status de marca de verificação verde indica que os discos estão íntegros ou OK, enquanto que um ponto de exclamação vermelho indica um SSD ou HDD com falha.

- Uma falha de dois ou mais discos de determinado tipo (HDD, SSD) ou uma falha consecutiva em um curto período pode resultar em mau funcionamento do sistema e possível perda de dados. Se isso ocorrer, entre em contato com o suporte técnico para obter assistência. Recomendamos a configuração de instantâneos em nuvem para todos os volumes que você precisa proteger em caso de falha do sistema.

#### Compartimento EBOD

O modelo do dispositivo StorSimple 8600 inclui um compartimento EBOD (Extended Bunch of Disks) além do compartimento primário. Um EBOD contém controladores EBOD e HDDs (unidades de disco rígido) protegidos por espaços espelhados. O uso de espaços espelhados garante que o dispositivo seja capaz de tolerar a falha de um ou mais HDDs. O compartimento EBOD está conectado ao compartimento primário por meio de cabos SAS redundantes.

- Verifique se ambos os módulos do controlador de compartimento EBOD, os cabos SAS e todos os discos rígidos estão sempre instalados.

- Se um compartimento EBOD ou um HDD falhar, solicite uma substituição imediatamente.

- Se um módulo do controlador de compartimento EBOD falhar, certifique-se de que o outro módulo do controlador esteja ativo antes de substituir o módulo com falha.

- Se um HDD falhar ou exigir substituição, remova somente HDD que exige a substituição. Não remova um HDD até que haja uma indicação de que os discos e a matriz estejam íntegros.

- Não remova mais de um HDD do sistema a qualquer momento.

- Durante uma substituição do módulo do controlador EBOD ou do HDD, monitore continuamente o status do componente relevante no serviço Gerenciador do StorSimple acessando **Manutenção** - status do **Hardware**.

- Se um cabo SAS falha ou exigir substituição (o Suporte da Microsoft deve participar dessa decisão), remova apenas o cabo SAS que exige a substituição.

- Não remova simultaneamente os dois cabos SAS do sistema em nenhum momento.

### Requisitos de alta disponibilidade para os computadores host

Leia com atenção estes requisitos e práticas recomendadas para garantir a alta disponibilidade dos hosts conectados ao dispositivo StorSimple.

- Configure o StorSimple com [configurações de cluster de servidores de arquivos com dois nós][1]. Ao remover os pontos individuais de falha e criando redundância no lado do host, a solução inteira se torna altamente disponível.

- Use compartilhamentos CA (disponíveis continuamente) disponíveis no Windows Server 2012 (SMB 3.0) para alta disponibilidade durante o failover dos controladores de armazenamento. Para obter informações adicionais sobre a configuração de clusters de servidor de arquivos e compartilhamentos Disponíveis Continuamente com o Windows Server 2012, consulte esta [demonstração em vídeo](http://channel9.msdn.com/Events/IT-Camps/IT-Camps-On-Demand-Windows-Server-2012/DEMO-Continuously-Available-File-Shares).

## Requisitos de rede para seu dispositivo StorSimple

Seu dispositivo StorSimple é um dispositivo bloqueado. No entanto, é preciso abrir portas no firewall para permitir o tráfego de gerenciamento, de nuvem ou iSCSI. A tabela a seguir lista as portas que precisam estar abertas no firewall. Nesta tabela, *entrada* ou *de entrada* refere-se à direção a partir da qual as solicitações de cliente acessam o dispositivo. *Saída* ou *de saída* refere-se à direção na qual seu dispositivo StorSimple envia dados externamente, além da implantação: por exemplo, saída para a Internet.

| Nº da porta<sup>1,2</sup> | Entrada ou saída | Escopo da porta | Obrigatório | Observações |
|------------------------|-----------|------------|----------|-------| 
|TCP 80 (HTTP)<sup>3</sup>| Saída | WAN | Não |<ul><li>A porta de saída é usada para acesso à Internet a fim de obter as atualizações.</li><li>O proxy da Web de saída pode ser configurado pelo usuário.</li><li>Para permitir atualizações do sistema, esta porta também deve estar aberta para os IPs fixos do controlador.</li></ul> |
|TCP 443 (HTTPS)<sup>3</sup>| Saída | WAN | Sim |<ul><li>A porta de saída é usada para acesso aos dados na nuvem.</li><li>O proxy da Web de saída pode ser configurado pelo usuário.</li><li>Para permitir atualizações do sistema, esta porta também deve estar aberta para os IPs fixos do controlador.</li></ul>|
|UDP 53 (DNS) | Saída | WAN | Em alguns casos; consulte as observações. |Esta porta só será necessária se você estiver usando um servidor DNS baseado na Internet. |
| UDP 123 (NTP) | Saída | WAN | Em alguns casos; consulte as observações. |Esta porta é necessária apenas se você estiver usando um servidor NTP baseado na Internet. |
| TCP 9354 | Saída | WAN | Em alguns casos; consulte as observações. |A porta de saída é usada pelo serviço Gerenciador do StorSimple para se comunicar com o dispositivo. Esta porta é exigida se a rede atual não oferecer suporte ao uso de HTTP 1.1 para se conectar à Internet. Por exemplo, se você estiver usando um servidor proxy baseado em HTTP 1.0.<br> Se estiver se conectando através de um servidor proxy, consulte [requisitos de barramento de serviço](https://msdn.microsoft.com/library/azure/ee706729.aspx) para obter informações detalhadas. |
| 3260 (iSCSI) | No | LAN | Não | Esta porta é usada para acessar dados em iSCSI.|
| 5985 | No | LAN | Não | Porta de entrada é usada pelo Gerenciador de Instantâneos do StorSimple para se comunicar com o dispositivo StorSimple.<br>Essa porta também é usada quando você se conecta remotamente ao Windows PowerShell para StorSimple via HTTP ou HTTPS. |

<sup>1</sup> Nenhuma porta de entrada precisa estar aberta na Internet pública.

<sup>2</sup> Se várias portas executarem uma configuração de gateway, a ordem do tráfego de saída roteado será determinada com base na ordem de roteamento de porta descrita abaixo.

<sup>3</sup> Os IPs fixos do controlador em seu dispositivo StorSimple devem ser roteáveis e conseguirem se conectar à Internet. Os endereços IP fixos são usados para fornecer as atualizações ao dispositivo. Se os controladores de dispositivo não puderem se conectar à Internet através de IPs fixa, não será possível atualizar o dispositivo StorSimple.

### Roteamento de porta

O roteamento de porta é diferente dependendo da versão de software em execução em seu dispositivo StorSimple.

- Se o dispositivo estiver executando uma versão do software anterior à Atualização 1, como GA, versão 0.1, 0.2 ou 0,3, o roteamento das portas será decidido da seguinte maneira:

     Interface de rede de 10 GbE configurada por último > Outras interfaces de rede de 10 GbE > Última interface de rede de 1 GbE configurada > Outra interface de rede de 1 GbE

- Se o dispositivo estiver executando a Atualização 1, o roteamento das portas será decidido da seguinte maneira:

     DADOS 0 > Interface de rede de 10 GbE configurada por último > Outras interfaces de rede de 10 GbE > Última interface de rede de 1 GbE configurada > Outra interface de rede de 1 GbE

Na Atualização 1, a métrica de roteamento de DADOS 0 é a mais baixa; portanto, todo o tráfego de nuvem é roteado por meio de DADOS 0. Anote isso se houver mais de uma interface de rede habilitada para nuvem em seu dispositivo StorSimple.

### Práticas recomendadas de rede

Além dos requisitos de rede acima, para obter o desempenho ideal de sua solução StorSimple, atenda às seguintes práticas recomendadas:

- Verifique se o dispositivo StorSimple tem uma largura de banda dedicada de 40 Mbps (ou mais) disponível sempre. Essa largura de banda não deve ser compartilhada com outros aplicativos.

- Verifique a conectividade de rede com a Internet está disponível sempre. Conexões de Internet esporádicas ou não confiáveis com os dispositivos, sem incluir qualquer conectividade com a Internet, resultarão em uma configuração sem suporte.

- Isole o tráfego iSCSI e o tráfego de nuvem com interfaces de rede dedicadas em seu dispositivo para acesso iSCSI e à nuvem. Para obter mais informações, veja como [modificar as interfaces de rede](storsimple-modify-device-config.md#modify-network-interfaces) em seu dispositivo StorSimple.

## Próximas etapas

- [Saiba mais sobre os limites do sistema StorSimple](storsimple-limits.md).
- [Saiba como implantar sua solução StorSimple](storsimple-deployment-walkthrough.md).
 
<!--Reference links-->
[1]: https://technet.microsoft.com/library/cc731844(v=WS.10).aspx

<!---HONumber=August15_HO8-->