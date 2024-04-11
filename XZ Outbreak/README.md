# XZ Outbreak
*by Lisa Mai Linh Glaziou*

*created: April 11th 2024*

*updated: April 11th 2024*

# I - Quick Presentation of the XZ Attack

![](https://media.licdn.com/dms/image/D5622AQG85tdVSaU75Q/feedshare-shrink_800/0/1711871765605?e=1715817600&v=beta&t=A_J_1tKnm_8Z4gKsrOxF3snPKB3iLV1xpl0XptO9HLc)

## A - What is XZ Utils Library ?

XZ Utils, and its underlying library liblzma, are open-source projects that implement the lzma compression and decompression. They are included in many Linux distributions out of the box, are very popular with developers, and are used extensively throughout the Linux ecosystem.

## B - The outbreak

The [CVE-2024-3094](https://access.redhat.com/security/cve/CVE-2024-3094) is a vulnerability recently discovered in the open-source library XZ Utils that stems from malicious code that was pushed into the library by one of its maintainers. 

- It was originally reported as an SSH authentication bypass backdoor, but [further analysis](https://bsky.app/profile/filippo.abyssdomain.expert/post/3kowjkx2njy2b) indicates that the backdoor actually enables remote code execution (RCE).
- The threat actor started contributing to the XZ project almost two years ago, slowly building credibility until they were given maintainer responsibilities. Such long-term operations are usually the realm of state-sponsored threat actors, but specific attribution does not currently exist.

---

# II - Timeline

**2021** – GitHub user Jia Tan (JiaT75) account created. Started contributing to several projects with 546 commits done in 2021, of which the most suspicious one was made to [libarchive](https://github.com/libarchive/libarchive/pull/1609). A more detailed account of this commit can be found below.

**2022, February 6th** – JiaT75 submits a first (legitimate) commit to the XZ repo. The [commit](https://git.tukaani.org/?p=xz.git;a=commitdiff;h=6468f7e41a8e9c611e4ba8d34e2175c5dacdbeb4) adds arguments validation to the LZMA and LZMA2 encoders.

**2022, November 30th** – Lasse Collins, XZ Utils’ creator and sole maintainer so far, [changes](https://git.tukaani.org/?p=xz.git;a=commitdiff;h=764955e2d4f2a5e8d6d6fec63af694f799e050e7) the bug reporting email to an alias that redirects emails to him and Jia Tan.

**2023, January 11th** – Lasse Collins [releases](https://git.tukaani.org/?p=xz.git;a=commitdiff;h=18b845e69752c975dfeda418ec00eda22605c2ee) his final version, 5.4.1.

**2023, March 18th** – Jia Tan [builds and releases](https://git.tukaani.org/?p=xz.git;a=commitdiff;h=6ca8046ecbc7a1c81ee08f544bfd1414819fb2e8) their first release, 5.4.2.

**2023, June 27-28th** – A series of changes were made to XZ Utils, possibly setting the ground for the attack. In these changes, support for ifunc implementation to crc64_fast.c, was added.

**2023, July 8th** – JiaT75 [opens a Pull Request in oss-fuzz](https://github.com/google/oss-fuzz/pull/10667), a project that performs fuzz testing on XZ and many other OSS projects. The PR disables ifunc fuzzing, which effectively prevents oss-fuzz from finding the malicious changes done in XZ.

**2024, February 15th** – JiaT75 [adds an ignore rule](https://git.tukaani.org/?p=xz.git;a=commit;h=4323bc3e0c1e1d2037d5e670a3bf6633e8a3031e) for build-to-host.m4 in the XZ repository, via its .gitignore file. This script file, soon to be included in actual release bundles, is executed during the package’s build, and contains the malicious M4 macros which initializes the backdoor’s installation on the victim’s machine.

**2024, February 23rd** – JiaT75 adds the obfuscated binary backdoor in [two tests files](https://git.tukaani.org/?p=xz.git;a=commit;h=cf44e4b7f5dfdbf8c78aef377c10f71e274f63c0) in the XZ repository :
- tests/files/bad-3-corrupt_lzma2.xz
- tests/files/good-large_compressed.lzma

**2024, February 24th** – JiaT75 releases version 5.6.0 with the malicious build-to-host.m4. **At this stage, the malicious payload is fully operational (any subsequent XZ version is compromised)**. Malicious xz-utils version 5.6.0 pulled by [Debian](https://salsa.debian.org/debian/xz-utils/-/commit/12388833e66a4ddafe08571882ad638a511cf68b), [Gentoo](https://gitweb.gentoo.org/repo/gentoo.git/commit/app-arch/xz-utils?id=a34162ecb85645cad876c4e6c7e0507a349afb9e) and [Arch Linux](https://gitlab.archlinux.org/archlinux/packaging/packages/xz/-/commit/a6ee49b9726826946c59486c5ec733de5eeb53c7).

**2024, February 27th** – Malicious xz-utils version 5.6.0 pulled by [Fedora](https://src.fedoraproject.org/rpms/xz/c/de73aff0c7dc3a64f7a15c65c431cd73e96a3b9b?branch=main).

**2024, March 5th** – Malicious xz-utils version 5.6.0 pulled by [openSUSE](https://build.opensuse.org/request/show/1155110).

**2024, March 9th** – JiaT75 updates the backdoor’s binaries to an [improved version](https://git.tukaani.org/?p=xz.git;a=commit;h=6e636819e8f070330d835fce46289a3ff72a7b89), and releases version 5.6.1. Malicious xz-utils version 5.6.1 pulled by [Fedora](https://src.fedoraproject.org/rpms/xz/c/d86c40e1d5057547f5dc0404db68b8c42041c254?branch=rawhide), [Gentoo](https://gitweb.gentoo.org/repo/gentoo.git/commit/app-arch/xz-utils?id=5b2cdd1c7d1743ea2937248ccc02bca9517a5771) and [Arch Linux](https://gitlab.archlinux.org/archlinux/packaging/packages/xz/-/commit/2cb78b18b29d987063f4eec1e5ac57898cc76f17)

**2024, March 10th** – Malicious xz-utils version 5.6.1 pulled by [openSUSE](https://build.opensuse.org/request/show/1158735).

**2024, March 11th** – Malicious xz-utils version 5.6.1 pulled by [Alpine](https://gitlab.alpinelinux.org/alpine/aports/-/commit/11bc4fbf6b6fe935f77e45706b1b8a2923b2b203).

**2024, March 26th** – Malicious xz-utils version 5.6.1 pulled by [Debian](https://salsa.debian.org/debian/xz-utils/-/commit/e5dff42eca160cb348ba9a99deee42e8013efd10).

**2024, March 29th** – A detailed account of the malicious activity found in XZ utils was published on the [oss-security mailing list](https://www.openwall.com/lists/oss-security/2024/03/29/4) by Andres Freund.

**2024, March 30th** – Lasse Collins, xz-utils original maintainer, made [an official announcement](https://tukaani.org/xz-backdoor) regarding the project’s breach.

---

# III - The backdoor itself

## A - The vulnerability

The vulnerability lies within the XZ Utils, it’s compression utilities included in most Linux distributions. Versions 5.6.0 and 5.6.1 were affected. 

When exploited, this flaw could allow a malicious actor to break SSH authentication and gain unauthorized access to the entire system remotely. Fortunately, widespread adoption of these versions had not yet occurred, minimizing the impact.

## B - Backdoor mechanism

The backdoor was not directly inserted into the source code of liblzma that is visible in version control systems or utilized by XZ directly. Instead, it was hidden within binary test files in the XZ compressed format. These files appeared benign and were theoretically part of the library’s test suite.

The attackers used a sophisticated method that split the backdoor into parts, which were then concealed within two XZ compressed files. These files were disguised as ordinary test files, evading detection from casual inspection or automated tools that scan for malicious patterns.

This caused parts of the backdoor to remain relatively hidden, while still being used during the build process of dependent projects.

The shared object itself, compiled into liblzma, overrides the standard function name resolution process. When a process is loaded, function names are resolved into pointers pointing to the process memory, indicating the binary code. However, the malicious library disrupts this resolution process. It replaces the function pointer for the OpenSSH function RSA_public_decrypt with its own malicious function. This function, as detailed in research by Filippo Valsorda, extracts a command from the authenticating client’s certificate (after confirming it as the threat actor) and passes it to the system() function for execution, thus enabling Remote Code Execution (RCE) before authentication.

![Fig. 1: The liblzma hooking process](https://www.akamai.com/site/en/images/blog/2024/critical-linux-backdoor-xz-utils-discovered-what-to-know-one.png)


## C - Potential Impact

**Currently, it appears as though the backdoor is added to the SSH daemon on the vulnerable machine, enabling a remote attacker to execute arbitrary code**. This means that any machine with the vulnerable package that exposes SSH to the internet is potentially vulnerable.

The attackers came perilously close to gaining instantaneous access to any Linux machine running an infected distribution, encompassing Fedora, Ubuntu, and Debian. However, their plans were thwarted by Andres Freund. While probing a 500 ms latency anomaly following a software update, Andres meticulously traced the problem to the xz package, ultimately uncovering the backdoor.

---

# IV - The discovery

On the 29th March 2024, Andres Freund posted this on [Openwall](https://www.openwall.com/lists/oss-security/2024/03/29/4) :

*“ After observing a few odd symptoms around liblzma (part of the xz package) on
Debian sid installations over the last weeks (logins with ssh taking a lot of
CPU, valgrind errors) I figured out the answer:*

*The upstream xz repository and the xz tarballs have been backdoored.*

*At first I thought this was a compromise of debian's package, but it turns out
to be upstream.”*

Andres Freund, a Microsoft engineer, noticed something peculiar. He was using a software tool called [SSH](https://en.wikipedia.org/wiki/Secure_Shell) for securely logging into remote computers on the internet, but the interactions with the distant machines were significantly slower than usual. So he did some digging and found malicious code embedded in a software package called XZ Utils that was running on his machine. This is a critical utility for compressing (and decompressing) data running on the Linux operating system, the OS that powers the vast majority of publicly accessible internet servers across the world. Which means that every such machine is running XZ Utils.

Freund’s digging revealed that the malicious code had arrived in his machine via two recent updates to XZ Utils, and he [alerted the Open Source Security list](https://www.openwall.com/lists/oss-security/2024/03/29/4) to reveal that those updates were the result of someone intentionally planting a backdoor in the compression software.

---

# Resources, Case of Studies and Examples

Akamai Security Intelligence Group (Apr 1st, 2024). [**XZ Utils Backdoor — Everything You Need to Know, and What You Can Do**](https://www.akamai.com/blog/security-research/critical-linux-backdoor-xz-utils-discovered-what-to-know)

Ardash Pandey (Apr 1st, 2024). [**XZ Outbreak: Unveiling the CVE 2024-3094 Backdoor**](https://medium.com/@adarshpandey180/xz-outbreak-unveiling-the-cve-2024-3094-backdoor-42332d075007)

Tom Abai (Mar 31, 2024). [**Critical Backdoor Found in XZ Utils (CVE-2024-3094) Enables SSH Compromise**](https://www.mend.io/blog/critical-backdoor-found-xz-utils-cve-2024-3094/)

Shachar Menashe, Jonathan Sar Shalom, Brian Moussalli (Mar 31, 2024). [**CVE-2024-3094 XZ Backdoor: All you need to know**](https://jfrog.com/blog/xz-backdoor-attack-cve-2024-3094-all-you-need-to-know/)

John Naughton (Apr 6, 2024). [**One engineer’s curiosity may have saved us from a devastating cyber-attack**](https://www.theguardian.com/commentisfree/2024/apr/06/xz-utils-linux-malware-open-source-software-cyber-attack-andres-freund)