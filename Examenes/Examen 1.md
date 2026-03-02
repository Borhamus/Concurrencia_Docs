### 1. Soluciones Tipo Spin-Lock:
a. Para solucionar qué problema se utilizan?
b. Cuando se utiliza una instrucción TS(Test-And-Set) para implementarla, qué problemas adicionales aparecen? - Explique en qué consisten esos problemas.
c. Cómo se solucionan los inconvenientes que se producen en b.?

### 2. Deadlocks
a. Qué es y bajo qué condiciones ocurre un deadlock?
b. De qué formas se puede "evadir". Explique cada una de ellas?

### 3. Rendezvous
a. Qué es Rendezvous?
b. En qué situaciones resulta de utilidad?
c. Con qué herramientas de sincronización se puede proveer?

### 4. Paralelismo y Concurrencia
a. Explique en qué consiste el concepto de paralelismo.
b. A qué se refiere la independencia de procesos paralelos y para qué sirve?
c. En qué principios se fundamente?
d. Los procesos de un programa concurrente son procesos paralelos? Justifique.

### 5. Sección Crítica
a. En qué consiste el problema de la "Sección Crítica"?
b. Qué propiedades debe tener una solución correcta?. Explique si se puede o no solucionar con "Barreras" y porque.

---
### 1. Soluciones Tipo Spin-Lock:
a. Se utiliza el Spin-Lock para solucionar el problema de la sección critica, se basa en el uso de una única variable compartida, llamada típicamente lock, donde el proceso circula en un bucle hasta obtener el acceso a un recurso.

b. Cuando nosotros implementamos Spin-Lock, sucede que aparecen 2 problemas graves:
* **Memory Contention:** Múltiples procesadores queriendo entrar al mismo bus de memoria.
* **Caché Invalidation:** Si muchos procesos están en un bucle de espera activa ejecutando `TS` constantemente, se la pasan borrando las cachés de los demás procesadores, lo que genera una gran sobrecarga y **empeora el rendimiento** de toda la computadora.

c. Existe el algoritmo de TTAS(Test-And-Test-And-Set), que es una optimización del TS, ya que al tener un chekeo adicional no anda modificando la cache de todos los procesadores todo el tiempo, pero también, hay mas soluciones, como El algoritmo de Tie-Break, Tickery y Bakery. Estas son soluciones de software un poco mas sofisticadas del el TTAS.
* **Tie-Break:** Pensada originalmente para cuando 2 procesos pelean por un recurso, utiliza flags y turno para desempatar.
* **Ticket:** Funciona de manera análoga a una tienda con tickets, sacas un numero, y un admin va controlando los turnos. llamando del mas bajo al mas alto.
* **Backery:** Los procesos que entran deben buscar cual es el proceso con el numero mas alto y asignarse x+1, donde x es el valor del proceso con el número mas alto. 


### 2. Deadlocks
a. Un Deadlock es una situación de interbloqueo que ocurre cuando suceden 4 condiciones a la vez:
1 - Exclusión Mutua: Al menos un recurso debe ser de uso exclusivo.
2 - No preemptivo: No le puedo sacar recursos a un proceso.
3 - Tomar y Esperar: Un proceso toma un recurso y espera obtener otros.
4 - Espera circular: Se forma una especie de cadena donde cada proceso espera el recurso del otro.

La condición 1, 2, 3 son necesarias, pero no suficientes.


b. Para evadir un deadlock, el sistema operativo toma decisiones dinamias basadas en los requerimientos futuros, hay 2 mecanismos: 
* **Denegación de inicio de procesos:** Si el proceso quiere mas procesos de los que actualmente hay + los que quieren los procesos que ya están, no se larga.
* **Denegación de Asignación de recursos(Algoritmo del Banquero):** El sistema simula que pasa si puede darle recursos, si el resultado es estable se le da, si no, no.


### 3. Rendezvous
a. Mecanismo de comunicación sincrónico, que permite que se pueda coordinar una serie de eventos con distintos procesos, creando un encuentro en un instancia de tiempo.

En términos simples, ocurre cuando un emisor envía un mensaje y se queda bloqueado (detenido) hasta que el receptor está listo para recibirlo, creando un "encuentro" exacto en el tiempo.

b. Es fundamental cuando los procesos necesitan **coordinar etapas paso a paso** o cuando el progreso de uno depende estrictamente de que el otro haya alcanzado el mismo punto.

c. Se puede implementar de varias formas según el modelo que estés usando:
- **Pasaje de Mensajes:** Utilizando primitivas de envío y recepción **bloqueantes** (donde el `send` espera al `receive`).
- **Monitores:** Mediante el uso de **variables de condición** que obligan a un proceso a esperar hasta que el otro señalice que ha llegado al punto de encuentro.
- **Semáforos:** A través de técnicas de **señalización**, donde un proceso hace un `signal` para avisar que llegó y un `wait` para esperar al otro.


### 4. Paralelismo y Concurrencia
a. El paralelismo es cuando en un sistema de mas de un núcleo, ejecuta procesos independientes.

b. Se refiere a que cada proceso puede ser ejecutado porque son independientes de los otros procesadores.

c. Se fundamenta en el principio conocido como la **Condición de Bernstein**. Este principio determina que dos partes de un programa son independientes si el **conjunto de escritura** (_write set_) de cada parte es **disjunto** (no tiene elementos en común) respecto al **conjunto de lectura** (_read set_) **y de escritura** de la otra parte.

d. No necesariamente. Hay dos formas de concurrencia, con **Interleaving** y **Overlapping**:
* **Interleaving**: Con Hardware de 1 núcleo, el sistema operativo hace salto de contexto, gracias a la pcb, entre procesos. Esta es una forma de concurrencia.
* **Overlapping**: Cuando tenemos Hardware de mas de un núcleo, el sistema operativo también puede hacer que los procesadores puedan ejecutar al mismo tiempo procesos independientes. A esto se le llama concurrencia real y es la otra forma de concurrencia.


### 5. Sección Crítica
a. En qué consiste el problema de la "Sección Crítica"?
b. Qué propiedades debe tener una solución correcta?. Explique si se puede o no solucionar con "Barreras" y porque.

a. Controlar el acceso a un recurso compartido entre procesos, para evitar la inconsistencia de datos. Al tratar una secuencia de instrucciones como una **única acción atómica de grano grueso**, garantizo que el acceso al recurso sea estrictamente exclusivo.

b. Propiedades Fundamentales
**1 - Exclusión Mutua**
**2 - Ausencia de Deadlock y Livelock**
**3 - Ausencia de Retardos Innecesarios**
**4 - Entrada Eventual (Propiedad de Vida)**

**Sobre el uso de "Barreras":** **No es posible** solucionar el problema de la sección crítica utilizando barreras. La barrera esta pensada para poder controlar que varios procesos se detengan hasta que una condición concreta suceda y liberarlos todos a la vez, esto no tiene relación directa con la exclusión mutua.

a. El problema de la **Sección Crítica (SC)** surge cuando un programa concurrente contiene n **procesos** que ejecutan repetidamente una secuencia de instrucciones para acceder a un **recurso u objeto compartido**. El núcleo del problema es que el acceso simultáneo y de forma solapada (_overlapping_) a estas variables compartidas puede provocar **inconsistencias en los datos** y resultados no esperados,. El objetivo fundamental de la sincronización es lograr que dicha secuencia sea tratada como una **única acción atómica de grano grueso**, garantizando que el acceso al recurso sea estrictamente exclusivo.

b. Para que una solución (protocolos de entrada y salida) al problema de la sección crítica se considere válida, debe satisfacer **cuatro propiedades fundamentales**:

1. **Exclusión Mutua:** En cualquier instante de tiempo, a lo sumo un solo proceso puede estar ejecutando su sección crítica,.
2. **Ausencia de Deadlock y Livelock:** Si varios procesos intentan entrar a la SC simultáneamente, al menos uno debe lograrlo; el sistema no debe quedar bloqueado ni atrapado en bucles infinitos de espera activa sin progreso,.
3. **Ausencia de Retardos Innecesarios:** Si un proceso desea entrar a su SC y los demás están en su código no crítico o ya terminaron, el proceso debe poder entrar sin ninguna restricción ni demora,.
4. **Entrada Eventual (Propiedad de Vida):** Todo proceso que intente entrar a su sección crítica debe lograrlo en algún momento, evitando ser postergado indefinidamente (inanición o _starvation_),.

**Sobre el uso de "Barreras":** **No es posible** solucionar el problema de la sección crítica utilizando barreras. Las fuentes establecen una distinción clara entre ambos conceptos:

- **Objetivos opuestos:** Mientras que la exclusión mutua busca que **solo un proceso** acceda al recurso a la vez, la barrera busca exactamente lo contrario: que **todos los procesos se detengan** en un punto para luego continuar simultáneamente,.
- **Propósito técnico:** La barrera es un mecanismo de sincronización por condición diseñado para coordinar etapas en **algoritmos paralelos**, asegurando que ninguna tarea avance a la siguiente fase hasta que el grupo completo haya finalizado la anterior. Por lo tanto, su naturaleza colectiva es incompatible con la restricción de acceso individual que requiere una sección crítica,.