

## Overview
This project demonstrates a method to bypass Windows Defender while executing certain operations such as dumping LSASS and executing Mimikatz in-memory. The tactics involved include manual PE (Portable Executable) loading, EAT (Export Address Table) hooking, and memory manipulation. 

## Tactics

### Manual PE Loading
- **Parsing**: The loader parses the EXE file to understand its structure and requirements.
- **Memory Allocation**: Allocates the necessary memory for the PE file execution.
- **Section Mapping**: Maps the sections of the PE to the allocated memory.
- **Base Relocations**: Performs base relocations to ensure correct addressing.
- **Import Address Table Fixing**: Fixes the IAT to resolve dependencies.
- **Execution**: Executes the loaded PE file from memory.

### EAT Hooking
- **Initialize Headers**: Retrieve the DOS and NT headers of the target module using `getHeaders`.
- **Get EAT Info**: Populate the `EAT_FUNCTION_INFO` structure using `getFunctionAddresses` to obtain pointers to important parts of the EAT and the number of functions.
- **Function Redirection**: Utilize `Hooking` to redirect a specified function in the EAT to your custom function. This modifies the EAT so any call to the specified function will now go to your custom function.

### AmsiScanBuffer Bypass
- **Absolute Hooking**: Perform absolute EAT hooking to redirect calls to `AmsiScanBuffer` to a custom function `CustomAmsiScanBuffer` that always returns `AMSI_RESULT_CLEAN`, indicating the buffer is clean.

By implementing these tactics, when Defender dynamically calls the function to scan memory buffers from dynamic loads, it's redirected to our fake function which always returns 0, indicating the buffer is clean.

## LSASS Dumping
- Bypass Defender by creating a callback to the `MiniDumpWriteDump` function.
- Store the dump chunk in memory in encrypted form, then export it to a file.
- When `MiniDumpWriteDump` is given `NULL` in the value of `HANDLE hFile`, the data is handled by our custom callback, processed, and stored securely.


## Usage
```bash
--gendmp   # To Generate LSASS Dump
--mimi     # To run Mimikatz
```

**Warning:** This project is designed for **educational and research purposes only**. Use at your own risk.

