---
title: "Building Linux From the Ground Up: My Journey with LFS"
tags: [Linux, LFS, Open Source, Dev Journal, Learning]
style: border
color: info
description: "How I went from struggling with Linux drivers to building my own Linux system from source. This is the story of my LFS journey—pain, curiosity, and all."
---

![](/assets/LFS.png)

**Linux From Scratch**, or LFS, is exactly what it sounds like: a project where you build a Linux system from the ground up, entirely from source code. No installers, no defaults—just a blank slate and a guide. It’s the sort of project that leaves you curious as to why someone would do it.

## Table of Contents

- [Why I Chose to Do It?](#why-i-chose-to-do-it)
- [From Nothing to Boot: One Command at a Time](#from-nothing-to-boot-one-command-at-a-time)
  - [Getting Started](#getting-started)
  - [Disk Discoveries](#disk-discoveries)
  - [Toolchain Adventures](#toolchain-adventures)
  - [Chroot and Beyond](#chroot-and-beyond)
  - [The Kernel Moment](#the-kernel-moment)
- [Conclusion: Was It Worth It?](#conclusion-was-it-worth-it)

## Why I Chose to Do It?

So, why would anyone bother building a Linux system from scratch? The LFS book kicks off with that question—why hassle with this when you could just download a distro and call it a day? I’ve asked myself that too.

My journey with Linux started back in my computer engineering college days. Like many students, I dove in with Ubuntu as my daily driver, dual-booting it alongside Windows. I even dabbled with Kali Linux because, let’s face it, hacking demos looked *cool*. But honestly? It was mostly pain. Something always seemed to break—especially on my laptop with hybrid graphics (NVIDIA and Intel). Drivers were a mess, updates would randomly break boot, and I spent more time fixing things than actually using the system.

And the worst part? Whenever I did sit down to do some actual work—assignments, projects, anything—I’d first have to wrestle with the OS. Whether it was fixing a boot issue, reconfiguring graphics, or trying to figure out why Wi-Fi just stopped working, it was frustrating. I didn’t know enough back then to confidently debug anything, and that helpless feeling stuck with me.

Things shifted when I started working and got my hands on a Mac. macOS gave me a cleaner, Unix-based system that just worked out of the box. Slowly, I got more comfortable with the command line, scripting, and understanding how things were wired behind the scenes. That confidence made me realize: I didn’t just want to use operating systems—I wanted to understand them, piece by piece.

That’s where Linux From Scratch came in. It wasn’t just about building a Linux system—it was about demystifying it. I’m the kind of learner who needs the big picture first. Throw me in blind, and I’m lost. Give me the basics, let me trace the wires, and I catch on fast. LFS felt like the **perfect sandbox**—my chance to crack Linux wide open and finally get how it all fits together.

## From Nothing to Boot: One Command at a Time

### Getting Started

To build Linux From Scratch, you need a working Linux system first—something like Ubuntu, Fedora, or any distro that runs on x86 hardware. I didn’t have a spare Linux machine lying around, and trying to set it up on my macOS system seemed like more trouble than it was worth. So, I decided to spin up an Ubuntu virtual machine on DigitalOcean, using some credits I had handy. It was a practical choice, nothing fancy—just a clean environment to follow the LFS book.

That LFS book laid everything out so well, I actually thought I could do this. I was buzzing with *excitement*, maybe a little nervous, ready to craft my own Linux from scratch. It was me, a terminal, and a whole lot of curiosity—game on.

### Disk Discoveries

Partitioning was my first big step—dividing the disk into sections for Linux to use. I wasn’t a complete stranger to it, but doing it in a Linux command-line environment with `parted` felt like a fresh challenge. The LFS book said I needed partitions but left the details up to me, so I dove in, figuring it out as I went. That’s when I got curious about filesystems, and things started to get exciting. I realized formatting isn’t just about clearing a disk—it’s creating a filesystem so the operating system can organize and access data. That hit me hard; I’d had a vague sense of it before, but now it was *crystal clear*. I also learned you mount the filesystem, not the partition itself, though people often mix the terms since the filesystem lives on the partition. Each piece I uncovered felt like a **small win**, showing me how Linux starts to take shape.

### Toolchain Adventures

Building the cross-compiler toolchain was my next step—a set of tools that runs on my host system to create binaries for my new Linux system. I hadn’t known much about cross-compilation, so I started looking it up, curious about how it all worked. The LFS book gave me the basics to get started, but I wanted to know more, so I dove into cross-compilation on my own. It’s a huge topic—way bigger than I expected—and I only scratched the surface, but every concept I uncovered felt *intriguing*. I kept thinking, “How does this even come together?”

One insight that really shifted my understanding was realizing what GCC actually is. I always thought GCC was the compiler—but it turns out that’s only part of the story. GCC is actually a **compiler driver**: it doesn’t perform all the compilation work itself but instead coordinates a series of tools that each handle a different stage of the process. It starts with the preprocessor (`cpp`) to expand macros and include headers, then passes the code to the compiler proper (`cc1` or `cc1plus`) to generate assembly. That’s handed off to the assembler (`as`) to produce object code, and finally to the linker (`ld`), which pulls everything together into an executable. Then there was bootstrapping, building the toolchain in phases, which sounded so wild I had to read up on it. Even though I was just grazing the edges of these ideas, each piece I learned kept me hooked, eager to push forward.

### Chroot and Beyond

After compiling the toolchain, we chroot into the environment—a mechanism that changes the root directory for a process and its children, isolating them to a specific filesystem tree. This isolates the build environment from the host, ensuring no unnecessary files or settings leak in, letting us build the actual system packages cleanly. Using chroot sparked my curiosity—I didn’t know much about it, so I started digging on my own to understand what made it tick.

That research led to some fascinating finds. I came across a talk from Docker Con EU where Jérôme Petazzoni said:

> A Container feels like a VM, but it’s just Chroot on steroids.

That idea—that chroot powers something as modern as containers—grabbed me. To learn more, I checked the Linux kernel and saw chroot was already a core part. So, I went deeper, diving into the [Unix history repo](https://github.com/dspinellis/unix-history-repo) maintained by Professor Diomidis Spinellis. That’s where I saw the chroot syscall show up around 1979 in V7 Unix, a piece of history that felt alive.

From there, we started compiling the system packages, and it hit me early on: Linux/GNU is a full operating system because of all these incredible open-source packages coming together. Running `make` and `make install` for package after package got boring, and waiting for big ones like GCC to compile dragged on, but even so, I picked up something valuable by exploring what binaries and shared objects each one held, uncovering their dependency tree. That curiosity pulled me into another topic: static and dynamic linking, and how shared objects let programs share code. Learning how these pieces fit was intriguing, like seeing the inner workings of Linux unfold. Each step built not just my system, but my understanding of how it all connects.

### The Kernel Moment

Until this point, we’d been using the kernel of the host system, mounting its kernel filesystems to keep things running in the chroot environment. Now it was time to compile our own Linux kernel—a **big step**. The kernel comes with thousands of configuration options, which sounded daunting at first. But using `make defconfig` to set a baseline and following the LFS book’s guidelines to enable or disable options wasn’t as tough as I’d feared. It felt like shaping the core of my system, and I was curious to see it come together.

The real challenges hit when setting up GRUB. I learned the hard way that GRUB needs a `bios_grub` partition right at the start of the disk. I hadn’t planned for that—my first partition was already set up for `/boot` files. So, I had to carve out a new partition, format the original one as `bios_grub`, and carefully transfer the `/boot` files to the new spot. That was fiddly, but I got through it. The bigger problem came after GRUB was set up. When I tried booting my system, I got nothing—just a blank screen staring back. After some digging, I found the issue: a missing graphics driver. I went back, recompiled the kernel, and added the right options for my graphics hardware. It was a slog, but worth it. When that login prompt finally appeared, the feeling was *surreal*. After pouring so much into this system, seeing it boot up, alive and mine, was unlike anything else.

### Conclusion: Was It Worth It?

So, did LFS live up to the hype? If you treat it like a checklist, blindly typing commands, it’s a grind—honestly, kind of a bore. But if you take your time, poke around, chase the “why” behind each step? It’s a **goldmine**. I learned more about Linux than I ever thought I could, from filesystems to kernels to how it all fits together. Those moments of clarity—like realizing chroot’s roots or watching my system boot—lit a fire in me. I’m not jumping into Beyond LFS; for now, I’m eyeing Arch Linux to apply what I’ve learned in a more practical setup. I’m also curious about setting up my old laptop as a Type-1 hypervisor with KVM to explore what else I can do. This journey wasn’t just about building a system—it was about building my confidence to keep exploring. If you’re thinking about LFS, my advice is simple: **don’t just follow the book**. Dig deeper, get curious, and make it yours.
