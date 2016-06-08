<properties 
   pageTitle="Notas de versão da Atualização 0.1 StorSimple Virtual Array | Microsoft Azure"
   description="Descreve os problemas em aberto críticos e resoluções para a StorSimple Virtual Array que executa a Atualização 0.1."
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
   ms.date="05/24/2016"
   ms.author="alkohli" />

# Notas de versão da Atualização 0.1 da StorSimple Virtual Array

## Visão geral

As notas de versão a seguir identificam as questões críticas em aberto e problemas resolvidos para a versão Atualização 0.1 da Microsoft Azure StorSimple Virtual Array. (A Microsoft Azure StorSimple Virtual Array também é conhecida como dispositivo virtual local StorSimple ou dispositivo virtual StorSimple.) Esta versão corresponde à versão do software **10.0.10279.0**.

As notas de versão são continuamente atualizadas e, à medida que são descobertas questões críticas que exijam uma solução alternativa, elas são adicionadas. Examine cuidadosamente as informações contidas nas notas de versão antes de implantar seu dispositivo virtual StorSimple.

> [AZURE.NOTE] A Atualização 0.1 é uma atualização com interrupção e reiniciará seu dispositivo. Se E/S estiver em andamento, o dispositivo causará tempo de inatividade.

## Novidades na Atualização 0.1

A Atualização 0.1 contém as seguintes correções de bug e aprimoramentos.

- **Resiliência em torno de interrupções de nuvem**: esta versão tem várias correções de bug em torno da recuperação de desastres, backup, restauração e camadas no caso de uma interrupção da conectividade da nuvem. 

- **Melhor desempenho de restauração**: esta versão tem correções de bug que reduziram significativamente o tempo de conclusão dos trabalhos de restauração.

- **Otimização de reclamação de espaço automatizada**: Quando os dados são excluídos em volumes escassamente provisionados, os blocos de armazenamento não utilizados deverão ser recuperados. Esta versão aprimorou o processo de recuperação de espaço da nuvem, o que resultou na disponibilização mais rápida do espaço não utilizado, em comparação com versões anteriores.

- **Novos VHD, VHDX e VMDK**: os novos VHD e VHDX VMDK agora estão disponíveis por meio do portal clássico do Azure. Você pode baixar essas imagens para provisionar novos dispositivos da Atualização 0.1.

- **Melhoria da precisão de status de trabalhos no portal**: na versão anterior do software, os relatórios de status de trabalhos no portal não eram granulares. Esse problema foi corrigido nesta versão.

- **Correções de bug relacionados à junção e renomeação de domínios** do dispositivo.


## Problemas resolvidos na Atualização 0.1

A tabela a seguir fornece um resumo dos problemas corrigidos nesta versão.

| Nº | Recurso | Problema |
|------|--------------------------------------|---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| 1 | VMDK | Em algumas versões do VMware, o disco do sistema operacional era visto como esparso, causando alertas e interrompendo as operações normais. Isso foi corrigido nesta versão. |
| 2 | Servidor iSCSI | Na última versão, o usuário foi solicitado a especificar um gateway para cada interface de rede habilitada do seu dispositivo virtual StorSimple. Esse comportamento mudou nesta versão para que o usuário tenha que configurar pelo menos um gateway para todas as interfaces de rede habilitadas. |
| 3 | Pacote de suporte | Na versão anterior do software, a coleção de pacotes de suporte falhou quando os tamanhos dos pacote foram maiores que 1 GB. Esse problema foi corrigido nesta versão. |
| 4 | Acesso à nuvem | Na última versão, se a StorSimple Virtual Array não tivesse conectividade de rede e fosse reiniciada, a interface do usuário local teria problemas de conectividade. Esse problema foi corrigido nesta versão. |
| 5 | Gráficos de monitoramento | Na versão anterior, após um failover de dispositivo, os gráficos de utilização de capacidade da nuvem exibiam valores incorretos no portal clássico do Azure. Isso foi corrigido na versão atual. |



## Problemas conhecidos na Atualização 0.1

A tabela a seguir fornece um resumo dos problemas conhecidos para a StorSimple Virtual Array e inclui os problemas observados das versões anteriores. **Os problemas observados nesta versão estão marcados com um asterisco. Quase todos os problemas nesta lista foram herdados da versão da StorSimple Virtual Array.**


| Nº | Recurso | Problema | Solução alternativa/comentários |
|-----|--------------------------|----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| **1.** | Atualizações | Os dispositivos virtuais criados na versão de visualização não podem ser atualizados para uma versão de Disponibilidade Geral com suporte. | Esses dispositivos virtuais devem fazer failover para a versão de Disponibilidade Geral usando um fluxo de trabalho de DR (Recuperação de Desastre). |
| **2.** | Disco de dados provisionado | Após você ter provisionado um disco de dados de um determinado tamanho especificado e criado o dispositivo virtual StorSimple correspondente, você não deve expandir nem reduzir o disco de dados. A tentativa de fazer isso resultará na perda de todos os dados nas camadas locais do dispositivo. | |
| **3.** | Política de grupo | Quando um dispositivo é associado a um domínio, a aplicação de uma política de grupo pode afetar a operação do dispositivo. | Certifique-se de que a matriz virtual esteja em sua própria OU (unidade organizacional) do Active Directory e que nenhum GPO (objeto de política de grupo) seja aplicado a ela.|
| **4.** | Interface do Usuário da Web local | Se os recursos de segurança aprimorados estão habilitados no Internet Explorer (IE ESC), algumas páginas da interface do usuário da Web local, como Solução de Problemas ou Manutenção, podem não funcionar corretamente. Os botões nessas páginas também podem não funcionar. | Desligue os recursos de segurança reforçada do Internet Explorer.|
| **5.** | Interface do Usuário da Web local | Em uma máquina virtual de Hyper-V, as interfaces de rede na interface do usuário da Web são exibidas como interfaces de 10 Gbps. | Esse comportamento é um reflexo do Hyper-V. O Hyper-V sempre mostra 10 Gbps para adaptadores de rede virtual. |
| **6.** | Compartilhamentos ou volumes em camadas | Não há suporte para bloqueio de intervalo de bytes para aplicativos que funcionam com os volumes em camadas do StorSimple. Se o bloqueio de intervalo de bytes estiver habilitado, a disposição em camadas do StorSimple não funcionará. | As medidas recomendadas incluem: <br></br>Desabilitar o bloqueio de intervalo de bytes na lógica do aplicativo.<br></br>Optar por inserir dados para este aplicativo em volumes fixos localmente em vez de volumes em camadas.<br></br>*Ressalva*: se estiver usando volumes fixados localmente e o bloqueio de intervalo de bytes estiver habilitado, esteja ciente de que o volume fixo local pode ficar online antes mesmo da restauração ser concluída. Nesses casos, se uma restauração está em andamento, você deve aguardar a restauração ser concluída. |
| **7.** | Compartilhamentos em camadas | Trabalhar com arquivos grandes pode resultar em uma divisão em camadas lenta. | Ao trabalhar com arquivos grandes, recomendamos que o maior arquivo seja inferior a 3% do tamanho do compartilhamento. |
| **8.** | Capacidade utilizada para compartilhamentos | Você pode ver o consumo de compartilhamento na ausência de quaisquer dados no compartilhamento. Isso ocorre porque a capacidade utilizada para compartilhamentos inclui metadados. | |
| **9.** | Recuperação de desastre | Você só pode executar a recuperação de desastres de um servidor de arquivos no mesmo domínio que o dispositivo de origem. Não há suporte nesta versão para a recuperação de desastres para um dispositivo de destino em outro domínio. | Isso será implementado em uma versão posterior. |
| **10.** | Azure PowerShell | Os dispositivos virtuais StorSimple não podem ser gerenciados por meio do Azure PowerShell nesta versão. | Todo o gerenciamento dos dispositivos virtuais deve ser feito por meio do portal clássico do Azure e da interface do usuário da Web local. |
| **11.** | Alteração de senha | O console do dispositivo de matriz virtual só aceita a entrada no formato de teclado pt-BR. | |
| **12.** | CHAP | Não é possível remover as credenciais CHAP depois de criadas. Além disso, se você modificar as credenciais CHAP, precisará colocar os volumes offline e colocá-los novamente online para que a alteração tenha efeito. | Isso será corrigido em uma versão futura. |
| **13.** | Servidor iSCSI | O 'Armazenamento usado' exibido para um volume iSCSI pode ser diferente no serviço StorSimple Manager e no host iSCSI. | O host iSCSI tem o modo de exibição do sistema de arquivos.<br></br>O dispositivo detecta os blocos atribuídos quando o volume está no tamanho máximo.|
| **14.** | Servidor de arquivos* | Se um arquivo em uma pasta tiver um Fluxo de Dados Alternativo (ADS) associado a ele, o ADS não será copiado ou restaurado por meio da recuperação de desastres, clonagem e Recuperação no Nível do Item.| |


## Próxima etapa

[Instale a Atualização 0.1](storsimple-ova-install-update-01.md) em sua StorSimple Virtual Array.

<!---HONumber=AcomDC_0525_2016-->