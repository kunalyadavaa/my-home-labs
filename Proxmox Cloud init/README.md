# Proxmox Cloud init

Proxmox Virtual Machine automation in Terraform
Build Ubuntu Cloud-init Template

1. Download Cloud Image
   wget <https://cloud-images.ubuntu.com/jammy/current/jammy-server-cloudimg-amd64.img>

​ 2. Create VM and Configure
Replace 101 with your VM ID (e.g., 100), <username>, <password>, and <path/to/public-key> with your values.
"# Create VM
qm create 101 --name ubuntu-cloud --cpu host --memory 2048 --cores 2 --net0 virtio,bridge=vmbr0

# Import and attach disk

qm importdisk 101 jammy-server-cloudimg-amd64.img local-lvm
qm set 101 --scsihw virtio-scsi-pci --scsi0 local-lvm:vm-101-disk-0

# Configure boot order

qm set 101 --boot c --bootdisk scsi0

# Add Cloud-Init drive

qm set 101 --ide2 local-lvm:cloudinit

# Set Cloud-Init settings

qm set 101 --ciuser <username> --cipassword "<password>" --sshkeys <path/to/public-key> --ipconfig0 ip=dhcp

# Enable QEMU Guest Agent and serial port

qm set 101 --agent 1 --serial0 socket --vga serial0

# Resize disk (optional)

qm resize 101 scsi0 20G

# Start VM

qm start 101

​ 4. Convert VM to Template
qm template 101

​ 5. Clone Template to Create New VMs
Replace <NEW-VM-ID> with a new ID (e.g., 101):

# Clone the template

qm clone 101 <NEW-VM-ID> --name ubuntu-clone --full

# Configure Cloud-Init for the new VM

qm set <NEW-VM-ID> --ciuser <username> --cipassword "<password>" --sshkeys <path/to/public-key> --ipconfig0 ip=dhcp

# Start the new VM

qm start <NEW-VM-ID>cd

​
Notes:
Replace all <placeholders> with your values.
For Windows VMs, use virtio-win drivers and skip Cloud-Init steps.
The template will retain QEMU Guest Agent and serial settings. Cloned VMs will need fresh Cloud-Init data (username, password, SSH keys).
