# WSL2 with docker journey

**Goal**  
To set up a docker-desktop like environment, without docker-desktop.

## Steps

* [Network connectivity](#network-connectivity) in WSL2 has to work when connected to corporate VPN.


### Network Connectivity
**Problem**  
WSL2 network is hindered by VPN  

**Solution**  
Use [vpnkit](https://github.com/moby/vpnkit). simple solution is [wsl-vpnkit](https://github.com/sakai135/wsl-vpnkit).
1. Download wsl-vpnkit
```pwsh
# Powershell
iwr "https://github.com/sakai135/wsl-vpnkit/releases/latest" -OutFile "$HOME\wsl-vpnkit.tar.gz"
```
2. Install
```pwsh
# Powershell
wsl --import wsl-vpnkit $env:USERPROFILE\wsl-vpnkit wsl-vpnkit.tar.gz --version 2
```
3. Activate  
Start wsl-vpnkit from your other WSL 2 distros. Add the command to your .profile or .bashrc to start wsl-vpnkit when you open your WSL terminal.
```sh
wsl.exe -d wsl-vpnkit service wsl-vpnkit start
```
