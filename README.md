**Table of contents**
* [Introduction](#introduction)
* [Configuration](#configuration)
* [Examples](#Examples)

# Introduction
The plugin uses [NSIS (Nullsoft Scriptable Install System)](https://nsis.sourceforge.io) to create a Windows installer for any gradle project, i.e. a single .EXE file which extract all binary files generated by the project to, for example, _C:/Program Files/MyProgram_. 

Different to other gradle plugins, this plugin comes with its own NSIS binaries which are automatically installed to a temporary folder. Therefore, no separate NSIS installation is needed, rendering this plugin suitable to be included e.g. in automatic builds of github repositories via _Actions_.

# Configuration
## How to include
The gradle-nsis plugin can be included via the standard plugin mechanism of Gradle 2.1 or higher:

    plugins {
      // Load gradle-nsis plugin
      id "com.github.langmo.gradlensis" version "0.1.0"
    }
    nsis {
      // Enter here the configuration settings of the gradle-nsis plugin. 
      // Note: a task "createInstaller" is automatically generated.
      
      // path to the NSIS configuration file
      configuration = file("${rootProject.projectDir}/installer_configuration.nsi")
      
      // folder where the NSIS installer generator is run. All paths 
      // in the NSIS configuration file are relative to this folder.
      runIn =  file("${rootProject.projectDir}")
    }

## How to configure
| Property Name | Default Value | Comment |
|---------------|---------------|---------|

# Examples
## Basic Example
This example creates an installer copying the file _basic.nsi_ into the installation folder. The raw files for this example can be found [here](https://github.com/langmo/gradle-nsis/tree/master/examples/basic).
Run `gradlew createInstaller` to generate the installer (the respective task is automatically generated).

File _/settings.gradle_:

    rootProject.name = 'basic'

File _/build.gradle_:

    // Load gradle-nsis plugin
    plugins {
        id "com.github.langmo.gradlensis" version "0.1.0"
    }

    // Configuration of gradle-nsis plugin.
    nsis {
        // path to the NSIS configuration file
        configuration = file("${rootProject.projectDir}/basic.nsi")
        // folder where the NSIS installer generator is run. All paths 
        // in the NSIS configuration file are relative to this folder.
        runIn =  file("${rootProject.projectDir}")
    }

File _/basic.nsi_ (see https://nsis.sourceforge.io/Docs/ for syntax):

    ;Include Modern UI
    !include "MUI2.nsh"
    
    ;Basic configuration
    Name "gradle-nsis basic example"
    OutFile "basic.exe"
    Unicode True
    ;Default installation folder
    InstallDir "$PROGRAMFILES64\gradle_nsis_basic_example"
    ;Request admin privileges for Vista/7/8/10
    RequestExecutionLevel admin
    !define MUI_ABORTWARNING
    
    ;Pages
    !insertmacro MUI_PAGE_LICENSE "LICENSE"
    !insertmacro MUI_PAGE_COMPONENTS
    !insertmacro MUI_PAGE_DIRECTORY
    !insertmacro MUI_PAGE_INSTFILES  
      
    ;Languages
    !insertmacro MUI_LANGUAGE "English"
    
    ;Installer Sections
    Section "gradle-nsis basic example" SecExample
        SetOutPath "$INSTDIR"
        FILE basic.nsi
    SectionEnd
    
    ;Descriptions
    LangString DESC_SecExample ${LANG_ENGLISH} "Copies the NSIS configuration."
    !insertmacro MUI_FUNCTION_DESCRIPTION_BEGIN
        !insertmacro MUI_DESCRIPTION_TEXT ${SecExample} $(DESC_SecExample)
    !insertmacro MUI_FUNCTION_DESCRIPTION_END
