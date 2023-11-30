# Guia de uso de Invoke-AtomicRedTeam

## <mark style="color:green;">Instalación</mark>

**Nota**: Requiere powershell 5.0 en adelante

### Usando powershell galley

Nota: Casi nunca detienen este tipo de instalación en otros medio

```powershell
Install-Module -Name invoke-atomicredteam,powershell-yaml -Scope CurrentUser
```

### Por IEX (Requiere salida a internet)

Nota: Normalmente con sistemas proxeados con esta forma puedes darle bypass

{% code overflow="wrap" %}
```powershell
IEX (IWR 'https://raw.githubusercontent.com/redcanaryco/invoke-atomicredteam/master/install-atomicredteam.ps1' -UseBasicParsing); Install-AtomicRedTeam -getAtomics -Force -noPayloads
```
{% endcode %}

#### En caso de requerir pasar pasar SSL

{% code overflow="wrap" %}
```powershell
[Net.ServicePointManager]::SecurityProtocol = [Net.SecurityProtocolType]::Tls12; IEX (IWR 'https://raw.githubusercontent.com/redcanaryco/invoke-atomicredteam/master/install-atomicredteam.ps1' -UseBasicParsing); Install-AtomicRedTeam -getAtomics -Force -noPayloads
```
{% endcode %}

### Por si se quiere cambiar la Ubicación por defecto

Por defecto se instala en la carpeta: **C:\AtomicRedTeam\atomics**

```powershell
Install-AtomicRedTeam -InstallPath "c:\tools"
```

### Instalación Ofline

Tener descargado los scripts de [https://github.com/redcanaryco/invoke-atomicredteam.git](https://github.com/redcanaryco/invoke-atomicredteam.git)

Tener también la carpeta C:\AtomicRedTeam\atomics ya creada

```powershell
Import-Module .\install-atomicredteam.ps1 ;
```

### ExecutionPolicy Bypass

Por si se tienen la ejecución de scripts deshabilitada, primero ejecutar

```powershell
powershell -ExecutionPolicy Bypass 
```

```powershell
powershell -exec bypass 
```

```powershell
powershell -Version 2 -nop -exec bypass
```

// Para Cambiar la ubicación de los atomics $PSDefaultParameterValues = @{"Invoke-AtomicTest:PathToAtomicsFolder"="C:\Users\myuser\Documents\code\atomic-red-team\atomics"}

## <mark style="color:green;">Ejecución de Atomics</mark>

### Solo mostrar información&#x20;

Muestra información con formato verbolse

```powershell
Invoke-AtomicTest T1089 -ShowDetails
```

### Ejecución de comandos

Ejecuta la TTP sin comandos de limpieza

```powershell
Invoke-AtomicTest T1089
```

Para ejecutar solo los Test específicos

```powershell
Invoke-AtomicTest T1089 -TestNumbers 1,2
```

Otra firma de ejecutar solo los Test que se quieren

```powershell
Invoke-AtomicTest T1089-1,2
```

Ejecuta los comandos de limpieza de la TTP ( No ejecuta la TTP)

```powershell
Invoke-AtomicTest T1089 -Cleanup
```

## Logger

Por defecto Invoke-AtomicTest guarda todos las ttps usadas en ($env:TEMP, %tmp%, or \tmp) // Para que no se guarden se puede utilizar

```powershell
Invoke-AtomicTest T1089 -NoExecutionLog
```

Guarda en registro (sin salidas de comando) a un fichero csv

```powershell
Invoke-AtomicTest T1089 -ExecutionLogPath 'C:\Temp\mylog.csv'
```

### Logger con mas información

{% code overflow="wrap" %}
```powershell
Invoke-AtomicTest T1016 -LoggingModule "Attire-ExecutionLogger" -ExecutionLogPath T1016-Windows.json
```
{% endcode %}

Genera un json e importas en la herramienta VECTR para verlo gráficamente

{% embed url="https://github.com/SecurityRiskAdvisors/VECTR" %}

## Auto Tool

Descargas la herramienta y luego ejecutas

### Auto Install

```
.\AutoAtomic.ps1 -i -p
```

### Auto atomic

```
.\AutoAtomic.ps1 -TestFile ttps.txt
```

Requiere de un archivo ttps.txt donde se especifique cuales atomics se van a ejecutar, por ejemplo:

{% code title="ttps.txt" %}
```
T1033
T1087.002
T1497.001-2
```
{% endcode %}

{% embed url="https://github.com/Fatake/AutoAtomic" %}
