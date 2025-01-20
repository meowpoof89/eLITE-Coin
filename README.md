# OmniLite and ElectrumX Setup Commands

This document provides a comprehensive list of commands for managing, debugging, and checking logs for your **OmniLite** and **ElectrumX** setup. Use this as a reference guide.

---

## **1. OmniLite Commands**

### **Starting and Stopping OmniLite**
- Start OmniLite in the background:
  ```bash
  omnilited -daemon
  ```

- Stop OmniLite:
  ```bash
  omnilite-cli stop
  ```

- Restart OmniLite:
  ```bash
  omnilite-cli stop && omnilited -daemon
  ```

### **Checking OmniLite Status**
- Get general blockchain information:
  ```bash
  omnilite-cli getblockchaininfo
  ```

- Check synchronization progress:
  ```bash
  omnilite-cli getblockchaininfo | grep verificationprogress
  ```

- View network information:
  ```bash
  omnilite-cli getnetworkinfo
  ```

- Check current block height:
  ```bash
  omnilite-cli getblockcount
  ```

- Get the hash of the genesis block:
  ```bash
  omnilite-cli getblockhash 0
  ```

### **OmniLayer-Specific Commands**
- Get OmniLayer information:
  ```bash
  omnilite-cli omni_getinfo
  ```

- List OmniLayer balances for all addresses:
  ```bash
  omnilite-cli omni_getallbalancesforid <property_id>
  ```

- Send an OmniLayer token:
  ```bash
  omnilite-cli omni_send <from_address> <to_address> <property_id> <amount>
  ```

### **Creating and Minting New Tokens**
#### **Step 1: Create a Fixed Supply Token**
- Create a fixed supply token:
  ```bash
  omnilite-cli omni_sendissuancefixed <from_address> <ecosystem> <type> <previous_id> <category> <subcategory> <name> <url> <data> <amount>
  ```
  Example:
  ```bash
  omnilite-cli omni_sendissuancefixed "mgDuVRFjjJRsV8VBL7VkW3n7beUDqxHt5p" 1 1 0 "Utilities" "Energy" "MyToken" "http://example.com" "Token description" "1000"
  ```
  - `<ecosystem>`: `1` for main ecosystem, `2` for test ecosystem.
  - `<type>`: `1` for indivisible tokens, `2` for divisible tokens.

#### **Step 2: Create a Managed Token**
- Create a managed token (allows future minting):
  ```bash
  omnilite-cli omni_sendissuancemanaged <from_address> <ecosystem> <type> <previous_id> <category> <subcategory> <name> <url> <data>
  ```
  Example:
  ```bash
  omnilite-cli omni_sendissuancemanaged "mgDuVRFjjJRsV8VBL7VkW3n7beUDqxHt5p" 1 1 0 "Finance" "Savings" "ManagedToken" "http://example.com" "Managed token description"
  ```

#### **Step 3: Mint More Tokens (Managed Only)**
- Mint additional tokens for a managed property:
  ```bash
  omnilite-cli omni_sendgrant <from_address> <to_address> <property_id> <amount>
  ```
  Example:
  ```bash
  omnilite-cli omni_sendgrant "mgDuVRFjjJRsV8VBL7VkW3n7beUDqxHt5p" "mfWxJ45yp2SFn7UciZyNpvDKrzbhyfKrY8" 2147483651 "500"
  ```

#### **Step 4: Revoke Tokens (Managed Only)**
- Revoke tokens from circulation:
  ```bash
  omnilite-cli omni_sendrevoke <from_address> <property_id> <amount>
  ```
  Example:
  ```bash
  omnilite-cli omni_sendrevoke "mgDuVRFjjJRsV8VBL7VkW3n7beUDqxHt5p" 2147483651 "100"
  ```

### **Checking OmniLite Logs**
- View the latest log entries:
  ```bash
  tail -f ~/.litecoin/debug.log
  ```

- Search for specific errors in the logs:
  ```bash
  grep "ERROR" ~/.litecoin/debug.log
  ```

---

## **2. ElectrumX Commands**

### **Starting and Stopping ElectrumX**
- Start ElectrumX in the foreground:
  ```bash
  python3 /root/electrumx/electrumx_server.py --conf /root/electrumx/electrumx.conf
  ```

- Start ElectrumX in the background:
  ```bash
  nohup python3 /root/electrumx/electrumx_server.py --conf /root/electrumx/electrumx.conf >> /root/electrumx/electrumx.log 2>&1 &
  ```

- Stop ElectrumX (find and kill the process):
  ```bash
  pkill -f electrumx_server.py
  ```

### **Checking ElectrumX Logs**
- View real-time logs:
  ```bash
  tail -f /root/electrumx/electrumx.log
  ```

- Check log size and clear if needed:
  ```bash
  du -h /root/electrumx/electrumx.log
  truncate -s 0 /root/electrumx/electrumx.log
  ```

### **ElectrumX Database Management**
- Rebuild the database (if needed):
  ```bash
  rm -rf ~/.litecoin/electrumx_db && python3 /root/electrumx/electrumx_server.py --conf /root/electrumx/electrumx.conf
  ```

- Check database directory size:
  ```bash
  du -sh ~/.litecoin/electrumx_db
  ```

---

## **3. System Monitoring and Maintenance**

### **Check System Resource Usage**
- Monitor CPU and memory usage:
  ```bash
  top
  ```

- Check disk usage:
  ```bash
  df -h
  ```

- Analyze ElectrumX memory usage:
  ```bash
  ps aux | grep electrumx
  ```

### **Setup Cron Job for Cleanup**
- Add a cron job to run `litecoin_cleanup.sh` every hour:
  ```bash
  crontab -e
  ```
  Add the following line:
  ```cron
  0 * * * * /bin/bash ~/litecoin_cleanup.sh >> ~/litecoin_cleanup.log 2>&1
  ```

### **Run the Cleanup Script Manually**
- Execute the cleanup script:
  ```bash
  bash ~/litecoin_cleanup.sh
  ```

---

## **4. Troubleshooting**

### **Check if OmniLite is Running**
- Verify the process is running:
  ```bash
  ps aux | grep omnilited
  ```

### **Check if ElectrumX is Running**
- Verify the process is running:
  ```bash
  ps aux | grep electrumx_server.py
  ```

### **Common Debugging Commands**
- Restart OmniLite and ElectrumX:
  ```bash
  omnilite-cli stop && omnilited -daemon
  pkill -f electrumx_server.py && nohup python3 /root/electrumx/electrumx_server.py --conf /root/electrumx/electrumx.conf >> /root/electrumx/electrumx.log 2>&1 &
  ```

- Check network connectivity:
  ```bash
  ping -c 4 google.com
  ```

- Test RPC connection to OmniLite:
  ```bash
  curl --user yourrpcuser:yourrpcpassword --data-binary '{"jsonrpc": "1.0", "id": "curltest", "method": "getblockchaininfo", "params": []}' -H 'content-type: text/plain;' http://127.0.0.1:9332/
  ```

---

## **5. Notes**
- Replace `<property_id>` and placeholder values with actual values for your setup.
- Always monitor log files to troubleshoot unexpected issues.

---

Keep this file updated with additional commands or changes to your setup!
