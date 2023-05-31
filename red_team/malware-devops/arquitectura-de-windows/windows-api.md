# Windows API

## Introducción

La API de Windows proporciona a los desarrolladores una forma de que sus aplicaciones interactúen con el sistema operativo Windows. Por ejemplo, si la aplicación necesita mostrar algo en la pantalla, modificar un archivo o consultar el registro, todas estas acciones se pueden realizar a través de la API de Windows. La API de Windows está muy bien documentada por Microsoft y puede consultarse aquí.

## Tipos de datos de Windows&#x20;

La API de Windows tiene muchos tipos de datos aparte de los conocidos (por ejemplo, int, float). Los tipos de datos están documentados y pueden consultarse en ek siguiente enlasce:

{% embed url="https://learn.microsoft.com/en-us/windows/win32/winprog/windows-data-types" %}

A continuación se enumeran algunos de los tipos de datos más comunes:

### DWORD

Un entero sin signo de 32 bits, tanto en sistemas de 32 bits como de 64 bits, utilizado para representar valores desde 0 hasta (2^32 - 1).

```c
DWORD dwVariable = 42;
```

### size\_t

Se utiliza para representar el tamaño de un objeto. Es un entero sin signo de 32 bits en sistemas de 32 bits que representa valores desde 0 hasta (2^32 - 1). Por otro lado, es un entero sin signo de 64 bits en sistemas de 64 bits que representa valores desde 0 hasta (2^64 - 1).

```c
SIZE_T sVariable = sizeof(int);
```

### VOID

Indica la ausencia de un tipo de dato específico.

```c
void* pVariable = NULL; // This is the same as PVOID
```

### PVOID

Puntero de 32 bits o 4 bytes de cualquier tipo de datos en sistemas de 32 bits. Alternativamente, un puntero de 64 bits u 8 bytes de cualquier tipo de datos en sistemas de 64 bits.

```c
PVOID pVariable = &SomeData;
```

### HANDLE&#x20;

Valor que especifica un objeto concreto que el sistema operativo está gestionando (por ejemplo, archivo, proceso, hilo).

```c
HANDLE hFile = CreateFile(...);
```

### HMODULE

Manejador de un módulo. Es la dirección base del módulo en memoria. Un ejemplo de MÓDULO puede ser un archivo DLL o EXE.

```c
HMODULE hModule = GetModuleHandle(...);
```

### LPCSTR/PCSTR

Un puntero a una cadena constante null de caracteres Windows de 8 bits (ANSI). La "L" significa "long" que se deriva del período de programación de Windows de 16 bits, hoy en día no afecta al tipo de datos, pero la convención de nomenclatura sigue existiendo. La "C" significa "constante" o variable de sólo lectura. Ambos tipos de datos son equivalentes a `const char*`.

```c
LPCSTR  lpcString   = "Hello, world!";
PCSTR   pcString    = "Hello, world!";
```

### LPSTR/PSTR

Igual que LPCSTR y PCSTR, la única diferencia es que LPSTR y PSTR no apuntan a una variable constante, y en su lugar apuntan a una cadena legible y escribible. Ambos tipos de datos son equivalentes a `char*`.

```c
LPSTR   lpString    = "Hello, world!";
PSTR    pString     = "Hello, world!";
```

### LPCWSTR\PCWSTR

Un puntero a una cadena constante terminada en cero de caracteres Windows Unicode de 16 bits (Unicode). Ambos tipos de datos son equivalentes a `const wchar*`.

```c
LPCWSTR     lpwcString  = L"Hello, world!";
PCWSTR      pcwString   = L"Hello, world!";
```

### PWSTR\LPWSTR

Igual que LPCWSTR y PCWSTR, la única diferencia es que 'PWSTR' y 'LPWSTR' no apuntan a una variable constante, y en su lugar apuntan a una cadena legible y escribible. Ambos tipos de datos son equivalentes a wchar\*.

```c
LPWSTR  lpwString   = L"Hello, world!";
PWSTR   pwString    = L"Hello, world!";
```

### wchar\_t

Igual que wchar, se utiliza para representar caracteres anchos.

```c
wchar_t     wChar           = L'A';
wchar_t*    wcString        = L"Hello, world!";
```

### ULONG\_PTR

Representa un entero sin signo que tiene el mismo tamaño que un puntero en la arquitectura especificada, lo que significa que en sistemas de 32 bits un `ULONG_PTR` tendrá un tamaño de 32 bits, y en sistemas de 64 bits, tendrá un tamaño de 64 bits. `ULONG_PTR` se utilizará en la manipulación de expresiones aritméticas que contengan punteros (por ejemplo, PVOID). Antes de ejecutar cualquier operación aritmética, un puntero será sometido a una conversión de tipo a `ULONG_PTR`. Este enfoque se utiliza para evitar la manipulación directa de punteros que puede dar lugar a errores de compilación.

```c
PVOID Pointer = malloc(100);
// Pointer = Pointer + 10; // not allowed
Pointer = (ULONG_PTR)Pointer + 10; // allowed
```

## Tipos de datos Puntero

La API de Windows permite a un desarrollador declarar un tipo de datos directamente o un puntero al tipo de datos. Esto se refleja en los nombres de los tipos de datos, donde los tipos de datos que empiezan por "P" representan punteros al tipo de datos real, mientras que los que no empiezan por "P" representan el tipo de datos real en sí.

Esto será útil más adelante cuando se trabaja con las API de Windows que tienen parámetros que son punteros a un tipo de datos. Los siguientes ejemplos muestran cómo se relacionan los tipos de datos "P" con sus equivalentes que no son punteros.

* `PHANDLE` es lo mismo que `HANDLE*`.&#x20;
* `PSIZE_T` es lo mismo que `SIZE_T*`.&#x20;
* `PDWORD` es lo mismo que `DWORD*`.

## Funciones ANSI y UNICODE

La mayoría de las funciones de la API de Windows tienen dos versiones que terminan con "A" o con "W". Por ejemplo, existe [CreateFileA](https://learn.microsoft.com/en-us/windows/win32/api/fileapi/nf-fileapi-createfilea) y [CreateFileW](https://learn.microsoft.com/en-us/windows/win32/api/fileapi/nf-fileapi-createfilew). Las funciones que <mark style="color:green;">terminan en "A" indican "ANSI"</mark>, mientras que las que <mark style="color:green;">terminan en "W" representan Unicode o "Wide"</mark>.

La principal diferencia a tener en cuenta es que las funciones ANSI aceptarán tipos de datos ANSI como parámetros, cuando proceda, mientras que las funciones Unicode aceptarán tipos de datos Unicode. Por ejemplo:

* El primer parámetro de `CreateFileA` es un LPCSTR, que es un puntero a una cadena constante terminada en cero de caracteres ANSI de Windows de 8 bits.
* Por otro lado, el primer parámetro para CreateFileW es LPCWSTR, un puntero a una cadena constante terminada en cero de caracteres Unicode de 16 bits.

Además, el número de bytes necesarios variará en función de la versión utilizada.

{% code overflow="wrap" %}
```c
char str1[] = "maldev"; // 7 bytes (maldev (6) + byte nulo).
wchar str2[] = L "maldev"; // 14 bytes, cada carácter es de 2 bytes (El byte nulo también es de 2 bytes)
```
{% endcode %}

## Parámetros In y Out

Las API de Windows tienen parámetros de [in](https://learn.microsoft.com/en-us/windows/win32/midl/in) y [out](https://learn.microsoft.com/en-us/windows/win32/midl/out-idl) . Un parámetro <mark style="color:green;">**IN**</mark> es un parámetro que se pasa a una función y se utiliza como entrada. Mientras que un parámetro <mark style="color:green;">**OUT**</mark> es un parámetro utilizado para devolver un valor a la entidad que llama a la función. Los parámetros de salida suelen pasarse por referencia a través de punteros.

Por ejemplo, el siguiente fragmento de código muestra una función `HackTheWorld` que recibe un puntero entero y le asigna el valor 123. Esto se considera un parámetro de salida, ya que el puntero es un parámetro de entrada. Esto se considera un parámetro de salida ya que el parámetro devuelve un valor.

```c
BOOL HackTheWorld(OUT int* num){
    // Setting the value of num to 123
    *num = 123;
    // Returning a boolean value
    return TRUE;
}

int main(){
    int a = 0;

    // 'HackTheWorld' will return true
    // 'a' will contain the value 123
    HackTheWorld(&a);
}
```

Tenga en cuenta que el uso de las palabras clave <mark style="color:green;">**OUT**</mark> o <mark style="color:green;">**IN**</mark> tiene por objeto facilitar a los desarrolladores la comprensión de lo que la función espera y lo que hace con estos parámetros. Sin embargo, vale la pena mencionar que la exclusión de estas palabras clave no afecta a si el parámetro se considera un parámetro de salida o de entrada.
