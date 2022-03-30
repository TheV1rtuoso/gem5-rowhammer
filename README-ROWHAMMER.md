#Rowhammer Implementation
###Parameter
To run gem5 with the rowhammer extension, the paramters _rohammer_threshold_ 
and _corruption_mask_, both take a unsigned integer value, have to be set for the DRAM in the configuration.
(Added in MemInterface.py) for more information on parameters for SimObjects see 
https://www.gem5.org/documentation/learning_gem5/part2/parameters/
<br>
###Debug Output
To see the when and where flips happen, set the debug-flag _RowHammer_.
To see the full debug output set the debug-flag _RowHammerExt_ additionally.



Debug flags are set with the comma seperated argument _--debug-flag_
<br>More on: https://www.gem5.org/documentation/general_docs/debugging_and_testing/debugging/trace_based_debugging

###Rowhammer Functions - a short introduction to the source code
Changes can be found in /src/mem/mem_interface.cc and the respective header.

The RowCounter can be found in the Class MemInterface::Bank. It gets modified by adjacent row accesses in 
setOpenRow(), this functions should be called after ever change of the open_row.

It gets resetted when the _REF_STATE_(Refresh State) of the Rank is set to _REF_RUN_ in the function resetRHCounter.

The Function _MemInterface::applyMemoryCorruption_ should be invoked with the return status of setOpenRow,
as it is responsible for XORing with the corruption mask if one row surpassed the threshold.

Note: NVMInterface and DRAMInterface define Rank differently.

###Future Work
One Problem with appying the bitflips is deriving the physical address based
on Rank, Bank, Row. The current implementation is in DRAMInterface::applyMemoryCorruption.
It always _(tries to)_ compute the first address of the first byte in the respective row.

<b>TODO</b> add offset (random or given as parameter), maybe add a set of addresses that may only be modified,
and check for correctness of addr computation.

Another Problem is that some syscalls are not supported yet and raise an exception, modify to only issue a warning
and maybe even figure out how to get the physical addresses insinde the simulation (read pagemap).

->This would enable to run more complex rh-test binaries.V
