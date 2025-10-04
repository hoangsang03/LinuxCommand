This is an excellent idea. Creating a clear, step-by-step document will help prevent this issue from happening again and provide a reliable troubleshooting guide for SSH-tunneled SQL Server connections.

Here is a comprehensive document structured around the diagnosis and solution we found.

-----

# Comprehensive Troubleshooting Guide: SQL Server Connection Refused (Error 1225) via PuTTY SSH Tunnel

## Problem Statement

When attempting to connect to a remote SQL Server instance (running on Linux or any remote host) via a PuTTY Local Port Forwarding (SSH Tunnel), the connection fails immediately with the error:

> **Cannot connect to localhost,11433.**
> **Error: 1225 - The remote computer refused the network connection.**
> **Provider: TCP Provider, error: 0 - The remote computer refused the network connection.**

## Root Cause Summary

This error, when using an SSH tunnel, almost always indicates a failure in the **final step** of the connection path, even if the SSH tunnel itself is established. The two key failure points are:

1.  **Port Mismatch (Solved in initial diagnosis):** The PuTTY Destination port does not match the SQL Server's actual listening port (default `1433`).
2.  **Client Protocol Mismatch (The Final Fix):** The Windows SQL client (e.g., SSMS, Azure Data Studio) is attempting to use the wrong network protocol (like Named Pipes) or the wrong loopback IP (like IPv6 `::1`) when connecting to `localhost`, which fails immediately over the tunnel.

## Step-by-Step Troubleshooting Document

### Phase 1: Verify Remote SQL Server Status (Linux Host)

The "Connection Refused" error means the remote service is unreachable on the target port.

1.  **Verify SQL Server Service is Running:**

      * Log into your Linux server via the PuTTY terminal.
      * Check the service status:
        ```bash
        sudo systemctl status mssql-server
        ```
      * **Result must be:** `Active: active (running)`

2.  **Verify Listening Address and Port (Crucial Check):**

      * Run the network utility command to see what addresses the SQL Server process is using:
        ```bash
        sudo netstat -tunpl | grep sqlservr
        ```
      * **Expected Output Analysis:** Look for the line indicating the main TCP listener. The default port is `1433`.
        ```
        tcp   0   0 0.0.0.0:1433  ... LISTEN ... /sqlservr
        ```
      * **Key takeaway:** The SQL Server is listening on **port 1433**.

3.  **Test Local Loopback Connectivity (Final Server Test):**

      * Test the direct connection that the SSH tunnel will attempt to make (the destination `127.0.0.1:1433`):
        ```bash
        # You may need to install 'telnet': sudo apt install telnet
        telnet 127.0.0.1 1433
        ```
      * **Expected Success:** You should see a message like `Connected to 127.0.0.1` (followed by gibberish).
      * **If this step fails with "Connection Refused":** The problem is a **local firewall (iptables/ufw)** on the Linux server blocking loopback traffic on port 1433. You must temporarily disable the firewall or add an exception to proceed.

### Phase 2: Verify PuTTY Configuration

1.  **Open PuTTY** and load your saved session.
2.  Navigate to **Connection** $\rightarrow$ **SSH** $\rightarrow$ **Tunnels**.
3.  Confirm the following entry is set up correctly for **Local** forwarding:
      * **Source Port:** `11433` (This is the arbitrary port on your local Windows machine).
      * **Destination:** **`127.0.0.1:1433`** (This points to the actual SQL Server port on the remote Linux host).
4.  **Check PuTTY Event Log (Post-Connection):**
      * Start the PuTTY session.
      * Right-click the PuTTY title bar and select **Event Log**.
      * **Expected Success:** The log should show the tunnel request without an immediate failure:
        `Local port 11433 forwarding to 127.0.0.1:1433 requested`

### Phase 3: Implement the Client-Side Solution (The Fix)

Since your PuTTY log shows a successful forwarding request, the final refusal is due to the SQL client driver attempting to use an incorrect protocol or IP version.

1.  **Open your SQL Client** (SSMS, Azure Data Studio, etc.).
2.  In the connection window, manually specify the **TCP protocol** and use the **IPv4 loopback address** for the SSH tunnel.

| Client Connection Field | Value | Reason |
| :--- | :--- | :--- |
| **Server Name** | **`tcp:127.0.0.1,11433`** | Forces the SQL client to use TCP/IP and the IPv4 address, which is the only reliable way to send traffic through a standard SSH tunnel. |
| **Authentication** | *(Use your standard SQL or Windows Auth credentials)* | The tunnel only handles transport; authentication is handled by the remote SQL Server. |

By using **`tcp:127.0.0.1,11433`**, you bypass the client's attempt to use protocols like Named Pipes or IPv6 that cannot travel over the SSH port forward, immediately resolving the "Connection Refused" error.