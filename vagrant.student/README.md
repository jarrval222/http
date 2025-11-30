# 🌐 Web Lab 1: Apache Virtual Hosts Configuration

This document summarizes the steps required to configure and verify name-based Virtual Hosts (`apolo.olimpo.test` and `atenea.olimpo.test`) on the web server `mercurio.olimpo.test`, including troubleshooting common lab setup issues.

---

## 💻 1. Initial Configuration & DNS Troubleshooting

The lab uses two VMs: `ns1.olimpo.test` (DNS, 192.168.57.10) and `mercurio.olimpo.test` (Web Server, 192.168.57.11).

### 🚨 Troubleshooting: Fixing DNS Resolution (Crucial for Installation)

If package installation (`apt install`) fails with **`Temporary failure resolving 'deb.debian.org'`**, your DNS setup is wrong. The machine `mercurio` must use `ns1` as its nameserver.

1.  **Edit the DNS configuration file on `mercurio`:**
    ```bash
    sudo nano /etc/resolv.conf
    ```

2.  **Ensure the content is the following (using the IP of `ns1`):**
    ```
    domain olimpo.test
    search olimpo.test
    nameserver 192.168.57.10
    ```

---

## 🛠️ 2. Apache Installation & Provisioning (on `mercurio`)

### 2.1 Install Apache2

After fixing DNS, update package lists and install Apache:

```bash
sudo apt update
sudo apt install apache2 -y
2.2 Create Document Roots and Custom FilesThe practice requires specific directory structures and custom error messages:Bash# Create document roots and messages directories
sudo mkdir -p /var/www/apolo.olimpo.test/messages
sudo mkdir -p /var/www/atenea.olimpo.test/messages

# Apolo Content (Directory Indexing MUST be allowed)
echo "<h1>Apolo</h1>" | sudo tee /var/www/apolo.olimpo.test/apolo.html
echo "Resource not found on apolo.olimpo.test" | sudo tee /var/www/apolo.olimpo.test/messages/404.html
echo "Contenido de apolo1" | sudo tee /var/www/apolo.olimpo.test/apolo1.txt
echo "Contenido de apolo2" | sudo tee /var/www/apolo.olimpo.test/apolo2.txt

# Atenea Content (Directory Indexing MUST be forbidden)
echo "<h1>Atenea</h1>" | sudo tee /var/www/atenea.olimpo.test/atenea.html
echo "Resource not found on atenea.olimpo.test" | sudo tee /var/www/atenea.olimpo.test/messages/404.html
echo "Contenido de atenea1" | sudo tee /var/www/atenea.olimpo.test/atenea1.txt
echo "Contenido de atenea2" | sudo tee /var/www/atenea.olimpo.test/atenea2.txt
🔗 3. Activating Virtual Hosts (Bypassing a2ensite)Since standard activation commands failed, we manually include the Virtual Host configuration files (.conf) into the main Apache configuration file.Edit the main Apache configuration file:Bashsudo nano /etc/apache2/apache2.conf
Add the following Include directives at the end of the file:ApacheInclude /etc/apache2/sites-available/apolo.olimpo.test.conf
Include /etc/apache2/sites-available/atenea.olimpo.test.conf
Verify Apache syntax and Restart the service:Bashsudo apache2ctl configtest
sudo systemctl restart apache2
✅ 4. Final VerificationThe configuration is verified using the provided weblab-1-en.hurl script.Test SummaryRequirementapolo.olimpo.testatenea.olimpo.testDirectory ListingAllowed (HTTP 200 on /)Forbidden (HTTP 403 on /)404 ErrorCustom page/message usedCustom page/message usedExecutionExecute the script from your client machine to confirm the configuration:Bashhurl weblab-1-en.hurl
The result must show success for all assertions.