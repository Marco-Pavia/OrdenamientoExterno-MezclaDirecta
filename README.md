# Ordenamiento Externo en Java: Mezcla Directa (Straight Merge)

Implementación en Java de la fase de **intercalación de bloques** del algoritmo de ordenamiento externo de **Mezcla Directa**. El método `intercalarBloques` lee dos archivos auxiliares (`F1` y `F2`) que contienen bloques ya ordenados de tamaño `L`, compara sus elementos frente a frente y los une de forma ordenada en un archivo de salida (`F3`), usando un contador por bloque para controlar cuántos elementos se han extraído de cada archivo.

**Autor:** Marco Antonio Pavia Flores.
**Materia:** AED-1026 Estructura de Datos — Unidad 5: Ordenamiento Externo
**Docente:** Víctor Hugo Vásquez Herrera
**Institución:** Instituto Tecnológico Superior de Xalapa

## Lenguaje de programación

**Java** (JDK 8 o superior; se recomienda JDK 17 o superior). No requiere librerías externas: solo clases nativas de `java.io`.

## Estructura del proyecto

```text
OrdenamientoExterno_MezclaDirecta/
├── src/
│   └── MezclaDirectaPractica.java
├── f1.txt
├── f2.txt
├── f3.txt                                   (se genera al ejecutar)
├── OrdenamientoExterno_MezclaDirecta.iml
└── README.md
```

| Archivo | Descripción |
|---|---|
| `MezclaDirectaPractica.java` | Contiene el método `intercalarBloques` con la lógica de mezcla y el método `main` que mezcla `f1.txt` y `f2.txt` con `L = 1`. |
| `f1.txt`, `f2.txt` | Archivos auxiliares con un número por renglón. Se deben crear **antes** de ejecutar (en el ejemplo: `45` y `12`). |
| `f3.txt` | Archivo de salida con los bloques intercalados. Lo crea el programa. |
| `OrdenamientoExterno_MezclaDirecta.iml` | Archivo de configuración del módulo de IntelliJ IDEA. |

La clase no declara paquete, por lo que se ejecuta directamente como `MezclaDirectaPractica`. Los archivos `.txt` deben estar en la **carpeta raíz** del proyecto (junto a `src`, no dentro de ella). Las carpetas `.idea/` y `out/` son locales del IDE y no es necesario subirlas al repositorio.

## Fundamento teórico

En la Mezcla Directa, los datos se distribuyen en dos archivos auxiliares `F1` y `F2` en bloques de longitud `L`; después se mezcla el bloque *i* de `F1` con el bloque *i* de `F2` para producir bloques ordenados de longitud `2L` en `F3`. Repitiendo el proceso con `L = 1, 2, 4, 8, …` se ordena todo el archivo en ⌈log₂ n⌉ pasadas. Este programa implementa **una** pasada de mezcla.

Lógica de `intercalarBloques` (para cada par de bloques):

| Caso | Condición | Acción |
|---|---|---|
| 1 | `elementosEscritos1 < L` **y** `elementosEscritos2 < L` | Compara `val1` con `val2` y escribe el menor (si hay empate, el de `F1`, lo que mantiene la estabilidad). |
| 2 | El bloque de `F2` ya llegó a `L` | Vacía lo que sobra del bloque de `F1`. |
| 3 | El bloque de `F1` ya llegó a `L` | Vacía lo que sobra del bloque de `F2`. |

### Código completado en los TODO

```java
// TODO 1: condición para saber si el número de F1 es menor o igual al de F2
if (val1 <= val2) {
    escritor.println(val1);              // Guardamos el de F1 en la salida
    linea1 = lector1.readLine();         // Avanzamos al siguiente numero de F1
    elementosEscritos1++;                // Aumentamos el contador del bloque 1
} else {
    // TODO 2: si el menor fue el de F2, lo mismo pero con F2
    escritor.println(val2);
    linea2 = lector2.readLine();
    elementosEscritos2++;
}
```

```java
// CASO 3: el bloque de F1 ya se acabó primero, vaciamos lo que sobró de F2
else {
    // TODO 3: las 3 líneas para vaciar el elemento restante de F2
    escritor.println(linea2);
    linea2 = lector2.readLine();
    elementosEscritos2++;
}
```

### Ajustes adicionales a la lógica base

Con solo los tres `TODO` resueltos, el código base mezcla correctamente mientras ambos bloques tienen elementos, pero puede dejar números sin escribir. Por eso se agregaron dos ajustes:

1. **Reinicio de contadores** al inicio de cada vuelta del `while`: cuando ambos bloques ya llegaron a `L`, los contadores vuelven a 0 para comenzar el siguiente par de bloques. Sin esto solo se podría mezclar un par de bloques.
2. **Vaciado final** después del `while`: el ciclo termina en cuanto uno de los archivos se acaba, así que se copia a la salida lo que quedó en el otro. Sin esto, con `f1.txt = 45` y `f2.txt = 12`, el `45` se perdería y `f3.txt` quedaría solo con el `12`.

```java
// Ajuste 1 (al inicio del while)
if (elementosEscritos1 == L && elementosEscritos2 == L) {
    elementosEscritos1 = 0;
    elementosEscritos2 = 0;
}
```

```java
// Ajuste 2 (después del while)
while (linea1 != null) {
    escritor.println(linea1);
    linea1 = lector1.readLine();
}
while (linea2 != null) {
    escritor.println(linea2);
    linea2 = lector2.readLine();
}
```

## Consideraciones sobre los datos de entrada

El programa no valida el contenido de los archivos, por lo que conviene tener en cuenta:

- `f1.txt` y `f2.txt` deben existir en la carpeta raíz; si no, se muestra `Error en E/S de archivos: ...` y no se genera la mezcla.
- Cada renglón debe tener **un número entero**. Una línea vacía o con texto no numérico provoca un `NumberFormatException` al compararla.
- Los bloques de tamaño `L` de cada archivo deben venir **ya ordenados**; el programa mezcla bloques, no ordena los datos desde cero.
- `L` debe ser un entero positivo y coincidir con el tamaño real de los bloques de los archivos.

## Datos de prueba

| Prueba | `f1.txt` | `f2.txt` | `L` |
|---|---|---|---|
| 1 (práctica) | `45` | `12` | 1 |
| 2 | `45`, `8`, `30` | `12`, `50`, `2` | 1 |
| 3 | `5 9 │ 1 4 │ 6 8` | `2 7 │ 3 8 │ 10 11` | 2 |
| 4 | `1 4 │ 6` | `2 3 │ 5 7` | 2 |

(Un número por renglón; la barra `│` solo separa los bloques. Para las pruebas 3 y 4 cambia el último argumento de `intercalarBloques` en `main` a `2`.)

## Cómo clonar, compilar y ejecutar

### Desde la terminal

1. Clona el repositorio y entra a la carpeta:
   ```bash
   git clone https://github.com/Marco-Pavia/OrdenamientoExterno_MezclaDirecta.git
   cd OrdenamientoExterno_MezclaDirecta
   ```
2. Verifica que `f1.txt` y `f2.txt` existan en la carpeta raíz (un número por renglón).
3. Compila desde la carpeta raíz:
   ```bash
   javac -d out src/MezclaDirectaPractica.java
   ```
4. Ejecuta **desde la carpeta raíz** (para que encuentre los `.txt`):
   ```bash
   java -cp out MezclaDirectaPractica
   ```
5. Revisa el archivo generado:
   ```bash
   cat f3.txt              # Linux / macOS
   type f3.txt             # Windows (CMD)
   ```

### Desde IntelliJ IDEA

1. Abre la carpeta del proyecto con *File → Open*.
2. Confirma que `MezclaDirectaPractica.java` esté dentro de `src` (marcada como *Sources Root*) y que el SDK esté configurado en *File → Project Structure → Project SDK*.
3. Verifica que `f1.txt` y `f2.txt` estén en la **raíz del proyecto**, al mismo nivel que `src`.
4. Abre `MezclaDirectaPractica.java` y presiona el botón verde ▶ junto al método `main`. IntelliJ ejecuta por defecto desde la raíz del proyecto, por lo que encuentra los `.txt` sin configuración extra (si no, revisa *Run → Edit Configurations → Working directory*).
5. El archivo `f3.txt` aparece en la raíz del proyecto; si no lo ves, haz clic derecho sobre el proyecto → *Reload from Disk*.

## Ejemplo de ejecución

**Prueba 1** (`java -cp out MezclaDirectaPractica`) con `f1.txt = 45` y `f2.txt = 12`:

```text
Bloque intercalado con exito!
```

Contenido de `f3.txt`:

```text
12
45
```

**Prueba 2** (`L = 1`) con `f1.txt = 45, 8, 30` y `f2.txt = 12, 50, 2`. Consola:

```text
Bloque intercalado con exito!
```

Contenido de `f3.txt`:

```text
12
45
8
50
2
30
```

**Si falta `f1.txt` o `f2.txt`:**

```text
Error en E/S de archivos: f1.txt (No such file or directory)
```

El texto exacto de este mensaje depende del sistema operativo.

## Resultados esperados

La columna «Solo TODO» es lo que produce el código base con únicamente los tres `TODO` completados (sin los ajustes); se incluye para mostrar el efecto de los dos ajustes.

| Prueba | Resultado esperado | Código final (con ajustes) | Solo TODO |
|---|---|---|---|
| 1 | `12 45` | `12 45` | `12` |
| 2 | `12 45 │ 8 50 │ 2 30` | `12 45 8 50 2 30` | `12 45 50 2` |
| 3 | `2 5 7 9 │ 1 3 4 8 │ 6 8 10 11` | `2 5 7 9 1 3 4 8 6 8 10 11` | `2 5 7 9 3 8 10 11` |
| 4 | `1 2 3 4 │ 5 6 7` | `1 2 3 4 5 6 7` | `1 2 3 4 5 7` |

- Con los dos ajustes, el resultado coincide con el esperado en las cuatro pruebas y no se pierde ningún elemento.
- Cada par de bloques de tamaño `L` produce un bloque ordenado de tamaño `2L` (en la prueba 3, bloques de 4 elementos).
