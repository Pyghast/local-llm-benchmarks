# Local LLM Benchmarks

Benchmarks and comparisons of locally hosted LLMs running on an NVIDIA GeForce RTX 5070 12GB.

## Hardware & Environment

- CPU: AMD Ryzen 7 7745HX
- MOBO: Minisforum BD775i SE
- RAM: 32GB SODIMM DDR5 (Kingston)
- GPU: NVIDIA GeForce RTX 5070 12GB
- OS: Windows 11 IoT Enterprise LTSC
- NVIDIA Driver: 610.88
- CUDA Toolkit: 12.8
- cuDNN: 9.24.0
- Backend: Ollama 0.32.14
- Context length: 4096 tokens

## Benchmark Results

| Model | Parameters | Quantization | Model Size | GPU / CPU | Context | Prompt Eval | Generation | Output Tokens | Total Time |
|---|---:|---:|---:|---:|---:|---:|---:|---:|---:|
| Gemma 4 E4B | 8.0B | Q4_K_M | 3.2 GB | 100% GPU | 4096 | 4.13 tok/s | **142.61 tok/s** | 420 | 8.57 s |
| Qwen3.5 9B | 9.7B | Q4_K_M | 5.5 GB | 100% GPU | 4096 | 297.44 tok/s | **104.58 tok/s** | 523 | 5.30 s |
| Qwen3.8 27B | 27.3B | Q4_K_M | 18 GB | 48% GPU / 52% CPU | 4096 | 16.60 tok/s | 7.47 tok/s | 478 | 65.70 s |
| Qwen3.6 35B-A3B | 36.0B | Q4_K_M | 23 GB | 41% GPU / 59% CPU | 4096 | 10.56 tok/s | **51.86 tok/s** | 491 | 12.15 s |
| LFM2 | 23.8B | Q4_K_M | 14 GB | 70% GPU / 30% CPU | 4096 | 6.92 tok/s | **99.01 tok/s** | 345 | 6.44 s |

## Results

### Qwen3.5 9B

Qwen3.5 9B fits entirely in the RTX 5070's VRAM and runs with 100% GPU offload.

- Model size: 5.5 GB
- GPU offload: 100%
- Generation: 104.58 tok/s
- Prompt processing: 297.44 tok/s
- Total benchmark time: 5.30 s

### Qwen3.8 27B

Qwen3.8 27B does not fit entirely within 12GB of VRAM.

At a 4096-token context, Ollama reports:

- 48% GPU
- 52% CPU
- Generation: 7.47 tok/s
- Prompt processing: 16.60 tok/s
- Total benchmark time: 65.70 s

Reducing context from 16384 to 4096 improved GPU offload only slightly, from approximately 45% to 48%.

The small change in GPU offload suggests that model size, rather than KV cache usage, is the dominant memory constraint in this setup.

### Qwen3.6 35B-A3B

Qwen3.6 35B-A3B does not fit entirely within the RTX 5070's VRAM and runs with partial GPU offload.

- Model size: 23 GB
- GPU offload: 41%
- CPU offload: 59%
- Generation: 51.86 tok/s
- Prompt processing: 10.56 tok/s
- Total benchmark time: 12.15 s

Despite being the largest model tested at 23 GB and having only 41% GPU offload, it achieves a generation speed of 51.86 tok/s.

### Gemma 4 E4B

Gemma 4 E4B is the lightest of them all and fits entirely on the GPU.

- Model size: 3.2 GB
- GPU offload: 100%
- Generation: 142.61 tok/s
- Prompt processing: 4.13 tok/s
- Total benchmark time: 8.57 s

It currently has the highest raw generation speed of the tested models.

Low prompt evaluation speed.

### LFM2

LFM2 does not fit entirely within the RTX 5070's VRAM and runs with partial GPU offload.

- Model size: 14 GB
- GPU offload: 70%
- CPU offload: 30%
- Generation: 99.01 tok/s
- Prompt processing: 6.92 tok/s
- Total benchmark time: 6.44 s

Despite requiring partial CPU offload, LFM2 achieves a generation speed of 99.01 tok/s.

## Generation Speed

| Model | Tokens/s |
|---|---:|
| Gemma 4 E4B | **142.61** |
| Qwen3.5 9B | **104.58** |
| LFM2 | **99.01** |
| Qwen3.6 35B-A3B | **51.86** |
| Qwen3.8 27B | **7.47** |

## Notes

Based on hardware and software configuration these results may vary.

Performance can vary depending on:

- Quantization
- Context length
- Ollama version
- GPU offload
- Prompt length
- Thinking/reasoning mode
- Model architecture

For meaningful comparisons, models should be tested using the same prompt, context length, and generation settings, in my case I asked each and every model "Explain how a transistor works in about 300 words." and used "--think=false" and "--verbose" CLI flags.

## License

MIT
