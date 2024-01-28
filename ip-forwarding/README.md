# Create the docker image
`docker build -t net-debian .`

# Create the networks
`docker network create --driver=bridge --subnet=172.21.0.0/16 --gateway=172.21.0.1 n1`

`docker network create --driver=bridge --subnet=172.22.0.0/16 --gateway=172.22.0.1 n2`

# Create the router container
docker run --name tmp_router -h tmp_router \
--cap-add=NET_ADMIN --cap-add=SYS_MODULE \
--sysctl net.ipv4.conf.all.forwarding=1 --sysctl net.ipv4.tcp_timestamps=0 \
--network n1 --ip 172.21.0.20 \
-it debian

## On the host machine, attach the router to the second network:
`docker network connect n2 tmp_router --ip 172.22.0.20`

# Create s1 container and attach it the network n1
docker run \
--name tmp_box21 -h tmp_box21 \
--cap-add=NET_ADMIN \
--net tmp_21 --ip 172.21.0.21 \
-dit debian

# Create s2 container and attach it the network n2
docker run \
--name tmp_box22 -h tmp_box22 \
--cap-add=NET_ADMIN \
--net tmp_22 --ip 172.22.0.22 \
-it debian

# Add route from s1 to s2 via the router
`docker exec s1 /bin/bash -c "ip route add 172.22.0.0/16 via 172.21.0.20"`

# Add route from s2 to s3 via the router
`docker exec s2 /bin/bash -c "ip route add 172.21.0.0/16 via 172.22.0.20"`