---
layout: post
title: Compiling oxDNA in SOL
date: 2024-04-01 00:00:00
description: Compiling oxDNA in SOL step-by-step 
tags: linux
categories: sample-posts
giscus_comments: true
featured: true
---

Below is a brief procedure to compile oxDNA in SOL 

1. Clone git repo from `https://github.com/Subhajit-Roy-Partho/oxDNA.git`

2. Create `empty.sh` and paste 
```#!/bin/sh
#SBATCH -q private
#SBATCH -p general
#SBATCH -G a100:1
#SBATCH -t 5-00:00
#SBATCH -c 8
#SBATCH -o code.out
#SBATCH -e code.err
#SBATCH -W
exec screen -Dm -S slurm$SLURM_JOB_ID```

3. Follow the configuration steps, it is trivial except for a place where it asks for path of header file it can't find. Add all of them seperated by comma for my case it was `/usr/local/cuda/include,/usr/local/cuda/lib64,/usr/local/cuda/nvvm/lib64,/usr/local/cuda/bin,/usr/,/usr/local/TensorRT-8.6.1.6`.

4. Optional - For my case I used gcc instead of clang for cuda compilation and other compilation and default compiler was set to `/usr/bin/gcc`.

5. Then run `bazel build -c opt --copt=-mavx --copt=-mavx2 --copt=-mfma --copt=-mfpmath=both --copt=-msse4.2 --config=cuda -k //tensorflow/tools/pip_package:build_pip_package`. But it was creating problem so restricted myself to `bazel build -c opt --copt=-march=native --copt=-mfpmath=both --config=cuda -k //tensorflow/tools/pip_package:build_pip_package`. Wait patiently.

6. Compile it into pip
```bash
./bazel-bin/tensorflow/tools/pip_package/build_pip_package /tmp/tensorflow_pkg
```

For nightly build
```bash
./bazel-bin/tensorflow/tools/pip_package/build_pip_package --nightly_flag /tmp/tensorflow_pkg
```

7. Finally install it with pip
```bash
pip install /tmp/tensorflow_pkg/tensorflow-version-tags.whl
```

### Conclusion

It is fairly difficult to install it from the built. There will be something or the other that is bound to go wrong and there are hardly any solution present. With very limited knowledge of bazel it was difficult for me to modify the compilation process. I encountered numerous errors in the beginning with clang and had to shift to gcc.

After compilation the cpu speed increased compared to the prebuilt pip installation but there wasn't any improvement on GPU side. (I was using RTX 3060).

### References

- https://www.tensorflow.org/install/source
- https://stackoverflow.com/questions/41293077/how-to-compile-tensorflow-with-sse4-2-and-avx-instructions