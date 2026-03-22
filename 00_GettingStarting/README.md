# Getting Started

Welcome to the PIO_Hub Getting Started project.

- Getting started guide: https://docs.arduino.cc/built-in-examples/basics/Blink/

## Important Notes for using funktion

- The `blink_led()` function must be declared before the `setup()` function. This ensures that the function is defined before it can be called in the `loop()` function.


## Using Libraries in PlatformIO

### Overview

PlatformIO provides a comprehensive library management system that is more flexible than the Arduino IDE. The PlatformIO Library Registry contains over 10,000 libraries from various sources (Git, Subversion, etc.) with advanced search features that allow you to search by library name, file name, or component name.

### Key Differences from Arduino IDE

**Arduino IDE Approach:**
- Libraries are imported globally and become available to all sketches in your workspace
- If you update a library to a newer version, all sketches immediately use the new version
- This can cause compatibility issues if a sketch depends on an older library version
- Naming conflicts can occur when libraries have the same name

**PlatformIO Approach:**
- Libraries can be imported globally (available to all projects) or per-project (specific to individual projects)
- Per-project libraries give you flexibility to use different versions for different projects without affecting others
- You have better control over library updates and dependencies
- Reduces the risk of breaking existing projects when updating libraries

### How to Add Libraries to Your Project

1. **Edit `platformio.ini`:**
   - Open the `platformio.ini` file in your project root
   - Add libraries to the `lib_deps` parameter:
     ```ini
     [env:nano33iot]
     platform = atmelsam@3.1.0
     board = nano_33_iot
     framework = arduino
     lib_deps = 
         Arduino_LSM6DS3
         Wire
         SPI
     ```

2. **Using PlatformIO CLI:**
   - Run the command in your terminal:
     ```bash
     platformio lib install "Arduino_LSM6DS3"
     ```

3. **Using VS Code Integration:**
   - Click on the PlatformIO Home icon in the sidebar
   - Navigate to Libraries
   - Search for the library you need
   - Click "Add to Project" to add it to your `platformio.ini`

### Library Configuration in platformio.ini

All library parameters and version specifications are stored in the `platformio.ini` file:

```ini
lib_deps = 
    arduino-libraries/Arduino_LSM6DS3@1.0.0    # Specific version
    Wire                                        # Latest version
    https://github.com/user/repo.git#v1.0      # Git repository
```

### Accessing Arduino Built-in Libraries

When using the Arduino framework in PlatformIO, you have automatic access to all Arduino built-in libraries (e.g., `Wire`, `SPI`, `Serial`), so you don't need to explicitly install them.

### Best Practices

- Use per-project libraries to maintain flexibility and control over your dependencies
- Specify library versions in `platformio.ini` to ensure reproducible builds
- Regularly check for library updates, especially for security and bug fixes
- Review the PlatformIO Library Registry for well-documented libraries with example code
