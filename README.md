# xv6-null-pages

## Adding support for xv6 null pages.

### Problem

By default, xv6 code is loaded into the first part of address space (from Virtual Address = 0). 
So, when trying to dereference a null pointer, it prints the first bit of code in the program that is being run.

The expected behaviour is to throw exception.

### Solution

To solve the null pointer dereference issue, we load the program into memory from virtual address 0x1000 ( = 4096 = 1st page) instead of 0. 
So, when the OS tries to access the virtual address at location 0, it gets a page fault and thus a trap.
When a new process is spawned through fork : In the child, we iterate over all the pages starting from the page at virtual address 0x1000, 
leaving out the first page (at virtual address 0). We shift the paging by a page, leaving out the first page. 
In exec routine, we change the location to 0x1000, where the program is to be loaded into memory. In the end, we change the entry point in the Makefile to 0x1000.


## xv6 mprotect and munprotect

### Problem

By default, xv6 code is marked read-write. So, it is possible that a buggy program will overwrite its own text.
So, we add 2 system calls to change the protection bits of parts of the page table to be read-only, thus preventing such overwrites.
We also add a provision to change them back to read-write

### Solution

For mprotect, we do sanity checks to see the length is greater than 0 and the address range lies in the process virtual address space. 
Once these are verified, we iterate over the page directory and find the page table entry for addr, unset its PTE_W bit. 
Then we update the cr3 to refresh the page table entries so that it tells the hardware about the modification done. 
Similarly, for munprotect we just unset the PTE_W bit in the flags for the page table entry.







