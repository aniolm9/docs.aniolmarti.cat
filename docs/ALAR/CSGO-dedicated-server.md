# Counter Strike: Global Offensive dedicated server

In order to install and configure a CSGO server you need SteamCMD.

## Installing SteamCMD
```bash
sudo -i
apt install lib32gcc1 libstdc++6 lib32stdc++6
adduser --system steam
mkdir -p /srv/games
chown -R steam:root /srv/games
su --shell /bin/bash steam
cd ~
wget https://steamcdn-a.akamaihd.net/client/installer/steamcmd_linux.tar.gz
tar xvzf steamcmd_linux.tar.gz
chmod +x steamcmd.sh
./steamcmd.sh
```

Now we'll have to wait until `Steam>` appears. 

## Downloading the CSGO server

It's about 10GB of data, so if your connection is not fast, good luck.

**In SteamCMD:**
```
login anonymous
force_install_dir /srv/games/csgo_1
app_update 740 validate
```

The number 740 stands for the CSGO server. You can see all the available applications [here](https://developer.valvesoftware.com/wiki/Steam_Application_IDs).  
When it finishes it will print the message `App '740' fully installed`. To exit SteamCMD just type `quit`.

## Booting the server
We will boot the server process using `screen`. If you are physically connected to the server you can just boot it in a tty.

**Using `screen`:**
```bash
apt install screen
script /dev/null
screen -S CSGO1
cd /srv/games/csgo_1
```

**Casual:**
```bash
./srcds_run -game csgo -console -usercon +game_type 0 +game_mode 0 +mapgroup mg_active +map de_dust2
```

**Classic Competitive:**
```bash
./srcds_run -game csgo -console -usercon +game_type 0 +game_mode 1 +mapgroup mg_active +map de_dust2
```

**Arms Race:**
```bash
./srcds_run -game csgo -console -usercon +game_type 1 +game_mode 0 +mapgroup mg_armsrace +map ar_shoots
```

**Demolition:**
```bash
./srcds_run -game csgo -console -usercon +game_type 1 +game_mode 1 +mapgroup mg_demolition +map de_lake
```

**Disable bots:** Add `-nobots`.  
**Change port:** Add `port 27016`.  
**Enable logging:**: Add `-condebug`.

To detach screen type Ctrl+A+D.  
To attach again `screen -r CSGO1`.

## Sources
[Valve Wiki](https://developer.valvesoftware.com/wiki/Counter-Strike:_Global_Offensive_Dedicated_Servers)