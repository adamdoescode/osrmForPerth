# OSRM docker quick and dirty instructions



Next steps:
1. map of perth pbf
2. make work in docker img
3. see if can get osrm dirs

Okay... so can I repeat this for Perth data?

Steps:
1. get perth data from ~~geofabrik~~ bbbike: https://download.bbbike.org/
2. extract and run

docker run -t -v "${PWD}:/data" \
    ghcr.io/project-osrm/osrm-backend osrm-extract \
    -p /opt/car.lua /data/perth.osm.pbf
#Process the osrm files:
docker run -t -v "${PWD}:/data" \
    ghcr.io/project-osrm/osrm-backend osrm-partition /data/perth.osrm
docker run -t -v "${PWD}:/data" \
    ghcr.io/project-osrm/osrm-backend osrm-customize /data/perth.osrm
#Start the docker img:
docker run -t -i -p 5000:5000 \
    -v "${PWD}:/data" \
    ghcr.io/project-osrm/osrm-backend osrm-routed \
        --algorithm mld /data/perth.osrm

A dud call:
`curl "http://127.0.0.1:5000/route/v1/driving/13.388860,52.517037;13.385983,52.496891?steps=true"`

```
placeA = (-31.9411869,116.0051333)
placeB = (-31.9552672,115.8264304)
print(
    f'curl "http://127.0.0.1:5000/route/v1/driving/',
    f'{placeA[1]},{placeA[0]};{placeB[1]},{placeB[0]}?steps=true"',
    sep=''
)
```


placeA:-31.9411869,116.0051333
placeB: -31.9552672,115.8264304
`curl "http://127.0.0.1:5000/route/v1/driving/115.8264304,-31.9552672;115.8264304,-31.9552672?steps=true"`
