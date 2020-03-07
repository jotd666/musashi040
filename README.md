# musashi040
a fork (temporary I hope) of musash 68k emulator, with 040 fpu improvements

changes from version 2.32 of official musashi

m68k_in.c:

- fixed all CHK2/CMP2 instructions to support signed bounds. Previously it only worked with unsigned. Also sped up a bit
  by changing | by || in Z evaluation formula
- cptrapcc opcodes are still unsupported, but if no exception occurs, at least PC is properly updated
- trapt/trapcc 16 & 32 instructions: PC is updated before exception is triggered (or if exception isn't triggered). This fixes the
  return address value (just in case code returns from trap with RTE) and also the stackframe (XDAda 68040 compiler
  read the data parameter to check exception type, wrong exception type is read if stackframe is incorrect)
  
m68kfpu.c:

- fatalerror message fprintf => vfprintf
- added address register + displacement mode (mode 5, READ_EA_FPE/WRITE_EA_FPE), also changed prototype to it can be called with this mode 
  from FMOVEM loop
- added immediate mode (READ_EA_FPE)
- fpgen_rm_reg (FMOVE to register): added a lot of missing condition code update (SET_CONDITION_CODES(REG_FP[dst]))
- added FSDIVS and FSMULS (easy as it's the same case as FDIV and FMUL). Updated condition codes for FDIV
- fmove_fpcr: connected softfloat 4 rounding modes to this instruction, that didn't set rounding at all, when it's
  perfectly emulated in softfloat module (just added:  float_rounding_mode = (REG_FPCR >> 4) & 0x3;)
- fmovem: added address register+displacement mode (READ_EA_FPE/WRITE_EA_FPE interface had to be changed)
- added FScc instruction

The number of cycles isn't accurate. But the modified sources allowed to run a complex avionic program that computes
a plane trajectory, exactly the same way as it's done using Basilisk UAE emulation core (and even better since Basilisk
doesn't support proper rounding in FPU instructions)
