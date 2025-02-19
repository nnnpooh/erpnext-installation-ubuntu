# Port Proxy from WSL

- The scenario is that you have ERPNext running on WSL on port 8000, but you want to access ERPNext from other machines in the same network.
- You may achieve this by adding a port proxy that listens on the host port. This proxy connects to the IP address for WSL.

## Steps

1. Setup in WSL

   - `sudo apt install net-tools`

2. Run `port-proxy-wsl.ps1` (in this repo)

## Information

- Host IP
  - `Get-NetIPAddress -AddressFamily IPV4 -InterfaceAlias Ethernet`
- Port availability
  - `netstat -na | Select-String "8000"`
- Proxy
  - `netsh interface portproxy show all`
- Host IP
  - `wsl hostname -I`
- Firewall
  - `Get-NetFirewallRule -DisplayName "8000"`

# References

- https://superuser.com/a/1645641
- https://stackoverflow.com/questions/42110526/why-doesnt-get-netfirewallrule-show-all-information-of-the-firewall-rule
- https://stackoverflow.com/questions/42110526/why-doesnt-get-netfirewallrule-show-all-information-of-the-firewall-rule
- https://www.elevenforum.com/t/windows-defender-firewall-vs-windows-defender-firewall-with-advanced-security.16569/#:~:text=Windows%20Firewall%20with%20Advanced%20Security,they%20are%20system%2Dlevel%20rules.
- https://learn.microsoft.com/en-us/answers/questions/189038/netsh-interface-portproxy-doesnt-work
- https://dev.to/vishnumohanrk/wsl-port-forwarding-2e22
