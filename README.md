# SwiftLint Setup for Xcode 15

## Installation

### Homebrew Installation
```bash
brew install swiftlint
```

### Custom Rules
If needed, create custom rules by creating a `.swiftlint.yml` configuration file:
```bash
touch .swiftlint.yml
open .swiftlint.yml
```

### Xcode Build Phase Script

   - **Xcode Build Phase Script:**
     To automatically run SwiftLint as part of your Xcode build process, add a build phase script:

     - Open your Xcode project.
     - Select your project in the Project Navigator.
     - Select the target for which you want to run SwiftLint.
     - Go to the "Build Phases" tab.
     - Click the "+" button and select "New Run Script Phase."
     - In the script text area, paste the following script:

     If you are on an arm64 architecture, you may need to update the PATH in your shell configuration. Add the following lines:

     ```bash
     if [[ "$(uname -m)" == arm64 ]]; then
         export PATH="/opt/homebrew/bin:$PATH"
     fi
     ```
     
       ```bash
       if which swiftlint > /dev/null; then
         swiftlint
       else
         echo "warning: SwiftLint not installed, download from https://github.com/realm/SwiftLint"
       fi
       ```

     - Drag the new run script phase above the "Compile Sources" phase to ensure SwiftLint runs before compilation.
    
## Xcode 15 Changes

Xcode 15 made a significant change by setting the default value of the `ENABLE_USER_SCRIPT_SANDBOXING` Build Setting from NO to YES. As a result, SwiftLint encounters an error related to missing file permissions, which typically manifests as follows:
```bash
error: Sandbox: swiftlint(19427) deny(1) file-read-data.
```

To address this issue, update the `ENABLE_USER_SCRIPT_SANDBOXING` setting to `YES`:
1. Open Xcode.
2. Select the project's TARGETS.
3. Navigate to Build Settings.
4. Search for `ENABLE_USER_SCRIPT_SANDBOXING`.
5. Set it to `YES`.

```bash
ENABLE_USER_SCRIPT_SANDBOXING=YES
```

## Problems and Solutions

### Xcode Developer Tools Configuration

The following command configures the path to the Xcode developer tools:

```bash
sudo xcode-select -s /Applications/Xcode.app/Contents/Developer/
```

Explanation:

1. **`sudo`**: Executes a command with elevated privileges.
2. **`xcode-select`**: Configures the path to the Xcode developer tools.
3. **`-s`**: Specifies the path.
4. **`/Applications/Xcode.app/Contents/Developer/`**: Path to the directory containing Xcode's developer tools.

In summary, this command with `sudo` sets the path to the Xcode developer tools, allowing system-wide access. It's necessary to run with elevated privileges due to system-level configurations.
```
