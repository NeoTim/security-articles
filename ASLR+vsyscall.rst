Goals
=====

This documents tries to address some concerns with enabling PIE.

Argument 1
==========

One of the criticism of "Using PIE by default on AMD64" is,

The advantages of address space randomization on x86-64 are grossly
exaggerated, given that the first 6 arguments are passed in registers (thus it
is harder to construct the right arguments compared to say i386 where just some
stack buffer overflow can lead to specifying both where to return to and what
arguments should be passed to it) and because address space on x86-64 is never
really randomized anyway (due to historical mistake, the vsyscall page is not
randomized, so you can always return to vsyscall page if you can't easily
return to libc or some other library).

See ``https://fedorahosted.org/fesco/ticket/1113#comment:10``

Counter-argument 1
==================

(all credit goes to #grsecurity guys, spender and pipacs for the detailed
answers).

* All of the useful instructions from the vsyscall page have been removed.

  In other words, in recent kernels it is not about the location of vsyscall
  but the contents and how those contents can be executed.

  The remaining code in the vsyscall page has simply been removed and replaced
  by a special trap instruction. An application trying to call into the
  vsyscall page will trap into the kernel, which will then emulate the desired
  virtual system call in kernel space. The result is a kernel system call
  emulating a virtual system call which was put there to avoid the kernel
  system call in the first place. The result is a "vsyscall" which takes a
  fraction of a microsecond longer to execute but, crucially, does not break
  the existing ABI. In any case, the slowdown will only be seen if the
  application is trying to use the vsyscall page instead of the vDSO.

  See ``http://lwn.net/Articles/446528/`` and ``arch/x86/kernel/vsyscall_emu_64.S``.

  These days the vsyscall page is stuffed with int3 instructions and the rest
  of the valid the rest of the valid code is probably too little to be useful
  as ROP gadgets. (needs confirmation).

* ret2libc like attacks aren't useful since the calls supported by vsyscall
  mechanism are harmless. Namely,

  - __NR_gettimeofday
  - __NR_time
  - __NR_getcpu

  Nothing dangerous really.

* vsyscall page is read-only. So we can't stuff code here for execution later.

  ffffffffff600000-ffffffffff601000 r-xp 00000000 00:00 [vsyscall]

* See ``https://bugs.launchpad.net/ubuntu/+source/linux/+bug/1018415``

  vsyscall is an obsolete method (replaced by vdso) to do vast system calls.
  Because it is part of the linux x86-64 ABI, it always has to be mapped to a
  static address by the kernel.

  This means that in the case of a vulnerability (in some user program), an
  attacker making use of return oriented programming can rely on useful gadgets
  at a known address (bypassing ASLR.) Using the gadgets in vsyscall it is
  possible to get arbitrary code execution with only one trivial extra gadget
  (or in some cases none at all.) This is why recent kernels emulate the
  obsolete vsyscall ABI in-kernel. The emulation makes sure that an attacker
  can only call functions defined by the ABI, like gettimeofday(), and cannot,
  for example, directly jump to a syscall & ret gadget. Only calls to offsets
  defined in the ABI are allowed. In my opinion, backporting the new default
  behaviour of emulating vsyscall to LTS would increase the time / effort /
  skill needed for exploit writers to write a successful exploit somewhat, and
  make the resulting exploits less generic.

  NOTE: upstream kernels (even those of 2011 era) already have this fix.

* See ``http://code.google.com/p/go/issues/detail?id=1933``

  In Linux 3.1 (and later), the vsyscall is never faster than the syscall, and
  someone might want to run Go programs in vsyscall=none mode someday. (Any
  distribution with new enough glibc will already work in vsyscall=none mode,
  and even older distributions are mostly functional).

  (This was the state in Dec 2011, things should be better now).

  Furthermore, vsyscall stays at the fixed address but it can be mapped as
  non-executable (via a sysctl setting). when there's an exec attempt in one of
  the few entry points in there, the kernel's page fault handler will recognize
  the special fault address and emulate the behaviour by redirecting the code
  to the intended syscall. so calling stuff in the vsyscall page becomes a slow
  way to executing those syscalls. (pipacs, paraphrased).

* There'll be an performance impact but only for apps that actually still use
  the vsyscall page.

  NOTE: syscall=emulate is default and no one seems to be complaining. So this
  performance argument is moot.

Argument 2
==========

Some of the criticism of "Using PIE by default on AMD64" are,

The most important for security is properly written code, then measures
that prevent exploits (-D_FORTIFY_SOURCE=2, -fsanitize=address, -fmudflap,
SELinux), and only then measures that make exploits less likely to succeed
(address space randomization).

SELINUX is a far far better security benefit than PIE, so we eat the cost
there. PIE is not a big enough benefit to counter that.

PIE security benefits are not so important as counter its cost.

On brute-forcing ASLR on AMD64,

On forking servers it is very much possible to break ASLR because after the
fork the address space is always the same so you can try multiple times.

Counter-argument 2
==================

* Effective security involves multiple-layers. It is not just a single layer or
  a single technique.

* If a security measure is not 100% fool-proof it doesn't mean that we
  shouldn't' bother trying it. PIE already covers a bunch of forking servers.
  It would help ensure attacking Linux is more work than it's worth.

* anti-bruteforcing measures can be implemented trivially in user-space to
  detect such brute-force attempts against ASLR (even for forking servers).

Summary
=======

ASLR on AMD64 is quite effective.

::

  $ uname -r
  3.10.0-rc3

  $ cat /etc/redhat-release
  Fedora release 18 (Spherical Cow)

  $ gcc --version
  gcc (GCC) 4.7.2 20121109 (Red Hat 4.7.2-8)
  ...


32-bit
-------

- executable address : 8 bits of entropy
- stack address      : 11 bits of entropy
- DSOs address       : 8 bits of entropy


64-bit
------

TODO: Measure / estimate entropy provided by ASLR on AMD64. My machine has been
trying to brute-force the ASLR space for days but it is *HUGE*. "XX" implies
good enough ;)

- executable address : XX bits of entropy
- stack address      : XX bits of entropy
- DSOs address       : XX bits of entropy


References
==========

* https://lists.ubuntu.com/archives/ubuntu-devel/2006-June/018450.html

* ``shacham2004ccs.pdf``

* http://www.vnsecurity.net/2012/02/exploiting-sudo-format-string-vunerability/

* http://www.win.tue.nl/~aeb/linux/lk/lk-4.html

Favorite Lines
==============

The irony is that the only examples I can think of that would really benefit
from a 3% performance increase are the network servers that we already have on
the mandatory list (apache, MariaDB, etc.) :-)

Notes
=====

* on ancient glibc ::

        $ objdump -d /lib64/libc-2.9.so | fgrep -A5 '<time>:'
        000000000008a510 <time>:
           8a510:       48 83 ec 08             sub    $0x8,%rsp
           8a514:       48 c7 c0 00 04 60 ff    mov    $0xffffffffff600400,%rax
           8a51b:       ff d0                   callq  *%rax
           8a51d:       48 83 c4 08             add    $0x8,%rsp
           8a521:       c3                      retq

* on modern (Fedora 18) glibc ::

        objdump -d /lib64/libc-2.16.so |  fgrep -A25 '<time>:'
        0000003193cabef0 <time>:
          3193cabef0:	48 83 ec 28          	sub    $0x28,%rsp
          3193cabef4:	48 8d 05 a1 a8 0c 00 	lea    0xca8a1(%rip),%rax        # 3193d7679c <xdr_zero+0x3c>
          3193cabefb:	48 8d 3d a1 bd 0c 00 	lea    0xcbda1(%rip),%rdi        # 3193d77ca3 <mallenv+0x83>
          3193cabf02:	48 89 e6             	mov    %rsp,%rsi
          3193cabf05:	c7 44 24 0c 01 00 00 	movl   $0x1,0xc(%rsp)
          3193cabf0c:	00
          3193cabf0d:	c7 44 24 08 f6 75 ae 	movl   $0x3ae75f6,0x8(%rsp)
          3193cabf14:	03
          3193cabf15:	48 89 04 24          	mov    %rax,(%rsp)
          3193cabf19:	48 c7 44 24 10 00 00 	movq   $0x0,0x10(%rsp)
          3193cabf20:	00 00
          3193cabf22:	e8 99 2d 08 00       	callq  3193d2ecc0 <_dl_vdso_vsym>
          3193cabf27:	48 c7 c2 00 04 60 ff 	mov    $0xffffffffff600400,%rdx
          3193cabf2e:	48 85 c0             	test   %rax,%rax
          3193cabf31:	48 0f 44 c2          	cmove  %rdx,%rax
          3193cabf35:	48 83 c4 28          	add    $0x28,%rsp
          3193cabf39:	c3                   	retq
          3193cabf3a:	66 0f 1f 44 00 00    	nopw   0x0(%rax,%rax,1)
