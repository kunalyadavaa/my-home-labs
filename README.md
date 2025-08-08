
![image](https://github.com/user-attachments/assets/a1f84a31-640c-4da0-9d0e-6762a92dcf98)

*Step 1*

Proxmox Cloud init
Proxmox Virtual Machine automation in Terraform
Build Ubuntu Cloud-init Template
1. Download Cloud Image
wget <https://cloud-images.ubuntu.com/jammy/current/jammy-server-cloudimg-amd64.img>

​
2. Create VM and Configure
Replace <VM-ID> with your VM ID (e.g., 100), <username>, <password>, and <path/to/public-key> with your values.
"# Create VM
qm create <VM-ID> --name ubuntu-cloud --cpu host --memory 2048 --cores 2 --net0 virtio,bridge=vmbr0

# Import and attach disk
qm importdisk <VM-ID> jammy-server-cloudimg-amd64.img local-lvm
qm set <VM-ID> --scsihw virtio-scsi-pci --scsi0 local-lvm:vm-<VM-ID>-disk-0

# Configure boot order
qm set <VM-ID> --boot c --bootdisk scsi0

# Add Cloud-Init drive
qm set <VM-ID> --ide2 local-lvm:cloudinit

# Set Cloud-Init settings
qm set <VM-ID> --ciuser <username> --cipassword "<password>" --sshkeys <path/to/public-key> --ipconfig0 ip=dhcp

# Enable QEMU Guest Agent and serial port
qm set <VM-ID> --agent 1 --serial0 socket --vga serial0

# Resize disk (optional)
qm resize <VM-ID> scsi0 20G

# Start VM
qm start <VM-ID>

​
4. Convert VM to Template
qm template <VM-ID>

​
5. Clone Template to Create New VMs
Replace <NEW-VM-ID> with a new ID (e.g., 101):
# Clone the template
qm clone <VM-ID> <NEW-VM-ID> --name ubuntu-clone --full

# Configure Cloud-Init for the new VM
qm set <NEW-VM-ID> --ciuser <username> --cipassword "<password>" --sshkeys <path/to/public-key> --ipconfig0 ip=dhcp

# Start the new VM
qm start <NEW-VM-ID>cd


​
Notes:
Replace all <placeholders> with your values.
For Windows VMs, use virtio-win drivers and skip Cloud-Init steps.
The template will retain QEMU Guest Agent and serial settings. Cloned VMs will need fresh Cloud-Init data (username, password, SSH keys).



**CLI**
```bash

terraform init 
terraform plan
terraform apply -auto-approve

```

**Cloudinit - Image's** 
https://cloud-init.io/
https://alpinelinux.org/cloud/
https://wiki.alpinelinux.org/wiki/K8s_with_cloud-init
https://pve.proxmox.com/wiki/Cloud-Init_Support
https://cloud-images.ubuntu.com/
https://github.com/canonical/cloud-init/tree/main/doc/examples

