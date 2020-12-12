# Minecraft Server on Linux

Configuration files for a basic Minecraft Server on Linux.

## Hosting

Use VPS with 1 CPU and 2 GB (PL) or 2 CPU and 4 GB (FR) from https://github.com/lvlup-pro folks.

## Security

Follow [Jak zabezpieczyć VPSa przed włamaniami botów na SSH](https://forum.lvlup.pro/t/jak-zabezpieczyc-vpsa-przed-wlamaniami-botow-na-ssh/96) (in Polish) to implement bare minimum of the server security:
  - Install fail2ban
  - Remove smbd
  - Remove Apache and other unused services
  - Allow SSH with key-based authentication only
  - Change SSH port
  - Configure Ubuntu automatic updates
  - ...

## Installation

```
sudo apt update
sudo apt upgrade
```

### Install Java

```
sudo apt install openjdk-8-jre-headless screen wget
```

### Create Minecraft User
```
sudo useradd --system --create-home --home /opt/minecraft --shell /bin/bash minecraft
sudo addgroup --system minecraft
sudo adduser minecraft minecraft
sudo chown -R minecraft.minecraft /opt/minecraft
```

### Install Minecraft Server (option without mods)

- 1.12.2: `wget -O /opt/minecraft/server.jar https://launcher.mojang.com/v1/objects/886945bfb2b978778c3a0288fd7fab09d315b25f/server.jar`
- 1.16.4: `wget -O /opt/minecraft/server.jar https://launcher.mojang.com/v1/objects/35139deedbd5182953cf1caa23835da59ca3d7cd/server.jar`

### Install Minecraft Forge Server (option with mods)

- `wget https://files.minecraftforge.net/maven/net/minecraftforge/forge/1.12.2-14.23.5.2854/forge-1.12.2-14.23.5.2854-installer.jar`
- `wget https://files.minecraftforge.net/maven/net/minecraftforge/forge/1.16.4-35.1.13/forge-1.16.4-35.1.13-installer.jar`

```
java -jar forge-X.Y.Z-A.B.C-installer.jar --installServer
rm forge*installer
mv minecraft_server.X.Y.Z.jar server.jar
```

### Install Systemd Service

```
wget -O /opt/minecraft/minecraft.service https://raw.githubusercontent.com/mloskot/minecraft-server-config/main/systemd/minecraft.service
sudo ln -s /opt/minecraft/minecraft.service /etc/systemd/system/minecraft.service
sudo systemctl enable minecraft.service
```

### Start

```
sudo bash -c "echo eula=true > /opt/minecraft/eula.txt"
```

```
sudo systemctl start minecraft.service
sudo systemctl status minecraft.service
sudo journalctl -f -u minecraft.service
```

### Inspect

Attach to Minecraft screen to interact with the server console and view logs:

```
sudo -u minecraft screen -r minecraft
```


#### Install Mods

Go to https://www.curseforge.com/minecraft/mc-mods and download mods for Minecraft 1.12.2 or 1.16.4, then deploy the JAR files in `/opt/minecraft/mods`:

```
$ ls -1 /opt/minecraft/mods/
Xaeros_Minimap_20.29.0_Forge_1.12.jar
'[1.12.2]+SecurityCraft+v1.8.20.2.jar'
buildcraft-all-7.99.24.7.jar
malisiscore-1.12.2-6.5.1.jar
malisisdoors-1.12.2-7.3.0.jar
worldedit-forge-mc1.12.2-6.1.10-dist.jar
```

#### Configure Users

- Edit `server.properties`

  ```
  enforce-whitelist=true
  white-list=true
  ```

- `ops.json`

  ```
  [
    {
      "uuid": "facf3075-d7f9-49db-91e3-80469f0cf04d",
      "name": "mloskot",
      "level": 2,
      "bypassesPlayerLimit": false
    }
  ]
  ```

- `whitelist.json`

  ```
  [
    {
      "uuid": "facf3075-d7f9-49db-91e3-80469f0cf04d",
      "name": "mloskot",
    }
  ]
  ```

### Restart Minecraft Server

```
sudo systemctl restart minecraft.service
```
