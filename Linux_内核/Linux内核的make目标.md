---
title: Linux内核的make目标
tags: Linux内核
---

------

&emsp;&emsp;<font color=blue>**_版权声明_**</font>：<font color=red>未经作者允许，<font color=blue>严禁用于商业出版</font>，否则追究法律责任。网络转载请注明出处，这是对原创者的起码的尊重！！！</font>

------

# 1 清理目标
* `$make clean`—— 删除大多数生成的文件，但保留配置和足够的构建支持以构建外部模块
* `$make mrproper` —— 删除所有生成的文件+ config +各种备份文件
* `$make distclean` —— `$make mrproper` +删除编辑器备份文件和补丁文件

# 2 配置目标
* 手动配置
	 * `$ make [ARCH=xxx] config`——基于文本的配置界面
	 * `$ make [ARCH=xxx] nconfig`——基于ncurses的配置界面
	 * `$ make [ARCH=xxx] menuconfig`——基于菜单程序的配置界面
	 * `$ make [ARCH=xxx] xconfig`——使用基于Qt的配置界面
	 * `$ make [ARCH=xxx] gconfig`——使用基于GTK+的配置界面
* 快速配置
	 * `$ make [ARCH=xxx] oldconfig`——使用之前配置好的.config作为基础更新当前配置
	 * `$ make [ARCH=xxx] localmodconfig `——更新当前配置，禁用当前内核未加载的模块
	 * `$ make [ARCH=xxx] localyesconfig`——更新当前配置，并将模块转为内核内建
	 * `$ make [ARCH=xxx] defconfig`——使用ARCH提供的defconfig默认配置
	 * `$ make [ARCH=xxx] savedefconfig `——将当前系统的内核配置保存为./defconfig（最小配置）
	 * `$ make [ARCH=xxx] allnoconfig`——新配置，其中所有选项均设置为no
	 * `$ make [ARCH=xxx] allyesconfig`——新配置，其中所有选项均设置为yes
	 * `$ make [ARCH=xxx] allmodconfig`——新配置，其中所有选项均尽可能设置为module
	 * `$ make [ARCH=xxx] alldefconfig`——新配置，其中所有选项均设置为默认值
	 * `$ make [ARCH=xxx] randconfig`——新配置，其中所有选项均设置为随机值
	 * `$ make [ARCH=xxx] listnewconfig`——列出新选项
	 * `$ make [ARCH=xxx] olddefconfig`——与oldconfig相同，但在没有提示的情况下将新符号设置为其默认值
	 * `$ make [ARCH=xxx] kvmconfig`——为kvm来宾内核支持启用其他选项
	 * `$ make [ARCH=xxx] xenconfig`——为xen dom0和来宾内核支持启用其他选项
	 * `$ make [ARCH=xxx] tinyconfig`——配置最小的内核
	 * `$ make [ARCH=xxx] tinyconfig`——运行 Kconfig 单元测试 (要求 安装python3 和 pytest)
# 3 其它通用目标
* all——编译所有加粗的目标
* **vmlinux**	  - Build the bare kernel
* **modules**——编译生成所有的模块
  modules_install - Install all modules to INSTALL_MOD_PATH (default: /)
  dir/            - Build all files in dir and below
  dir/file.[ois]  - Build specified target only
  dir/file.ll     - Build the LLVM assembly file
                    (requires compiler support for LLVM assembly generation)
  dir/file.lst    - Build specified mixed source/assembly target only
                    (requires a recent binutils and recent build (System.map))
  dir/file.ko     - Build module including final link
  modules_prepare - Set up for building external modules
  tags/TAGS	  - Generate tags file for editors
  cscope	  - Generate cscope index
  gtags           - Generate GNU GLOBAL index
  kernelrelease	  - Output the release version string (use with make -s)
  kernelversion	  - Output the version stored in Makefile (use with make -s)
  image_name	  - Output the image name (use with make -s)
  headers_install - Install sanitised kernel headers to INSTALL_HDR_PATH
                    (default: ./usr)

Static analysers:
  checkstack      - Generate a list of stack hogs
  namespacecheck  - Name space analysis on compiled kernel
  versioncheck    - Sanity check on version.h usage
  includecheck    - Check for duplicate included header files
  export_report   - List the usages of all exported symbols
  headers_check   - Sanity check on exported headers
  headerdep       - Detect inclusion cycles in headers
  coccicheck      - Check with Coccinelle

Kernel selftest:
  kselftest       - Build and run kernel selftest (run as root)
                    Build, install, and boot kernel before
                    running kselftest on it
  kselftest-clean - Remove all generated kselftest files
  kselftest-merge - Merge all the config dependencies of kselftest to existing
                    .config.

Userspace tools targets:
  use "make tools/help"
  or  "cd tools; make help"

Kernel packaging:
  rpm-pkg             - Build both source and binary RPM kernel packages
  binrpm-pkg          - Build only the binary kernel RPM package
  deb-pkg             - Build both source and binary deb kernel packages
  bindeb-pkg          - Build only the binary kernel deb package
  snap-pkg            - Build only the binary kernel snap package (will connect to external hosts)
  tar-pkg             - Build the kernel as an uncompressed tarball
  targz-pkg           - Build the kernel as a gzip compressed tarball
  tarbz2-pkg          - Build the kernel as a bzip2 compressed tarball
  tarxz-pkg           - Build the kernel as a xz compressed tarball
  perf-tar-src-pkg    - Build perf-4.18.7.tar source tarball
  perf-targz-src-pkg  - Build perf-4.18.7.tar.gz source tarball
  perf-tarbz2-src-pkg - Build perf-4.18.7.tar.bz2 source tarball
  perf-tarxz-src-pkg  - Build perf-4.18.7.tar.xz source tarball

Documentation targets:
 Linux kernel internal documentation in different formats from ReST:
  htmldocs        - HTML
  latexdocs       - LaTeX
  pdfdocs         - PDF
  epubdocs        - EPUB
  xmldocs         - XML
  linkcheckdocs   - check for broken external links (will connect to external hosts)
  refcheckdocs    - check for references to non-existing files under Documentation
  cleandocs       - clean all generated files

  make SPHINXDIRS="s1 s2" [target] Generate only docs of folder s1, s2
  valid values for SPHINXDIRS are: core-api networking input sound userspace-api gpu media process maintainer doc-guide crypto vm sh dev-tools driver-api filesystems kernel-hacking admin-guide

  make SPHINX_CONF={conf-file} [target] use *additional* sphinx-build
  configuration. This is e.g. useful to build with nit-picking config.

  Default location for the generated documents is Documentation/output

Architecture specific targets (x86):
* bzImage      - Compressed kernel image (arch/x86/boot/bzImage)
  install      - Install kernel using
                  (your) ~/bin/installkernel or
                  (distribution) /sbin/installkernel or
                  install to $(INSTALL_PATH) and run lilo
  fdimage      - Create 1.4MB boot floppy image (arch/x86/boot/fdimage)
  fdimage144   - Create 1.4MB boot floppy image (arch/x86/boot/fdimage)
  fdimage288   - Create 2.8MB boot floppy image (arch/x86/boot/fdimage)
  isoimage     - Create a boot CD-ROM image (arch/x86/boot/image.iso)
                  bzdisk/fdimage*/isoimage also accept:
                  FDARGS="..."  arguments for the booted kernel
                  FDINITRD=file initrd for the booted kernel

  i386_defconfig           - Build for i386
  x86_64_defconfig         - Build for x86_64

  make V=0|1 [targets] 0 => quiet build (default), 1 => verbose build
  make V=2   [targets] 2 => give reason for rebuild of target
  make O=dir [targets] Locate all output files in "dir", including .config
  make C=1   [targets] Check re-compiled c source with $CHECK (sparse by default)
  make C=2   [targets] Force check of all c source with $CHECK
  make RECORDMCOUNT_WARN=1 [targets] Warn about ignored mcount sections
  make W=n   [targets] Enable extra gcc checks, n=1,2,3 where
		1: warnings which may be relevant and do not occur too often
		2: warnings which occur quite often but may still be relevant
		3: more obscure warnings, can most likely be ignored
		Multiple levels can be combined with W=12 or W=123

Execute "make" or "make all" to build all targets marked with [*] 
For further info see the ./README file

------

&emsp;&emsp;<font color=blue>**_版权声明_**</font>：<font color=red>未经作者允许，<font color=blue>严禁用于商业出版</font>，否则追究法律责任。网络转载请注明出处，这是对原创者的起码的尊重！！！</font>

------