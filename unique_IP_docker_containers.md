1. ✅ Step 1: Build your image

docker build -t openplc-docker .

2. ✅ Step 2: Create macvlan network (not eth0 for my case as I have my IEDs communicating with MacBook using the bridge100 interface)
(Replace eth0 with the actual NIC of your host machine, e.g. en0 on macOS/Linux.)
docker network create -d macvlan \
  --subnet=192.168.1.0/24 \
  --gateway=192.168.1.1 \
  -o parent=eth0 \
  plc_macvlan

This makes a special Docker network where each container looks like a separate device on your LAN (just like your VMs).

**Step 2: Create the MacVLAN network on bridge100**

```
docker network create -d macvlan \
  --subnet=192.168.56.0/24 \
  --gateway=192.168.56.1 \
  -o parent=bridge100 \
  plc-macvlan
```

- single line command:
  ```

4. ✅ Step 3: Run containers with static IPs
Here’s where the difference comes in:
On a bridge network, you must use -p host_port:container_port because containers are hidden behind the host, and ports must be forwarded.
On a macvlan network, the container already has its own IP on the LAN → so you don’t need -p at all.
Instead, you access OpenPLC’s web interface directly at the container’s IP and port 8080, and Modbus TCP directly at port 502.
Example:
docker run --name plc1 -d \
  --net=plc_macvlan --ip=192.168.1.50 \
  -v "/Users/sreedevirajavelu/Desktop/POWER PLANT TWIN/ST file (for reference)/EG_PLC.st":/home/openplc/scripts/EG_PLC.st \
  openplc-docker
Now:
OpenPLC web UI → http://192.168.1.50:8080
Modbus TCP → 192.168.1.50:502
If you run a second PLC:
docker run --name plc2 -d \
  --net=plc_macvlan --ip=192.168.1.51 \
  -v "/Users/sreedevirajavelu/Desktop/POWER PLANT TWIN/ST file (for reference)/GT_PLC.st":/home/openplc/scripts/GT_PLC.st \
  openplc-docker
It will show up as:
OpenPLC web UI → http://192.168.1.51:8080
Modbus TCP → 192.168.1.51:502
⚠️ Important note about -p

✅ Use -p only if you’re on the default bridge network.

❌ Don’t use -p with macvlan → it can cause conflicts since the container already has its own IP.
👉 So your procedure is correct — just drop the -p when using macvlan.
Do you want me to also show you a docker-compose.yml setup where you can define all 4 PLCs (EG_PLC, GT_PLC, HRSG_PLC, ST_PLC) at once, each with its own IP? That way you don’t need to run docker run manually each time.



Yes — the commands you listed create a MacVLAN interface on the Ubuntu VM that lets the VM itself act like a device on the same subnet as the MacVLAN containers.
Without this, MacVLAN containers cannot communicate with their parent host (the Ubuntu VM) due to how MacVLAN works.
After adding macvlan0 with a host IP (192.168.56.10), your VM can access the PLC containers using their MacVLAN IPs.
However, there’s one important point about your docker run command:
docker run -d --name plc1 --net plc-macvlan --ip 192.168.56.100 -p 8081:8080 --privileged openplc:v3
The -p 8081:8080 port mapping is not needed for MacVLAN, because MacVLAN containers already have their own IPs. The host port mapping works only on bridge networks.
You can access the PLC at http://192.168.56.100:8080 from both your MacBook and the Ubuntu VM (using macvlan0) once it’s up.
So, the steps to make your PLCs accessible from both the VM and your MacBook:
## Create the MacVLAN interface on the Ubuntu VM:

```

- sudo ip link add macvlan0 link enp0s9 type macvlan mode bridge
- sudo ip addr add 192.168.56.10/24 dev macvlan0
- sudo ip link set macvlan0 up

```

Pesist the step above using systemd service

🔹 systemd service
What it is: systemd is the init system Ubuntu uses to start services and processes at boot.
What a service does: You can write a custom .service file that tells Ubuntu:
"Run this command (or script) when the machine boots"
"Restart it if it fails"
"Run it after the network is ready"  

- docker network create -d macvlan --subnet=192.168.56.0/24 --gateway=192.168.56.1 -o parent=enp0s9 plc-macvlan

Run the PLC container on the MacVLAN network without -p:

- docker run -d --name plc1 --net plc-macvlan --ip 192.168.56.100 --privileged openplc:v3

Test access:
  From the VM:
  
- ping 192.168.56.100
  
From the VM or MacBook:

- http://192.168.56.100:8080

### Had to add bridged adapter , promiscuous mode set to Allow All

<img width="2352" height="1196" alt="image" src="https://github.com/user-attachments/assets/d85f109c-07e7-48ab-a329-06276cae1ead" />
