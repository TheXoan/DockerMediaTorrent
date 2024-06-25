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