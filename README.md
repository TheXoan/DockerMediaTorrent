# DockerMediaTorrent
### Configuración de (Jellyseerr - Sonarr - Radarr - Prowlarr - Jacket - Transmission) sobre Docker

Creación y configuración de servidor MediaTorrent sobre docker. Esta no es una guía didáctica sino práctica, enseñaré como configurar los contenedores y daré algún consejo. Además mostraré algún error con el que me encontré.

Los DockerFiles los crearé en carpetas separadas en /home/juan/dockersFile/. Aquí crearé una carpeta por cada contenedor donde habrá el docker-compose.yml y una carpeta llamada _nombreappData_

También tendré un recurso compartido por SMB con las siguientes opciones: //ip/RecursoCompartido /mnt/torrent cifs    defaults,uid=1000,gid=1000,credentials=/root/.smb.cred,file_mode=0777,dir_mode=0777,nobrl,iocharset=utf8     0       0 "

Todos los contenedores están sobre la misma máquina virtual

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

- El primer volumen será donde se almacene la configuración de la aplicación, así, en el momento de borrarla o actualizarla se mantendrán los archivos

- El segundo volumen es donde se moverán las series y peliculas una vez estén descargados desde Sonarr o Radarr

- Una vez creemos el fichero docker-compose.yml, en la ruta donde este el fichero ejecutamos: docker compose up -d

Para más información: https://hub.docker.com/r/fallenbagel/jellyseerr

TIPS:
- En Jellyseerr se pueden usar los usuarios que tengamos en Jellyfin si lo tenemos instalado
- Para que nos muestre los títulos en español debemos seleccionarlo en ajustes:
![Imagen](Images/LanguageJellyseerr.png)

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

- El primer volumen corresponde a la configuración de la aplicación

- El segundo volumen corresponde a la ruta donde dejará las peliculas que se descarguen desde nuestra aplicación Torrent, Radarr las moverá a esta ruta, se configurará en la aplicación

- El tercer volumen corresponde a una ruta que deberemos mapear con la ruta donde nuestra aplicación de Torrent descargue. Así Radarr podrá verificar que se descargaron y además es la ruta donde irá a buscar los archivos que descargue para moverlos al anterior volumen

Para más información: https://hub.docker.com/r/linuxserver/radarr

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

- El primer volumen corresponde a la configuración de la aplicación

- El segundo volumen corresponde a la ruta donde dejará las series que se descarguen desde nuestra aplicación Torrent, Sonarr las moverá a esta ruta, se configurará en la aplicación

- El tercer volumen corresponde a una ruta que deberemos mapear con la ruta donde nuestra aplicación de Torrent descargue. Así Sonarr podrá verificar que se descargaron y además es la ruta donde irá a buscar los archivos que descargue para moverlos al anterior volumen<

Para más información: https://hub.docker.com/r/linuxserver/sonarr

## Prowlarr

docker-compose.yml

<p> 

```docker
services:
  prowlarr:
    image: lscr.io/linuxserver/prowlarr:latest
    container_name: prowlarr
    environment:
      - PUID=1000
      - PGID=1000
      - TZ=Europe/Madrid
    volumes:
      - /home/juan/dockersFile/Prowlarr/prowlarrData:/config
    ports:
      - 9696:9696
    restart: unless-stopped
```
</p>

- El primer y único volumen se corresponderá a la configuración de la aplicación.

En este caso no tendremos más volumenes ya que el intercambio de indexers se hará a través de API

_NOTA: Prowlarr es una aplicación muy nueva y aún en desarrollo por lo la mayoría de indexers que tiene son en inglés,si como yo buscas torrents en español, la mejor opción es Jackett que es el servicio que instalaremos a continuación_

Para más información: https://hub.docker.com/r/linuxserver/prowlarr

## Jackett

docker-compose.yml

<p> 

```docker
services:
  jackett:
    image: lscr.io/linuxserver/jackett:latest
    container_name: jackett
    environment:
      - PUID=1000
      - PGID=1000
      - TZ=Europe/Madrid
      - AUTO_UPDATE=true #optional
      #- RUN_OPTS= #optional
    volumes:
      - /home/juan/dockersFile/Jackett/jackettData:/config
      - /mnt/torrent/01_Links:/downloads # Optional
    ports:
      - 9117:9117
    restart: unless-stopped
```
</p>

- El primer volumen corresponde como siempre a la configuración de la aplicación
- El segundo volumen corresponde al repositorio "Blackhole" donde podremos indicar que deje las descargas de torrents para que, por ejemplo, a través de Transmission los coja automáticamente cuando detecte un torrent nuevo. Pero para este caso, al igual que con Prowlarr, los indexers se los pasaremos tanto a Radarr como a Sonarr a través de API

Para más información: https://hub.docker.com/r/linuxserver/jackett

## Transmission

docker-compose.yml

<p> 

```docker
services:
  transmission:
    image: lscr.io/linuxserver/transmission:latest
    container_name: transmission
    environment:
      - PUID=1000
      - PGID=1000
      - TZ=Europe/Madrid
      - USER=username
      - PASS=contraseña
      #- WHITELIST= Ejemplo: 172.16.20.*,172.16.2.*,172.16.3.*,10.73.0.*
    volumes:
      - /home/juan/dockersFile/Transmission/transmissionData:/config
      - /mnt/torrent/00_Downloads:/downloads
      - /mnt/torrent/01_Links/:/watch
    ports:
      - 9091:9091
      - 51413:51413
      - 51413:51413/udp
    restart: unless-stopped
```
</p>

- El primer volumen al igual que en los demás casos corresponde a la configuración de la aplicación
- El segundo volumen será donde Transmission descargue por defecto los torrents
- El tercer volumen será donde Transmission estará esperando que se la añadan los archivos torrent para descargarlos automáticamente