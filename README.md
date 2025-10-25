## 1.ssh into instance then download netboot
To begin, elevate to the root user:

```sudo su```
Download the netboot ARM64 image to /boot/efi/netboot.efi:
```
wget https://boot.netboot.xyz/ipxe/netboot.xyz-arm64.efi && \
  sudo install \
    --owner=root \
    --group=root \
    --mode=664 \
    netboot.xyz-arm64.efi \
    /boot/efi/netboot.efi
```    
## 2. run netboot

1.from ssh: `sudo su`

2.then: `reboot`

3.at same time on cloud shell press ESC
pick: Boot Manager > EFI internal Shell

4.press ESC before 5s

5.type: `fs0:netboot.efi`

6.pick:
Distributions > Linux Network Installs (arm64)
NixOS
NixOS nixos-25.05

##  3.repartition
```
sudo su
cd "$(mktemp --directory)"
curl \
  --show-error \
  --fail \
  https://raw.githubusercontent.com/hoangbits/nixos-a1-vm/refs/heads/main/disk-config.nix \
  > disk-config.nix

nix \
  --experimental-features 'nix-command flakes' \
  run \
  github:nix-community/disko/v1.11.0 \
  -- \
  --mode destroy,format,mount \
  disk-config.nix
```
note: type "yes" when prompt appear.  

## 4.install nixos    
```
nixos-generate-config --no-filesystems --root /mnt
mv disk-config.nix /mnt/etc/nixos/
curl \
  --show-error \
  --fail \
  https://raw.githubusercontent.com/hoangbits/nixos-a1-vm/refs/heads/main/configuration.nix \
  > /mnt/etc/nixos/configuration.nix && \
  curl \
    --show-error \
    --fail \
    https://raw.githubusercontent.com/hoangbits/nixos-a1-vm/refs/heads/main/vars.nix \
    > /mnt/etc/nixos/vars.nix

nixos-install --no-root-password
When the install completes, reboot the VM:

shutdown --reboot now    
```

## 5.verfiy
```
nix-shell -p neofetch --command neofetch
```