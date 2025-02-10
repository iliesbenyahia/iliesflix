![Iliesflix in Action](./misc/iliesflix-logo.gif)

# Iliesflix: Media Management Stack

Iliesflix is a self-hosted media management stack built with Docker containers. The stack includes Sonarr, Radarr, Jellyfin, and more, all routed through Traefik as a reverse proxy. This setup allows you to access services via `yourdomain/servicename`.

---

## Table of Contents

1. [Overview](#overview)
2. [Stack Components](#stack-components)
3. [Prerequisites](#prerequisites)
4. [Setup Instructions](#setup-instructions)
5. [Customization](#customization)
6. [Troubleshooting](#troubleshooting)

---

## Overview
Iliesflix uses Docker to deploy a collection of media tools behind a Traefik reverse proxy. This configuration ensures easy access via URLs with base paths and simplifies management through a unified stack.

### Key Features
- Media organization with Sonarr and Radarr
- Streaming via Jellyfin
- Torrent management with qBittorrent
- URL routing through Traefik with custom base paths

---

## Stack Components

### Services
- **Sonarr**: TV series management.
- **Radarr**: Movie management.
- **Jellyfin**: Media streaming server.
- **Prowlarr**: Indexer manager.
- **qBittorrent**: Torrent client (image has a built in vpn support)
- **Jellyseerr**: Request management for Jellyfin.
- **Joal**: Fake seeder on torrents, not sharing is not cool i know :

### Traefik Configuration
Traefik routes all services with base path URLs (e.g., `yourdomain/sonarr`).

---

## Prerequisites

1. **Docker and Docker Compose**
   - Install Docker: [https://docs.docker.com/get-docker/](https://docs.docker.com/get-docker/)
   - Install Docker Compose: [https://docs.docker.com/compose/install/](https://docs.docker.com/compose/install/)

2. **Traefik Network**
   - Create the Traefik network:
     ```bash
     docker network create traefik
     ```

3. **Domain Setup**
   - Replace `yourdomain` with your actual domain or subdomain in the `docker-compose.yml` labels.

---

## Setup Instructions

1. **Clone Repository**
   ```bash
   git clone <repository-url>
   cd iliesflix
   ```

2. **Configure Volumes**
   - The stack uses an NFS-mounted directory on your NAS (or any other storage)  to store media files. Each service’s media path points to a specific subdirectory within this NAS/storage mount. This setup ensures compatibility with hardlinks and instant moves, as described in the [Trash Guides](https://trash-guides.info/File-and-Folder-Structure/Hardlinks-and-Instant-Moves/).
   
     - Example directory structure on your NAS (or any other storage):
       ```
       /srv/nas/
       ├── downloads/
       ├── tv/
       ├── movies/
       └── music/
       ```
     - The volumes in `docker-compose.yml` map to these paths, ensuring seamless interaction across services:
       - `/srv/nas/tv/` for Sonarr
       - `/srv/nas/movies/` for Radarr
       - `/srv/nas/downloads/` for qBittorrent
       - `/srv/nas/medias/` for Jellyfin

     - Create the necessary directories locally:
       ```bash
       mkdir -p sonarr/config radarr/config prowlarr/config qbittorrent/config jellyfin/config jellyseerr/config
       ```
   
3. **Update `docker-compose.yml`**
   - Replace `yourdomain` in the labels with your actual domain.

4. **Start the Stack**
   ```bash
   docker-compose up -d
   ```

5. **Verify Services**
   - Access the services via their URLs:
     - Sonarr: `http://yourdomain/sonarr`
     - Radarr: `http://yourdomain/radarr`
     - Jellyfin: `http://yourdomain/jellyfin`

---

## Customization

### Base Path Adjustments
- The `PathPrefix` in each service's Traefik labels ensures routing through `yourdomain/servicename`. You can modify these paths if needed.

### Environment Variables
- Adjust `PUID`, `PGID`, and `TZ` in the `docker-compose.yml` file to match your environment.

### Adding New Services
- Follow the pattern in `docker-compose.yml` to add new services with Traefik routing.

### VPN 
- I personnaly use ProtonVPN. The qbittorrent image used here is from hotio.dev and has builtin support for it. You'll need to download your wireguard conf from your vpn provider and put it in the right folder (qbitorrent/wireguard/wg0.conf for instance). Check here : `https://hotio.dev/containers/qbittorrent/` 

### Joal
- Joal is useful when you download torrents from private trackers and when it's too difficult for you to seed as much as you leech. Indeed, these trackers check that you are sharing at least as much as you downloads. But it can be hard to seed has much, especially when your upload speed is low ... Do i need to justify myself ?   
If you want to use Joal, you'll need to download and unpack different torrent clients and a config.json file : `https://github.com/anthonyraymond/joal/`
Check the readme for more details (or feel free to ask me :) ! )

---

## Troubleshooting

### Common Issues
- **Containers not starting**: Check for errors with:
  ```bash
  docker-compose logs <container_name>
  ```
- **Traefik routing issues**: Verify domain and base path configuration in the Traefik labels.

### Logs and Debugging
- View logs for individual containers:
  ```bash
  docker logs <container_name>
  ```

---

## Acknowledgments

- **Sonarr**: [https://sonarr.tv/](https://sonarr.tv/)
- **Radarr**: [https://radarr.video/](https://radarr.video/)
- **Jellyfin**: [https://jellyfin.org/](https://jellyfin.org/)
- **Traefik**: [https://traefik.io/](https://traefik.io/)
- **Joal**: [https://github.com/anthonyraymond/joal/](https://github.com/anthonyraymond/joal/)
- **TrashGuides**: [https://trash-guides.info/](https://trash-guides.info)
