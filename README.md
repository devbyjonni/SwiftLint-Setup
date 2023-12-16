# SwiftLint Setup for Xcode

### Homebrew Installation

1. Install SwiftLint via Homebrew:
   ```bash
   brew install swiftlint
   ```

2. Verify the installed SwiftLint version:
   ```bash
   swiftlint version
   ```

### Custom Rules

To set up custom linting rules, create a `.swiftlint.yml` file in your project root:

```bash
touch .swiftlint.yml
open .swiftlint.yml
```

You can define custom rules, exclusions, and settings inside this file.

[Example `.swiftlint.yml` file](https://github.com/devbyjonni/SwiftLintSetup/blob/main/swiftlint.yml)

---

## Xcode Build Phase Script

### Option 1: Inline Script in Xcode

1. In Xcode, select your target, go to **Build Phases**, and add a new **Run Script Phase**.
2. Add the following script to the **Run Script Phase** to ensure SwiftLint runs during the build process:

   ```bash
   export PATH="$PATH:/opt/homebrew/bin"

   if which swiftlint >/dev/null; then
       swiftlint
   else
       echo "warning: SwiftLint not installed. Install using 'brew install swiftlint' or download from https://github.com/realm/SwiftLint"
       exit 1
   fi
   ```

---

### Option 2: Using a Custom Script

To automate SwiftLint using a custom script:

1. **Create the `format-lint.sh` script** in the `Scripts` folder:
   
   ```bash
   cd YourProject
   mkdir -p Scripts
   touch Scripts/format-lint.sh
   open -e Scripts/format-lint.sh


3. **Add the following content** to `format-lint.sh`:

   ```bash
   
   #!/bin/bash

   if ! which swiftlint >/dev/null; then
     echo "SwiftLint is not installed."
     exit 1
   fi

   # Define the path to the SwiftLint config file
   CONFIG_FILE="${SRCROOT}/.swiftlint.yml"
   echo "Using config file: $CONFIG_FILE"

   # Check if the config file exists and run SwiftLint accordingly
   if [ -f "$CONFIG_FILE" ]; then
     swiftlint --config "$CONFIG_FILE"
   else
     echo "No .swiftlint.yml found, using default SwiftLint rules."
     swiftlint
   fi

   echo "SwiftLint finished."
   exit 0

   ```

4. **Set the appropriate permissions** for the script:
   ```bash
   cd Scripts
   chmod +x format-lint.sh
   ```

5. **Configure Xcode to use the script**:

   - In Xcode, select your target, go to **Build Phases**, and add a new **Run Script Phase**.
   - Add the following content to the script:

     ```bash
     
      export PATH="$PATH:/opt/homebrew/bin"

      if [ -f "${SRCROOT}/Scripts/format-lint.sh" ]; then
        "${SRCROOT}/Scripts/format-lint.sh"
      else
        echo "warning: Linting script not found"
        exit 1
      fi
     
     ```

---

### Build Script Configuration

In the **Run Script Phase**, configure the following options:

- **Disable**:
  - For install builds only
  - Based on dependency analysis

- **Enable**:
  - Show environment variables in the build log

---

### Xcode 15 Changes

If you encounter permission errors related to sandboxing, set `ENABLE_USER_SCRIPT_SANDBOXING` to `NO`:

1. Open Xcode, select your target, go to **Build Settings**, and search for `ENABLE_USER_SCRIPT_SANDBOXING`.
2. Set it to `NO`.

```bash
ENABLE_USER_SCRIPT_SANDBOXING = NO
```

---

## Running SwiftLint

1. **Run SwiftLint in the current directory**:
   ```bash
   swiftlint
   ```

2. **Lint a specific directory or file**:
   ```bash
   swiftlint lint --path <directory>
   swiftlint lint --path <file.swift>
   ```

3. **Automatically correct violations**:
   ```bash
   swiftlint autocorrect
   ```

4. **Generate a SwiftLint configuration file**:
   ```bash
   swiftlint generate-config
   ```

---
