---
description: Portable Executable
---

# PE

## Introducción&#x20;

Los <mark style="color:green;">**Portable Executable (PE)**</mark> es el formato de archivo para ejecutables en Windows. Algunos ejemplos de extensiones de archivos PE son `.exe`, `.dll`, `.sys` y `.scr`. En esta página se abordará de forma introductoria la estructura PE, que es importante conocer al construir o realizar ingeniería inversa de malware.



## Estructura de los PE

La siguiente imagen muestra una estructura simplificada de un Portable Executable. Cada encabezado que se muestra en la imagen se define como una estructura de datos que contiene información sobre el archivo PE.

<figure><img src="../../.gitbook/assets/image (10).png" alt=""><figcaption></figcaption></figure>

### **Cabecera DOS (IMAGE\_DOS\_HEADER)** <a href="#623bcda4-21f5-4dda-8598-06e30235e609" id="623bcda4-21f5-4dda-8598-06e30235e609"></a>

El primer encabezado de un archivo PE siempre está precedido por dos bytes, <mark style="color:green;">**0xAD**</mark> y <mark style="color:green;">**0x5A**</mark>, comúnmente conocidos como MZ. Estos bytes representan la firma del encabezado DOS, que se utiliza para confirmar que el archivo que se está analizando o inspeccionando es un archivo PE válido. El encabezado DOS es una estructura de datos definida de la siguiente manera:

{% code overflow="wrap" %}
```
typedef struct _IMAGE_DOS_HEADER {    // Encabezado DOS de un archivo .EXE
    WORD e_magic;                     // Número mágico
    WORD e_cblp;                      // Bytes en la última página del archivo
    WORD e_cp;                        // Páginas en el archivo
    WORD e_crlc;                      // Relocalizaciones
    WORD e_cparhdr;                   // Tamaño del encabezado en párrafos
    WORD e_minalloc;                  // Párrafos adicionales mínimos necesarios
    WORD e_maxalloc;                  // Párrafos adicionales máximos necesarios
    WORD e_ss;                        // Valor SS inicial (relativo)
    WORD e_sp;                        // Valor SP inicial
    WORD e_csum;                      // Suma de comprobación
    WORD e_ip;                        // Valor IP inicial
    WORD e_cs;                        // Valor CS inicial (relativo)
    WORD e_lfarlc;                    // Dirección de archivo de la tabla de reubicación
    WORD e_ovno;                      // Número de overlay
    WORD e_res[4];                    // Palabras reservadas
    WORD e_oemid;                     // Identificador OEM (para e_oeminfo)
    WORD e_oeminfo;                   // Información OEM; e_oemid específico
    WORD e_res2[10];                  // Palabras reservadas
    LONG e_lfanew;                    // Desplazamiento hacia el encabezado NT
} IMAGE_DOS_HEADER, *PIMAGE_DOS_HEADER;
```
{% endcode %}

Los miembros más importantes de la estructura son `e_magic` y `e_lfanew`.

* <mark style="color:green;">**e\_magic**</mark> ocupa 2 bytes y tiene un valor fijo de <mark style="color:green;">0x5A4D</mark> o <mark style="color:green;">MZ</mark>.
* <mark style="color:green;">**e\_lfanew**</mark> es un valor de 4 bytes que contiene el desplazamiento al inicio del encabezado NT. Cabe destacar que e\_lfanew siempre se encuentra en un desplazamiento de <mark style="color:green;">0x3C</mark>.

### DOS Stub&#x20;

Antes de pasar a la estructura del encabezado NT, está el DOS stub, que es un mensaje de error que imprime "Este programa no se puede ejecutar en modo DOS" en caso de que el programa se cargue en modo DOS o "Disk Operating Mode". Vale la pena señalar que el mensaje de error puede ser <mark style="color:green;">modificado por el programador en tiempo de compilación</mark>. Esto no es un encabezado PE, pero es bueno tenerlo en cuenta.

### Encabezado NT (IMAGE\_NT\_HEADERS)&#x20;

El encabezado NT es esencial ya que incorpora otros dos encabezados de imagen: `FileHeader` y `OptionalHeader`, que incluyen una gran cantidad de información sobre el archivo PE. Al igual que el encabezado DOS, el encabezado NT contiene un miembro de firma que se utiliza para verificarlo. Por lo general, el elemento de firma es igual a la cadena "PE", que está representada por los bytes <mark style="color:green;">**0x50**</mark> y <mark style="color:green;">**0x45**</mark>. Pero como la firma es de tipo DWORD, la firma se representa como 0x50450000, que sigue siendo "PE", excepto que está rellenada con dos bytes nulos. El encabezado NT se puede acceder utilizando el miembro e\_lfanew dentro del encabezado DOS.

La estructura del encabezado NT varía según la arquitectura de la máquina.

#### Estructura para 32 bits

{% code overflow="wrap" %}
```
typedef struct _IMAGE_NT_HEADERS {
  DWORD                   Signature;
  IMAGE_FILE_HEADER       FileHeader;
  IMAGE_OPTIONAL_HEADER32 OptionalHeader;
} IMAGE_NT_HEADERS32, *PIMAGE_NT_HEADERS32;
```
{% endcode %}

#### Estructura para 64 bits

```
typedef struct _IMAGE_NT_HEADERS64 {
    DWORD                   Signature;
    IMAGE_FILE_HEADER       FileHeader;
    IMAGE_OPTIONAL_HEADER64 OptionalHeader;
} IMAGE_NT_HEADERS64, *PIMAGE_NT_HEADERS64;
```

La única diferencia entre ambas versiones de bits es el `OptionalHeader`, `IMAGE_OPTIONAL_HEADER32` e `IMAGE_OPTIONAL_HEADER64`.



