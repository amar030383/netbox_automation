# NetBox Device Automation

Automate device creation and management in NetBox using Ansible. This playbook collects device serial numbers from Cisco IOS, NX-OS, and Juniper devices and creates corresponding entries in NetBox.

## Features

- Automatic collection of device serial numbers from Cisco IOS, NX-OS, and Juniper devices
- Creation of devices in NetBox with proper device type, role, and site information
- Support for both IOS, NX-OS, and Juniper device types
- Device information pulled from inventory file

## Requirements

- Ansible 2.10 or higher
- Required Ansible collections:
  - `cisco.ios`
  - `cisco.nxos`
  - `netbox.netbox`
- NetBox API access with proper permissions

### Installing Required Collections

To install the required collections, run:

```bash
ansible-galaxy collection install -r requirements.yml
```

This will install all necessary collections for Cisco and Juniper device support.

## Inventory Structure

The inventory file should be structured with the following variables for each device:

```ini
[site_name]
hostname ansible_host=IP_ADDRESS ansible_network_os=OS_TYPE site=SITE_NAME tags=TAGS type=DEVICE_TYPE manufacturer=MANUFACTURER role=ROLE
```

Example:
```ini
[dfc01]
cisco-ios-dfc02-rw1 ansible_host=192.168.85.154 ansible_network_os=cisco.ios.ios site=dfc02 tags=development_line type=router manufacturer=cisco role=distribution

[dfc02]
cisco-ios-dfc02-sw1 ansible_host=192.168.85.161 ansible_network_os=cisco.nxos.nxos site=dfc02 tags=production_line type=switch manufacturer=cisco role=distribution
```

## Environment Variables

The following environment variables are required:

- `NETBOX_TOKEN`: NetBox API token
- `ansible_password`: Device password
- `ansible_user`: Device username
- `ansible_become_pass`: Enable password

### Setting Environment Variables

#### Temporary (for current session):
```bash
export NETBOX_TOKEN="your_token_here"
export ansible_password="your_device_password"
export ansible_user="your_device_username"
export ansible_become_pass="your_enable_password"
```

#### Permanent (for current user):
1. Add to your `~/.bashrc` or `~/.bash_profile`:
```bash
echo 'export NETBOX_IP="your_netbox_ip_here"' >> ~/.bashrc
echo 'export NETBOX_TOKEN="your_token_here"' >> ~/.bashrc
echo 'export ansible_password="your_device_password"' >> ~/.bashrc
echo 'export ansible_user="your_device_username"' >> ~/.bashrc
echo 'export ansible_become_pass="your_enable_password"' >> ~/.bashrc
```

#### For Juniper devices:
```bash
echo 'export JUNIPER_USER="your_juniper_username"' >> ~/.bashrc
echo 'export JUNIPER_PASSWORD="your_juniper_password"' >> ~/.bashrc
```

2. Reload your shell configuration:
```bash
source ~/.bashrc
```

#### Secure Storage
For better security, consider using a secrets management tool like `pass` or Ansible Vault to store sensitive credentials.

## Usage

1. Set up the required environment variables:
```bash
export NETBOX_TOKEN="your_netbox_token"
export ansible_password="your_device_password"
export ansible_user="your_device_username"
export ansible_become_pass="your_enable_password"
```

2. Run the playbook:
```bash
ansible-playbook -i inventory.ini data_collector_netbox.yml
```

## Playbook Structure

The playbook is organized as follows:

```
.
├── data_collector_netbox.yml          # Main playbook
├── inventory.ini                      # Device inventory
├── ios/
│   └── ios_tasks.yml                 # IOS-specific tasks
├── nxos/
│   └── nxos_tasks.yml                # NX-OS-specific tasks
├── tasks/
│   └── netbox/
│       └── create_device.yml         # NetBox device creation task
└── tests/
    └── test_netbox_tasks.yml         # Test playbook for NetBox tasks
```

## Device Information Flow

1. Device-specific tasks (ios_tasks.yml/nxos_tasks.yml):
   - Collect serial number using device-specific facts
   - Store serial number in `device_serial` variable
   - Set device variables from inventory

2. NetBox device creation:
   - Uses collected serial number
   - Creates device with proper type, role, and site information
   - Stores serial number in NetBox device entry

## Error Handling

The playbook includes conditional execution based on device OS type and skips tasks for unsupported devices. It also handles undefined variables gracefully where possible.

## Contributing

Feel free to submit issues and enhancement requests!

## License

MIT License

## Support

If you have questions or need help, please open an issue in the GitHub repository.
