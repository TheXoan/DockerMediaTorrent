# DockerMediaTorrent
### Configuración de (Jellyseerr - Sonarr - Radarr - Prowlarr - Jacket) sobre Docker

Creación y configuración de servidor MediaTorrent sobre docker

## Jellyseer

docker-compose.yml

<p> 

```docker
version: '3'
services:
    jellyseerr:
       image: fallenbagel/jellyseerr:latest
       container_name: jellyseerr
       environment:
            - LOG_LEVEL=debug
            - TZ=Europe/Madrid
       ports:
            - 5055:5055
       volumes:
            - /home/juan/dockersFile/Jellyseerr/jellyseerrData:/app/config
            - /mnt/torrent/movies:/movies #optional
       restart: unless-stopped
```
</p>

El primer volumen será donde se almacene la configuración de la aplicación, así, en el momento de borrarla o actualizarla se mantendrán los archivos

El segundo volumen es donde se moverán las series y peliculas una vez estén descargados desde Sonarr o Radarr

Una vez creemos el fichero docker-compose.yml, en la ruta donde este el fichero ejecutamos: docker compose up -d

Para más información: https://hub.docker.com/r/fallenbagel/jellyseerr

## Radarr

docker-compose.yml

<p> 

```docker
services:
  radarr:
    image: lscr.io/linuxserver/radarr:latest
    container_name: radarr
    environment:
      - PUID=1000
      - PGID=1000
      - TZ=Europe/Madrid
    volumes:
      - /home/juan/dockersFile/Radarr/radarrData:/config
      - /mnt/torrent/movies:/movies #optional
      - /mnt/torrent/00_Downloads/complete:/downloads #optional
    ports:
      - 7878:7878
    restart: unless-stopped
```
</p>

El primer volumen corresponde a la configuración de la aplicación

El segundo volumen corresponde a la ruta donde dejará las peliculas que se descarguen desde nuestra aplicación Torrent, Radarr las moverá a esta ruta, se configurará en la aplicación

El tercer volumen corresponde a una ruta que deberemos mapear con la ruta donde nuestra aplicación de Torrent descargue. Así Radarr podrá verificar que se descargaron y además es la ruta donde irá a buscar los archivos que descargue para moverlos al anterior volumen

## Sonarr

docker-compose.yml

<p> 

```docker
services:
  sonarr:
    image: lscr.io/linuxserver/sonarr:latest
    container_name: sonarr
    environment:
      - PUID=1000
      - PGID=1000
      - TZ=Europe/Madrid
    volumes:
      - /home/juan/dockersFile/Sonarr/sonarrData:/config
      - /mnt/torrent/03_Sonarr/tv/:/tv #optional
      - /mnt/torrent/03_Sonarr/downloads/:/downloads #optional
    ports:
      - 8989:8989
    restart: unless-stopped
```
</p>

El primer volumen corresponde a la configuración de la aplicación

El segundo volumen corresponde a la ruta donde dejará las series que se descarguen desde nuestra aplicación Torrent, Sonarr las moverá a esta ruta, se configurará en la aplicación

El tercer volumen corresponde a una ruta que deberemos mapear con la ruta donde nuestra aplicación de Torrent descargue. Así Sonarr podrá verificar que se descargaron y además es la ruta donde irá a buscar los archivos que descargue para moverlos al anterior volumen