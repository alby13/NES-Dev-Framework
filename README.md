# NES Dev Framework
A Python-based framework designed to assist in the development of NES games using 6502 assembly language, specifically targeting the **cc65 toolchain** (ca65 assembler, ld65 linker).

This framework aims to streamline common NES development tasks by:

1.  **Scaffolding Projects:** Automatically creating a standard directory structure, Makefile, and linker configuration file (`nes.cfg`).
2.  **Generating Code:** Providing foundational assembly routines for essential NES functions like RESET, NMI (VBlank handling), IRQ, controller input reading, PPU initialization, and RAM clearing.
3.  **Asset Management (Basic):** Includes Python classes (`SpriteEditor`, `BackgroundEditor`) to define sprite and background tile data, and logic to generate CHR ROM data from these definitions.
4.  **Game Logic Structure:** Offers helpers (`GameStateManager`, `CollisionDetectionHelper`) to structure game states and generate basic collision detection code.
5.  **PPU Update Buffering:** Includes a system for queuing PPU writes (nametable, attribute, palette updates) from the main game loop to be safely executed during the NMI handler.
6.  **Documentation:** Generates basic documentation for the project's memory map and hardware register usage.
7.  **Features AI Assistance:** Integrates with the Google Gemini AI API (via `google-genai`) to provide an interactive assistant for generating code snippets, explaining concepts, and answering NES development questions within the context of the framework.

**Note:** This framework focuses on code generation and project setup. The provided "editors" (`SpriteEditor`, `BackgroundEditor`, `MusicEditor`) are Python classes for defining data programmatically and they do not function as traditional graphical tools.

## Features

*   **Project Generation:** Creates a ready-to-compile NES project structure.
*   **Makefile:** Generates a Makefile for easy building (`make`), running (`make run`), cleaning (`make clean`), and debugging (`make debug`) using cc65 and an emulator (Mesen by default).
*   **Linker Script:** Generates a `nes.cfg` file for `ld65`, configuring memory segments (ZP, RAM, PRG ROM, CHR ROM/RAM, Vectors).
*   **Standard Routines:** Includes pre-written, commented assembly routines for:
    *   `RESET`: Initialization sequence.
    *   `NMI`: VBlank handler (OAM DMA, controller reads, PPU updates, scroll).
    *   `IRQ`: Basic IRQ handler structure.
    *   `ClearRAM`/`ClearOAM`: Memory clearing utilities.
    *   `WaitVBlank`/`WaitNotVBlank`: PPU synchronization.
    *   `InitPPU`: PPU setup (palettes, control/mask registers).
    *   `ReadController1`, `IsButtonPressed`, `IsButtonTriggered`: Controller input handling.
    *   `SetSpriteData`, `HideSprite`, etc.: OAM buffer management.
    *   `PPU Update Queue`: Routines (`QueuePPUWrite_Byte`, `QueuePPUWrite_Multi`, `DoPPUUpdates`) for safe background updates.
*   **Asset Handling:**
    *   Define sprites and background tiles in Python.
    *   Generates CHR ROM `*.bin` data (or configures for CHR RAM).
    *   Generates a `tile_map.json` documenting asset indices.
*   **Game State Management:** Helper class to define states (e.g., Title, Gameplay, GameOver) with Init/Update logic and generate jump tables.
*   **Collision Detection:** Generates basic AABB (Axis-Aligned Bounding Box) and point-vs-tile collision checking routines.
*   **Documentation Generation:** Creates `memory_map.txt` and `hardware_registers.txt`.
*   **AI Assistant (Optional):**
    *   Interactive chat interface via the command line.
    *   Leverages Google Gemini (via API key or Vertex AI) for code generation and assistance.
    *   Context-aware of the current framework project structure.
    *   Handles framework-specific commands (`build project`, `show routine`, etc.).

## Requirements

1.  **Python:** Version 3.7 or higher recommended.
2.  **cc65 Toolchain:** The NES development suite including `ca65` (assembler) and `ld65` (linker). Must be installed and accessible in your system's PATH.
    *   Download/Info: [https://cc65.github.io/](https://cc65.github.io/)
3.  **NES Emulator:** Required for running the compiled ROM (e.g., Mesen, FCEUX, Nestopia). Mesen is recommended for its debugging features and is the default for `make run`/`make debug`.
4.  **Google GenAI Python Library (Recommended, but not required):** If you want to use the AI Assistant feature.
    ```bash
    pip install google-genai
    ```
5.  **(Optional) Google Cloud SDK:** Required *only* if using the AI Assistant with **Vertex AI**. You'll also need a configured Google Cloud project and location.

## Installation

1.  **Clone the repository (or download the script):**
    ```bash
    git clone <repository-url>
    cd <repository-directory>
    ```
    (Replace `<repository-url>` and `<repository-directory>` accordingly). If you only have the Python script (`nesdevframework.py`), save it to a directory.

2.  **Install cc65:** Follow the instructions on the [cc65 website](https://cc65.github.io/) for your operating system. Ensure `ca65` and `ld65` are in your PATH.

3.  **(Optional) Install Google GenAI:**
    ```bash
    pip install google-genai
    ```

## Usage

### 1. Running the Framework Script

The Python script (`nesdevframework.py` in the provided code, rename if desired) primarily serves to:
a. Define the `NESFramework` class and helper classes.
b. Provide an example `create_example_project()` function.
c. Run the optional AI Assistant.

To generate the example project, simply run the script:

```bash
python nesdevframework.py

uilding the Generated Project
After the framework script has generated the project files:

Navigate to the project directory:

cd output/NESExample/
Use code with caution.
Bash
Build the ROM: Run make. This uses ca65 to assemble src/main.s into an object file in build/obj/ and then uses ld65 with nes.cfg to link it into the final build/NESExample.nes ROM file.

make
Use code with caution.
Bash
Run the ROM: Run make run. This first ensures the ROM is built (by depending on the make target) and then launches it using the emulator specified by the EMU variable (default: mesen).

make run
# Or specify an emulator:
make run EMU=fceux
Use code with caution.
Bash
Debug the ROM: Run make debug. This builds the ROM and launches the emulator with debugging enabled (uses Mesen's --debugger flag by default).

make debug
Use code with caution.
Bash
Clean Build Files: Run make clean to remove the build/ directory contents (object files, ROM, map file) and other generated artifacts.

make clean
Use code with caution.
Bash
4. Customizing the Framework
Instead of just running the example, you can modify the Python script to:

Create a NESFramework instance with your desired project name.

Use the SpriteEditor and BackgroundEditor to define your game's specific graphics.

Use framework.add_custom_routine() to add your own assembly code modules.

Use framework.add_game_state() to define the different screens and logic flows of your game.

Modify the default memory map (framework.memory_map, framework.zp_variables) before building if needed.

Call framework.build_project() with appropriate parameters (PRG/CHR banks, mapper, mirroring) for your target hardware configuration.

AI Assistant (Optional)
If the google-genai library is installed and configured, the script will launch an interactive AI assistant after generating the example project.

Setup
Install Library: pip install google-genai

Configure API Access: Choose ONE method:

Gemini API Key: Set the GOOGLE_API_KEY environment variable to your API key obtained from Google AI Studio.

Vertex AI:

Set the GOOGLE_GENAI_USE_VERTEXAI=true environment variable.

Set GOOGLE_CLOUD_PROJECT to your Google Cloud project ID.

Set GOOGLE_CLOUD_LOCATION to your project's region (e.g., us-central1).

Ensure you are authenticated: Run gcloud auth application-default login.

Usage
When the script runs and detects valid configuration, the assistant prompt will appear:

========================= NES AI Dev Assistant  =========================
Project 'NESExample' is active.
Type 'help' for commands, 'build project' to generate files, or 'quit' to exit.

You:
Use code with caution.
Type your requests or commands.

Assistant Commands
help: Show available commands.

build project: Regenerate project files using framework.build_project().

show context: Display a summary of the framework's current state and conversation history length.

clear history: Clear the assistant's conversation history (does not affect framework state).

show routine <name>: Display the assembly code for a defined routine.

show state <name>: Display the Init and Update assembly code for a defined game state.

list routines: List the names of all routines currently in the framework.

list states: List the names of all game states currently in the framework.

quit / exit: Exit the assistant.

Any other input is sent to the Google Gemini model for processing. The assistant will attempt to provide relevant 6502 assembly code, Python framework code, explanations, or debugging help based on your request and the current project context.

Warning: The assistant may suggest code in blocks. Execute these with caution, as it can be dangerous. Review suggested code carefully.

Code Structure Overview
NESFramework: The main class managing the project structure, assets, routines, states, and build process.

SpriteEditor: Class for defining 8x8 or larger sprites programmatically and managing their pixel data.

BackgroundEditor: Class for defining 8x8 background tiles, managing their pixel data, and setting up nametable/attribute table layouts.

MusicEditor: Placeholder class for defining music/SFX data structures (requires integration with a specific NES sound engine).

CollisionDetectionHelper: Generates basic collision detection assembly routines (AABB, Tile).

GameStateManager: Helper to define game states (Init/Update code) and generate state switching logic.

NESDevAssistant (Optional): Integrates with Google Gemini for AI-powered development assistance.


Limitations & Future Work
Basic Asset Editing: The "editors" are data definition tools, not visual tools. Consider integrating with external tools like NES Screen Tool (NESST) or YY-CHR.

Sound Engine: No specific sound/music engine is included. MusicEditor is a placeholder; generated music/SFX data needs to be formatted for an engine like FamiTone, Pently, or GGSound.

Mapper Support: Primarily designed for simple mappers like NROM (Mapper 0). Adding support for bank switching logic for mappers like UxROM, MMC1, MMC3 would require significant additions.

PPU Update Queue: The current queue is basic. More advanced features like Run-Length Encoding (RLE) or compression for background updates could be added.

Error Handling: Error handling in generated code and the framework itself could be more robust.

Abstraction: Tightly coupled to cc65; adding support for other assemblers (e.g., NESASM, ASM6) would require significant changes.

AI Safety: Executing AI-generated Python code is inherently risky. Sandboxing, backing up your code, or manual review is essential.


Contributing
Contributions are welcome! Please feel free to fork the repository. For major changes, please open an issue first to discuss the proposed changes.


## License
More information coming soon.  
