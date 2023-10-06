# Destroying Defender (Educational Project Only)

This project is designed for **educational and research purposes only**. The objective is to investigate and demonstrate the process of LSASS dumping and Mimikatz source file execution while Windows Defender is operational. The project comprises of a custom loader and a function hook to bypass memory buffer checks conducted by Defender.

**Warning:** Unauthorized use, distribution or modification of this code may violate legal agreements and laws. Use at your own risk.

## Features
- **LSASS Dumping**:
  - Create a LSASS dump bypassing Defender by implementing a callback to the `MiniDumpWriteDump` function.
  - Store the dump chunk in memory in an encrypted form, then export it to a file.

- **Mimikatz Execution**:
  - Build a loader to parse and execute Mimikatz in memory while Defender is running.
  - Manual Portable Executable (PE) loader conducts the following operations:
    - Reads PE file into memory.
    - Allocates necessary memory for PE file execution.
    - Maps the sections of the PE to the allocated memory.
    - Performs base relocations.
    - Fixes the Import Address Table (IAT).
    - Executes the loaded PE file from memory.

- **Buffer Check Bypass**:
  - Bypass `AmsiScanBuffer` function that checks memory buffers.
  - Employ absolute hooking technique on the Export Address Table (EAT) to redirect function calls.
  - Allocate memory post-module to avoid negative RVA issues.
  - Utilize assembly-driven redirection for accurate address resolution.

## Tactical Breakdown
1. **Initialize Headers**:
   - Use `getHeaders` to retrieve the DOS and NT headers of the target module.

2. **Retrieve EAT Info**:
   - Employ `getFunctionAddresses` to populate the `EAT_FUNCTION_INFO` structure with pointers to crucial parts of the EAT and the number of functions.

3. **Function Hooking**:
   - Utilize `Hooking` to redirect a specified function in the EAT to your custom function, modifying the EAT so any call to the specified function now redirects to your custom function.

By implementing these tactics, when Defender dynamically calls the function to scan memory buffers, it's redirected to our fake function which always returns 0, indicating the buffer is clean.

## LSASS Dumping
- Bypass Defender by creating a callback to the `MiniDumpWriteDump` function.
- Store the dump chunk in memory in encrypted form, then export it to a file.
- When `MiniDumpWriteDump` is given `NULL` in the value of `HANDLE hFile`, the data is handled by our custom callback, processed, and stored securely.

## Usage
```bash
--gendmp   # To Generate LSASS Dump
--mimi     # To run Mimikatz
