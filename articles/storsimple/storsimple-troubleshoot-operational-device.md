<properties 
   pageTitle="Solucionar problemas de um dispositivo operacional do StorSimple"
   description="Descreve como diagnosticar e corrigir os erros que ocorrem em um dispositivo StorSimple que esteja operacional."
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
   ms.date="04/09/2015"
   ms.author="v-sharos" />

# Solucionar problemas de um dispositivo operacional do StorSimple

## Visão geral

Este artigo oferece orientações úteis de solução de problemas para resolver problemas de configuração que você poderá encontrar depois que seu dispositivo StorSimple estiver implantado e operacional. Descreve problemas comuns, possíveis causas e etapas recomendadas para ajudá-lo a resolver problemas que podem ocorrer durante a execução do StorSimple. Essas informações se aplicam ao dispositivo físico local do StorSimple e ao dispositivo virtual do StorSimple.

## Processo do assistente de instalação de dispositivos operacionais

Você pode usar o assistente de instalação (Invoke-HcsSetupWizard) para verificar a configuração do dispositivo e tomar a ação corretiva, se necessário.

Quando você executa o assistente de instalação em um dispositivo previamente configurado e operacional, o fluxo do processo é diferente. Você pode alterar apenas as seguintes entradas:

- Endereço IP, máscara de sub-rede e gateway
- Servidor DNS primário
- Servidor NTP primário
- Configuração do proxy da Web opcional

O assistente de instalação não executa as operações relacionadas à coleta de senha e ao registro do dispositivo.

## Erros que ocorrem durante as execuções subsequentes do assistente de instalação

A tabela a seguir descreve os erros que você poderá encontrar ao executar o assistente de instalação em um dispositivo operacional, as possíveis causas dos erros e as ações recomendadas para resolvê-los.

| Nº | Mensagem ou condição de erro | Possíveis causas | Ação recomendada |
| --- | -------------------------- | --------------- | ------------------ |
| 1 | Erro 350032: este dispositivo já foi desativado. | Você verá esse erro se executar o assistente de instalação em um dispositivo que esteja desativado. | [Contate o Suporte da Microsoft Support](https://msdn.microsoft.com/library/azure/dn757750.aspx) para as próximas etapas. Um dispositivo desativado não pode ser colocado em serviço. Uma redefinição de fábrica pode ser necessária antes que seja possível ativar o dispositivo novamente. |
| 2 | Invoke-HcsSetupWizard : ERROR_INVALID_FUNCTION(Exception from HRESULT: 0x80070001) | A atualização do servidor DNS está falhando. As configurações de DNS são configurações globais e são aplicadas a todas as interfaces de rede habilitadas. | Habilite a interface e aplique as configurações de DNS novamente. Isso poderá afetar a rede para outras interfaces habilitadas, já que essas configurações são globais. |
| 3 | O dispositivo parece estar online no portal de serviço do StorSimple Manager, mas quando você tenta concluir a instalação mínima e salvar a configuração, a operação falha. | Durante a instalação inicial, o proxy da Web não foi configurado, mesmo havendo um servidor proxy real pronto. | Use o cmdlet [Test-HcsmConnection](https://msdn.microsoft.com/library/azure/eedae62d-0957-4005-b346-9248724f90e0#sec05) para localizar o erro. [Contate o Suporte da Microsoft](https://msdn.microsoft.com/library/azure/dn757750.aspx) se você não puder corrigir o problema. |
| 4 | Invoke-HcsSetupWizard: Value não está no intervalo esperado. | Uma máscara de sub-rede incorreta gera esse erro. As possíveis causas são: <ul><li> A máscara de sub-rede está ausente ou vazia.</li><li>O formato do prefixo Ipv6 está incorreto.</li><li>A interface está habilitada para a nuvem, mas o gateway está ausente ou incorreto.</li></ul>Observe que DATA 0 será automaticamente habilitado para nuvem se configurado por meio do assistente de instalação. | Para determinar o problema, use a sub-rede 0.0.0.0 ou 256.256.256.256 e, em seguida, examine a saída. Insira os valores corretos para a máscara de sub-rede, o gateway e o prefixo Ipv6, como necessário. |
 
## Próximas etapas
Se você não puder resolver o problema, [contate o Suporte da Microsoft](https://msdn.microsoft.com/library/azure/dn757750.aspx) para obter ajuda.

<!---HONumber=July15_HO4-->