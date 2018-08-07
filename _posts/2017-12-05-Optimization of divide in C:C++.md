---
layout: post
title:  Optimization of divide in C/C++
categories: c/c++
tags: code-optimization
---

I'd heard about that before:

> The modern compiler will optimized division by 2 (and its power) as right-shift for integer.

But rently I'm tring to optimized some codes and found that's only half right, morden compiler will only try to replace division by 2 (and its power) by right-shift implicit for unsigned integer.

Since shift of an negative integer is **implement defined**, which means the result of right shift on a negative interger might be different with division, for example:

```c++
-5 / 2  // => -2
-5 >> 1 // => -3
```

That's why compiler wouldn't replace division by 2 (and its power) as right-shift for signed integer, following is bench mark result for division by 2 and right shift with various integer:

```bash
Run on (4 X 2400 MHz CPU s)
CPU Caches:
  L1 Data 32K (x2)
  L1 Instruction 32K (x2)
  L2 Unified 262K (x2)
  L3 Unified 3145K (x1)
---------------------------------------------------------------
Benchmark                        Time           CPU Iterations
---------------------------------------------------------------
BM_div_int                   29162 ns      29101 ns      23729
BM_div_unsigned_int           3779 ns       3761 ns     187839
BM_div_short                 50725 ns      50643 ns      12916
BM_div_unsigned_short         7282 ns       7266 ns      94185
BM_div_char                  29228 ns      29177 ns      23323
BM_div_unsigned_char          3707 ns       3694 ns     197558
BM_shift_int                  3671 ns       3653 ns     186107
BM_shift_unsigned_int         3896 ns       3873 ns     184216
BM_shift_short                3683 ns       3673 ns     183546
BM_shift_unsigned_short       7466 ns       7447 ns      95329
BM_shift_char                 3656 ns       3651 ns     184243
BM_shift_unsigned_char        3680 ns       3666 ns     191142
```

Here's the code for benchmark, I tested on Apple LLVM version 9.0.0 (clang-900.0.38), with -O3 switch.

```c++
#include <benchmark/benchmark.h>

template <typename T>
static T div(T x)
{
    for (size_t i = 0; i < 100000; ++i)
        x = x / 2;
    return x;
}

template <typename T>
static T shift(T x)
{
    for (size_t i = 0; i < 100000; ++i)
        x = x >> 1;
    return x;
}

template <typename T>
static void BM_div(benchmark::State& state)
{
    for (auto _ : state)
        benchmark::DoNotOptimize(div<T>(rand()));
}

static void BM_div_int(benchmark::State& state) { BM_div<int>(state); }
BENCHMARK(BM_div_int);
static void BM_div_unsigned_int(benchmark::State& state) { BM_div<unsigned int>(state); }
BENCHMARK(BM_div_unsigned_int);
static void BM_div_short(benchmark::State& state) { BM_div<short>(state); }
BENCHMARK(BM_div_short);
static void BM_div_unsigned_short(benchmark::State& state) { BM_div<unsigned short>(state); }
BENCHMARK(BM_div_unsigned_short);
static void BM_div_char(benchmark::State& state) { BM_div<char>(state); }
BENCHMARK(BM_div_char);
static void BM_div_unsigned_char(benchmark::State& state) { BM_div<unsigned char>(state); }
BENCHMARK(BM_div_unsigned_char);

template <typename T>
static void BM_shift(benchmark::State& state)
{
    for (auto _ : state)
        benchmark::DoNotOptimize(shift<T>(rand()));
}

static void BM_shift_int(benchmark::State& state) { BM_shift<int>(state); }
BENCHMARK(BM_shift_int);
static void BM_shift_unsigned_int(benchmark::State& state) { BM_shift<unsigned int>(state); }
BENCHMARK(BM_shift_unsigned_int);
static void BM_shift_short(benchmark::State& state) { BM_shift<short>(state); }
BENCHMARK(BM_shift_short);
static void BM_shift_unsigned_short(benchmark::State& state) { BM_shift<unsigned short>(state); }
BENCHMARK(BM_shift_unsigned_short);
static void BM_shift_char(benchmark::State& state) { BM_shift<char>(state); }
BENCHMARK(BM_shift_char);
static void BM_shift_unsigned_char(benchmark::State& state) { BM_shift<unsigned char>(state); }
BENCHMARK(BM_shift_unsigned_char);

BENCHMARK_MAIN();
```