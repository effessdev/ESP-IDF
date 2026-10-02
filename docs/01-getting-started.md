# Getting started

!!! warning "This is just a draft"
    It's taken from one of my DEV.to posts, and has not been properly optimized for the purpose of this course. I will update it soon.

## Install ESP-IDF

### Install EIM

Espressif Systems provides a graphical tool called **EIM (ESP-IDF Installation Manager)** to install ESP-IDF. Click the link below to go to the official page to download EIM:

<https://dl.espressif.com/dl/eim/>

Make sure you are in the "Online Installer" tab. The exact file to download depends on your system:

- Windows: Download `eim-gui-windows-x64.exe`. Run this installer to install EIM.
- Linux x64 (Ubuntu): Download and install the `.deb` package (`eim-gui-linux-x64.deb`).

### Install ESP-IDF using EIM

Now that we have installed EIM, let's install ESP-IDF using it.

1. Open EIM.
2. Under "New Installation" click "Start Installation".
3. Under "Easy Installation", click "Start Easy Installation" to install the latest stable version of ESP-IDF with default settings.
4. If there are no problems, you will see the "Ready to Install" page. Click "Start Installation".

## Install ESP-IDF VS Code Extension

We use this extension as a high-level wrapper for ESP-IDF. Most times, we do not use ESP-IDF directly. For example, if we need to compile our source code, we ask the extension to do it, which uses the ESP-IDF we just installed internally to to compile the source code.

Install the extension named "ESP-IDF" by "Espressif Systems" in VS Code.

### Verify installation

After installing, restart VS Code. Use the shortcut `Ctrl + Shift + P` to open the **command palette** (remember this shortcut, we are going to use it a lot). Inside the command palette, search `ESP-IDF`. You will see many entries which start with `ESP-IDF:`. Those commands are provided my the ESP-IDF extension. These commands are what we use for almost everything.

### Note

If you are not in an ESP-IDF project, you might see this:

> No standard ESP-IDF project was found in this workspace. Do you want to activate the ESP-IDF extension anyway?

If you want to use the extension (in this case we do), you should click "Activate Anyway". Then only we can use the extension. If you mistakenly clicked "X", restart VS Code, wait for that to pop up again, and click "Activate Anyway".

Keep this in mind since you might need this in the future.

## Creating a New Project

Like I said before, we are going to use the `ESP-IDF: New Project` command from the command palette to create the project. Press `Ctrl + Shift + P` to open the command palette.

- From the command palette, search and click on `ESP-IDF: New Project`, and wait.
- Select your ESP-IDF version (you might see only one version since we only installed one), and wait.
- A new tab will pop up. Inside that tab, under `ESP-IDF Templates` select the `sample_project` template and click the "Create Project" button.

The tab will refresh, and you will see a form to fill in your project details. Fill in the details:

- Project name: Your project name.
- Project directory: Your project directory.
- ESP-IDF target: esp32.
- ESP-IDF board: Custom board.
- Serial port: Detect.
- OpenOCD configuration files: Keep the default value.
- ESP-IDF component directory: Keep the input empty.

Click "Create Project" and wait again. After creation, in the new tab which pops up, click "Open Project". This will open your brand new ESP-IDF project in a fresh VS Code window.

> If you are prompted to generate `compile_commands.json`, accept it. If not, or you mistakenly clicked "Decline", do `Ctrl + Shift + P -> ESP-IDF: Run idf.py reconfigure Task`, which does the same thing. `compile_commands.json` is essential for Intellisense to work correctly. Otherwise, you will encounter error squiggles everywhere.

> If you are using Clangd instead of the Microsoft C/C++ extension, run `ESP-IDF: Configure project for ESP-Clang` from the VS Code command palette to make sure Clangd IntelliSense works correctly. Also, make sure both of them aren't activated at the same time, as they can interfere with each other.

## Configure your project

You need to configure your project whenever you:

- Start a new project (like we did now)
- Adding, removing, or renaming source files
- Adding or changing component dependencies
- Fixing corrupted build files
- Etc.

But it's safe to do again and again, even if you haven't done anything above or have any problems. Since we just created a new project, let's run it.

Like we did before, we are again going to use the command palette. Open the command palette (do you remember how?), and click `Ctrl + Shift + P -> ESP-IDF: Run idf.py reconfigure Task`.

This will take some time. While it's working, let's learn what it does:

- Generates (or regenerates) the build files
- Generates `compile_commands.json`, which is essential for Microsoft C/C++ Extension or Clangd to provide Intellisense
- And a lot more (just know these two for now)

## Let's write some code

If you look at `main.c` right now, you will see and empty main function:

```c
#include <stdio.h>

void app_main(void)
{

}
```

Let's log "Hello world!" inside it:

```c
#include "esp_log.h" // Add this header for logging
#include <stdio.h>

// Define a tag for your log messages
static const char *TAG = "MAIN";

void app_main(void) {
  // Log "Hello world!"
  ESP_LOGI(TAG, "Hello world!");
}
```

## Building (compiling) the project

Like we always do, open the command palette and run 

```
ESP-IDF: Build Your Project
```

This will also take some time. Be patient.

## Flashing your project

Flashing is like uploading the compiled code to your ESP32. You need to physically connect the ESP32 to your computer. After doing that, run the following command from the command palette:

```
ESP-IDF: Flash (UART) Your Project
```

It will probably won't work out of the box. The fix differ dipending on your platform.

### If you are using Windows

If you are on Windows, flashing might show this error:

```plaintext
A fatal error occurred: Could not connect to an Espressif device on any of the 1 available serial ports.
```

If that happens, the next time you try, press and hold the BOOT button on your board as soon as
you see `Connecting...`. After some time, the `Connecting.....` will stop and you see several outputs. Stop holding the BOOT button when you see these somewhere:

```
Uploading stub flasher...
Running stub flasher...
Stub flasher running.
```

It might require some trial and error to find out how long you will have to hold the BOOT button.

### If you are using Linux (e.g., Ubuntu)

You might get this error while flashing:

```
A fatal error occurred: Could not open /dev/ttyUSB0, the port is busy or doesn't exist.
([Errno 13] could not open port /dev/ttyUSB0: [Errno 13] Permission denied: '/dev/ttyUSB0')

Hint: Try to add user into dialout or uucp group.
```

If that happens, run this command, log out, log back in (or reboot), and try flashing again:

```bash
sudo usermod -aG dialout $USER
```

You have to do it only once per system. Subsequent flashing will work without running this comamnd.

## Monitoring the device

You should keep your ESP32 connected to your computer so that it recieves power and be running. Let's check whether the "Hello world!" got logged.

On the bottom of VS Code, you will see several icons. Find the icon which looks like a monitor and hover over it. If you see "Monitor Device" when you hover, you found the correct icon.

Click on that icon. You will see several logs popping up. These are send internally. But among those log messages you will find our Hello world:

```
I (258) main_task: Started on CPU0
I (258) main_task: Calling app_main()
I (258) MAIN: Hello world!   <--------------- this
I (258) main_task: Returned from app_main()
```

You might see different numbers than 258. That's not a problem.

So, congratulations! You just set up your computer for ESP-IDF, created a new project, wrote some code, compiled, flashed, and monitored the output! Next, you can check out the [official documentation](https://docs.espressif.com/projects/esp-idf/en/latest/esp32/index.html#) to learn more about ESP-IDF. I also have some great tutorials which you can [read on my website](https://effessdev.github.io/read/) or on [DEV.to](https://dev.to/effessdev).

## Tips

### Using the `idf.py` command

`idf.py` may not be available in your terminal, but it's always available in the ESP-IDF terminal, which you can open using `Ctrl + Shift + P -> ESP-IDF: Open ESP-IDF Terminal`.

### Adding the VS Code Configuration Folder

If you accidentally deleted/edited the `.vscode` folder, you can regenerate it using the `Ctrl + Shift + P -> ESP-IDF: Add VS Code Configuration Folder` command.
