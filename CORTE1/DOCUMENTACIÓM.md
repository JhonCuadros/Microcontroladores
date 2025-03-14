### **DOCUMENTACIÓN: Laboratorio 1**

**INTRODUCCIÓN**

- **Objetivo:**
Este Laboratorio consiste en la implementación de una Unidad Aritmética Lógica (ALU) en un microcontrolador PIC mediante un programa escrito en lenguaje C. La ALU realiza operaciones aritméticas y lógicas básicas (suma, resta, AND, OR, multiplicación y división) entre dos operandos proporcionados por el usuario a través de entradas digitales. Los resultados se visualizan en un conjunto de LEDs conectados al microcontrolador.

- **Herramientas utilizadas:**

  - Microcontrolador PIC18F45K22.

  - PICKit 3 para programación y depuración.

  - Entorno de desarrollo (MPLAB X, XC8 Compiler).

  - Hardware adicional (LEDs, botones, resistencias, etc.).

### **DESCRIPCIÓN GENERAL DEL CODIGO**

**Estructura del programa**

**Codigo Fuente**

- // PIC18F45K22 Configuration Bit Settings

En este aparto es en donde se configuran los bits del microcontrolador controlando aspectos importantes como el tipo de oscilador,la habilitación de protecciones,como el comportamiento del reset y otras caracteristicas especificas del hardware, ademas permite desactivar protecciones innesesarias. Entre las mas importantes estan:


- `CONFIG1H`: Configura el oscilador (`XT`), desactiva el `PLL`, habilita el reloj primario, y desactiva el monitor de fallos del reloj y el cambio entre osciladores interno/externo.
- `CONFIG2L`: Habilita el temporizador de encendido (`PWRTEN`) para un arranque estable.
- `CONFIG2H`: Desactiva el temporizador de vigilancia (`WDTEN`) y configura su postescalador. 
- `CONFIG3H`: Configura multiplexores para periféricos como `CCP2`, `CCP3`, Timer3, y otros.
- `CONFIG4L`: Habilita el reinicio por desbordamiento de pila (STVREN), desactiva la programación de bajo voltaje (`LVP`), y desactiva el conjunto de instrucciones extendido (`XINST`).

**Definición de variables globales y constantes**

Se utilizan varias variables globales y constantes para almacenar datos, controlar el flujo del programa y facilitar la comunicación entre funciones. A continuación, se detalla cada una de ellas:

1. **Constantes**

   `#define _XTAL_FREQ 4000000`:

   **Descripción:** Define la frecuencia del oscilador del microcontrolador en 4 MHz.

   **Uso**: Esta constante es utilizada por las funciones de delay (`__delay_ms` y `__delay_us`) para calcular retardos precisos basados en la frecuencia del oscilador.

2. **Variables globales**

   Las variables globales se declaran fuera de cualquier función, lo que permite que todas las funciones del programa puedan acceder a ellas. A continuación, se describen las variables utilizadas en este codigo:

   **FUNCIONES**

   **`void CONF_PUERTOS(void)`**

   **Descripción:** La función `CONF_PUERTOS` configura los puertos del microcontrolador para su uso en el proyecto. Define qué pines se utilizarán como entradas y cuáles como salidas, además de configurar opciones adicionales como resistencias pull-up.

   **Uso de `void` en la función `CONF_PUERTOS`**

   - Cuando `void` aparece antes del nombre de la función (en este caso, `void CONF_PUERTOS`), indica que la función no devuelve ningún valor.

   - Cuando `void` aparece dentro de los paréntesis después del nombre de la función (en este caso, `CONF_PUERTOS(void)`), indica que la función no acepta ningún parámetro. Es decir, no requiere argumentos para ejecutarse.

   El uso de void aplica también para las demás funciones ya que siguen el mismo patrón: no devuelven ningún valor y no reciben parámetros. A continuación se explicara de manera resumida el proposito de las demas funciones.

   - `void BORRAR_BASURA(void);`: Limpia las variables y registros para evitar basura en los datos.

   - `void LEER_OPERANDO_1(void);`: Lee el primer operando desde el puerto B y lo almacena en la variable `OPERANDO_1`.

   - `void LEER_OPERANDO_2(void);`: Lee el segundo operando desde el puerto B y lo almacena en la variable `OPERANDO_2`.

   - `void LEER_OPERACION(void);`: Lee la operación seleccionada desde el puerto B y la almacena en la variable selector.

   - `void FUNCION_ENTER(void);`: Espera a que el usuario presione y suelte un botón (en RB4) para confirmar una acción. 

   - `void CALCULAR_RESULTADO(void);`: Realiza la operación seleccionada (suma, resta, AND, OR, multiplicación o división) y almacena el resultado en la variable RESULTADO.

   - `void MOSTRAR_RESULTADO(void);`: Muestra el resultado en los LEDs conectados al puerto D durante 20 segundos.

**Variables unsigned char**

Las variables de tipo unsigned char son enteros sin signo de 8 bits, lo que significa que pueden almacenar valores en el rango de 0 a 255. En este codigo, se utilizan para almacenar datos como operandos, resultados y selecciones de operación.

   `unsigned char OPERANDO_1;`

   **Descripción:** Almacena el primer operando ingresado por el usuario.

   **Uso:** Se utiliza para guardar el valor leído desde el puerto B (pines RB0-RB3) cuando el usuario ingresa el primer número.

   **Rango:** Puede almacenar valores de 0 a 15 (4 bits).

   `unsigned char OPERANDO_2;`

   **Descripción:** Almacena el segundo operando ingresado por el usuario.

   **Uso:** Se utiliza para guardar el valor leído desde el puerto B (pines RB0-RB3) cuando el usuario ingresa el segundo número.

   **Rango:** Puede almacenar valores de 0 a 15 (4 bits).

   `unsigned char selector;`

   **Descripción:** Almacena la operación seleccionada por el usuario.

**Uso:** Se utiliza para guardar el valor leído desde el puerto B (pines RB0-RB3) cuando el usuario selecciona la operación a realizar (suma, resta, AND, OR, multiplicación o división).

**Rango:** Puede almacenar valores de 0 a 5, donde cada valor representa una operación

- **0:** Suma.

- **1:** Resta.

- **2:** AND lógico.

- **3:** OR lógico.

- **4:** Multiplicación.

- **5:** División.

`unsigned char RESULTADO;`

**Descripción:** Almacena el resultado de la operación realizada por la ALU.

**Uso:** Se utiliza para guardar el valor calculado en la función CALCULAR_RESULTADO y mostrarlo en los LEDs conectados al puerto D.

**Rango:** Depende de la operación realizada, pero generalmente puede almacenar valores de 0 a 255 (8 bits).

**Relación entre Variables y Funciones**

**OPERANDO_1 y OPERANDO_2:**

Son leídos por las funciones `LEER_OPERANDO_1` y `LEER_OPERANDO_2`, respectivamente.

Se utilizan como entradas para la función `CALCULAR_RESULTADO`.

**selector:**

Es leído por la función `LEER_OPERACION`.

Determina la operación que se realizará en la función `CALCULAR_RESULTADO`.

**RESULTADO:**

Es calculado en la función `CALCULAR_RESULTADO`.

Se muestra en los LEDs mediante la función `MOSTRAR_RESULTADO`.

**3. BUCLE WHILE**

El bucle main comienza con la función principal del programa: 

``` c

void main(void) 
{
    while(1)
    {

    }
}
```

Aquí, el programa entra en un bucle infinito (`while(1)`), lo que significa que el código dentro de este bucle se ejecutará repetidamente hasta que se apague el microcontrolador.

**Explicación del bucle While**

**1. Configuración Inicial:**

Llamada a `CONF_PUERTOS();`:

- Configura los puertos del microcontrolador como entradas o salidas.

  - `TRISB = 0b00011111;`: Configuración de pines RB0-RB4 como entradas (para leer los operandos y la operación).

  - `TRISC = 0b00000000;` y `TRISD = 0b00000000;`: Configuración de puertos C y D como salidas (para mostrar resultados en LEDs).

- Habilita resistencias pull-up en el puerto B para los botones.
  - `INTCON2bits.RBPU = 0;`: Habilita las resistencias pull-up en el puerto B

- Desactiva funciones analógicas en los pines necesarios.
  - `ANSELB = 0b00000000;`: Desactiva las funciones analógicas en el puerto B.


Llamada a `BORRAR_BASURA();`:

- Limpia las variables globales (OPERANDO_1, OPERANDO_2, selector, RESULTADO) y los registros de salida (LATC, LATD).

- Asegura que no haya datos residuales que puedan afectar el funcionamiento del programa.

**BREVE REPASO A LA CONFIGURACIÓN PULL-UP**

Antes de avanzar vamos a hacer un repaso breve sobre qué son las resistencias pull-up, por qué son importantes y por qué es necesario configurar el microcontrolador de esa forma en este trabajo.

**¿Qué es un Pull-Up?**

Un Pull-Up es una resistencia conectada entre una línea de entrada (como un pin de un microcontrolador) y la fuente de alimentación (VDD). Su función es asegurar que la entrada tenga un valor definido (alto, 1) cuando no está activamente conectada a un nivel bajo (GND, 0).

![Figura](https://naylampmechatronics.com/img/cms/Blog/Resistencias%20pullup%20pulldown/Resistenci/pullup.jpg)

**Características de un Pull-Up:**

- **Valor por defecto:** Cuando no hay una señal externa activa, el pin se mantiene en 1 (alto) debido a la resistencia Pull-Up.

- **Uso común:** Se utiliza en entradas digitales, especialmente con botones o interruptores, para evitar estados flotantes (indefinidos).

**¿Por qué es necesario configurar el microcontrolador con Pull-Up?**

- **Evitar estados flotantes:**

  - Sin una resistencia Pull-Up, un pin de entrada puede quedar en un estado flotante (indefinido) cuando no está conectado a nada. Esto puede causar lecturas erráticas o falsos disparos en el programa.

  - Con un Pull-Up, el pin tiene un valor definido (1) cuando no está activamente conectado a GND.

- **Funcionamiento correcto de botones/interruptores:**

  - Cuando se usa un botón, este normalmente conecta el pin a GND (0) cuando se presiona. Sin un Pull-Up, el pin no tendría un valor definido cuando el botón no está presionado.

  - Con un Pull-Up, el pin está en 1 cuando el botón no está presionado y en 0 cuando se presiona.


**¿Qué pasaría si se usa un Pull-Down en lugar de un Pull-Up?**

- **Comportamiento inverso:**

  - Con un Pull-Down, el pin estaría en 0 por defecto y cambiaría a 1 cuando se active (por ejemplo, al presionar un botón).

  - Esto es lo opuesto a un Pull-Up, donde el pin está en 1 por defecto y cambia a 0 al activarse.

- **Cambios en el diseño del circuito:**

  - Si se usa un Pull-Down, el botón o interruptor debe conectar el pin a VDD (1) cuando se presiona, en lugar de a GND (0).

  - Esto puede requerir cambios en el diseño del hardware.

- **Impacto en el software:**

  - El código debe ajustarse para interpretar correctamente los niveles lógicos. Por ejemplo, en lugar de esperar un 0 para detectar un botón presionado, se esperaría un 1.

Ahora que ya se entiende mejor esta configuración procedemos a seguir explicando el programa

**2. Lectura del Primer Operando**

**Llamada a `FUNCION_ENTER();`**

- Espera a que el usuario presione y suelte el botón conectado a RB4 (función de "ENTER").

  - `while(PORTBbits.RB4 == 0) {}`: Espera a que el botón se suelte.

  - `while(PORTBbits.RB4 == 1) {}`: Espera a que el botón se presione.

  - `__delay_ms(50);`: Introduce un pequeño retardo para evitar rebotes del botón.

Esto asegura que el usuario confirme la entrada del primer operando.

**Llamada a `LEER_OPERANDO_1();`**

- Lee el valor del primer operando desde los pines RB0-RB3 del puerto B.

  - `OPERANDO_1 = PORTB & 0b00001111;`: Almacena los 4 bits menos significativos del puerto B en OPERANDO_1.

**Mostrar valor en los Leds**

La linea `LATD = OPERANDO_1;` muestra el valor de OPERANDO_1 en los LEDs conectados al puerto D.

**Lectura de segundo Operando**

**Llamada a `LEER_OPERANDO_2();`**

Lee el valor del primer operando desde los pines RB0-RB3 del puerto B.

  - `OPERANDO_2 = PORTB & 0b00001111;`: Almacena los 4 bits menos significativos del puerto B en OPERANDO_2.

**Mostrar valor en los Leds**

La linea `LATD = OPERANDO_2;` muestra el valor de OPERANDO_1 en los LEDs conectados al puerto D.

**4. Selección de la Operación**

**Llamada a `FUNCION_ENTER();`**

Espera a que el usuario presione y suelte el botón de "ENTER".

**Llamada a LEER_OPERACION();**

Lee la operación seleccionada desde los pines RB0-RB3 del puerto B.

- `selector = PORTB & 0b00001111;`: Almacena los 4 bits menos significativos del puerto B en selector.

**Mostrar la Operación Seleccionada en los LEDs**

- `LATC = selector;`: Muestra el valor de selector en los LEDs conectados al puerto C.

**5. Cálculo del Resultado**

**Llamada a `FUNCION_ENTER();`**

Espera a que el usuario presione y suelte el botón de "ENTER".

**Llamada a `CALCULAR_RESULTADO();`**

Realiza la operación seleccionada (`selector`) entre OPERANDO_1 y OPERANDO_2.

- Usa un `switch-case` para seleccionar la operación (suma, resta, AND, OR, multiplicación o división).

Almacena el resultado en la variable RESULTADO.

**6. Mostrar resultado**

**Llamada a `MOSTRAR_RESULTADO();`**

Muestra el valor de RESULTADO en los LEDs conectados al puerto D.

- `LATD = RESULTADO;`: Enciende los LEDs correspondientes al resultado.

- `__delay_ms(20000);`: Mantiene el resultado visible durante 20 segundos.

- `LATD = 0;`: Apaga los LEDs después de mostrar el resultado.

### **PROCESO DE COMUNICACIÓN CON EL PICKIT 3**

El PICKit 3 es una herramienta de programación y depuración para microcontroladores PIC. Permite cargar el código compilado en el microcontrolador, depurar el programa y verificar su funcionamiento. A continuación, se explicara el proceso de comunicación:

**1. Conexión Física del PICKit 3**
Descripción:

- El PICKit 3 se conecta al microcontrolador mediante un cable ICSP (In-Circuit Serial Programming).

- Los pines principales son:

  - VDD: Alimentación del microcontrolador.

  - VSS: Tierra (GND).

  - PGC: Pin de reloj para la programación.

  - PGD: Pin de datos para la programación.

  - MCLR: Pin de reset (se utiliza para entrar en modo programación).

  **Diagrama de pines del Pickit 3**

  ![Figura1: Diagrama de Pines del Pickit 3](https://electronilab.co/wp-content/uploads/2016/03/PICkit3_MCU_Programmer_debuger_pinout.jpg)
  
**Descripción de los Pines de Conexión del PICKit 3**

**VPP/MCLR (Pin 1):**

- Función: Este pin se utiliza para el voltaje de programación y el reset del microcontrolador (MCLR).

- Uso: Durante la programación, este pin aplica un voltaje específico para entrar en modo programación. También se usa para resetear el microcontrolador.

**VDD Target (Pin 2):**

- Función: Proporciona alimentación (VDD) al microcontrolador objetivo.

- Uso: Suministra el voltaje necesario para que el microcontrolador funcione durante la programación y depuración.

**VSS (Pin 3):**

- Función: Conexión a tierra (GND) del microcontrolador objetivo.

- Uso: Completa el circuito eléctrico, proporcionando una referencia de tierra.

**ICSPDAT/PGD (Pin 4):**

- Función: Pin de datos para la programación ICSP (In-Circuit Serial Programming).

- Uso: Transfiere datos entre el PICKit 3 y el microcontrolador durante la programación y depuración.

**ICSPCLK/PGC (Pin 5):**

- Función: Pin de reloj para la programación ICSP.

- Uso: Sincroniza la transferencia de datos entre el PICKit 3 y el microcontrolador.

**LVP (Pin 6):**

- Función: Pin para la programación de bajo voltaje (Low-Voltage Programming).

- Uso: Permite la programación del microcontrolador a voltajes más bajos, si es compatible.

**Conexiones entre el Pickit 3 y el microcontrolador Pic18f45k22**

La siguiente imagen muestra como debe ser la conexión entre el pickit 3 y el pic(Microcontrolador),aunque el modelo del pic de la imagen sea el 18f4550 no hay diferencia en la conexion respecto al modelo 18f45k22 ya que ambos tienen la misma arquitectura de pines.

![alt text](image.png)

**2. Carga del Programa en el Microcontrolador**

- **Configuración en el Software (MPLAB X)**

  **Creación del Proyecto:**

  - En MPLAB X, se crea un nuevo proyecto y se selecciona el microcontrolador objetivo (en este caso el PIC16F45K22).

  ![alt text](<Captura de pantalla 2025-03-12 211258.png>)

  - Se selecciona el tipo de compilador.
  
  ![alt text](<Captura de pantalla 2025-03-12 212546.png>)

  - Se nombra el documuendo y se le da click en Finish.

  ![alt text](<Captura de pantalla 2025-03-12 213008.png>)

  - Se crea el documento tipo .c.

  ![alt text](<Captura de pantalla 2025-03-12 213932.png>)

    El nombre que se le halla dado al proyecto aparacera en la ventana de **proyect** donde se le dara click derecho al nombre(En este caso el nombre es CODIGO) despues **New** y luego **main.c**

    -Luego se procede a configurar el microcontrolador(es decir la configuración de los bits que se explico al principio) y a diseñar el codigo(en este caso es [CODIGOALU.c](https://github.com/JhonCuadros/Microcontroladores/blob/JHON-ALEXANDER-CUADROS/CORTE1/CODIGOALU.c)).

  **Selección del PICKit 3:**

  - En la configuración del proyecto, se selecciona el PICKit 3 como herramienta de programación.
  
![alt text](<Captura de pantalla 2025-03-12 215522.png>)

   - se hace en la sección de "Hardware Tool" (Herramienta de hardware).

  **Configuración del Compilador:**

  - Se asegura que el compilador XC8 esté configurado correctamente.

  - Se verifica que la frecuencia del oscilador y otras configuraciones coincidan con el hardware.

  **Compilación del codigo fuente:**

  - En MPLAB X, se compila el código fuente (.c) para generar el archivo .hex.

  ![alt text](<Captura de pantalla 2025-03-12 220252.png>)

    Esta imagen muestra la configuración del codigo fuente que se explico al principio en donde se desactivaron y activaron algunas opciones.
  
  - Una vez configurado se da click en **Generate source code to output.**

**Carga del Programa en el Microcontrolador**

- **Programación:**

  Una vez que el código se ha compilado correctamente, se utiliza la opción "Make and Program Device" para cargar el archivo .hex en el microcontrolador.

  ![alt text](<Captura de pantalla 2025-03-12 221254.png>)

  El PICKit 3 envía los datos al microcontrolador a través de los pines PGC y PGD.

- **Verificación del Funcionamiento**

  Después de cargar el programa, se verifica que el microcontrolador funcione según lo esperado.

  Esto incluye probar las entradas (botones) y salidas (LEDs) para asegurar que el programa responde correctamente.


**DIAGRAMA DE CONEXIONES**

![alt text](<Imagen de WhatsApp 2025-03-13 a las 22.51.53_a7022a63.jpg>)
