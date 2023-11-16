bullseye-slimでmcmapを動かす。

```dockerfile
FROM debian:bullseye-slim AS builder

RUN apt update &&\
    apt install -y git make g++ libpng-dev cmake libspdlog-dev &&\
    mkdir src &&\
    cd src &&\
    git init &&\
    git remote add origin https://github.com/spoutn1k/mcmap.git &&\
    git fetch --depth 1 origin a897b492b8b0287794b752a1749ce0285825090b &&\
    git reset --hard FETCH_HEAD &&\
    mkdir build &&\
    cd build &&\
    cmake .. &&\
    make -j
# built binary location is /src/build/bin/mcmap

FROM debian:bullseye-slim
RUN apt update &&\
    apt install -y libpng-dev libgomp1
COPY --from=builder /src/build/bin/mcmap /usr/local/bin

# before apt install
# ldd mcmap
#         linux-vdso.so.1 (0x0000ffffafc3c000)
#         libpng16.so.16 => not found
#         libz.so.1 => /lib/aarch64-linux-gnu/libz.so.1 (0x0000ffffafac8000)
#         libpthread.so.0 => /lib/aarch64-linux-gnu/libpthread.so.0 (0x0000ffffafa97000)
#         libgomp.so.1 => not found
#         libstdc++.so.6 => /usr/lib/aarch64-linux-gnu/libstdc++.so.6 (0x0000ffffaf8bf000)
#         libm.so.6 => /lib/aarch64-linux-gnu/libm.so.6 (0x0000ffffaf814000)
#         libgcc_s.so.1 => /lib/aarch64-linux-gnu/libgcc_s.so.1 (0x0000ffffaf7f0000)
#         libc.so.6 => /lib/aarch64-linux-gnu/libc.so.6 (0x0000ffffaf67c000)
#         /lib/ld-linux-aarch64.so.1 (0x0000ffffafc0c000)

# after apt install
# ldd mcmap
#         linux-vdso.so.1 (0x0000ffff99bf6000)
#         libpng16.so.16 => /usr/lib/aarch64-linux-gnu/libpng16.so.16 (0x0000ffff99a65000)
#         libz.so.1 => /lib/aarch64-linux-gnu/libz.so.1 (0x0000ffff99a3b000)
#         libpthread.so.0 => /lib/aarch64-linux-gnu/libpthread.so.0 (0x0000ffff99a0a000)
#         libgomp.so.1 => /usr/lib/aarch64-linux-gnu/libgomp.so.1 (0x0000ffff999bd000)
#         libstdc++.so.6 => /usr/lib/aarch64-linux-gnu/libstdc++.so.6 (0x0000ffff997e5000)
#         libm.so.6 => /lib/aarch64-linux-gnu/libm.so.6 (0x0000ffff9973a000)
#         libgcc_s.so.1 => /lib/aarch64-linux-gnu/libgcc_s.so.1 (0x0000ffff99716000)
#         libc.so.6 => /lib/aarch64-linux-gnu/libc.so.6 (0x0000ffff995a2000)
#         /lib/ld-linux-aarch64.so.1 (0x0000ffff99bc6000)
#         libdl.so.2 => /lib/aarch64-linux-gnu/libdl.so.2 (0x0000ffff9958e000)
```
