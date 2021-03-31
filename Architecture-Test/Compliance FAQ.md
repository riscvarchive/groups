# <p style="text-align: center;">Architectural Compatibility FAQ</p>
<p style="text-align: center;">Ken Dockser</p>
<p style="text-align: center;">2020 07 20</p>

*Note: This document is a work in progress. Feedback is encouraged.*
 
The goal of this document is to look at the current RISC-V Architectural Compatibility efforts in the larger context of architectural Architectural Compatibility so that we can determine what steps to take and in what
order to fulfill our mandate.

# What is Architectural Compatibility?

Architectural Architectural Compatibility means that an implementation of the architecture meets all the requirements under all the conditions specified in the architecture. The implementation includes all the required portions of the architecture and that it properly implements any optional portions. This includes all the required instructions, registers, capabilities and features of the architecture and supported extensions.

In short, it means that the CPU design is *functionally* bug free.

# Is it possible to test for Architectural Compatibility?

Since *full* Architectural Compatibility requires an implementation to be free from functional bugs, it is essentially impossible to create a set of tests that can prove any implementation is bug free.

In theory, formal methods could be applied to mathematically prove that an implementation complies with the RISC-V Golden Formal Model. However, the effort to create an environment where any and all designs could be turn-key formally verified is huge and well beyond the scope of this task group.

It is possible to test for Architectural Compatibility in limited areas. Some such tests could be comprehensive –- testing for full Architectural Compatibility in a specific area -- while others would attempt to go after the most likely points of non-Architectural Compatibility.

# What is the primary test target for RISC-V Architectural Compatibility?

The RISC-V Architectural Compatibility tests (ACT) are written for the primary purpose of testing hardware designs in **RTL**. Implementers of RISC-V processors are required to run the ACT in RTL simulations, and send the result signatures from each test to the ISA Infrastructure Horizontal Committee for review and acceptance.

While ACTs are written so that they can run on models, this is not their primary purpose. Indeed, models should have more extensive tests written for them, especially if they are being used as a reference that designs are verified against.

Result signatures must be created in such a way that it is extraordinarily unlikely that an incorrect result could be masked by another error. Either the signatures are the instruction by instruction results themselves (which aren’t really signatures) or there needs to be some sort of hashing where the results in each bit are spread across many bits and the order of the results affects the signature.

We need to set rules on when a design is considered to have passed a set of ACTs.
For example, we need to avoid designs self certifying. The results will need to be reviewed and officially confirmed in writing by the ISA Infratructure HC.

# What are the goals of the Architectural Compatibility tests?

## 1. Helping ensure that RISC-V is perceived as a viable replacement for currently entrenched architectures

The RISC-V community is working hard to get a variety of RISC-V implementations accepted in various markets. While we have the benefit of so many of us working toward a common goal, we don’t have the same control over what is released as owners of proprietary architectures, While implementers are responsible for their own verification, all of RISC-V looks bad if a design is released with a headline-making bug. The Architectural Compatibility suite is our main line of defense against the release of designs that could negative impact on the perception of RISC-V as a world-class architecture.

While ACTs are only a subset of verification testing, it would not inspire confidence for people to hear that the buggy design passed “RISC-V ACTs”. That is why we need to:

1. create a very efficient (i.e., small and robust) Architectural Compatibility test suite.
2. provide concise text that can attached to a design when it has passed the current Architectural Compatibility suite. For example, “passed basic ACTs” is much more apt than “passed the ACT suite”
3. encourage implementers to subject their designs to much more rigorous verification.
    a. Open Source implementations that are created by small teams should actively seek other contributors who can help verify the design

People outside of the tech community -- and even many of those on the inside -- mistakenly think that RISC-V International is developing open source hardware, not an open standard. They easily conflate the architecture with an implementation. Thus, a bug in one design can be viewed as a bug in all of RISC-V.

## 2. Ensuring that implementers understood the spec

When someone creates a RISC-V implementation, they need a means to determine if they properly understood the spec. While it would be preferable for them to study the Golden Model (i.e., the RISC-V Sail Formal Model), or even run test cases on it to see the expected behavior, this will not always be done. In some cases, the implementer will assume they understand the spec and have no need to reference the model. In other cases, the implementer will throw some gates at the design and rely on verification to let them know if they got something wrong – don’t be this implementer!

The ACTs – or at least a portion of them – can be used as a quick check that they got the idea basically correct.

# Which architecture are we talking about?

The RISC-V Architecture has many components. Beyond implementing (at least) one Base Integer ISA and Machine Mode, everything else is optional. Furthermore, most of the components have optional parts.

The components of the Architecture can be viewed as forming a pyramid with the most basic functions (Base Integer ISA) forming the base and higher-level functions forming the subsequent layers:

           Hardware Platform
       Privileged ISA (hypervisor)
    Base Integer ISA (memory model, extensions)

## Base Integer ISA:

 1. RV32I
 2. RV64I
 3. RV128I
 4. RV32E

## Memory consistency models

1. RVWMO – weak memory model
2. Ztso – Total Store Ordering

## Standard Unprivileged Extensions

1. M – integer multiply and divide
2. A – Atomic instructions
3. F – IEEE FP: SP
4. D – IEEE FP: DP
5. Q – IEEE FP: QP
6. K - Crypto Extension
8. L – IEEE FP: Decimal
9. C – Compressed instructions
10. B - BitManip
11. J – Dynamically translated languages
12. T – Transactional Memory
13. P – Packed SIMD
14. V - Vector
15. Z - sub-extensions of a base, including
    1.  Zicsr – CSR instructions
    2.  Zifencei – Instruction-fetch fence
    3.  Zam – misaligned atomics
    4.  Zfinx – FP on GPRs
    5.  Zicounters: Performance & timers
    and many others

## Privileged ISA:

1. Machine ISA
2. Supervisor ISA

## Standard Privileged Extensions

1. Hypervisor

## Hardware Platform Architectures

1. Unix-Class   Platform Architecture (RVA\<yy\>)
2. Microcontrollers Platform Architecture (RVM\<yy\>)

# What subsets of Architectural Compatibility are practical and meaningful to test?

Architectural Compatibility can be broken down into many categories. It makes sense to start at the bottom of the architecture pyramid and work our way up. Starting with the Base Integer ISA, we can target specific subsets. Note that each subset doesn’t need to be tested separately; they can be combined into a single testplan where it makes sense.

>When an implementation passes a specific testplan, it does not make sense to say that it has “passed RISC-V Architectural Compatibility” even if there is only one the one testplan (thus far). If a design passes, for example, the **single-instruction instruction-decode tests**, then it can only claim that that is what it passed.

## Single-instruction execution:

1. Instruction alignment 16 vs 32-bit
2. Instruction decode <ol type="a">
   <li>major opcodes</li> 
   <li>minor opcodes – loosely defined as all other instruction bits that specify the function of the instruction</li></ol>
3. Register decode <ol type="a">
     <li> Special handling of X0 (source, destination)
     <li>Destructive encoding</ol>
4. Immediate value handling
5. Instruction functional execution <ol type="a">
     <li>Arithmetic/Logical</li>
     <li>Compare</li>
     <li>Shift</li>
     <li>Branch/Jump</li>
     <li>Storage (Load/Store)</li>
     <li>System</li></ol>
6. Instruction execution corner cases<ol type="a">
     <li>architecturally specified</li>
     <li>implicit</li>
     <li>common</li></ol>
7. Profile Specified behavior (e.g., trap on misaligned storage)
8. Exception detection<ol type="a">
    <li>Access Privilege violation</li>
    <li> Page Fault</li>
    <li> Misaligned Access (code or data)</li>
    <li> Illegal Instruction</li></ol>
   and others

## Multiple instruction execution:

1. Instruction Alignment
2. Mixed width instructions (when C supported)
3. General Purpose Register dependencies<ol type="a">
    <li>RAW</li>
    <li>WAW</li>
    <li>WAR</li></ol>
4. CSR Dependencies: changes to CSRs take effect on the next instruction

# How do ACTs differ from verification?

Quite simply, ACTS are a subset of verification. In practice, ACTs are a proper subset of verification. In fact, ACTs are a very small but important subset of verification.

ACTs have different constraints and requirements from verification. For one thing, ACTs are typically performed on RTL before a chip is manufactured. Therefore, the complete ACT suite must be small enough to be *simulated* on the RTL design. Since simulation is relatively slow, the tests must necessarily be relatively short.

Sometime Architectural Compatibility tests for ISAs include some long tests that need to be run under accelerated simulation or even emulation. However, requiring such tests puts a great deal of burden on the implementer, and not all implementers have the resources to use simulators or emulators. Therefore, it is better to avoid requiring such tests whenever possible.

# Does passing all the Architectural Compatibility tests mean that the design is compatible?

No! Passing the ACTs mean just that and no more. It means that the design has
passed a variety of tests that examine the behavior of the design in a variety of scenarios and in those specific scenarios it has behaved according to the specification.

>NB: We are not using the term *Compliance* in connection with the tests that are produced by the ACT Task Group Group as this is misleading at best. It is better to name the tests according to what they do. For example, one section of tests could be called the Basic Instruction Functionality Tests.

It is incumbent upon the implementer to come up with a well thought out test plan to more thoroughly tests their design for Architectural Compatibility with the standard. Their plan should include extensive testing for their unique microarchitecture but must also cover areas that are included in the Architectural Compatibility suite – but with more extensive testing.

It is incumbent upon the ACT Task Group to specify and implement tests that are well chosen to ferret out areas where designs are non-compliant. However, given the constraints of the tests, only a limited number of these areas can be tested.

Designs that pass the tests could be largely non-compliant. This could happen in a case where, for example, the ACTs are run on an early non-debugged design and then each failure is fixed in isolation with little attention to what a bug might imply about other bugs not caught by the test. Even if other verification is run after this, it is possible that such efforts are insufficient and that the design may not be Architectural Compatible in many corner cases.

It can be beneficial to hold off running the ACT suite until initial bring up and debugging of the design is complete. This way, the ACT suite is not being inadvertently relied upon too much for verification. On the other hand, it can be beneficial to run the ACT suite early in design development to see if the implementors have misunderstood a part of the specification.

A reasonable compromise is to perform initial verification before running the *basic instruction tests* within the ACT suite. Then, when the basic instructions tests of the ACT suite are run, any failures should be closely evaluated to determine if they are due to mis-interpreting the specification or due to missed cases in early verification.

In either case, a failure should result in the designers avoiding the knee jerk reaction of fixing the bug but instead taking a step back and rethinking things. If the failure is due to a misunderstanding of the specification, the designers should take the time to get a correct understand and then rewrite their RTL for that section. If the failure shows a failure in the verification plan, the designers should determine what in their verification plan was missed at a high level.

# What if a design fails a Architectural Compatibility test?

There could be a bug in their design or their could be a bug in the ACT test. The designer should first endeavor to see if there is an issue with their design. Once they have made a reasonable effort to determine the correct behavior for the failing scenario and have determined that their design is behaving in such a fashion, they should file an issue in the riscv/arch-test github repository. They should provide enough information about the problem that others can understand and possibly recreate the issue.

The Architectural Compatibility SIG will need a standard procedure to document, investigate, and respond to all such bug and issue reports. The group will then decide how to avoid the issue in the future – this may include changing the test or replacing it with new tests.

# What is the Device Under Test (DUT) for purposes of Architectural Compatibility?

What is tested depends on which tests are being run. The initial tests being created are for the Unprivileged Architecture and for uniprocessors. For these tests, the RTL for the CPU proper is the DUT. A test environment needs to be specified so that the CPU can read and write to memory (including I/O).

These base initial tests are independent of any specific “Platform” although they will need to be flexible enough to be used for testing of RTL designs against the forthcoming “Platform Specifications” which will further define the requirements for each specific “Standard Hardware Platform” such as what instructions must be supported, what instructions can be emulated, how reserved opcodes are handled and even specific requirements on individual instructions.

As the Architectural Compatibility test branch out into more complex areas such as memory ordering, the test
environment specification will need to include more complex structures include those that can
act as transactors and checkers

# Can the Architectural Compatibility tests be used to test SW models?

Yes, but as with RTL this is not sufficient to determine that a SW model is compliant. Extensive testing is required to have good confidence that any RISC-V implementation, whether it is RTL or a high-level model, is compliant.

# Can a design be given waivers for certain tests?

While it is best to avoid the use of waivers, there are times when they are necessary. For example, a design might fail a test due to a bug in the test. It might be unreasonable to require an implementer to wait until a new test is developed especially if it is determined that additional tests need to be added to help ensure that that the test is useful.

Tests that have non-deterministic behavior is another example. These are tests that may have multiple legal result that depend on timing, concurrency of other asynchronous events, and/or microarchitectural state. If the reference model can only exhibit a single behavior in a test, the result will be a mismatch that is, nontheless, legal.

Another such case is when an implementor decides to not implement an instruction but plans to emulate it. For example, this could be a divide instruction. In such a case, they could be given a waiver and they would also need to clearly state that they passed the tests with a specific waiver. The alternative is design would be considered incompatible. This should become less of an issue when “Platform Specifications” are developed and ACTs are created for these where instruction emulation can be permitted (by the specification) and tested.

There can also be cases where the specification is unclear (the Formal models might even disagree - with the Golden model giving the minority report), it would require extensive redesign to get the expected behavior, and the exhibited behavior didn’t cause a major issue.

It is probably best for a design to be listed as having passed the test suite with the exception of specific functionality (and a listed group of tests).

# What about cases where the RISC-V Architecture doesn’t have a specific required result?

There are some cases where there is more than one result permitted by the architecture. For example, when writing a non-legal value to a field in a CSR that is WARL, an implementation-specific answer may be provided (within limits).

When a variety of different results are possible, and the reference model cannot be configured to model the exact (legal) behavior, the tests need to check that the result returned by the design is:

1. Permitted by the architecture, and
2. Consistent with the rest of the implementation
3. Deterministic, depending only on the state of the hart

# How can Architectural Compatibility be broken down into categories?

Since it is impossible to test for full Architectural Compatibility, it makes sense to break it  down into different categories and then decide which of these categories are essential for the purposes of RISC-V ACTs. Even the categories themselves can be impractical to fully test, therefore it is necessary to determine what subset of the chosen categories are essential for our purposes.

What follows is a list of some of the categories that ACTs can be broken down into:

1. Basic architecture - behavior independent of “platform specifications”<ol type="a">
    <li>Unprivileged architecture</li>
    <li>Privileged architecture </li></ol>
2. “Platform Specifications”<ol type="a">
    <li>Specific requirements</li>
    <li>Unimplemented instructions</li>
    <li>Reserved encoding space</li></ol>
1. Instruction decode<ol type="a">
    <li>Major opcode decode</li><ol type="i">
       <li>Standard</li>
       <li>Reserved for standard</li>
       <li>Reserved for implementation</li></ol>
    <li>Minor opcode decode - includes all bits used to define the behavior of an instruction even if they are included in areas typically used for immediate values<ol type="i">
       <li> Standard instruction decode</li>
       <li>Reserved instruction decode - includes forms of standard instructions that are not valid</li>
       <li>For standard</li>
       <li>For implementation use</li>
       <li>Implementation defined instruction decode</li></ol>
    <li>Register Decode - all possible register combinations for an instruction including:<ol type="i">
       <li>Destructive encodings - destination is the same as one or more sources</li>
       <li>X0 source</li>
       <li>X0 destination</li></ol>
    <li>Immediate Decode - all possible immediate values</li></ol>
1. Instruction execution/operation<ol type="a">
    <li>Operands<ol type="i">
       <li>Explicit special cases</li>
       <li>Interesting cases - based on the operation <ol type="1">
          <li> Carry propagation </li></ol>
       <li>All combinations <ol type="1">
          <li> Corner cases</li></ol>
    <li>Exceptions<ol type="1">
          <li> Sub exceptions (e.g., different type of floating-point invalid
exception)</li></ol></ol>
3. Instruction ordering<ol type="a">
    <li>Dependencies<ol type="i">
       <li>RAW</li>
       <li>WAW</li>
       <li>WAR</li></ol>
    <li>Distance</li>
       <li> Memory ordering<ol type="i">
       <li>RVWMO</li>
       <li>RVTSO</li></ol></li></li>

# Dangers of designing to Architectural Compatibility tests

ACTs, like most tests, only probe a specific point within the whole architectural space. They are effectively samples. If someone creates a design that fails one or more tests, and then fixes their design to pass the specific failures, they are only fixing the samples and are not necessarily fixing the areas between the samples. The result can be a design full of bugs that passes the test.

At least in theory, a design that passes a test the first time is superior to a design that fails a test and then is patched to pass the test. The first-time-passer has shown that it can pass an unknown test - one composed of unknown samples; in theory it could pass any test. One that fails the first time has shown that it didn’t meet a minimum standard and likely has other failures as well, in proportion to what was found. Of course, the errors fixed will likely fix other potential failures. But unless the actual test were perfect and complete examples of all other possible cases, there are likely other failures. The type and quantity of failures depends on the cause of the failures. For example, if these were from a part of the spec that was unclear, or misunderstood or if they were due to typographical errors, cut-and-paste errors, conceptual oversights, lack of understanding, or ...

# The need for clearly stating what the tests do and don’t do

It would be a disaster for people to think that passing ACTs proves that a design is correct. Or even substantially correct. Passing Architectural Compatibility is like passing a code check on a house: not every pipe weld or electrical splice is inspected on a code check, and even if it were, the house could still have serious issues. Passing Architectural Compatibility doesn’t mean the design is all correct, it just means that it passed a few (hopefully well chosen) tests and seems to be OK.

Passing ACTs is always necessary but never sufficient (not even close) to reasonably prove that a design will behave as it is intended. It is therefore essential that we temper expectations by including clear qualifications in what we call Architectural Compatibility.

Since it is not possible to prove a design is fully compatible, we should never declare a design is compatible by virtue of passing the ACTs. In fact, we should not call these Architectural Compatibility tests as that is misleading. We should refer to the tests based on what subset of Architectural Compatibility they cover. For example, “instruction decode and register access Architectural Compatibility”.


