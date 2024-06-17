# Scenario Test

## **Reconnaissance**

### Install Nikto

```bash
sudo apt install nikto -y
```

### Scan Web Server

```bash
sudo nikto -host <VM의 공용 아이피>
```

![Untitled](Scenario%20Test%2040cc55ebafe54c048b50b5db21955188/Untitled.png)

### Scan Web Server with WAF

```bash
sudo nikto -host <애플리케이션 게이트웨이의 공용 IP>
```

![Untitled](Scenario%20Test%2040cc55ebafe54c048b50b5db21955188/Untitled%201.png)

### IP별 일치/차단된 요청

```sql
AzureDiagnostics
| where ResourceProvider == "MICROSOFT.NETWORK" and Category == "ApplicationGatewayFirewallLog"
| summarize count() by clientIp_s, bin(TimeGenerated, 1m)
| render timechart
```

### URI별 일치/차단된 요청

```sql
AzureDiagnostics
| where ResourceProvider == "MICROSOFT.NETWORK" and Category == "ApplicationGatewayFirewallLog"
| summarize count() by requestUri_s, bin(TimeGenerated, 1m)
| render timechart
```

## Port Scan

### Install NMap

```bash
sudo apt install nmap -y
```

### Scan Port

```bash
sudo nmap -O <애플리케이션 게이트웨이의 공용 IP>
```

```sql
// All firewall decisions 
// All decision taken by firewall. Contains hits on network, application and NAT rules, as well as threat intelligence hits and IDPS signature hits. 
AZFWNetworkRule
| union AZFWApplicationRule, AZFWNatRule, AZFWThreatIntel, AZFWIdpsSignature
| order by TimeGenerated desc 
| take 100
```