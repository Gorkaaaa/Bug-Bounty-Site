# 👁️ BugHorizon

![thumb\_hu37f88119bb88d50a62cf30a7c95a3554\_114356\_1400x600\_fill\_q75\_box\_smart1](https://github.com/user-attachments/assets/c45691e0-cd6c-4f54-91a3-cf2d1ab5942e)

## **Web Scanning Script Documentation**

### **Overview**

This script automates the process of web scanning using two powerful tools: **Katana** and **Dirsearch**. It processes a list of domains from a file, executes scans, and organizes the results in a clean, structured format for easy analysis.

***

### **Usage**

#### **Command Syntax**

```bash
./BugHorizon.sh
```

#### **Prerequisites**

1. **Install Required Tools**:
   *   **Katana**: Install Katana by following the instructions:

       ```bash
       go install github.com/projectdiscovery/katana/cmd/katana@latest
       export PATH=$PATH:$HOME/go/bin
       source ~/.bashrc
       source ~/.zshrc
       ```
   *   **Dirsearch**: Install Dirsearch using pip:

       ```bash
       pip install dirsearch
       ```
2.  **Create `valid_domains.txt`**:\
    Prepare a file named `valid_domains.txt` with the domains to scan, one per line:

    ```plaintext
    https://example.com
    https://subdomain.example.com
    ```

***

### **Execution Flow**

#### **1. Input Validation**

* The script checks for the existence of `valid_domains.txt`.
* If the file is missing, the script exits with an error message.

#### **2. Results Directory Creation**

* Creates a directory named `web_scan_results` to store all outputs.

#### **3. Scanning with Katana**

* **Tool**: Katana
* **Action**: Performs URL discovery for each domain.
* **Output**: Results are saved in `$DIRECTORIO/[domain].katana.txt`.
* **Highlights**: The top 10 results are displayed in the terminal.

#### **4. Scanning with Dirsearch**

* **Tool**: Dirsearch
* **Action**: Performs directory brute-forcing for each domain.
* **Output**: Results are saved in `$DIRECTORIO/[domain].dirsearch.txt`.
* **Highlights**: Displays the top 10 HTTP responses (status codes: 200, 301, 302).

#### **5. Process Domains**

* Each domain in `valid_domains.txt` is processed in sequence.
* Both tools are executed for each domain.
* If no results are found, the script notifies the user.

#### **6. Final Results**

* All results are stored in the `web_scan_results` directory.
* Key findings are summarized in the terminal.

***

### **Example Usage**

#### **1. Prepare the Domains File**

Create a file named `valid_domains.txt` with the following content:

```plaintext
https://example.com
https://subdomain.example.com
```

<figure><img src="../.gitbook/assets/404580058-56164ac4-20e8-4b64-8051-4bbcbd094b60.png" alt=""><figcaption></figcaption></figure>

#### **2. Run the Script**

Execute the script:

```bash
./BugHorizon.sh
```

#### **3. Sample Output**

Terminal output will look like this:

```plaintext
[+] Processing domains from 'valid_domains.txt'...
[+] Processing domain: https://example.com
    [+] Running Katana on https://example.com...
        Katana found results for https://example.com:
        https://example.com/page1
        https://example.com/page2
    [+] Running Dirsearch on https://example.com...
        Dirsearch found results for https://example.com:
        200 - https://example.com/admin
        301 - https://example.com/login
[+] Processing domain: https://subdomain.example.com
    [+] Running Katana on https://subdomain.example.com...
        No results found for https://subdomain.example.com.
    [+] Running Dirsearch on https://subdomain.example.com...
        Dirsearch found results for https://subdomain.example.com:
        302 - https://subdomain.example.com/dashboard
[+] Scanning completed. Results are located in 'web_scan_results'.
```

***

### **Benefits**

* **Automation**: Combines multiple tools for efficient web scanning.
* **Ease of Use**: Handles file structure and result organization automatically.
* **Actionable Insights**: Displays key findings directly in the terminal while saving detailed results.

***

### **Next Steps**

* Extend the script by integrating additional tools like `httpx` for HTTP probing.
* Modify the output format for compatibility with other workflows or tools.



{% embed url="https://github.com/Gorkaaaa/BugHorizon.git" %}
