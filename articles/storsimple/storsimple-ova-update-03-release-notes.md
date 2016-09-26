<properties 
   pageTitle="Notas de versão das Atualizações do StorSimple Virtual Array | Microsoft Azure"
   description="Descreve os problemas em aberto críticos e resoluções para a StorSimple Virtual Array que executa a Atualização 0.3."
   services="storsimple"
   documentationCenter=""
   authors="alkohli"
   manager="carmonm"
   editor="" />
<tags 
   ms.service="storsimple"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="NA"
   ms.date="09/14/2016"
   ms.author="alkohli" />

# Notas de versão da Atualização 0.3 da StorSimple Virtual Array

## Visão geral

As notas de versão a seguir identificam as questões críticas em aberto e os problemas resolvidos para as atualizações do Microsoft Azure StorSimple Virtual Array.

As notas de versão são continuamente atualizadas e, à medida que são descobertas questões críticas que exijam uma solução alternativa, elas são adicionadas. Antes de implantar o StorSimple Virtual Array, examine cuidadosamente as informações contidas nas notas de versão.

A Atualização 0.3 corresponde à versão de software **10.0.10288.0**.

> [AZURE.NOTE] As atualizações causam interrupção e reiniciam seu dispositivo. Se processos de E/S estiverem em andamento, o dispositivo sofrerá tempo de inatividade.


## Novidades na Atualização 0.3

A Atualização 0.3 é, basicamente, um build de correção de bug. Nesta versão, diversos bugs resultando em falhas de backup na versão anterior foram resolvidos.

## Problemas resolvidos na Atualização 0.3

A tabela a seguir fornece um resumo dos problemas corrigidos nesta versão.

| Nº | Recurso | Problema |
|------|--------------------------------------|---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| 1 | Backups |Um problema foi observado na versão anterior em que os backups não conseguiam ser concluídos. A causa raiz foi identificada e corrigida nesta versão. <br></br> A correção não se aplica retroativamente a compartilhamentos que já estão observando esse problema. Os clientes que estão vendo esse problema primeiro devem aplicar a Atualização 0.3, contatar o Suporte da Microsoft para executar um backup completo do sistema para corrigir o problema. Em vez de entrar em contato com o Suporte da Microsoft, os clientes também podem restaurar para um novo compartilhamento de um backup íntegro para os compartilhamentos afetados. |
| 2 | iSCSI | Um problema foi visto na versão anterior em que os volumes desaparecerão ao copiar dados para um volume do StorSimple Virtual Array. Esse problema foi corrigido nesta versão. <br></br> As correções em vigor somente em volumes recém-criados. As correções não se aplicam retroativamente a volumes que já estão observando esse problema. Os clientes são aconselhados a colocar os volumes afetados online por meio do portal clássico do Azure, executar um backup para esses volumes e, em seguida, restaurar esses volumes para novos volumes. |


## Problemas conhecidos na Atualização 0.3

A tabela a seguir fornece um resumo dos problemas conhecidos para a StorSimple Virtual Array e inclui os problemas observados das versões anteriores.


| Nº | Recurso | Problema | Solução alternativa/comentários |
|-----|--------------------------|----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| **1.** | Atualizações | Os dispositivos virtuais criados na versão de visualização não podem ser atualizados para uma versão de Disponibilidade Geral com suporte. | Esses dispositivos virtuais devem fazer failover para a versão de Disponibilidade Geral usando um fluxo de trabalho de DR (Recuperação de Desastre). |
| **2.** | Disco de dados provisionado | Após você ter provisionado um disco de dados de um determinado tamanho especificado e criado o dispositivo virtual StorSimple correspondente, você não deve expandir nem reduzir o disco de dados. A tentativa de fazer isso resulta na perda de todos os dados nas camadas locais do dispositivo. | |
| **3.** | Política de grupo | Quando um dispositivo é associado a um domínio, a aplicação de uma política de grupo pode afetar a operação do dispositivo. | Certifique-se de que a matriz virtual esteja em sua própria OU (unidade organizacional) do Active Directory e que nenhum GPO (objeto de política de grupo) seja aplicado a ela.|
| **4.** | Interface do Usuário da Web local | Se os recursos de segurança aprimorados estão habilitados no Internet Explorer (IE ESC), algumas páginas da interface do usuário da Web local, como Solução de Problemas ou Manutenção, podem não funcionar corretamente. Os botões nessas páginas também podem não funcionar. | Desligue os recursos de segurança reforçada do Internet Explorer.|
| **5.** | Interface do Usuário da Web local | Em uma máquina virtual de Hyper-V, as interfaces de rede na interface do usuário da Web são exibidas como interfaces de 10 Gbps. | Esse comportamento é um reflexo do Hyper-V. O Hyper-V sempre mostra 10 Gbps para adaptadores de rede virtual. |
| **6.** | Compartilhamentos ou volumes em camadas | Não há suporte para bloqueio de intervalo de bytes para aplicativos que funcionam com os volumes em camadas do StorSimple. Se o bloqueio de intervalo de bytes estiver habilitado, a disposição em camadas do StorSimple não funcionará. | As medidas recomendadas incluem: <br></br>Desabilitar o bloqueio de intervalo de bytes na lógica do aplicativo.<br></br>Optar por inserir dados para este aplicativo em volumes fixos localmente em vez de volumes em camadas.<br></br>*Ressalva*: ao usar volumes fixados localmente e o bloqueio de intervalo de bytes estiver habilitado, esteja ciente de que o volume fixo local pode ficar online antes mesmo da restauração ser concluída. Nesses casos, se uma restauração está em andamento, você deve aguardar a restauração ser concluída. |
| **7.** | Compartilhamentos em camadas | Trabalhar com arquivos grandes pode resultar em uma divisão em camadas lenta. | Ao trabalhar com arquivos grandes, recomendamos que o maior arquivo seja inferior a 3% do tamanho do compartilhamento. |
| **8.** | Capacidade utilizada para compartilhamentos | Você pode ver o consumo de compartilhamento quando não houver dados no compartilhamento. Isso ocorre porque a capacidade utilizada para compartilhamentos inclui metadados. | |
| **9.** | Recuperação de desastre | Você só pode executar a recuperação de desastres de um servidor de arquivos no mesmo domínio que o dispositivo de origem. Não há suporte nesta versão para a recuperação de desastres para um dispositivo de destino em outro domínio. | Isso será implementado em uma versão posterior. |
| **10.** | Azure PowerShell | Os dispositivos virtuais StorSimple não podem ser gerenciados por meio do Azure PowerShell nesta versão. | Todo o gerenciamento dos dispositivos virtuais deve ser feito por meio do portal clássico do Azure e da interface do usuário da Web local. |
| **11.** | Alteração de senha | O console do dispositivo de matriz virtual só aceita a entrada no formato de teclado pt-BR. | |
| **12.** | CHAP | Não é possível remover as credenciais CHAP depois de criadas. Além disso, se você modificar as credenciais CHAP, precisará fazer com que os volumes fiquem offline e depois online novamente para que a alteração entre em vigor. | Esse problema será corrigido em uma versão futura. |
| **13.** | Servidor iSCSI | O 'Armazenamento usado' exibido para um volume iSCSI pode ser diferente no serviço StorSimple Manager e no host iSCSI. | O host iSCSI tem o modo de exibição do sistema de arquivos.<br></br>O dispositivo detecta os blocos atribuídos quando o volume está no tamanho máximo.|
| **14.** | Servidor de arquivos | Se um arquivo em uma pasta tiver um Fluxo de Dados Alternativo (ADS) associado a ele, o ADS não será copiado ou restaurado por meio da recuperação de desastres, clonagem e Recuperação no Nível do Item.| |


## Próxima etapa

[Instale a Atualização 0.3](storsimple-ova-install-update-01.md) em sua StorSimple Virtual Array.

## Referências

Procurando uma nota de versão mais antiga? Acesse:

- [Notas de versão as Atualizações 0.1 e 0.2 do StorSimple Virtual Array](storsimple-ova-update-01-release-notes.md)

- [Notas de versão de disponibilidade geral do StorSimple Virtual Array](storsimple-ova-pp-release-notes.md)
 

<!---HONumber=AcomDC_0914_2016-->