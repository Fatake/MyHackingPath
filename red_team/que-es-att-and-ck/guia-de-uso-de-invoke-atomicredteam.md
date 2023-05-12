# Guia de uso de Invoke-AtomicRedTeam

## Instalación



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

Por defecto se instala en la carpeta: **C:\AtomicRedTeam\atomics**&#x20;

```powershell
Install-AtomicRedTeam -InstallPath "c:\tools"
```

```powershell
Install-AtomicsFolder -InstallPath "c:\tools"
```

### Instalación Ofline

Tener descargado los scripts de [https://github.com/redcanaryco/invoke-atomicredteam.git](https://github.com/redcanaryco/invoke-atomicredteam.git)

Tener también la carpeta C:\AtomicRedTeam\atomics ya creada

```powershell
Import-Module .\install-atomicredteam.ps1 ;
```

### En caso de no poder ejecutar scripts en la máquina

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

## Ejecución de Atomics

Muestra sin ejecutar el atomic los comandos que se utilizarán en ese atomic Información completa

```
Invoke-AtomicTest T1089 -ShowDetails
```

// Muesta solo los nombres y numero de test que tiene el atomic sin ejecutarlos Invoke-AtomicTest T108 -ShowDetailsBrief

// Ejecuta la TTP sin comandos de limpieza&#x20;

Invoke-AtomicTest T1089

// Ejecuta la TTP con mas información en las salidas&#x20;

Invoke-AtomicTest T1089 -Interactive

// Para ejecutar solo los Test específicos

&#x20;Invoke-AtomicTest T1089 -TestNumbers 1,2

// Otra foprma de ejecutar solo los Test que se quieren&#x20;

Invoke-AtomicTest T1089-1,2

// Ejecuta los comandos de limpieza de la TTP ( No ejecuta la TTP)&#x20;

Invoke-AtomicTest T1089 -Cleanup

## Logger

Por defecto Invoke-AtomicTest guarda todos las ttps usadas en ($env:TEMP, %tmp%, or \tmp) // Para que no se guarden se puede utilizar&#x20;

Invoke-AtomicTest T1089 -NoExecutionLog

// Guarda en registro (sin salidas de comando) a un fichero csv&#x20;

Invoke-AtomicTest T1089 -ExecutionLogPath 'C:\Temp\mylog.csv'

// Redirección de la salida para guardar el registro&#x20;

Invoke-AtomicTest T1089 \*>&1 | Tee-Object atomic-out.txt -Append
