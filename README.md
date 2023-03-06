# OSRM docker quick and dirty instructions

Data was extracted thanks to the fantastic service offered by [BBBike.org](https://extract.bbbike.org/)

Modified from https://hub.docker.com/r/osrm/osrm-backend/

Note, on MacOS port 5000 isn't available.

[Solution to disable the AirPlay service.](https://stackoverflow.com/questions/69818376/localhost5000-unavailable-in-macos-v12-monterey)

What's inside:
- map data: `perth.osm.pbf`
- this readme `README.md`
- not much else (the dockerfile comes from dockerhub in the first docker command)

1. Extract and run

```
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
```

2. Make a dud call:

`curl "http://127.0.0.1:5000/route/v1/driving/13.388860,52.517037;13.385983,52.496891?steps=true"`

3. Make a real call:

Below is some very simple python code to generate the curl command used below:
```
placeA = (-31.9411869,116.0051333)
placeB = (-31.9552672,115.8264304)
print(
    f'curl "http://127.0.0.1:5000/route/v1/driving/',
    f'{placeA[1]},{placeA[0]};{placeB[1]},{placeB[0]}?steps=true"',
    sep=''
)
```

This curl command should return valid directions through Perth:

`curl "http://127.0.0.1:5000/route/v1/driving/115.8264304,-31.9552672;115.8264304,-31.9552672?steps=true"`


