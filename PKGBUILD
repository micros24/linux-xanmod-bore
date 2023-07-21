# Maintainer: micros24 <jasperjano.ph at protonmail dot com>
# Contributor: Joan Figueras <ffigue at gmail dot com>
# Contributor: Torge Matthies <openglfreak at googlemail dot com>
# Contributor: Jan Alexander Steffens (heftig) <jan.steffens@gmail.com>

##
## The following variables can be customized at build time. Use env or export to change at your wish
##
##   Example: env _microarchitecture=98 use_numa=n use_tracers=n makepkg -sc
##
## Look inside 'choose-gcc-optimization.sh' to choose your microarchitecture
## Valid numbers between: 0 to 99
## Default is: 0 => generic
## Good option if your package is for one machine: 98 (Intel native) or 99 (AMD native)
if [ -z ${_microarchitecture+x} ]; then
  _microarchitecture=0
fi

## Disable NUMA since most users do not have multiple processors. Breaks CUDA/NvEnc.
## Archlinux and Xanmod enable it by default.
## Set variable "use_numa" to: n to disable (possibly increase performance)
##                             y to enable  (stock default)
if [ -z ${use_numa+x} ]; then
  use_numa=n
fi

## Since upstream disabled CONFIG_STACK_TRACER (limits debugging and analyzing of the kernel)
## you can enable them setting this option. Caution, because they have an impact in performance.
## Stock Archlinux has this enabled. 
## Set variable "use_tracers" to: n to disable (possibly increase performance, XanMod default)
##                                y to enable  (Archlinux default)
if [ -z ${use_tracers+x} ]; then
  use_tracers=n
fi

# Unique compiler supported upstream is GCC
## Choose between GCC and CLANG config (default is GCC)
## Use the environment variable "_compiler=clang"
if [ "${_compiler}" = "clang" ]; then
  _compiler_flags="CC=clang HOSTCC=clang LLVM=1 LLVM_IAS=1"
fi

# Choose between the 4 main configs for stable branch. Default x86-64-v1 which use CONFIG_GENERIC_CPU2:
# Possible values: config_x86-64-v1 (default) / config_x86-64-v2 / config_x86-64-v3 / config_x86-64-v4
# This will be overwritten by selecting any option in microarchitecture script
# Source files: https://github.com/xanmod/linux/tree/5.17/CONFIGS/xanmod/gcc
if [ -z ${_config+x} ]; then
  _config=config_x86-64-v1
fi

# Compress modules with ZSTD (to save disk space)
if [ -z ${_compress_modules+x} ]; then
  _compress_modules=n
fi

# Compile ONLY used modules to VASTLY reduce the number of modules built
# and the build time.
#
# To keep track of which modules are needed for your specific system/hardware,
# give module_db script a try: https://aur.archlinux.org/packages/modprobed-db
# This PKGBUILD read the database kept if it exists
#
# More at this wiki page ---> https://wiki.archlinux.org/index.php/Modprobed-db
if [ -z ${_localmodcfg} ]; then
  _localmodcfg=n
fi

# Set the hertz of the tickrate (1000, 500, 300, 250, 100)
if [ -z ${_tickrate_HZ+x} ]; then
  _tickrate_HZ=500
fi

# Set the tickrate handling between (tickless, idle, constant)
if [ -z ${_tickrate+x} ]; then
  _tickrate=tickless
fi

# Set the preemption model (preemptible, voluntary, server)
if [ -z ${_preemptmodel+x} ]; then
  _preemptmodel=preemptible
fi

# Use O3 (y, n)
if [ -z ${_use_O3+x} ]; then
  _use_O3=y
fi

# Tweak kernel options prior to a build via nconfig
if [ -z ${_makenconfig} ]; then
  _makenconfig=n
fi

### IMPORTANT: Do no edit below this line unless you know what you're doing
pkgbase=linux-xanmod-bore
_major=6.4
pkgver=${_major}.4
_branch=6.x
xanmod=1
_revision=
pkgrel=${xanmod}
pkgdesc='Linux Xanmod (Stable) with BORE CPU scheduler and tickrate customizations'
url="http://www.xanmod.org/"
arch=('x86_64')
license=(GPL2)
makedepends=(
  bc cpio kmod libelf perl tar xz
)
_jobs=$(nproc)
_core=$(nproc --all)
if [ "${_compiler}" = "clang" ]; then
  makedepends+=(clang llvm lld python)
fi
options=('!strip')
_srcname="linux-${pkgver}-xanmod${xanmod}"
source=("https://cdn.kernel.org/pub/linux/kernel/v${_branch}/linux-${_major}.tar."{xz,sign}
        "patch-${pkgver}-xanmod${xanmod}${_revision}.xz::https://github.com/xanmod/linux/releases/download/${pkgver}-xanmod${xanmod}${_revision}/patch-${pkgver}-xanmod${xanmod}.xz"
        choose-gcc-optimization.sh
        "https://raw.githubusercontent.com/micros24/linux-xanmod-bore/${_major}/0001-bore.patch"
        "https://raw.githubusercontent.com/micros24/linux-xanmod-bore/${_major}/0002-glitched-cfs.patch"
        "https://raw.githubusercontent.com/micros24/linux-xanmod-bore/${_major}/0003-glitched-cfs-additions.patch"
        "https://raw.githubusercontent.com/micros24/linux-xanmod-bore/${_major}/0004-o3-optimization.patch")
validpgpkeys=(
    'ABAF11C65A2970B130ABE3C479BE3E4300411886' # Linux Torvalds
    '647F28654894E3BD457199BE38DBBDC86092693E' # Greg Kroah-Hartman
)

# Archlinux patches
_commit="ec9e9a4219fe221dec93fa16fddbe44a34933d8d"
_patches=()
for _patch in ${_patches[@]}; do
    source+=("${_patch}::https://raw.githubusercontent.com/archlinux/svntogit-packages/${_commit}/trunk/${_patch}")
done
sha256sums=('8fa0588f0c2ceca44cac77a0e39ba48c9f00a6b9dc69761c02a5d3efac8da7f3'
            'SKIP'
            '03b3711b1465131219025aaa17783e1ac4fbc94df477180552ef0cc92e2ce5d6'
            '5c84bfe7c1971354cff3f6b3f52bf33e7bbeec22f85d5e7bfde383b54c679d30'
            '2b0a54b6b92e55b5ff4fd902263c72958137b1d5f847f1c3b529eb9e756d8f11'
            '01eea507af27ad3b1329ea6856fc302691ec13a5b13495f79eeb784b6cca521a'
            '0b7fc3efb55c277acdeb023905794c319f2cff3d27576c9edb0c8bbab0bdf6df'
            '3e55d402cc5867b2d44fd5fb183f68735a2b76f36abcd17852a71517ee1928bf')
            

export KBUILD_BUILD_HOST=${KBUILD_BUILD_HOST:-archlinux}
export KBUILD_BUILD_USER=${KBUILD_BUILD_USER:-makepkg}
export KBUILD_BUILD_TIMESTAMP=${KBUILD_BUILD_TIMESTAMP:-$(date -Ru${SOURCE_DATE_EPOCH:+d @$SOURCE_DATE_EPOCH})}

prepare() {
  cd linux-${_major}

  # Apply Xanmod patch
  patch -Np1 -i ../patch-${pkgver}-xanmod${xanmod}${_revision}

  echo "Setting version..."
  echo "-$pkgrel" > localversion.10-pkgrel
  echo "${pkgbase#linux-xanmod}" > localversion.20-pkgname

  # Archlinux patches
  local src
  for src in "${source[@]}"; do
    src="${src%%::*}"
    src="${src##*/}"
    [[ $src = *.patch ]] || continue
    echo "Applying patch $src..."
    patch -Np1 < "../$src"
  done

  # Applying configuration
  cp -vf CONFIGS/xanmod/gcc/${_config} .config
  # enable LTO_CLANG_FULL
  if [ "${_compiler}" = "clang" ]; then
    scripts/config --enable LTO
    scripts/config --enable LTO_CLANG
    scripts/config --enable ARCH_SUPPORTS_LTO_CLANG
    scripts/config --enable ARCH_SUPPORTS_LTO_CLANG_THIN
    scripts/config --disable LTO_NONE
    scripts/config --enable HAS_LTO_CLANG
    scripts/config --enable LTO_CLANG_FULL
    scripts/config --disable LTO_CLANG_THIN
  fi

  # Setting HZ tick rate
  echo "Setting Tickrate HZ..."
  if [ "$_tickrate_HZ" = "1000" ]; then
    scripts/config --disable HZ_500
    scripts/config --enable HZ_1000
  elif [ "$_tickrate_HZ" = "300" ]; then
    scripts/config --disable HZ_500
    scripts/config --enable HZ_300
  elif [ "$_tickrate_HZ" = "250" ]; then
    scripts/config --disable HZ_500
    scripts/config --enable HZ_250
  elif [ "$_tickrate_HZ" = "100" ]; then
    scripts/config --disable HZ_500
    scripts/config --enable HZ_100
  fi

  # Selecting tick type
  echo "Setting tick type..."
  if [ "$_tickrate" = "tickless" ]; then
    scripts/config --disable HZ_PERIODIC
    scripts/config --disable NO_HZ_IDLE
    scripts/config --disable CONTEXT_TRACKING_FORCE
    scripts/config --enable NO_HZ_FULL_NODEF
    scripts/config --enable NO_HZ_FULL
    scripts/config --enable NO_HZ
    scripts/config --enable NO_HZ_COMMON
    scripts/config --enable CONTEXT_TRACKING
  elif [ "$_tickrate" = "idle" ]; then
    scripts/config --disable HZ_PERIODIC
    scripts/config --disable NO_HZ_FULL
    scripts/config --enable NO_HZ_IDLE
    scripts/config --enable NO_HZ
    scripts/config --enable NO_HZ_COMMON
  elif [ "$_tickrate" = "constant" ]; then
    scripts/config --disable NO_HZ_IDLE
    scripts/config --disable NO_HZ_FULL
    scripts/config --disable NO_HZ
    scripts/config --disable NO_HZ_COMMON
    scripts/config --enable HZ_PERIODIC
  fi

  # Selecting Preemption model
  echo "Setting preemption model..."
  if [ "$_preemptmodel" = "preemptible" ]; then
    scripts/config --disable PREEMPT_NONE
    scripts/config --disable PREEMPT_VOLUNTARY
    scripts/config --enable PREEMPT_BUILD
    scripts/config --enable PREEMPT
    scripts/config --enable PREEMPT_COUNT
    scripts/config --enable PREEMPTION
    scripts/config --enable PREEMPT_DYNAMIC
  elif [ "$_preemptmodel" = "voluntary" ]; then
    scripts/config --disable PREEMPT_NONE
    scripts/config --disable PREEMPT
    scripts/config --disable PREEMPT_DYNAMIC
    scripts/config --enable PREEMPT_BUILD
    scripts/config --enable PREEMPT_VOLUNTARY
    scripts/config --enable PREEMPT_COUNT
    scripts/config --enable PREEMPTION
  elif [ "$_preemptmodel" = "server" ]; then
    scripts/config --disable PREEMPT_VOLUNTARY
    scripts/config --disable PREEMPT
    scripts/config --disable PREEMPTION
    scripts/config --disable PREEMPT_DYNAMIC
    scripts/config --enable PREEMPT_NONE_BUILD
    scripts/config --enable PREEMPT_NONE
  fi

  # Setting O3
  if [ "$_use_O3" = "y" ]; then
    echo "Enabling O3..."
    scripts/config --disable CC_OPTIMIZE_FOR_PERFORMANCE
    scripts/config --enable CC_OPTIMIZE_FOR_PERFORMANCE_O3
  fi

  # CONFIG_STACK_VALIDATION gives better stack traces. Also is enabled in all official kernel packages by Archlinux team
  scripts/config --enable CONFIG_STACK_VALIDATION

  # Enable IKCONFIG following Arch's philosophy
  scripts/config --enable CONFIG_IKCONFIG \
                 --enable CONFIG_IKCONFIG_PROC

  # User set. See at the top of this file
  if [ "$use_tracers" = "y" ]; then
    echo "Enabling CONFIG_FTRACE only if we are not compiling with clang..."
    if [ "${_compiler}" = "gcc" ]; then
      scripts/config --enable CONFIG_FTRACE \
                     --enable CONFIG_FUNCTION_TRACER \
                     --enable CONFIG_STACK_TRACER
    fi
  fi

  # Disabling NUMA
  if [ "$use_numa" = "n" ]; then
    echo "Disabling NUMA..."
    scripts/config --disable CONFIG_NUMA
    scripts/config --disable NUMA
    scripts/config --disable AMD_NUMA
    scripts/config --disable X86_64_ACPI_NUMA
    scripts/config --disable NODES_SPAN_OTHER_NODES
    scripts/config --disable NUMA_EMU
    scripts/config --disable USE_PERCPU_NUMA_NODE_ID
    scripts/config --disable ACPI_NUMA
    scripts/config --disable ARCH_SUPPORTS_NUMA_BALANCING
    scripts/config --disable NODES_SHIFT
    scripts/config --undefine NODES_SHIFT
    scripts/config --disable NEED_MULTIPLE_NODES
    scripts/config --disable NUMA_BALANCING
    scripts/config --disable NUMA_BALANCING_DEFAULT_ENABLED
  fi

  # Compress modules by default (following Arch's kernel)
  if [ "$_compress_modules" = "y" ]; then
    scripts/config --enable CONFIG_MODULE_COMPRESS_ZSTD
  fi

  # Let's user choose microarchitecture optimization in GCC
  # Use default microarchitecture only if we have not choosen another microarchitecture
  if [ "$_microarchitecture" -ne "0" ]; then
    ../choose-gcc-optimization.sh $_microarchitecture
  fi

  # This is intended for the people that want to build this package with their own config
  # Put the file "myconfig" at the package folder (this will take preference) or "${XDG_CONFIG_HOME}/linux-xanmod/myconfig"
  # If we detect partial file with scripts/config commands, we execute as a script
  # If not, it's a full config, will be replaced
  for _myconfig in "${SRCDEST}/myconfig" "${HOME}/.config/linux-xanmod/myconfig" "${XDG_CONFIG_HOME}/linux-xanmod/myconfig" ; do
    if [ -f "${_myconfig}" ] && [ "$(wc -l <"${_myconfig}")" -gt "0" ]; then
      if grep -q 'scripts/config' "${_myconfig}"; then
        # myconfig is a partial file. Executing as a script
        echo "Applying myconfig..."
        bash -x "${_myconfig}"
      else
        # myconfig is a full config file. Replacing default .config
        echo "Using user CUSTOM config..."
        cp -f "${_myconfig}" .config
      fi
      echo
      break
    fi
  done

  ### Optionally load needed modules for the make localmodconfig
  # See https://aur.archlinux.org/packages/modprobed-db
  if [ "$_localmodcfg" = "y" ]; then
    if [ -f $HOME/.config/modprobed.db ]; then
      echo "Running Steven Rostedt's make localmodconfig now"
      make ${_compiler_flags} LSMOD=$HOME/.config/modprobed.db localmodconfig
    else
      echo "No modprobed.db data found"
      exit 1
    fi
  fi

  make ${_compiler_flags} olddefconfig

  make -s kernelrelease > version
  echo "Prepared %s version %s" "$pkgbase" "$(<version)"

  if [ "$_makenconfig" = "y" ]; then
    make ${_compiler_flags} nconfig
  fi

  # save configuration for later reuse
  cat .config > "${SRCDEST}/config.last"
}

build() {
  cd linux-${_major}
  make ${_compiler_flags} all
}

_package() {
  pkgdesc="Linux Xanmod (Stable) with BORE CPU scheduler and tickrate customizations"
  depends=(coreutils kmod initramfs)
  optdepends=('crda: to set the correct wireless channels of your country'
              'linux-firmware: firmware images needed for some devices')
  provides=(VIRTUALBOX-GUEST-MODULES
            WIREGUARD-MODULE
            KSMBD-MODULE
            NTFS3-MODULE)

  cd linux-${_major}
  local _kernver="$(<version)"
  local _modulesdir="$pkgdir/usr/lib/modules/$_kernver"

  echo "Installing boot image..."
  # systemd expects to find the kernel here to allow hibernation
  # https://github.com/systemd/systemd/commit/edda44605f06a41fb86b7ab8128dcf99161d2344
  install -Dm644 "$(make -s image_name)" "$_modulesdir/vmlinuz"

  # Used by mkinitcpio to name the kernel
  echo "$pkgbase" | install -Dm644 /dev/stdin "$_modulesdir/pkgbase"

  echo "Installing modules..."
  make INSTALL_MOD_PATH="$pkgdir/usr" INSTALL_MOD_STRIP=1 modules_install

  # remove build and source links
  rm "$_modulesdir"/{source,build}
}

_package-headers() {
  pkgdesc="Headers and scripts for building modules for the $pkgdesc kernel"
  depends=(pahole)

  cd linux-${_major}
  local _builddir="$pkgdir/usr/lib/modules/$(<version)/build"

  echo "Installing build files..."
  install -Dt "$_builddir" -m644 .config Makefile Module.symvers System.map \
    localversion.* version vmlinux
  install -Dt "$_builddir/kernel" -m644 kernel/Makefile
  install -Dt "$_builddir/arch/x86" -m644 arch/x86/Makefile
  cp -t "$_builddir" -a scripts

  # required when STACK_VALIDATION is enabled
  install -Dt "$_builddir/tools/objtool" tools/objtool/objtool

  # required when DEBUG_INFO_BTF_MODULES is enabled
  if [ -f "tools/bpf/resolve_btfids/resolve_btfids" ]; then install -Dt "$_builddir/tools/bpf/resolve_btfids" tools/bpf/resolve_btfids/resolve_btfids ; fi

  echo "Installing headers..."
  cp -t "$_builddir" -a include
  cp -t "$_builddir/arch/x86" -a arch/x86/include
  install -Dt "$_builddir/arch/x86/kernel" -m644 arch/x86/kernel/asm-offsets.s

  install -Dt "$_builddir/drivers/md" -m644 drivers/md/*.h
  install -Dt "$_builddir/net/mac80211" -m644 net/mac80211/*.h

  # https://bugs.archlinux.org/task/13146
  install -Dt "$_builddir/drivers/media/i2c" -m644 drivers/media/i2c/msp3400-driver.h

  # https://bugs.archlinux.org/task/20402
  install -Dt "$_builddir/drivers/media/usb/dvb-usb" -m644 drivers/media/usb/dvb-usb/*.h
  install -Dt "$_builddir/drivers/media/dvb-frontends" -m644 drivers/media/dvb-frontends/*.h
  install -Dt "$_builddir/drivers/media/tuners" -m644 drivers/media/tuners/*.h

  # https://bugs.archlinux.org/task/71392
  install -Dt "$_builddir/drivers/iio/common/hid-sensors" -m644 drivers/iio/common/hid-sensors/*.h

  echo "Installing KConfig files..."
  find . -name 'Kconfig*' -exec install -Dm644 {} "$_builddir/{}" \;

  echo "Removing unneeded architectures..."
  local arch
  for arch in "$_builddir"/arch/*/; do
    [[ $arch = */x86/ ]] && continue
    echo "Removing $(basename "$arch")"
    rm -r "$arch"
  done

  echo "Removing documentation..."
  rm -r "$_builddir/Documentation"

  echo "Removing broken symlinks..."
  find -L "$_builddir" -type l -printf 'Removing %P\n' -delete

  echo "Removing loose objects..."
  find "$_builddir" -type f -name '*.o' -printf 'Removing %P\n' -delete

  echo "Stripping build tools..."
  local file
  while read -rd '' file; do
    case "$(file -bi "$file")" in
      application/x-sharedlib\;*)      # Libraries (.so)
        strip -v $STRIP_SHARED "$file" ;;
      application/x-archive\;*)        # Libraries (.a)
        strip -v $STRIP_STATIC "$file" ;;
      application/x-executable\;*)     # Binaries
        strip -v $STRIP_BINARIES "$file" ;;
      application/x-pie-executable\;*) # Relocatable binaries
        strip -v $STRIP_SHARED "$file" ;;
    esac
  done < <(find "$_builddir" -type f -perm -u+x ! -name vmlinux -print0)

  echo "Stripping vmlinux..."
  strip -v $STRIP_STATIC "$_builddir/vmlinux"
  
  echo "Adding symlink..."
  mkdir -p "$pkgdir/usr/src"
  ln -sr "$_builddir" "$pkgdir/usr/src/$pkgbase"
}

pkgname=("${pkgbase}" "${pkgbase}-headers")
for _p in "${pkgname[@]}"; do
  eval "package_$_p() {
    $(declare -f "_package${_p#$pkgbase}")
    _package${_p#$pkgbase}
  }"
done

# vim:set ts=8 sts=2 sw=2 et:
