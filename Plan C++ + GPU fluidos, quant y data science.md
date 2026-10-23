# C++ & GPU Computing Roadmap: CFD, Quant Finance and Data Science

Oct 21, 2026 · @Jesus Leobardo Nagao

## Summary

A 26-week, project-based roadmap (Oct 2026 – Apr 2027) for building high-performance C++ and GPU skills, at one hour per day. It covers 12 projects across 7 phases, from CPU fundamentals to GPU fluid solvers and quantitative finance engines. Each project is split into small milestones with a measurable completion criterion, and each one ships as a documented repository. The common thread: the same GPU patterns (map, reduce, stencil, Monte Carlo) power both aerodynamic simulation and derivatives pricing.

**Weekly cadence (5–6 h):**

- Mon–Thu: build. Roughly one milestone per session.
- Fri: close out the pending milestone, update the README, and answer the project's review questions.
- Sat (optional): data science session in Python (see Data science track).

**Working rules:**

- End every session with a "next step" note in the README, so the next session starts in two minutes.
- One monorepo (`cpp-gpu-lab/`) with a folder per project and CMake at the root, building on macOS and Windows from day one.
- If a project runs long, cut scope rather than pushing the schedule.
- Hardware: PC with an NVIDIA GPU (CUDA) and a Mac with Apple Silicon (Metal).

## Tooling stack

No single GPU API runs natively on both machines: macOS does not support CUDA and Windows does not support Metal. The approach: CUDA as the primary compute language (the standard in HPC, CFD and quant finance), Metal for the Mac, and WebGPU as the cross-platform layer for graphics.

| Layer | PC (NVIDIA) | Mac (Apple Silicon) | Rationale |
| --- | --- | --- | --- |
| Compiler / build | MSVC or clang + CMake + vcpkg | clang (Xcode CLT) + CMake + Homebrew | CMake is the industry-standard build system |
| GPU compute | CUDA (nvcc), then Thrust/CUB | Metal via metal-cpp | CUDA dominates HPC and finance; metal-cpp keeps the Mac code in C++ |
| Graphics | WebGPU (Dawn or wgpu-native) + GLFW | Same: WebGPU runs on top of Metal | One rendering codebase for both machines, with compute support |
| CPU math | Eigen | Eigen | Standard C++ linear algebra library |
| Testing / benchmarking | Catch2 + Google Benchmark | Same | Measurement is half of GPU work |
| Python bridge | nanobind or pybind11 | Same | Exposes C++ engines to pandas and notebooks |
| Profiling | Nsight Systems / Nsight Compute | Xcode Instruments (Metal System Trace) | Performance claims need profiler evidence |

**Why not OpenGL:** Apple froze it at version 4.1 with no compute shaders. WebGPU is the modern successor that keeps the Mac in play. Vulkan is the alternative for maximum control on the PC, but it is far more verbose for a one-hour daily budget.

## References

None are required reading; they are consulted when a milestone gets stuck. Starred (★) titles are the most useful. Editions are listed from memory; check for the latest before buying.

### Modern C++ and performance

| Reference | Used in |
| --- | --- |
| ★ Stroustrup, *A Tour of C++* (3rd ed., 2022) | Phases 0–1. Fast tour of C++20 for experienced programmers |
| Meyers, *Effective Modern C++* | Phase 1. Move semantics, smart pointers, lambdas |
| Williams, *C++ Concurrency in Action* (2nd ed.) | Phase 1, threading milestones |
| Slotin, *Algorithms for Modern Hardware* (free online) | Caches, SIMD, why fast code looks the way it does |

### GPU computing

| Reference | Used in |
| --- | --- |
| ★ Hwu, Kirk & El Hajj, *Programming Massively Parallel Processors* (4th ed., 2022) | Phase 2 onward. Tiling, reductions, scans, stencils |
| NVIDIA, *CUDA C++ Programming Guide* and *Best Practices Guide* (free) | Ongoing reference |
| Apple, *Metal Shading Language Specification* + metal-cpp samples (free) | Phase 2, Mac ports |
| Michel, *Learn WebGPU for C++* (free online) | Phase 3. Step-by-step rendering and compute |
| Akenine-Möller et al., *Real-Time Rendering* (4th ed.) | Optional graphics depth |

### Fluid dynamics and aerodynamics

| Reference | Used in |
| --- | --- |
| ★ Bridson, *Fluid Simulation for Computer Graphics* (2nd ed.) | Phase 3. Stable fluids, grids, pressure projection |
| ★ Krüger et al., *The Lattice Boltzmann Method: Principles and Practice* (2017) | Phase 4. LBM, the most GPU-friendly CFD method |
| Ferziger, Perić & Street, *Computational Methods for Fluid Dynamics* (4th ed.) | Formal finite-volume reference |
| Harris, "Fast Fluid Dynamics Simulation on the GPU", *GPU Gems* ch. 38 (free) | Phase 3. Single chapter, straight to code |

### Quantitative finance

| Reference | Used in |
| --- | --- |
| ★ Joshi, *C++ Design Patterns and Derivatives Pricing* (2nd ed.) | Phase 5. C++ and pricing together |
| Hull, *Options, Futures, and Other Derivatives* | Finance reference (Black-Scholes, Greeks) |
| Glasserman, *Monte Carlo Methods in Financial Engineering* | Phase 5. Variance reduction, quasi-Monte Carlo |
| Chan, *Quantitative Trading* (2nd ed.) | Phase 6. Honest backtesting |
| López de Prado, *Advances in Financial Machine Learning* | Phase 6. Bridge between quant finance and ML |
| Zhou, *A Practical Guide to Quantitative Finance Interviews* | 2–3 problems per week alongside the plan |

### Data science

| Reference | Used in |
| --- | --- |
| ★ James, Witten, Hastie, Tibshirani & Taylor, *An Introduction to Statistical Learning with Applications in Python* (free) | Saturday sessions |
| McKinney, *Python for Data Analysis* (3rd ed., free online) | pandas in depth |
| Géron, *Hands-On Machine Learning* (latest edition) | Moving from classical models to neural networks |

## Roadmap

12 projects over 26 weeks. Every milestone lists what to research and a measurable *Done when* criterion: meet it and move on, even if the code isn't perfect.

| Phase | Weeks | Focus | Projects |
| --- | --- | --- | --- |
| 0 | 1–2 | Setup and C++ ramp-up | P0 |
| 1 | 3–6 | Modern C++ and fast CPU code | P1, P2 |
| 2 | 7–10 | GPU compute | P3, P4 |
| 3 | 11–14 | Graphics and 2D fluids | P5, P6 |
| 4 | 15–19 | Aerodynamics with Lattice Boltzmann | P7, P8 |
| 5 | 20–22 | GPU derivatives pricing | P9, P10 |
| 6 | 23–26 | Trading: order book and backtester | P11, P12 |

### Phase 0 — Setup (weeks 1–2)

If stuck: *A Tour of C++* ch. 1–5.

**P0 · Monte Carlo estimation of π** (wk 1–2). Throw random points into a unit square; the fraction inside the quarter circle × 4 ≈ π. Each milestone takes 1–2 one-hour sessions.

- [ ] **M1 · Hello world on Mac and PC.** Research: minimal `CMakeLists.txt`, `add_executable`, `target_compile_features(... cxx_std_20)`, out-of-source builds (`cmake -B build`), `.gitignore` for `build/`. *Done when:* the same command builds and runs on both machines and the repo is on GitHub.
- [ ] **M2 · First estimate.** Research: `<random>`, `std::mt19937_64`, `std::uniform_real_distribution<double>`, why not `rand()`. *Done when:* it prints π ≈ 3.14 for N = 10⁶.
- [ ] **M3 · Arguments and reproducibility.** Research: `argc/argv`, `std::stoull`, fixed seed vs `std::random_device`. *Done when:* `./pi 1000000 42` always returns the same value; without a seed it varies.
- [ ] **M4 · Measure speed.** Research: `std::chrono::steady_clock`, `duration_cast`, `CMAKE_BUILD_TYPE` Debug vs Release, what `-O2` does. *Done when:* it prints samples per second and you record the Release speedup.
- [ ] **M5 · Convergence.** Research: `std::ofstream` to write CSV, absolute error vs `std::numbers::pi`. Sweep N from 10² to 10⁸ and plot in Python. *Done when:* a log-log plot of error vs N shows slope ≈ −1/2.
- [ ] **M6 · Clean structure.** Research: `.hpp`/`.cpp` split, `add_library` + `target_link_libraries`, `namespace`, passing the generator by reference. *Done when:* `main.cpp` is \~15 lines calling `estimate_pi(n, rng)`.
- [ ] **M7 · Bonus: multiple threads.** Research: `std::thread` or `std::async` + `std::future`, `std::thread::hardware_concurrency`, why each thread needs its own generator. *Done when:* a table of runtime vs thread count (1, 2, 4, 8…).

*Review questions:* What does CMake do versus the compiler? Why is Release faster? Why does the error fall as 1/√N? What happens if two threads share a generator?

### Phase 1 — Modern C++ (weeks 3–6)

If stuck: *A Tour of C++* ch. 6–13; *Effective Modern C++* items 1–30 as needed.

**P1 · Mini linear algebra library** (wk 3–4). Custom `Vector` and `Matrix` types, reused in P2 and P4.

- [ ] **M1 · Generic vector.** Research: `template<typename T>`, `std::vector<T>` as storage, `operator[]`, `std::size_t`. *Done when:* `Vector<double> v(3); v[0] = 1.0;` compiles and works.
- [ ] **M2 · Operators.** Research: overloading `operator+`, `-`, scalar `*`, `dot()` and `norm()` methods, const correctness, pass by `const&`. *Done when:* `auto c = a + 2.0 * b;` gives the correct result.
- [ ] **M3 · Tests.** Research: Catch2 via `FetchContent`, `TEST_CASE`, `REQUIRE`, `Catch::Approx`, `ctest`. *Done when:* `ctest` passes with ≥10 tests.
- [ ] **M4 · Matrix.** Research: row-major storage (`i*cols + j`), `operator()(i, j)`, naive multiplication. *Done when:* tests for `A * I == A` and `A * B` against a hand-computed case pass.
- [ ] **M5 · Copies vs moves.** Research: copy and move constructors, rule of zero/five, `std::move`. Add a `std::cout` to each constructor. *Done when:* you can explain how many copies `c = a + b` makes, and reduce them.
- [ ] **M6 · Benchmark.** Research: Google Benchmark, `ijk` vs `ikj` loop order, CPU caches, Eigen via `FetchContent` or vcpkg. Matmul FLOPs = 2n³. *Done when:* a GFLOP/s table for naive, reordered loops and Eigen at n = 256, 512, 1024.

*Review questions:* Why is `ikj` faster than `ijk`? What is template instantiation? When does a move avoid work?

**P2 · Gravitational N-body** (wk 5–6).

- [ ] **M1 · Forces.** Research: `struct Body` with position, velocity and mass; O(n²) force summation; softening to avoid division by zero. *Done when:* two bodies hold a stable circular orbit.
- [ ] **M2 · Integrator.** Research: explicit Euler vs leapfrog (velocity Verlet), kinetic + potential energy. *Done when:* an energy-vs-time plot where Euler drifts and leapfrog stays within 0.1 %.
- [ ] **M3 · Visualize.** Research: write positions to CSV, animate with `matplotlib.animation`. *Done when:* a GIF of the figure-eight three-body orbit.
- [ ] **M4 · Manual threading.** Research: `std::thread`, splitting the `i` range across threads, `join`, data races, why the outer loop is safe to parallelize. *Done when:* a speedup table for 1–8 threads at n = 2000.
- [ ] **M5 · OpenMP.** Research: `#pragma omp parallel for`, `find_package(OpenMP)` (on Mac: `brew install libomp`; Apple's libc++ lacks `std::execution` policies). *Done when:* manual threads vs OpenMP comparison.
- [ ] **M6 · Memory layout.** Research: Array of Structs vs Struct of Arrays. *Done when:* the same simulation in SoA with the measured difference. This sets up the GPU phase.

*Review questions:* What is a data race? Why does leapfrog conserve energy? Why does hardware prefer SoA?

### Phase 2 — GPU compute (weeks 7–10)

If stuck: *Programming Massively Parallel Processors* ch. 1–6 and 10 (reductions).

**P3 · Core GPU kernels** (wk 7–9). The four patterns that show up everywhere: map, reduction, matrix multiply, and the same code on a second API.

- [ ] **M1 · First kernel.** Research: `project(... LANGUAGES CXX CUDA)`, `CMAKE_CUDA_ARCHITECTURES`, `.cu` files, `__global__`, in-kernel `printf`, `threadIdx`/`blockIdx`. *Done when:* a kernel prints every thread's id.
- [ ] **M2 · SAXPY.** Research: `cudaMalloc`, `cudaMemcpy`, `<<<blocks, threads>>>`, global index calculation, a CUDA error-check macro, `cudaEvent` timing. *Done when:* `y = a*x + y` on 10⁸ elements is correct, and achieved bandwidth (GB/s) is compared against the card's theoretical peak.
- [ ] **M3 · Unified memory.** Research: `cudaMallocManaged` vs explicit copies. *Done when:* SAXPY both ways, with a note on when each fits.
- [ ] **M4 · Reduction.** Research: `__shared__`, `__syncthreads()`, tree reduction, `__shfl_down_sync`, `atomicAdd`. *Done when:* the sum of 10⁸ numbers matches the CPU result (mind float vs double), across 3 progressively faster versions.
- [ ] **M5 · Matrix multiply.** Research: naive version, shared-memory tiling (16×16), memory coalescing, cuBLAS as the baseline. *Done when:* tiled is ≥5× faster than naive, and its % of cuBLAS is reported.
- [ ] **M6 · Port to Metal (wk 9).** Research: metal-cpp, `MTL::Device`, `.metal` shader files, compute pipeline, command buffer and encoder, `threadgroup` memory (the shared-memory equivalent). *Done when:* SAXPY and tiled matmul run on the Mac; CPU vs CUDA vs Metal table.

*Review questions:* What are a warp, a block and a grid? Why does coalesced access matter? What limits SAXPY (memory) versus matmul (compute)?

**P4 · 2D heat equation on the GPU** (wk 10).

- [ ] **M1 · CPU reference.** Research: 5-point stencil, stability condition dt ≤ dx²/(4α). Reuse the P1 `Matrix`. *Done when:* a CPU solution that diffuses as expected.
- [ ] **M2 · 2D kernel.** Research: `dim3` for 2D blocks, ping-pong buffers swapped each step, boundary handling. *Done when:* max difference vs CPU < 10⁻¹².
- [ ] **M3 · Tiling with halo.** Research: loading a tile plus its borders into shared memory. *Done when:* measured speedup over the simple kernel.
- [ ] **M4 · Metal version.** *Done when:* identical results on the Mac.

*Review questions:* Why are stencils memory-bound? What happens when the stability condition is violated?

### Phase 3 — Graphics and 2D fluids (weeks 11–14)

If stuck: *Learn WebGPU for C++* (basics and compute sections); Bridson ch. 1–5; *GPU Gems* ch. 38.

**P5 · Real-time visualizer** (wk 11–12). Sharing buffers between CUDA and WebGPU is complex, so the interactive simulations run as WebGPU compute shaders; CUDA stays for heavy offline compute.

- [ ] **M1 · Window.** Research: GLFW, Dawn or wgpu-native (the *Learn WebGPU for C++* repo ships a working CMake setup), `glfw3webgpu` for the surface. *Done when:* a window clears to a color on Mac and PC.
- [ ] **M2 · Triangle.** Research: render pipeline, WGSL vertex and fragment shaders, vertex buffers. *Done when:* the classic colored triangle renders.
- [ ] **M3 · Texture on screen.** Research: full-screen quad, `queue.writeTexture`, samplers, bind groups. *Done when:* a CPU-generated image is displayed.
- [ ] **M4 · Colormap.** Research: mapping a scalar to color in the fragment shader (viridis approximation). *Done when:* the field sin(x)·cos(y) renders in color.
- [ ] **M5 · GPU simulation.** Research: compute pipelines, storage buffers, ping-pong in WGSL. Port the P4 stencil. *Done when:* live heat diffusion at ≥60 fps on both machines.
- [ ] **M6 · Interaction.** Research: GLFW mouse callbacks, Dear ImGui with the WebGPU backend. *Done when:* clicking paints heat and a slider changes α live.

*Review questions:* What is the difference between a render pipeline and a compute pipeline? What is a bind group?

**P6 · Stable Fluids (Stam) on the GPU** (wk 13–14). Builds on the P5 infrastructure.

- [ ] **M1 · Fields.** Research: storing velocity (u, v), pressure and dye in ping-pong buffers. *Done when:* the data structures are in place and dye is visible.
- [ ] **M2 · Advection.** Research: semi-Lagrangian advection (backtracing), bilinear interpolation. *Done when:* dye moves along a fixed velocity field.
- [ ] **M3 · Forces.** Research: converting mouse drag into velocity. *Done when:* the fluid can be pushed around.
- [ ] **M4 · Projection.** Research: divergence, Jacobi iteration for pressure, gradient subtraction (the same Chorin projection used in the Julia solver). *Done when:* max divergence after projection is ≈ 0, measured and shown in ImGui.
- [ ] **M5 · Polish.** Research: vorticity confinement, boundary conditions, obstacles. *Done when:* a 512×512 grid runs stably at 60 fps with an obstacle.

*Review questions:* Why is semi-Lagrangian advection unconditionally stable? What does Stable Fluids give up compared with a production CFD solver?

### Phase 4 — Aerodynamics with Lattice Boltzmann (weeks 15–19)

If stuck: Krüger et al. ch. 1–5 (BGK, boundary conditions) and 7 (forces).

**P7 · LBM D2Q9: flow past a cylinder** (wk 15–16).

- [ ] **M1 · Minimal CPU LBM.** Research: the 9 velocities and weights of D2Q9, equilibrium distribution, BGK collision, the relation ν = (τ − 0.5)/3. *Done when:* Poiseuille channel flow matches the analytical parabolic profile.
- [ ] **M2 · CUDA port.** Research: one thread per node, SoA layout `f[q][node]`, pull vs push streaming. *Done when:* the same Poiseuille result on the GPU, plus a first MLUPS figure.
- [ ] **M3 · Cylinder.** Research: obstacle mask, half-way bounce-back, velocity inlet (Zou-He) and outlet. *Done when:* steady, symmetric flow at Re = 20.
- [ ] **M4 · Vorticity view.** Research: discrete curl; copy the field to host and upload it as a P5 texture, or write PNGs with `stb_image_write`. *Done when:* a von Kármán vortex street at Re = 100.
- [ ] **M5 · Strouhal number.** Research: probe v at a point behind the cylinder, FFT with numpy. *Done when:* St ≈ 0.16–0.2.

*Review questions:* How does LBM recover Navier-Stokes? Why does τ near 0.5 become unstable?

**P8 · NACA 4412 airfoil in LBM** (wk 17–18). The flagship aerospace portfolio piece.

- [ ] **M1 · Geometry.** Research: NACA 4-digit airfoil equations, rotation by angle of attack, point-in-polygon test. *Done when:* the airfoil mask is visually correct at 0° and 10°.
- [ ] **M2 · Forces.** Research: momentum exchange on bounce-back links. Validate on the cylinder first. *Done when:* cylinder Cd at Re = 20 is close to the reference value (\~2).
- [ ] **M3 · Polar.** Research: sweeping α from −4° to 12°, settling time per case. 2D GPU LBM works best at low Re (\~10³); run the Julia solver at the same Re. *Done when:* Cl vs α and Cd vs α curves.
- [ ] **M4 · Comparison.** *Done when:* a plot of LBM vs the Julia finite-difference solver vs reference data, with a README explaining the differences.

**Week 19 · Performance optimization**

- [ ] **M1 · Profile.** Research: Nsight Systems for the timeline, Nsight Compute for occupancy and memory throughput. *Done when:* the bottleneck is identified.
- [ ] **M2 · Speed up.** Research: fused stream-collide kernel, float vs double, AA pattern or esoteric twist to halve memory. *Done when:* MLUPS before and after.
- [ ] **M3 · Mac.** Port the kernel to Metal. *Done when:* MLUPS table for Mac vs PC.

### Phase 5 — GPU derivatives pricing (weeks 20–22)

If stuck: Joshi ch. 1–8; Hull's chapters on Black-Scholes and the Greeks; Glasserman ch. 4 (variance reduction).

**P9 · Monte Carlo pricing of European options** (wk 20–21). P0 scaled up: same idea, now applied to pricing.

- [ ] **M1 · Closed-form Black-Scholes.** Research: call and put formulas, `std::erfc` for the normal CDF, put-call parity. *Done when:* prices match an online calculator and parity holds in a test.
- [ ] **M2 · Greeks.** Research: analytical delta, gamma and vega vs finite differences. *Done when:* both agree to 4 decimal places.
- [ ] **M3 · CPU Monte Carlo.** Research: geometric Brownian motion, exact step S\_T = S₀·exp((r − σ²/2)T + σ√T·Z), `std::normal_distribution`, standard error. *Done when:* the price falls within 2 standard errors of the analytical value.
- [ ] **M4 · CUDA Monte Carlo.** Research: cuRAND (`curand_init` per thread, `curand_normal`), reusing the P3 reduction. *Done when:* 10⁸ paths with measured speedup over CPU.
- [ ] **M5 · Variance reduction.** Research: antithetic variates, control variates (the underlying or the closed form). *Done when:* an error-vs-compute-time plot for CPU, GPU and GPU with variance reduction.

*Review questions:* Why price under the risk-neutral measure? Why do antithetic variates reduce variance?

**P10 · Exotic options and a Python bridge** (wk 22).

- [ ] **M1 · Path-dependent payoffs.** Research: time-stepped paths, arithmetic Asian options (with the closed-form geometric Asian as a control variate), down-and-out barriers. *Done when:* the barrier price converges to its closed form as the step count increases.
- [ ] **M2 · nanobind.** Research: building a module, passing numpy arrays in and out, CMake integration. *Done when:* `import pricer` works in a notebook.
- [ ] **M3 · Price surface.** *Done when:* a 3D plot of price vs strike and maturity, produced from Python on top of the C++ engine.

*Review questions:* Why are exotics usually priced by Monte Carlo? What bias does discrete barrier monitoring introduce?

### Phase 6 — Trading systems (weeks 23–26)

If stuck: Chan ch. 1–3; López de Prado ch. 2–3 and 7 (bars, labeling, purged cross-validation).

**P11 · Limit order book** (wk 23). The core of every exchange and a classic quant-developer interview topic.

- [ ] **M1 · Types.** Research: `struct Order` (id, side, price, quantity, timestamp), why prices are stored as integer ticks rather than `double`, `enum class`. *Done when:* types are defined and tested.
- [ ] **M2 · Book.** Research: `std::map<price, std::deque<Order>>` for bids (descending) and asks, `std::unordered_map` from id to position for fast cancels. *Done when:* add and cancel pass their tests.
- [ ] **M3 · Matching engine.** Research: price-time priority, partial fills, trade reporting. *Done when:* tests cover 5 scenarios (full cross, partial fill, multi-level sweep, cancel, empty book).
- [ ] **M4 · Latency.** Research: synthetic message generator, per-operation `chrono` timing, percentiles. *Done when:* ≥1 million messages processed with p50/p99 latency reported.
- [ ] **M5 · Bonus.** Research: replacing the `map` with an array indexed by price level. *Done when:* measured p99 improvement.

*Review questions:* Why not use `double` for prices? Which data structure would a production system use, and why?

**P12 · Event-driven backtester** (wk 24–26).

- [ ] **M1 · Data.** Research: daily prices via `yfinance` to CSV, a C++ CSV parser. *Done when:* 10 years of data for several tickers loads.
- [ ] **M2 · Event engine.** Research: `std::variant` for events (market, signal, order, fill), `std::visit`, an event queue. *Done when:* the loop processes all data with no strategy attached.
- [ ] **M3 · Strategy and portfolio.** Research: a strategy interface (virtual class or C++20 concept), moving-average crossover, PnL and position tracking. *Done when:* an equity curve.
- [ ] **M4 · Realism.** Research: commissions, slippage, look-ahead bias (write a test that catches it). *Done when:* equity curves with and without costs side by side.
- [ ] **M5 · Python analysis.** Research: exposing the engine via nanobind, Sharpe ratio, max drawdown, walk-forward validation. *Done when:* a notebook report with an honest section on overfitting risk.

*Review questions:* What is look-ahead bias? Why is a high backtested Sharpe ratio usually misleading?

## Data science track (Saturdays, 1 h)

Data science work happens in Python, so it runs as a parallel track: 26 Saturday sessions following *ISLP*, each block ending in a mini-project. From week 18 on, the datasets come from the C++ simulators and engines built in the main roadmap, so the two tracks converge.

| Saturdays | Topic | Mini-project |
| --- | --- | --- |
| 1–4 | pandas, cleaning, EDA (McKinney ch. 5–10) | EDA of retail sales data from a hardware-store business: seasonality, top products, basket size |
| 5–8 | Linear regression and classification (ISLP ch. 3–4) | Weekly demand forecast by product category |
| 9–12 | Resampling and regularization (ch. 5–6) | Proper cross-validation plus Ridge/Lasso on the same problem |
| 13–17 | Trees, random forests, boosting (ch. 8) | Benchmark against the linear model; feature importance |
| 18–21 | Surrogate modeling | Train a model that predicts P8's Cl/Cd from α and Re: an ML-for-CFD portfolio piece |
| 22–26 | Time series and financial validation (López de Prado ch. 4 and 7) | Features and labels for P12; purged cross-validation instead of random splits |

For data science interview preparation, add one SQL and one probability problem per session during weeks 20–26.

## Outcomes (April 2027)

Three portfolio pieces, one per career direction:

- **Aerospace / CFD:** a GPU Lattice Boltzmann solver (CUDA + Metal), validated against an independent finite-difference Navier-Stokes solver (Julia) on the NACA 4412 airfoil.
- **Quant:** a GPU Monte Carlo pricer with variance reduction, plus an event-driven C++ backtester and limit order book, exposed to Python.
- **Data science:** an aerodynamic surrogate model and a demand-forecasting analysis on real retail data.

**Beyond week 26**, depending on direction: 3D LBM and unstructured meshes (CFD), or market microstructure and low-latency C++ (quant).
