# RISC-V Working Groups: RASD Charter
In order to promote development of RISC-V in server domain, we need a complete specification to guide implementation of RAS in the design of SoC, firmware and OS.  

### Tasks in scope include
#### RAS terminology interpretation:  
Interpretation of RAS concept and terminology (e.g. diagnosability, recoverability, types of error).

#### RAS framework design
##### A framework covers the full path of error handling
* Error recording: Standard error record formats (e.g. register banks, APEI­)
* Error reporting: Error event reporting methods (e.g. exceptions, NMI, local/global interrupts)
* Error recovery: strategies adopted to handle the error (e.g. neglect/warning/recover/isolation/halt)

#### RAS feature support:
##### Engage specific RAS features into the framework 
* E2E Data protection
* error isolation
* data poisoning containment;
* advanced error reporting for PCIe
