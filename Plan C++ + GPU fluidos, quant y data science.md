# Plan C++ + GPU: fluidos, quant y data science

Oct 21, 2026 · @Jesus Leobardo Nagao

## Resumen

Son 26 semanas (≈ abril 2027) a 1 hora diaria, en 7 fases. Cada fase termina en un proyecto pequeño publicable en GitHub. El hilo conductor: el mismo kernel de GPU sirve para fluidos y para finanzas, así que lo que aprendes en una fase se reutiliza en la siguiente.

**Ritmo semanal sugerido (5–6 h):**

- Lun–Jue: construir. Un milestone por sesión, más o menos.
- Vie: terminar el milestone pendiente, actualizar el README y contestar las preguntas de "Al terminar".
- Sáb (opcional): sesión de data science en Python (ver sección Data science).

**Reglas para que funcione con 1 h/día:**

- Deja cada sesión con una nota de "lo siguiente que haré" en el README; así arrancas en 2 minutos al día siguiente.
- Un solo repositorio monorepo (`cpp-gpu-lab/`) con una carpeta por proyecto y CMake en la raíz. Compila en Mac y en PC desde el día 1.
- Si un proyecto se alarga, recorta alcance, no la semana siguiente.
- Hardware: PC con GPU NVIDIA (CUDA) y Mac con Apple Silicon (Metal).

## Stack de herramientas

No existe una sola API de GPU que sea "la mejor" en ambas máquinas: la Mac no corre CUDA y la PC no corre Metal. La estrategia es aprender CUDA como lenguaje principal (es el estándar en CFD y en quant), Metal para la Mac, y WebGPU como capa multiplataforma para gráficos.

| Capa | PC (NVIDIA) | Mac (Apple Silicon) | Por qué |
| --- | --- | --- | --- |
| Compilador / build | MSVC o clang + CMake + vcpkg | clang (Xcode CLT) + CMake + Homebrew | CMake es lo que usan todos los proyectos serios |
| Cómputo GPU | CUDA (nvcc), luego Thrust/CUB | Metal vía metal-cpp | CUDA domina en HPC y finanzas; metal-cpp te deja escribir C++ en vez de Swift |
| Gráficos | WebGPU (Dawn o wgpu-native) + GLFW | Igual: WebGPU corre sobre Metal | Un solo código de render para ambas; también hace compute |
| Matemáticas CPU | Eigen | Eigen | Álgebra lineal estándar en C++ |
| Tests / benchmark | Catch2 + Google Benchmark | Igual | Medir es la mitad del trabajo en GPU |
| Puente a Python | nanobind o pybind11 | Igual | Para usar tu C++ desde pandas/notebooks |
| Profiling | Nsight Systems / Nsight Compute | Xcode Instruments (Metal System Trace) | Sin profiler no sabes por qué es lento |

**Por qué no OpenGL:** Apple lo dejó congelado en 4.1 y no tiene compute shaders. Ya lo usaste en Julia, así que WebGPU te da el salto moderno sin perder la Mac. Si prefieres máxima potencia en la PC, Vulkan es la alternativa, pero es mucho más verboso para 1 h/día.

## Libros de referencia

Ninguno es obligatorio: son para consultar cuando un milestone se atore. Los ★ son los más útiles. Ediciones citadas de memoria: verifica la más reciente al comprar.

### C++ moderno y rendimiento

| Libro | Uso en el plan |
| --- | --- |
| ★ Stroustrup, *A Tour of C++* (3.ª ed., 2022) | Fases 0–1. Tour rápido de C++20 para alguien que ya programa |
| Meyers, *Effective Modern C++* | Fase 1. Move semantics, smart pointers, lambdas: los errores clásicos |
| Williams, *C++ Concurrency in Action* (2.ª ed.) | Fase 1, semana de threads. Consulta |
| Slotin, *Algorithms for Modern Hardware* (gratis en línea) | Caché, SIMD, por qué el código rápido se ve así |

### GPU

| Libro | Uso en el plan |
| --- | --- |
| ★ Hwu, Kirk y El Hajj, *Programming Massively Parallel Processors* (4.ª ed., 2022) | Fase 2 en adelante. La biblia de CUDA: tiling, reducciones, scan, stencils |
| Guía oficial *CUDA C++ Programming Guide* y *Best Practices Guide* (NVIDIA, gratis) | Consulta constante |
| *Metal Shading Language Specification* + ejemplos de metal-cpp (Apple, gratis) | Fase 2, versión Mac |
| Michel, *Learn WebGPU for C++* (gratis en línea) | Fase 3. Tutorial paso a paso de render y compute |
| Akenine-Möller et al., *Real-Time Rendering* (4.ª ed.) | Consulta si te enganchas con gráficos |

### Fluidos y aerodinámica

| Libro | Uso en el plan |
| --- | --- |
| ★ Bridson, *Fluid Simulation for Computer Graphics* (2.ª ed.) | Fase 3. Stable fluids, grids, presión: muy de implementar |
| ★ Krüger et al., *The Lattice Boltzmann Method: Principles and Practice* (2017) | Fase 4. LBM es el método más natural para GPU |
| Ferziger, Perić y Street, *Computational Methods for Fluid Dynamics* (4.ª ed.) | Consulta formal de volúmenes finitos; útil para la maestría |
| Harris, "Fast Fluid Dynamics Simulation on the GPU", *GPU Gems* cap. 38 (gratis) | Fase 3. Un capítulo, directo al código |

### Finanzas cuantitativas

| Libro | Uso en el plan |
| --- | --- |
| ★ Joshi, *C++ Design Patterns and Derivatives Pricing* (2.ª ed.) | Fase 5. Exactamente C++ + pricing; corto y práctico |
| Hull, *Options, Futures, and Other Derivatives* | Consulta para el lado financiero (Black-Scholes, griegas) |
| Glasserman, *Monte Carlo Methods in Financial Engineering* | Fase 5. Reducción de varianza, quasi-Monte Carlo |
| Chan, *Quantitative Trading* (2.ª ed.) | Fase 6. Backtesting honesto, cómo no engañarte |
| López de Prado, *Advances in Financial Machine Learning* | Fase 6. Puente directo entre quant y ML |
| Zhou, *A Practical Guide to Quantitative Finance Interviews* | En paralelo, 2–3 problemas por semana si vas en serio por quant |

### Data science

| Libro | Uso en el plan |
| --- | --- |
| ★ James, Witten, Hastie, Tibshirani y Taylor, *An Introduction to Statistical Learning with Applications in Python* (gratis) | Sesiones de sábado |
| McKinney, *Python for Data Analysis* (3.ª ed., gratis en línea) | pandas a fondo |
| Géron, *Hands-On Machine Learning* (edición más reciente) | Cuando pases de modelos clásicos a redes |

## Plan semana a semana

12 proyectos en 26 semanas. Cada uno tiene un criterio de "terminado" medible: si lo cumples, avanzas aunque el código no sea perfecto.

| Fase | Semanas | Tema | Proyectos |
| --- | --- | --- | --- |
| 0 | 1–2 | Setup y reentrada a C++ | P0 |
| 1 | 3–6 | C++ moderno y CPU rápido | P1, P2 |
| 2 | 7–10 | Cómputo en GPU | P3, P4 |
| 3 | 11–14 | Gráficos + fluidos 2D | P5, P6 |
| 4 | 15–19 | Aerodinámica con LBM | P7, P8 |
| 5 | 20–22 | Pricing de derivados en GPU | P9, P10 |
| 6 | 23–26 | Trading: order book y backtester | P11, P12 |

### Fase 0 — Setup (semanas 1–2)

Solo si te atoras: *A Tour of C++* caps. 1–5.

**P0 · Monte Carlo de π** (sem 1–2). Idea: lanza puntos al azar en un cuadrado de 1×1; la fracción que cae dentro del cuarto de círculo × 4 ≈ π. Cada milestone es 1–2 sesiones de 1 h.

- [ ] **M1 · Hola mundo que compila en Mac y PC.** Investiga: `CMakeLists.txt` mínimo, `add_executable`, `target_compile_features(... cxx_std_20)`, compilar fuera del código fuente (`cmake -B build`), `.gitignore` para `build/`. *Logro:* el mismo comando compila y corre en ambas máquinas, y el repo está en GitHub.
- [ ] **M2 · Primera estimación.** Investiga: `<random>`, `std::mt19937_64`, `std::uniform_real_distribution<double>`, por qué no usar `rand()`. *Logro:* imprime π ≈ 3.14 con N = 10⁶.
- [ ] **M3 · Argumentos y reproducibilidad.** Investiga: `argc/argv`, `std::stoull`, semilla fija vs `std::random_device`. *Logro:* `./pi 1000000 42` da siempre el mismo número; sin semilla, cambia cada vez.
- [ ] **M4 · Medir velocidad.** Investiga: `std::chrono::steady_clock`, `duration_cast`, `CMAKE_BUILD_TYPE` Debug vs Release y qué hace `-O2`. *Logro:* imprime muestras por segundo y anotas cuántas veces más rápido corre en Release.
- [ ] **M5 · Convergencia.** Investiga: `std::ofstream` para escribir CSV, error absoluto vs `std::numbers::pi`. Loop de N = 10² a 10⁸ y grafica en Python. *Logro:* gráfica log-log de error vs N con pendiente ≈ −1/2.
- [ ] **M6 · Ordenar el código.** Investiga: separar `.hpp`/`.cpp`, `add_library` + `target_link_libraries`, `namespace`, pasar el generador por referencia. *Logro:* `main.cpp` de \~15 líneas que llama a `estimate_pi(n, rng)`.
- [ ] **M7 · Bonus: varios hilos.** Investiga: `std::thread` o `std::async` + `std::future`, `std::thread::hardware_concurrency`, por qué cada hilo necesita su propio generador. *Logro:* tabla de tiempo vs número de hilos (1, 2, 4, 8…).

**Al terminar deberías poder explicar:** qué hace CMake y qué hace el compilador; por qué Release es más rápido; por qué el error cae como 1/√N; qué pasa si dos hilos comparten un generador.

### Fase 1 — C++ moderno (semanas 3–6)

Solo si te atoras: *Tour* caps. 6–13; *Effective Modern C++* ítems 1–30 salteados según lo que uses.

**P1 · Mini librería de álgebra lineal** (sem 3–4). Tu propio `Vector` y `Matrix`; la reusarás en P2 y P4.

- [ ] **M1 · Vector genérico.** Investiga: `template<typename T>`, `std::vector<T>` como almacenamiento, `operator[]`, `std::size_t`. *Logro:* `Vector<double> v(3); v[0] = 1.0;` compila y funciona.
- [ ] **M2 · Operadores.** Investiga: sobrecarga de `operator+`, `-`, `*` (escalar), métodos `dot()` y `norm()`, `const` correctness, pasar por `const&`. *Logro:* `auto c = a + 2.0 * b;` da el resultado correcto.
- [ ] **M3 · Tests.** Investiga: Catch2 con `FetchContent`, `TEST_CASE`, `REQUIRE`, `Catch::Approx`, `ctest`. *Logro:* `ctest` en verde con ≥10 tests.
- [ ] **M4 · Matriz.** Investiga: almacenamiento row-major (`i*cols + j`), `operator()(i, j)`, multiplicación naive. *Logro:* test `A * I == A` y `A * B` contra un caso calculado a mano.
- [ ] **M5 · Copias vs moves.** Investiga: constructor de copia y de movimiento, rule of zero/five, `std::move`. Pon un `std::cout` en cada constructor. *Logro:* explicas cuántas copias hace `c = a + b` y logras bajarlas.
- [ ] **M6 · Benchmark.** Investiga: Google Benchmark, orden de loops `ijk` vs `ikj`, caché de CPU, Eigen vía `FetchContent` o vcpkg. FLOPs de matmul = 2n³. *Logro:* tabla GFLOP/s de naive, loops reordenados y Eigen para n = 256, 512, 1024.

*Al terminar deberías poder explicar:* por qué `ikj` es más rápido que `ijk`; qué es instanciar un template; cuándo un move evita trabajo.

**P2 · N-body gravitacional** (sem 5–6).

- [ ] **M1 · Fuerzas.** Investiga: `struct Body` con posición, velocidad y masa; suma de fuerzas O(n²); softening para evitar división entre cero. *Logro:* dos cuerpos en órbita circular estable.
- [ ] **M2 · Integrador.** Investiga: Euler explícito vs leapfrog (velocity Verlet), energía cinética + potencial. *Logro:* gráfica de energía vs tiempo donde Euler deriva y leapfrog se mantiene en <0.1 %.
- [ ] **M3 · Ver lo que pasa.** Investiga: escribir posiciones a CSV y animar con `matplotlib.animation`. *Logro:* GIF del problema de 3 cuerpos en "figura 8".
- [ ] **M4 · Hilos a mano.** Investiga: `std::thread`, dividir el rango de `i` entre hilos, `join`, data races y por qué paralelizar el loop externo es seguro. *Logro:* tabla de speedup con 1–8 hilos para n = 2000.
- [ ] **M5 · OpenMP.** Investiga: `#pragma omp parallel for`, `find_package(OpenMP)` (en Mac: `brew install libomp`; las execution policies de `std::execution` no están en el libc++ de Apple). *Logro:* comparación hilos a mano vs OpenMP.
- [ ] **M6 · Layout de memoria.** Investiga: Array of Structs vs Struct of Arrays. *Logro:* misma simulación en SoA y diferencia de tiempo medida. Esto prepara la GPU.

*Al terminar deberías poder explicar:* qué es una data race; por qué leapfrog conserva energía; por qué SoA le gusta al hardware.

### Fase 2 — Cómputo en GPU (semanas 7–10)

Solo si te atoras: *PMPP* caps. 1–6 y 10 (reducciones).

**P3 · Kernels fundamentales** (sem 7–9). Los 4 patrones que aparecen en todo: map, reducción, matmul y el mismo código en otra API.

- [ ] **M1 · Primer kernel.** Investiga: `project(... LANGUAGES CXX CUDA)`, `CMAKE_CUDA_ARCHITECTURES`, archivos `.cu`, `__global__`, `printf` dentro del kernel, `threadIdx`/`blockIdx`. *Logro:* un kernel imprime el id de cada hilo.
- [ ] **M2 · SAXPY.** Investiga: `cudaMalloc`, `cudaMemcpy`, `<<<bloques, hilos>>>`, cálculo de índice global, macro para revisar errores de CUDA, `cudaEvent` para medir. *Logro:* `y = a*x + y` con 10⁸ elementos correcto, y el ancho de banda logrado en GB/s vs el teórico de tu tarjeta.
- [ ] **M3 · Memoria unificada.** Investiga: `cudaMallocManaged` vs copias explícitas. *Logro:* SAXPY de las dos formas y explicas cuándo conviene cada una.
- [ ] **M4 · Reducción.** Investiga: `__shared__`, `__syncthreads()`, reducción en árbol, `__shfl_down_sync`, `atomicAdd`. *Logro:* suma de 10⁸ números igual a la de CPU (cuidado con float vs double), con 3 versiones cada vez más rápidas.
- [ ] **M5 · Matmul.** Investiga: versión naive, tiling en shared memory (16×16), coalescing de memoria, cuBLAS como referencia. *Logro:* tiled ≥5× la naive y qué % de cuBLAS alcanzas.
- [ ] **M6 · Lo mismo en Metal (sem 9).** Investiga: metal-cpp, `MTL::Device`, archivo `.metal`, compute pipeline, command buffer y encoder, `threadgroup` memory (el equivalente a shared). *Logro:* SAXPY y matmul tiled corriendo en la Mac; tabla CPU vs CUDA vs Metal.

*Al terminar deberías poder explicar:* qué son un warp, un bloque y un grid; por qué el acceso coalescido importa; qué limita a SAXPY (memoria) y qué limita a matmul (cómputo).

**P4 · Ecuación de calor 2D en GPU** (sem 10).

- [ ] **M1 · Referencia en CPU.** Investiga: stencil de 5 puntos, condición de estabilidad dt ≤ dx²/(4α). Usa tu `Matrix` de P1. *Logro:* solución CPU que se difunde como esperas.
- [ ] **M2 · Kernel 2D.** Investiga: `dim3` para bloques 2D, dos buffers que se intercambian cada paso (ping-pong), fronteras. *Logro:* diferencia máxima vs CPU < 10⁻¹².
- [ ] **M3 · Tiling con halo.** Investiga: cargar un bloque + bordes a shared memory. *Logro:* speedup medido vs el kernel simple.
- [ ] **M4 · Versión Metal.** *Logro:* mismos números en la Mac.

*Al terminar deberías poder explicar:* por qué los stencils están limitados por memoria; qué pasa si violas la condición de estabilidad.

### Fase 3 — Gráficos y fluidos 2D (semanas 11–14)

Solo si te atoras: *Learn WebGPU for C++* (secciones básicas y compute); *Bridson* caps. 1–5; *GPU Gems* cap. 38.

**P5 · Visualizador en vivo** (sem 11–12). Nota: pasar datos de CUDA a WebGPU es complicado, así que la simulación visual corre en compute shaders de WebGPU; CUDA queda para el cómputo pesado sin ventana.

- [ ] **M1 · Ventana.** Investiga: GLFW, Dawn o wgpu-native (el repo de *Learn WebGPU for C++* trae el CMake listo), `glfw3webgpu` para la surface. *Logro:* ventana con un color de fondo en Mac y PC.
- [ ] **M2 · Triángulo.** Investiga: render pipeline, shaders de vértice y fragmento en WGSL, vertex buffer. *Logro:* el clásico triángulo de colores.
- [ ] **M3 · Textura en pantalla.** Investiga: quad de pantalla completa, `queue.writeTexture`, sampler, bind groups. *Logro:* mostrar una imagen generada en CPU.
- [ ] **M4 · Colormap.** Investiga: mapear un escalar a color en el fragment shader (aproximación de viridis). *Logro:* el campo sin(x)·cos(y) se ve con colores.
- [ ] **M5 · Simulación en GPU.** Investiga: compute pipeline, storage buffers, ping-pong en WGSL. Porta el stencil de P4. *Logro:* difusión de calor en vivo a ≥60 fps en ambas máquinas.
- [ ] **M6 · Interacción.** Investiga: callbacks de mouse de GLFW, Dear ImGui con backend WebGPU. *Logro:* pintas calor con el click y un slider cambia α en vivo.

*Al terminar deberías poder explicar:* la diferencia entre render pipeline y compute pipeline; qué es un bind group.

**P6 · Stable Fluids (Stam) en GPU** (sem 13–14). Reutiliza toda la infraestructura de P5.

- [ ] **M1 · Campos.** Investiga: guardar velocidad (u, v), presión y tinta en buffers con ping-pong. *Logro:* estructura lista y tinta visible.
- [ ] **M2 · Advección.** Investiga: advección semi-lagrangiana (rastrear hacia atrás), interpolación bilineal. *Logro:* la tinta se mueve con un campo de velocidad fijo.
- [ ] **M3 · Fuerzas.** Investiga: convertir el arrastre del mouse en velocidad. *Logro:* puedes "empujar" el fluido.
- [ ] **M4 · Proyección.** Investiga: divergencia, Jacobi para la presión, restar el gradiente. Es lo mismo que tu Chorin en Julia. *Logro:* la divergencia máxima tras proyectar es ≈ 0 (mídela y muéstrala en ImGui).
- [ ] **M5 · Pulido.** Investiga: vorticity confinement, condiciones de frontera, obstáculos. *Logro:* 512×512 estable a 60 fps con un obstáculo en medio.

*Al terminar deberías poder explicar:* por qué la advección semi-lagrangiana es incondicionalmente estable; qué pierde Stable Fluids frente a un solver de CFD real.

### Fase 4 — Aerodinámica con Lattice Boltzmann (semanas 15–19)

Solo si te atoras: *Krüger* caps. 1–5 (BGK, condiciones de frontera), 7 (fuerzas).

**P7 · LBM D2Q9: cilindro** (sem 15–16).

- [ ] **M1 · LBM mínimo en CPU.** Investiga: las 9 velocidades y pesos de D2Q9, distribución de equilibrio, colisión BGK, relación ν = (τ − 0.5)/3. *Logro:* flujo de Poiseuille en un canal que coincide con el perfil parabólico analítico.
- [ ] **M2 · Port a CUDA.** Investiga: un hilo por nodo, layout SoA `f[q][nodo]`, streaming tipo pull vs push. *Logro:* mismo Poiseuille en GPU y tu primer número de MLUPS.
- [ ] **M3 · Cilindro.** Investiga: máscara de obstáculo, bounce-back half-way, entrada de velocidad (Zou-He) y salida. *Logro:* flujo estable y simétrico a Re = 20.
- [ ] **M4 · Ver la vorticidad.** Investiga: rotacional discreto; copiar el campo al host y subirlo como textura a P5, o escribir PNGs con `stb_image_write`. *Logro:* calle de vórtices de von Kármán a Re = 100.
- [ ] **M5 · Strouhal.** Investiga: registrar v en un punto detrás del cilindro, FFT con numpy. *Logro:* St ≈ 0.16–0.2.

*Al terminar deberías poder explicar:* cómo LBM recupera Navier-Stokes; por qué τ cerca de 0.5 se vuelve inestable.

**P8 · NACA 4412 en LBM** (sem 17–18). La pieza para la aplicación a Southampton.

- [ ] **M1 · Geometría.** Investiga: ecuaciones del perfil NACA de 4 dígitos, rotación por ángulo de ataque, prueba punto-dentro-de-polígono. *Logro:* máscara del perfil visualmente correcta a 0° y 10°.
- [ ] **M2 · Fuerzas.** Investiga: momentum exchange en los enlaces de bounce-back. Valida primero con el cilindro. *Logro:* Cd del cilindro a Re = 20 cercano al valor de referencia (\~2).
- [ ] **M3 · Polar.** Investiga: barrido de α de −4° a 12°, cuánto tarda en estabilizarse cada caso. LBM 2D en GPU funciona bien a Re bajos (\~10³); usa el mismo Re en tu solver de Julia. *Logro:* curvas Cl vs α y Cd vs α.
- [ ] **M4 · Comparación.** *Logro:* gráfica LBM vs tu solver Julia vs datos de referencia, y un README que explique las diferencias.

**Semana 19 · Optimización**

- [ ] **M1 · Perfilar.** Investiga: Nsight Systems para la línea de tiempo y Nsight Compute para occupancy y throughput de memoria. *Logro:* identificas el cuello de botella.
- [ ] **M2 · Acelerar.** Investiga: fusionar streaming + colisión en un kernel, float vs double, esquema AA o esoteric twist para ahorrar memoria. *Logro:* MLUPS antes y después.
- [ ] **M3 · Mac.** Porta el kernel a Metal. *Logro:* tabla de MLUPS Mac vs PC.

### Fase 5 — Pricing de derivados en GPU (semanas 20–22)

Solo si te atoras: *Joshi* caps. 1–8; *Hull* caps. de Black-Scholes y griegas; *Glasserman* cap. 4 (reducción de varianza).

**P9 · Monte Carlo de opciones europeas** (sem 20–21). Es tu P0 con esteroides: misma idea, ahora con dinero.

- [ ] **M1 · Black-Scholes cerrado.** Investiga: fórmula de call y put, `std::erfc` para la normal acumulada, paridad put-call. *Logro:* tus precios coinciden con una calculadora en línea y la paridad se cumple en un test.
- [ ] **M2 · Griegas.** Investiga: delta, gamma, vega analíticas vs diferencias finitas. *Logro:* ambas coinciden hasta 4 decimales.
- [ ] **M3 · Monte Carlo en CPU.** Investiga: movimiento browniano geométrico, paso exacto S\_T = S₀·exp((r − σ²/2)T + σ√T·Z), `std::normal_distribution`, error estándar. *Logro:* el precio cae dentro de 2 errores estándar del analítico.
- [ ] **M4 · Monte Carlo en CUDA.** Investiga: cuRAND (`curand_init` por hilo, `curand_normal`), reusar tu reducción de P3. *Logro:* 10⁸ trayectorias y speedup vs CPU.
- [ ] **M5 · Reducción de varianza.** Investiga: variables antitéticas, variable de control (el subyacente o la fórmula cerrada). *Logro:* gráfica error vs tiempo de cómputo para CPU, GPU y GPU con reducción de varianza.

*Al terminar deberías poder explicar:* por qué se usa la medida neutral al riesgo; por qué las antitéticas reducen varianza.

**P10 · Exóticas + puente a Python** (sem 22).

- [ ] **M1 · Dependientes de trayectoria.** Investiga: discretizar la trayectoria en pasos, opción asiática aritmética (con la geométrica cerrada como variable de control), barrera down-and-out. *Logro:* la barrera converge a su fórmula cerrada al aumentar los pasos.
- [ ] **M2 · nanobind.** Investiga: crear un módulo, recibir y devolver arrays de numpy, integrarlo en CMake. *Logro:* `import pricer` funciona en un notebook.
- [ ] **M3 · Superficie.** *Logro:* gráfica 3D de precio vs strike y vencimiento hecha desde Python con tu motor en C++.

*Al terminar deberías poder explicar:* por qué las exóticas casi siempre se valúan por Monte Carlo; qué sesgo mete discretizar una barrera.

### Fase 6 — Trading (semanas 23–26)

Solo si te atoras: *Chan* caps. 1–3; *López de Prado* caps. 2–3 y 7 (barras, etiquetado, validación cruzada purgada).

**P11 · Limit order book** (sem 23). El corazón de cualquier exchange; pregunta clásica de entrevista quant/dev.

- [ ] **M1 · Tipos.** Investiga: `struct Order` (id, lado, precio, cantidad, timestamp), por qué los precios van en enteros (ticks) y no en `double`, `enum class`. *Logro:* tipos definidos con tests.
- [ ] **M2 · Libro.** Investiga: `std::map<precio, std::deque<Order>>` para bids (orden descendente) y asks, `std::unordered_map` de id → posición para cancelar rápido. *Logro:* add y cancel funcionan con tests.
- [ ] **M3 · Matching.** Investiga: prioridad precio-tiempo, ejecuciones parciales, emitir trades. *Logro:* tests de 5 escenarios (cruce completo, parcial, varios niveles, cancelación, libro vacío).
- [ ] **M4 · Latencia.** Investiga: generador de mensajes sintéticos, medir cada operación con `chrono`, percentiles. *Logro:* procesa ≥1 millón de mensajes y reporta p50/p99.
- [ ] **M5 · Bonus.** Investiga: reemplazar el `map` por un arreglo indexado por nivel de precio. *Logro:* mejora medida en p99.

*Al terminar deberías poder explicar:* por qué no se usan `double` para precios; qué estructura usarías en producción y por qué.

**P12 · Backtester event-driven** (sem 24–26).

- [ ] **M1 · Datos.** Investiga: bajar precios diarios con `yfinance` a CSV, parser de CSV en C++. *Logro:* cargas 10 años de varios tickers.
- [ ] **M2 · Motor de eventos.** Investiga: `std::variant` para eventos (mercado, señal, orden, fill), `std::visit`, una cola de eventos. *Logro:* el loop recorre todos los datos sin estrategia.
- [ ] **M3 · Estrategia y portafolio.** Investiga: interfaz de estrategia (clase virtual o concept de C++20), cruce de medias móviles, PnL y posición. *Logro:* curva de equity.
- [ ] **M4 · Realismo.** Investiga: comisiones, slippage, look-ahead bias (escribe un test que lo detecte). *Logro:* curva de equity con y sin costos lado a lado.
- [ ] **M5 · Análisis en Python.** Investiga: exponer el motor con nanobind, Sharpe, max drawdown, validación walk-forward. *Logro:* reporte en notebook con una sección honesta de por qué podría ser sobreajuste.

*Al terminar deberías poder explicar:* qué es look-ahead bias; por qué un Sharpe alto en backtest suele ser mentira.

## Data science (sábados, 1 h)

Data science no se hace en C++, así que va como hilo paralelo en Python: 26 sesiones de sábado siguiendo *ISLP*, cada bloque con un mini-proyecto. A partir de la semana 18, los datos salen de tus propios simuladores, y ahí el C++ y el data science se juntan.

| Sábados | Tema (ISLP) | Mini-proyecto |
| --- | --- | --- |
| 1–4 | pandas, limpieza, EDA (McKinney caps. 5–10) | EDA de ventas de las ferreterías: estacionalidad, top productos, tickets |
| 5–8 | Regresión lineal y clasificación (ISLP caps. 3–4) | Predecir demanda semanal por categoría de producto |
| 9–12 | Resampling y regularización (caps. 5–6) | Validación cruzada bien hecha + Ridge/Lasso sobre el mismo problema |
| 13–17 | Árboles, random forest, boosting (cap. 8) | Comparar contra el modelo lineal; importancia de variables |
| 18–21 | Surrogate models | Entrena un modelo que prediga Cl/Cd de P8 a partir de α y Re: un "ML para CFD" de portafolio |
| 22–26 | Series de tiempo y validación financiera (López de Prado caps. 4 y 7) | Features y etiquetas para P12; validación cruzada purgada en vez de split aleatorio |

Si el objetivo es entrevistar en data science, añade en las semanas 20–26 un problema de SQL y uno de probabilidad por sesión.

## Lo que tendrás en abril 2027

Tres piezas de portafolio, una por cada dirección:

- **Aerodinámica:** solver LBM en GPU (CUDA + Metal) con validación contra tu solver de Julia en NACA 4412. Refuerza directamente la aplicación a la maestría en Southampton.
- **Quant:** pricer Monte Carlo en GPU con reducción de varianza + backtester event-driven en C++ usable desde Python.
- **Data science:** surrogate model de aerodinámica y análisis de demanda con datos reales del negocio familiar.

**Después de las 26 semanas**, según qué dirección te jale más: LBM en 3D y mallas no estructuradas (CFD), o microestructura de mercado y C++ de baja latencia (quant). Esa decisión es más fácil de tomar con los proyectos ya hechos.
