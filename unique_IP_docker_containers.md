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
docker network create -d macvlan \
  --subnet=192.168.56.0/24 \
  --gateway=192.168.56.1 \
  -o parent=bridge100 \
  plc-macvlan

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
