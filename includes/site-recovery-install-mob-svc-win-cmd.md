1. Copie o instalador para uma pasta local (digamos, C:\Temp) no servidor que você deseja proteger e execute os seguintes comandos em um prompt de comando com privilégios elevados.

  ```
  cd C:\Temp
  ren Microsoft-ASR_UA*Windows*release.exe MobilityServiceInstaller.exe
  MobilityServiceInstaller.exe /q /xC:\Temp\Extracted
  cd C:\Temp\Extracted.
  ```
2. Agora podemos instalar o serviço de mobilidade usando a linha de comando a seguir

  ```
  UnifiedAgent.exe /Role "Agent" /CSEndpoint "IP Address of Configuration Server" /PassphraseFilePath <Full path to the passphrase file>``
  ```

#### <a name="mobility-service-installer-command-line-arguments"></a>Argumentos de linha de comando do instalador de serviço de mobilidade

```
Usage :
UnifiedAgent.exe [/Role <Agent/MasterTarget>] [/InstallLocation <Installation Directory>] [/CSIP <IP address>] [/PassphraseFilePath <Passphrase file path>] [/LogFilePath <Log File Path>]<br/>
```

  | Parâmetro|Tipo|Descrição|Valores possíveis|
  |-|-|-|-|
  |/Role|Obrigatório|Especifica se o serviço de Mobilidade deve ser instalado|Agente </br> MasterTarget|
  |/InstallLocation|Obrigatório|Local onde o Serviço de Mobilidade será instalado|Qualquer pasta no computador|
  |/CSIP|Obrigatório|Endereço IP do Servidor de Configuração| Qualquer endereço IP válido|
  |/PassphraseFilePath|Obrigatório|Local de armazenamento da Senha |Qualquer caminho de arquivo UNC ou Local válido|
  |/LogGilePath|Opcional|Local para o log de instalação|Qualquer pasta válida no computador|

#### <a name="sample-usage"></a>Exemplo de uso

```
  UnifiedAgent.exe /Role "Agent" /CSEndpoint "I192.168.2.35" /PassphraseFilePath "C:\Temp\MobSvc.passphrase"
```


<!--HONumber=Jan17_HO3-->


