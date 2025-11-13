# Ouster Configuration for Multi-Payload Deployment

This directory contains configuration files for the Ouster LiDAR driver across multiple payload systems.

## Configuration Files

- `driver_params.yaml` - Default/template configuration with all parameters
- `driver_params_payload0.yaml` - Configuration for payload0 (sensor: 169.254.68.17)
- `driver_params_payload1.yaml` - Configuration for payload1 (sensor: 192.168.1.11)
- `driver_params_payload2.yaml` - Configuration for payload2 (sensor: 169.254.68.17)
- `driver_params_payload3.yaml` - Configuration for payload3 (sensor: 169.254.18.107, udp_dest: 169.254.18.10)
- `driver_params_payload4.yaml` - Configuration for payload4 (sensor: 169.254.253.236)

**Note:** Each payload-specific file contains the complete configuration. The files are kept in sync with only the `sensor_hostname` and `udp_dest` parameters differing between payloads.

## How It Works

The `global_launch.py` file automatically selects the correct configuration file based on the system hostname:

```python
# In global_launch.py
launch_arguments={
    'params_file': os.path.join(
        get_package_share_directory("ouster_ros"),
        "config",
        f"driver_params_{COMPUTER_HOSTNAME}.yaml"
    )
}.items()
```

When the system boots, it reads the hostname (payload0-4) and loads the corresponding configuration file, ensuring each payload uses the correct Ouster sensor IP address.

## Benefits

- **No uncommitted changes**: Each payload can use the same codebase without local modifications
- **Easy to maintain**: All sensor configurations are explicitly defined in version control
- **Clear documentation**: The mapping between payloads and sensor IPs is visible in the config files
- **Scalable**: Adding new payloads just requires creating a new config file

## Adding a New Payload

1. Copy one of the existing `driver_params_payloadX.yaml` files
2. Rename it to match the new hostname (e.g., `driver_params_payload5.yaml`)
3. Update the `sensor_hostname` and `udp_dest` values as needed
4. Add the hostname to `HOSTNAME_TO_DOMAIN_ID` in `global_launch.py`
5. Commit the new file to version control

## Hostname to IP Mapping

| Hostname | Sensor IP       | UDP Destination | Notes                    |
|----------|-----------------|-----------------|--------------------------|
| payload0 | 169.254.68.17   | -               |                          |
| payload1 | 192.168.1.11    | -               |                          |
| payload2 | 169.254.68.17   | -               |                          |
| payload3 | 169.254.18.107  | 169.254.18.10   | Uses custom UDP dest     |
| payload4 | 169.254.253.236 | -               |                          |
