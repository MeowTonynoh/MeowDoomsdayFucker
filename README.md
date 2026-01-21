# Meow Doomsday Fucker

Forensic analysis tool to detect the "Doomsday" cheat client on Minecraft through RAM memory scanning and Windows system file analysis.

## ⚠️ Important Notice

This tool uses **obfuscated code** to protect detection signatures from reverse engineering. For this reason:

- **VirusTotal and other antiviruses may flag it as a false positive**
- The behavior (reading process memory, downloading external files) can be interpreted as suspicious
- **The tool is completely safe** - source code is available for verification
- Some antiviruses may block JARParser download: in that case, temporarily disable Real-Time protection during usage

---

## Usage

Download and run the `.exe` file. The program offers two main functionalities accessible through menu:

**[1] Scan Memory Processes** - RAM memory forensic analysis

**[2] JAR Parser** - Windows Prefetch file analysis

---

## How It Works

### Option 1: Memory Process Scanner

This function performs a forensic RAM memory analysis similar to those used by professional incident response tools.

#### Phase 1: Process Discovery

**Java process enumeration**
- Identifies all `java.exe` and `javaw.exe` processes active on the system
- Collects metadata: PID, startup time, uptime
- Requests handle with `PROCESS_VM_READ` and `PROCESS_QUERY_INFORMATION` permissions

#### Phase 2: Memory Mapping

**Memory region mapping**
- Enumerates all process memory regions via `VirtualQueryEx`
- Filters only committed regions (`MEM_COMMIT`)
- Selects regions with readable protection attributes (READ, READWRITE, EXECUTE_READ, EXECUTE_READWRITE)
- Excludes protected regions with `PAGE_GUARD` flag
- Ignores regions larger than 100MB to optimize performance

#### Phase 3: Pattern Matching

**Signature search in memory**
- Reads memory in 50MB chunks via `ReadProcessMemory`
- Performs byte-by-byte pattern matching on unique **obfuscated strings**
- The strings are exclusive signatures of the Doomsday client present only when the cheat is loaded in RAM
- **Real strings are kept private** to prevent bypass of detection by cheat developers
- Real-time progress bar shows percentage of analyzed memory

#### Phase 4: Classification

**Results:**
- **CLEAN**: No signature detected → legitimate process
- **FUCKED**: One or more signatures found → Doomsday client active in memory

---

### Option 2: JAR Parser (Prefetch Forensics)

This function leverages forensic analysis of Windows **Prefetch files** to identify cheat client traces.

#### What are Prefetch files?

Windows maintains `.pf` files in the `C:\Windows\Prefetch` folder that record:
- Every executable launched on the system
- Execution timestamps
- Files and DLLs loaded during execution
- Complete paths of accessed files

These files are fundamental in forensic analysis to reconstruct system activity.

#### How it works

**Automatic component download**
- Downloads `JARParser.exe` (v1.2) from Orbdiff's official GitHub repository
- Downloads `JarInspector.class` (v1.1) for Java bytecode analysis
- Saves to temporary directory: `%TEMP%\JARParserTool\`

**Prefetch Analysis**

JARParser performs:
- Parsing of all `.pf` files in `C:\Windows\Prefetch`
- Extraction of `.jar` file references in accessed paths
- Timestamp correlation to identify recent executions
- Pattern matching on suspicious file names (common cheat clients)

**Bytecode Inspection**
- `JarInspector.class` decompiles found JAR files
- Analyzes Java class structure
- Detects packages and methods typical of cheat clients

**Execution**
- Launches JARParser with elevated privileges (UAC prompt)
- Opens separate window for detailed output

---

## System Requirements

### Required Administrative Privileges

The tool requires elevated permissions for:

**Memory Scanner:**
- Access to external process memory
- Direct RAM reading via Windows APIs
- Memory structure queries

**JAR Parser:**
- Read access to `C:\Windows\Prefetch` (system-protected folder)
- JARParser execution with elevated privileges

### Behavior without Admin

If run without administrative privileges:
- Yellow warning is shown with option to continue
- Operations will fail due to lack of permissions
- **Running as administrator is strongly recommended**

---

## Detection Methodology

### Why is memory scanning reliable?

**Unique signatures**: Strings are specific to Doomsday, not present in legitimate mods

**Runtime detection**: Detects cheat only if currently loaded in memory (no false positives from residual files)

**Byte-level matching**: Exact byte-level search, impossible to bypass with simple obfuscation

### Why is Prefetch analysis important?

**Forensic persistence**: Even after closing the cheat, Prefetch maintains historical traces

**Timeline reconstruction**: Identifies when the cheat was executed over time

**File correlation**: Finds suspicious JARs even if renamed or moved

### Limitations

- **Advanced packers/crypters**: If Doomsday is encrypted at runtime with advanced techniques, strings may not be visible
- **Memory protection**: Some cheats implement anti-dumping that prevents memory reading
- **Prefetch disabled**: On some Windows systems, Prefetch may be disabled in settings

---

## VirusTotal False Positives

**It is normal for VirusTotal to flag the file as suspicious.**

Technical reasons:
1. **String obfuscation**: Detection signatures are obfuscated → detected as "obfuscated code"
2. **Memory reading APIs**: Use of memory reading APIs → common in forensic tools but also in malware
3. **Runtime download**: Downloads and executes external files → behavior some AVs consider suspicious
4. **Elevated privilege request**: Requires administrative permissions → red flag for some antiviruses

**Verify authenticity:**
- You can directly DM me if you have any suspicions :3

---

## Security and Privacy

- **The tool does NOT send data online** - all analysis is local
- **The tool does NOT modify memory** - read-only mode only
- **The tool does NOT collect personal information**
- Downloaded files come from official and verifiable GitHub repositories

---

## Contacts

**Creator**: Tonynoh

💬 Discord: `tonyboy90_`

📎 GitHub: [MeowTonynoh](https://github.com/MeowTonynoh)

🎥 YouTube: tonynoh-07
