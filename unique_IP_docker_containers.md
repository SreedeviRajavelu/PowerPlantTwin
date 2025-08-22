**Step 1: Build your image**
( After following steps in the repository: https://github.com/thiagoralves/OpenPLC_v3.git )

docker build -t openplc-docker .

Preface before step 2:  
- Unlike Linux, macOS network interfaces can’t be directly assigned to macvlan. Macvlan works on Linux because it can create a virtual interface on top of a real NIC. 

- On macOS, Docker Desktop runs in a Linux VM (inside HyperKit/UTM), so it doesn’t have direct access to the Mac’s physical interfaces for macvlan.

Implication:
- You cannot directly use bridge100 as the parent on macOS.
- Macvlan networking for containers with unique LAN IPs is essentially not supported on Docker Desktop for macOS.
You’ll need to either:
1. Use host networking (but that only works for Linux hosts).
2. Use the default bridge network and map ports (-p) uniquely for each PLC container.
3. Run a Linux VM (e.g., Ubuntu VM) on your Mac and create macvlan networks there. The VM handles the macvlan, and Docker containers inside it can get unique LAN IPs.

Since you already have an Ubuntu VM for IEDs, the recommended approach is:

- Run all your PLC containers inside that Ubuntu VM.
- Create a **macvlan network in that VM pointing to the VM’s virtual NIC.**
- Assign each PLC container a unique IP from your LAN range.

**Step 2: Create MacVLAN network**

Inside the Ubuntu VM for IEDs, create a MacVLAN network in that VM and then assign each PLC docker container a unique IP. This Ubuntu VM is on virtualbox on MacBook:

1) Check which network interface of your Ubuntu VM connects to the same network as your IEDs. Check with `ip a` inside the VM.

Single line command for creating Docker MacVLAN network inside Ubuntu VM using enp0s9 as the parent interface and 192.168.56.0/24 as the subnet:

' docker network create -d macvlan --subnet=192.168.56.0/24 --gateway=192.168.56.1 -o parent=enp0s9 plc-macvlan `


This makes a special Docker network where each container looks like a separate device on your LAN (just like your VMs).


- single line command:
 ` docker network create -d macvlan --subnet=192.168.56.0/24 --gateway=192.168.56.1 -o parent=enp0s9 plc-macvlan `


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

sudo ip link add macvlan0 link enp0s9 type macvlan mode bridge
sudo ip addr add 192.168.56.10/24 dev macvlan0
sudo ip link set macvlan0 up

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


## Persisting MacVLAN interface across reboots using systemd service:

1. sudo nano /etc/systemd/system/macvlan0.service

2. Paste this content:
```
   [Unit]
Description=Macvlan0 network interface
After=network-online.target
Wants=network-online.target

[Service]
Type=oneshot
ExecStart=/sbin/ip link add macvlan0 link enp0s9 type macvlan mode bridge
ExecStart=/sbin/ip addr add 192.168.56.10/24 dev macvlan0
ExecStart=/sbin/ip link set macvlan0 up
RemainAfterExit=yes

ExecStop=/sbin/ip link set macvlan0 down
ExecStop=/sbin/ip link delete macvlan0

[Install]
WantedBy=multi-user.target
```

3. Reload systemd and enable the service:
```
sudo systemctl daemon-reload
sudo systemctl enable macvlan0
```

4. Start it immediately (without reboot):
`sudo systemctl start macvlan0`

5. Verify after reboot
`ip a show macvlan0`
 


