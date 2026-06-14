# Setup Guide: Java for PySpark
Apache Spark is built on Scala and runs inside a Java Virtual Machine (JVM). To run PySpark locally for this capstone project, you must have a local Java Development Kit (JDK) installed and properly configured in your system environment variables.


This guide details the setup process for both macOS and Windows.
## 📋 Prerequisites
* Recommended Java Version: Java 11 or Java 17 (Java 17 is highly recommended for stability with modern PySpark).
* Python Version: Python 3.8 to 3.12.
## 🍏 macOS Installation
### Option 1: Using Homebrew (Recommended)
#### 1. Install OpenJDK 17:
Open your terminal and run:
``` bash
brew install openjdk@17
```
#### 2. Create System Symlink:
macOS needs to know where the system Java wrappers point. Link the Homebrew installation to the system Java directory:
``` Bash
sudo ln -sfn /opt/homebrew/opt/openjdk@17/libexec/openjdk.jdk /Library/Java/JavaVirtualMachines/openjdk-17.jdk
```
#### 3. Configure Environment Variables:
Open your shell profile (usually ~/.zshrc or ~/.bash_profile):
Bash
nano ~/.zshrc
Add the following lines at the bottom of the file:
Bash
# Java Configuration for PySpark
export JAVA_HOME="/opt/homebrew/opt/openjdk@17"
export PATH="$JAVA_HOME/bin:$PATH"
Save and exit (Ctrl+O, Enter, Ctrl+X).
Apply Changes:
Bash
source ~/.zshrc
Option 2: Manual Installation (No Homebrew)
Download the macOS x64 or aarch64 (M1/M2/M3) DMG installer from Adoptium (Temurin 17).
Run the installer packages.
Open ~/.zshrc and point your JAVA_HOME to the standard installation path:
Bash
export JAVA_HOME="/Library/Java/JavaVirtualMachines/temurin-17.jdk/Contents/Home"
export PATH="$JAVA_HOME/bin:$PATH"
🪟 Windows Installation
Step 1: Download and Install the JDK
Download the Windows x64 Installer executable (.exe) for Adoptium Temurin 17.
Run the installer.
Crucial: During installation, ensure you check the option "Set JAVA_HOME environment variable" if prompted. If not, follow the manual steps below.
By default, it will install to C:\Program Files\Eclipse Foundation\jdk-17.x.x... or C:\Program Files\Java\jdk-17. Note this path down.
Step 2: Configure Environment Variables
Press the Windows Key, search for "Edit the system environment variables", and press Enter.
In the System Properties window, click the Environment Variables... button at the bottom right.
Under System variables (the bottom section), click New...:
Variable name: JAVA_HOME
Variable value: C:\Program Files\Eclipse Foundation\jdk-17.x.x (Replace this with your actual path to the JDK folder, ensuring there is no \bin at the end).
Scroll through the System variables list, find the Path variable, and click Edit....
Click New on the right side and add:
Plaintext
%JAVA_HOME%\bin
Click OK on all windows to save and exit.
Step 3: Windows Bonus Requirement (winutils.exe)
Hadoop requires a native Windows library to initialize filesystems correctly. Without it, PySpark will throw errors on Windows.
Download winutils.exe and hadoop.dll for your Spark version (Hadoop 3.x is standard) from a trusted source like jaceklaskowski/spark-workshop or cdarlint/winutils.
Create a folder structure on your PC, for example: C:\hadoop\bin.
Place winutils.exe and hadoop.dll inside that bin folder.
Add a new System Variable named HADOOP_HOME with the value C:\hadoop.
🧪 Verification
To verify that Java is correctly configured, open a brand new terminal session (Command Prompt/PowerShell on Windows, or zsh on Mac) and run:
Bash
java -version
Expected Output:
Plaintext
openjdk version "17.0.x" ...
OpenJDK Runtime Environment ...
OpenJDK 64-Bit Server VM ...
🛠️ Common Local PySpark Warnings Fix
When running PySpark locally, you might see several noisy WARN lines. You can clear them up programmatically by adding this template configuration to the very top of your Python scripts:
Python
import os
import sys

# 1. Silences the macOS local loopback lookup warning
os.environ["SPARK_LOCAL_IP"] = "127.0.0.1"

from pyspark.sql import SparkSession

# 2. Initialize Spark Session
spark = SparkSession.builder \
    .appName("CapstoneProject") \
    .master("local[*]") \
    .getOrCreate()

# 3. Suppress verbose Hadoop library load warnings 
spark.sparkContext.setLogLevel("ERROR")

print("✨ PySpark initialized cleanly and successfully!")