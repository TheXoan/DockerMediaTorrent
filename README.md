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
            - /mnt/torrent/02_Radarr/movies:/movies #optional
       restart: unless-stopped
```
</p>

