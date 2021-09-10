# Nonsys-Win-Docker
How to modify Windows 10 to run Docker Desktop and WSL on a non-system (C:) drive.

## Move the Docker installation & service dependencies
1. Download and install Docker for Windows Desktop.
  - https://docs.docker.com/desktop/windows/install/
2. Copy all docker installation files to a non-system drive.
  - Default source: `C:\Program Files\Docker\Docker`
  - Example destination: `Z:\Docker\`
3. Open PowerShell as Administrator.
4. Stop the Docker service.
  - `sc.exe stop docker`
5. Navigate to the new Docker location where you copied the installation files to in step 2.
  - `cd Z:\Docker\`
6. Unregister the Docker service.
  - `./dockerd --unregister-service`
7. Register the Docker service with a new data-root.
  - `./dockerd --register-service --data-root Z:\Docker\`
8. Modify the Docker binary path for the Docker service.
  - `sc.exe config com.docker.service binPath="Z:\Docker\com.docker.service`
9. Modify the System PATH environment variable for Docker to the new destination:
  - Default: `C:\Program Files\Docker\Docker\resources\bin`
  - Updated: `Z:\Docker\resources\bin`
11. Create or modify the daemon configuration JSON file:
  - `C:\ProgramData\Docker\config\daemon.json`
  - Add the line: `"data-root": "Z:\\Docker"`
  - Save and close the file.

## Move the WSL .vhdx files
1. Stop the Docker service.
2. Open PowerShell as Administrator.
3. Shutdown all WSL distros:
  - `wsl --shutdown`
4. Export docker-desktop-data to a .tar file:
  - `wsl --export docker-desktop-data Z:\docker-desktop-data.tar`
5. Unregister the current docker-desktop-data location:
  - `wsl --unregister docker-desktop-data`
6. Import the docker-desktop-data from the .tar file in the new location:
  - `wsl --import docker-desktop-data Z:\Docker\data Z:\docker-desktop-data.tar --version 2`

Docker should now be successfully moved to the new location on your non-system drive. Enjoy!

Don't forget to modify your desktop & start menu shortcuts to the updated Docker binary location:
  - Right-click the shortcut in: `C:\ProgramData\Microsoft\Windows\Start Menu`
  - Change the Target field to: `"Z:\Docker\Docker Desktop.exe"`


Credit to the following sources:

https://forums.docker.com/t/how-could-i-install-docker-for-windows-on-drive-e-my-ssd-c-is-full/52347/9

http://thedebugdude.com/install-docker-in-d-drive/

https://dev.to/kimcuonthenet/move-docker-desktop-data-distro-out-of-system-drive-4cg2
