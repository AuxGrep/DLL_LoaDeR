# DLL_LoaDeR
Add a hardcoded DLL  to any EXE and load it inside a memory .
Modify the IAT tables in memory to include an extra DLL, and continue execution. This would result in the injection of our target DLL file.
I know it is very complicated than modifying the PE headers in memory as we need to maintain the original structure of the EXE file while adding extra entries to the headers but let's make it happen.

This method may be useful for persistence - for example, if a system already runs a particular process on startup (abc.exe), we can modify this EXE to automatically load our DLL in the background.

NB: ONLY FOR CYBER SECURITY PURPOSES !! NOT MISUSE !! 

# HOW IT WORKS ?

This works as follows:

1. Load the original EXE file into memory.
2. Check if the EXE is 32-bit or 64-bit - this is important as the NT headers are different lengths.
3. Find the original import table location from the IMAGE_DIRECTORY_ENTRY_IMPORT data directory.
4. Convert the import table location from a virtual address to a file pointer - I have created a function to automate this called VirtualAddressToFilePtr.
5. Take a copy of the original import table and store it in memory.
6. Create a cloned version of this import table with one extra entry for our new DLL file.
7. Create a blank EXE output file.
8. Write the contents of the original EXE file to the new output file (up to the end of the last section).
9. Append the new extended IAT table to the new EXE file (directly after the last section).
10. Update the IMAGE_DIRECTORY_ENTRY_IMPORT directory to point to the new IAT table.
11. Increase the SizeOfRawData and VirtualSize fields of the last section to ensure the new IAT table is loaded into memory correctly. The section data on disk must be aligned to the value of pImageNtHeader->OptionalHeader.FileAlignment, so extra padding may be necessary here.
12. Check if any remaining data exists in the original EXE after the last header. Debug symbols and embedded files are often appended to the end of EXE files - these are not part of the EXE format, so they are not loaded into memory by default. The pImageNtHeader->FileHeader.PointerToSymbolTable value will be updated to point to the correct location if necessary, although these are unlikely to exist in production software.

Enjoy!!
@AuxGrep





