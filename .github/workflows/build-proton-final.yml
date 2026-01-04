#!/bin/bash
set -e

# ===== CONFIG =====
DEVICE=viloet
DEFCONFIG=violet_defconfig
CORES=$(nproc)
KERNEL_DIR=$(pwd)
OUT=$KERNEL_DIR/out

# Proton Clang
export CLANG_PATH=$KERNEL_DIR/proton-clang/bin
export PATH=$CLANG_PATH:$PATH

export ARCH=arm64
export SUBARCH=arm64
export CC=clang
export LD=ld.lld
export AR=llvm-ar
export NM=llvm-nm
export STRIP=llvm-strip
export OBJCOPY=llvm-objcopy
export OBJDUMP=llvm-objdump
export LLVM=1
export LLVM_IAS=1

# ===== CLEAN =====
rm -rf out
mkdir -p out

# ===== KernelSU Next v1.1.1 =====
echo ">>> Setup KernelSU Next v1.1.1"
git clone https://github.com/KernelSU-Next/KernelSU-Next.git
cd KernelSU-Next
git checkout v1.1.1
cd kernel
bash setup.sh
cd ../..

# ===== DEFCONFIG =====
make O=out $DEFCONFIG

# ===== ENSURE CONFIG =====
scripts/config --file out/.config \
  -e CONFIG_KSU \
  -e CONFIG_KSU_MANUAL_HOOK

make O=out olddefconfig

# ===== BUILD =====
make -j$CORES O=out \
  CC=clang \
  LD=ld.lld

# ===== PREPARE ANYKERNEL =====
echo ">>> Prepare AnyKernel3"
git clone -b sixteen https://github.com/Joker-V2/AnyKernel3.git AnyKernel3

cp out/arch/arm64/boot/Image.gz AnyKernel3/
cp out/arch/arm64/boot/dtbo.img AnyKernel3/

# ===== ZIP =====
cd AnyKernel3
zip -r ../KernelSU-Next-violet.zip *
cd ..

# ===== SIGN ZIP =====
java -jar zipsigner-3.0-dexed.jar \
  KernelSU-Next-violet.zip \
  KernelSU-Next-violet-signed.zip

echo "✅ DONE: KernelSU-Next-violet-signed.zip جاهز للتفليش"
