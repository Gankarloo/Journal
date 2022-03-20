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
$githubLatestReleases = "https://api.github.com/repos/sakai135/wsl-vpnkit/releases/latest"
$githubLatestReleasesJson = ((Invoke-WebRequest $gitHubLatestReleases) | ConvertFrom-Json).assets | where { $_.content_type -eq "application/x-gzip"}
iwr $githubLatestReleases.browser_download_url -OutFile $githubLatestReleasesJson.name
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

## install podman in windows

1. download msi installer
```pwsh
# Powershell
$githubLatestReleases = "https://api.github.com/repos/containers/podman/releases/latest"
$githubLatestReleasesJson = ((Invoke-WebRequest $gitHubLatestReleases) | ConvertFrom-Json).assets | where {  $_.name -like "*.msi"}
iwr $githubLatestReleasesJson.browser_download_url -OutFile $githubLatestReleasesJson.name
```

2. install podman
```pwsh
ii $githubLatestReleasesJson.name
```
note. the msi exited silently and it took a while before the installation were completed.
check $programfiles/redhat

## install podman in wsl
### Fedora 
Latest podman is in the 4.0 and has breaking changes compared to v3. 
For Fedora 35 enable a copr repo.

```sh
# Enable repo
sudo dnf copr enable rhcontainerbot/podman4

# install podman
sudo dnf install podman
```

Edit settings. 
```sh
# copy config to ~
cp /usr/share/containers/containers.conf ~/.config/containers/.

# change cgroup_manager to cgroupfs (not systemd)
sed -i 's/#^cgroup_manager.*$/cgroup_manager = "cgroupfs"/' ~/.config/containers/containers.conf
# change events_logger to file (not journald)
sed -i 's/^#events_logger.*$' ~/.config/containers/containers.conf
# change log_driver  to k8s-file (not journald)
sed -i 's/^log_driver.*$/log_driver = "file"/' ~/.config/containers/containers.conf
```

Create run path
```sh
sudo mkdir /run/podman
sudo chown $USERNAME /run/podman
```

verify podman
```sh
podman run -it docker.io/library/alpine:latest
podman run --rm -it hello-world

Start podman socket
```sh
podman system service --time=0 unix:///run/podman/podman.sock&
```

## install ssh server
### Fedora
```sh
sudo dnf install openssh-server
# Generate hostkeys
sudo ssh-keygen -f /etc/ssh/ssh_host_rsa_key     -N '' -t rsa
sudo ssh-keygen -f /etc/ssh/ssh_host_ecdsa_key   -N '' -t ecdsa
sudo ssh-keygen -f /etc/ssh/ssh_host_ed25519_key -N '' -t ed25519

# start ssh server
openssh-server -D&
```
## configure windows podman

