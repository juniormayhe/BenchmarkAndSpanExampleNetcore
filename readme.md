## Getting string token faster

This is a quick dive on Steve Gordon article by trying out other netcore frameworks. https://www.stevejgordon.co.uk/introduction-to-benchmarking-csharp-code-with-benchmark-dot-net

First two runs of Span on netcore 3.0 preview were slower, after those results got equivalent to netcore 2.2 and 2.1

### BenchmarkAndSpanExampleNetcore21

```
BenchmarkDotNet=v0.11.3, OS=Windows 10.0.17763.316 (1809/October2018Update/Redstone5)
Intel Core i7-3632QM CPU 2.20GHz (Ivy Bridge), 1 CPU, 8 logical and 4 physical cores
.NET Core SDK=2.2.200
  [Host]     : .NET Core 2.1.8 (CoreCLR 4.6.27317.03, CoreFX 4.6.27317.03), 64bit RyuJIT
  DefaultJob : .NET Core 2.1.8 (CoreCLR 4.6.27317.03, CoreFX 4.6.27317.03), 64bit RyuJIT

                    Method |      Mean |     Error |    StdDev | Ratio | Rank | Gen 0/1k Op | Gen 1/1k Op | Gen 2/1k Op | Allocated Memory/Op |
-------------------------- |----------:|----------:|----------:|------:|-----:|------------:|------------:|------------:|--------------------:|
       GetLastNameWithSpan |  20.80 ns | 0.1546 ns | 0.1207 ns |  0.09 |    1 |           - |           - |           - |                   - |
 GetLastNameUsingSubstring |  68.93 ns | 0.7426 ns | 0.6946 ns |  0.29 |    2 |      0.0126 |           - |           - |                40 B |
               GetLastName | 233.54 ns | 4.7248 ns | 5.0554 ns |  1.00 |    3 |      0.0505 |           - |           - |               160 B |

                    Method |      Mean |     Error |    StdDev | Ratio | Rank | Gen 0/1k Op | Gen 1/1k Op | Gen 2/1k Op | Allocated Memory/Op |
-------------------------- |----------:|----------:|----------:|------:|-----:|------------:|------------:|------------:|--------------------:|
       GetLastNameWithSpan |  20.87 ns | 0.1667 ns | 0.1392 ns |  0.09 |    1 |           - |           - |           - |                   - |
 GetLastNameUsingSubstring |  68.51 ns | 0.5383 ns | 0.5035 ns |  0.30 |    2 |      0.0126 |           - |           - |                40 B |
               GetLastName | 228.77 ns | 2.5852 ns | 2.4182 ns |  1.00 |    3 |      0.0505 |           - |           - |               160 B |


                    Method |      Mean |     Error |    StdDev | Ratio | Rank | Gen 0/1k Op | Gen 1/1k Op | Gen 2/1k Op | Allocated Memory/Op |
-------------------------- |----------:|----------:|----------:|------:|-----:|------------:|------------:|------------:|--------------------:|
       GetLastNameWithSpan |  20.94 ns | 0.0599 ns | 0.0531 ns |  0.09 |    1 |           - |           - |           - |                   - |
 GetLastNameUsingSubstring |  69.22 ns | 0.4910 ns | 0.4593 ns |  0.30 |    2 |      0.0126 |           - |           - |                40 B |
               GetLastName | 232.14 ns | 3.9765 ns | 3.7196 ns |  1.00 |    3 |      0.0505 |           - |           - |               160 B |
```

### BenchmarkAndSpanExampleNetcore22

```
BenchmarkDotNet=v0.11.3, OS=Windows 10.0.17763.316 (1809/October2018Update/Redstone5)
Intel Core i7-3632QM CPU 2.20GHz (Ivy Bridge), 1 CPU, 8 logical and 4 physical cores
.NET Core SDK=3.0.100-preview3-010431
  [Host]     : .NET Core 2.2.2 (CoreCLR 4.6.27317.07, CoreFX 4.6.27318.02), 64bit RyuJIT
  DefaultJob : .NET Core 2.2.2 (CoreCLR 4.6.27317.07, CoreFX 4.6.27318.02), 64bit RyuJIT

                    Method |      Mean |     Error |    StdDev | Ratio | Rank | Gen 0/1k Op | Gen 1/1k Op | Gen 2/1k Op | Allocated Memory/Op |
-------------------------- |----------:|----------:|----------:|------:|-----:|------------:|------------:|------------:|--------------------:|
       GetLastNameWithSpan |  21.78 ns | 0.1331 ns | 0.1180 ns |  0.10 |    1 |           - |           - |           - |                   - |
 GetLastNameUsingSubstring |  67.47 ns | 1.7610 ns | 2.0963 ns |  0.30 |    2 |      0.0126 |           - |           - |                40 B |
               GetLastName | 225.56 ns | 0.9658 ns | 0.8562 ns |  1.00 |    3 |      0.0508 |           - |           - |               160 B |

                    Method |      Mean |     Error |    StdDev | Ratio | Rank | Gen 0/1k Op | Gen 1/1k Op | Gen 2/1k Op | Allocated Memory/Op |
-------------------------- |----------:|----------:|----------:|------:|-----:|------------:|------------:|------------:|--------------------:|
       GetLastNameWithSpan |  21.72 ns | 0.1912 ns | 0.1695 ns |  0.09 |    1 |           - |           - |           - |                   - |
 GetLastNameUsingSubstring |  68.01 ns | 1.0019 ns | 0.8367 ns |  0.29 |    2 |      0.0126 |           - |           - |                40 B |
               GetLastName | 231.42 ns | 3.0836 ns | 2.8844 ns |  1.00 |    3 |      0.0505 |           - |           - |               160 B |

                    Method |      Mean |     Error |    StdDev | Ratio | Rank | Gen 0/1k Op | Gen 1/1k Op | Gen 2/1k Op | Allocated Memory/Op |
-------------------------- |----------:|----------:|----------:|------:|-----:|------------:|------------:|------------:|--------------------:|
       GetLastNameWithSpan |  21.65 ns | 0.1613 ns | 0.1430 ns |  0.10 |    1 |           - |           - |           - |                   - |
 GetLastNameUsingSubstring |  66.38 ns | 0.6083 ns | 0.5690 ns |  0.29 |    2 |      0.0126 |           - |           - |                40 B |
               GetLastName | 227.01 ns | 1.9017 ns | 1.6858 ns |  1.00 |    3 |      0.0508 |           - |           - |               160 B |
```

### BenchmarkAndSpanExampleNetcore30

```
BenchmarkDotNet=v0.11.3, OS=Windows 10.0.17763.316 (1809/October2018Update/Redstone5)
Intel Core i7-3632QM CPU 2.20GHz (Ivy Bridge), 1 CPU, 8 logical and 4 physical cores
.NET Core SDK=3.0.100-preview3-010431
  [Host]     : .NET Core 3.0.0-preview3-27503-5 (CoreCLR 4.6.27422.72, CoreFX 4.7.19.12807), 64bit RyuJIT
  DefaultJob : .NET Core 3.0.0-preview3-27503-5 (CoreCLR 4.6.27422.72, CoreFX 4.7.19.12807), 64bit RyuJIT

                    Method |      Mean |     Error |    StdDev | Ratio | Rank | Gen 0/1k Op | Gen 1/1k Op | Gen 2/1k Op | Allocated Memory/Op |
-------------------------- |----------:|----------:|----------:|------:|-----:|------------:|------------:|------------:|--------------------:|
       GetLastNameWithSpan |  51.51 ns | 0.1009 ns | 0.0843 ns |  0.09 |    1 |           - |           - |           - |                   - |
 GetLastNameUsingSubstring | 170.61 ns | 0.8733 ns | 0.8169 ns |  0.29 |    2 |      0.0126 |           - |           - |                40 B |
               GetLastName | 596.63 ns | 2.3562 ns | 2.0887 ns |  1.00 |    3 |      0.0458 |           - |           - |               144 B |

                    Method |      Mean |      Error |     StdDev |    Median | Ratio | RatioSD | Rank | Gen 0/1k Op | Gen 1/1k Op | Gen 2/1k Op | Allocated Memory/Op |
-------------------------- |----------:|-----------:|-----------:|----------:|------:|--------:|-----:|------------:|------------:|------------:|--------------------:|
       GetLastNameWithSpan |  52.55 ns |  1.1117 ns |  1.1895 ns |  52.04 ns |  0.08 |    0.01 |    1 |           - |           - |           - |                   - |
 GetLastNameUsingSubstring | 170.37 ns |  0.9685 ns |  0.8586 ns | 170.15 ns |  0.27 |    0.02 |    2 |      0.0126 |           - |           - |                40 B |
               GetLastName | 617.14 ns | 13.6648 ns | 30.5633 ns | 605.01 ns |  1.00 |    0.00 |    3 |      0.0458 |           - |           - |               144 B |
```

Just curious if Windows background processes had something to do with slower results for Span, next runs were faster

```
			        Method |      Mean |     Error |    StdDev | Ratio | RatioSD | Rank | Gen 0/1k Op | Gen 1/1k Op | Gen 2/1k Op | Allocated Memory/Op |
-------------------------- |----------:|----------:|----------:|------:|--------:|-----:|------------:|------------:|------------:|--------------------:|
       GetLastNameWithSpan |  20.39 ns | 0.1511 ns | 0.1180 ns |  0.08 |    0.00 |    1 |           - |           - |           - |                   - |
 GetLastNameUsingSubstring |  69.92 ns | 4.6613 ns | 4.9876 ns |  0.29 |    0.02 |    2 |      0.0126 |           - |           - |                40 B |
               GetLastName | 244.72 ns | 4.9347 ns | 5.8745 ns |  1.00 |    0.00 |    3 |      0.0458 |           - |           - |               144 B |

                    Method |      Mean |     Error |    StdDev | Ratio | Rank | Gen 0/1k Op | Gen 1/1k Op | Gen 2/1k Op | Allocated Memory/Op |
-------------------------- |----------:|----------:|----------:|------:|-----:|------------:|------------:|------------:|--------------------:|
       GetLastNameWithSpan |  20.22 ns | 0.1463 ns | 0.1369 ns |  0.08 |    1 |           - |           - |           - |                   - |
 GetLastNameUsingSubstring |  68.03 ns | 1.3958 ns | 1.6616 ns |  0.28 |    2 |      0.0126 |           - |           - |                40 B |
               GetLastName | 239.74 ns | 4.2343 ns | 3.9607 ns |  1.00 |    3 |      0.0458 |           - |           - |               144 B |

                    Method |      Mean |     Error |    StdDev | Ratio | Rank | Gen 0/1k Op | Gen 1/1k Op | Gen 2/1k Op | Allocated Memory/Op |
-------------------------- |----------:|----------:|----------:|------:|-----:|------------:|------------:|------------:|--------------------:|
       GetLastNameWithSpan |  20.64 ns | 0.4593 ns | 0.5290 ns |  0.09 |    1 |           - |           - |           - |                   - |
 GetLastNameUsingSubstring |  67.32 ns | 0.8982 ns | 0.8402 ns |  0.28 |    2 |      0.0126 |           - |           - |                40 B |
               GetLastName | 240.11 ns | 4.6513 ns | 6.2093 ns |  1.00 |    3 |      0.0458 |           - |           - |               144 B |

                    Method |      Mean |     Error |    StdDev | Ratio | Rank | Gen 0/1k Op | Gen 1/1k Op | Gen 2/1k Op | Allocated Memory/Op |
-------------------------- |----------:|----------:|----------:|------:|-----:|------------:|------------:|------------:|--------------------:|
       GetLastNameWithSpan |  20.58 ns | 0.1336 ns | 0.1249 ns |  0.09 |    1 |           - |           - |           - |                   - |
 GetLastNameUsingSubstring |  66.05 ns | 1.3836 ns | 1.8471 ns |  0.28 |    2 |      0.0126 |           - |           - |                40 B |
               GetLastName | 241.89 ns | 4.1271 ns | 3.8605 ns |  1.00 |    3 |      0.0458 |           - |           - |               144 B |
			   ```