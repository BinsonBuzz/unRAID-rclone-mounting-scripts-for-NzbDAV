# Native Storage Mounting & Library Maintenance for Infinidysk & Decypharr

This repository provides a robust automation toolkit for managing native FUSE/DFS mounts and media stack health on Unraid. It is specifically designed for **Infinidysk V2** and **Decypharr**, ensuring your cloud/DFS media mounts stay active, container updates run smoothly without race conditions, and your array shuts down cleanly.

---

## What's in the Box?

You get two core scripts:

### 1. Mount Script
Run on a cron to continuously checks Infinidysk V2 and Decypharr mounts.
* **Auto-Healing & Lockdown:** If a mount drops, it safely halts dependent media apps (e.g. Silo, Plex, Arr apps etc) to prevent database corruption, restarts storage containers, and restores clients sequentially once mounts are verified.
* **Integrated Daily Auto-Updates:** Features an interlocked container update engine. Uses a persistent date-stamp token (`last_container_update.date`) to ensure `docker compose pull` and `up -d` run **exactly once per day**, completely eliminating update race conditions.
* **Hard Reset Mode:** Instantly clears process locks, halts client dockers, restarts storage engines, and resets system state.

### 2. Array Stop Script
Executes at **Array Stop** to guarantee a 100% clean shutdown without hanging drives.
* **Container Lockdown:** Gracefully stops all active Docker containers and background sentinel monitors.
* **Database & Cache Safety:** Properly flushes and stops database containers (Postgres, Redis) and flushes filesystem caches.
* **Unmount & Pool Export:** Unmounts storage paths and cleanly exports ZFS pools to eliminate "unclean shutdown" parity resyncs.

---

## How It Works

The scripts operate on a **Foundation & Consumer** architecture. The **Heartbeat** script guarantees that the foundation (Infinidysk & Decypharr native mounts) is active before allowing consumer applications (Plex, Arr suite, Silo) to run. If the foundation fails or is undergoing its scheduled daily update, health checks pause automatically to prevent database errors or missing library scans.

---

## Assumptions

* You are running **Unraid** with the **User Scripts** plugin installed.
* Your media mounts reside on native pools (e.g., `/mnt/cache/streaming/nzbdav`).
* Your media stacks are organized in standard Docker Compose projects .

---

## Quick Setup Guide

1. **Configure:** Open each script and check Section `1.0 USER CONFIGURATION` at the top. Set your mount paths, container names, and preferred `UPDATE_TIME` (default: `04:00`).
2. **Deploy via User Scripts Plugin:**
   * **Native Storage Heartbeat:** Set to run on a custom cron schedule (`*/5 * * * *` for every 5 minutes).
   * **The Janitor:** Set to run **At Array Stop**.

---

## Support

For detailed setup discussions and assistance, visit the [Unraid Support Thread](https://forums.unraid.net/topic/198498-rclone-scripts-to-mount-nzbdav-to-create-a-large-plex-library-with-fast-launch-times-and-efficient-arr-usage/).

---

## Disclaimer

**Use at your own risk.** These scripts are provided "as-is" for personal use. Always verify your configuration paths before deploying to production environments.
