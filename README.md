# ROS Jazzy container configuration guide
To implement this template first make sure you have a proper ros workspace that colcon can build ([guide](https://docs.ros.org/en/jazzy/Tutorials/Beginner-Client-Libraries/Creating-A-Workspace/Creating-A-Workspace.html)). Feel free to treat this template as the base workspace - it should work.

## Clone the repo
```bash
git clone https://github.com/wisniax/template_ws.git your_name
```

## First configuration
1. Add all package dependencies to `.devcontainer/Dockerfile` to line after comment: 
    > \# Add required ros packages here
    
    **Note!** This is the only line in entire `.devcontainer/Dockerfile` that's supposed to be modified! Any other changes might not be persistent.
2. Modify `docker-compose.yml` and there:
    1. Change ssh port: `"[ssh-port-here]:22"` to port assigned in internal ros related miro board for your repo.
    2. Change every instance of `ros-template` to your repo name. Please for convenience follow the pattern: `ros-[repo-name]`.
3. Modify `.devcontainer/docker-entrypoint.sh` and change `[ssh-port-here]` below comment:
    > \# Change ssh port below to the one assigned in docker-compose.yml
4. Copy all ros nodes to `src/`
5. Replace `.git` catalogue with proper one
6. Modify `master-launch.yaml` so that it starts ros nodes in your repo properly.
    > **Note!** This is required for autostart feature and integrated, preconfigured vscode debugging.
7. Set proper include paths for linter to `.vscode/c_cpp_properties.json` to `"includePath"`. For example:
    ```json
    "includePath": [
        "/opt/ros/jazzy/include/**",
        "/usr/include/**",
        "${workspaceFolder}/src/can_bridge/include",
        "${workspaceFolder}/src/libVescCan/include",
        "${workspaceFolder}/src/can_bridge/libVescCan/include"
    ],
    ```
8. ### That's IT!

> **Note!** Feel free to delete the above section above from this README once everything works. It's a one time config after all.

# Setting things up

## Build the container
`docker compose build`
> To enable passwordless ssh into rex: \
> Copy your public ssh files to .devcontainer/public_keys.d \
> **Note!** Do this step before building the container or rebuild after!

## Start the container
`docker compose up -d`

### To stop the container
`docker compose down`

### Check container logs
`docker compose logs -f`

### Environment variables
> **Note!** To modify those edit `docker-compose.yml` file
- `ROS_ENABLE_AUTOSTART` *(somewhat works)*
    - **`0`** (default) - ROS autostart disabled
    - `1` - ROS autostart enabled
- `ROS_BUILD_ON_STARTUP` *(to be implemented)*
    - `never` - No build on startup. \
    No boot performance inpact.
    - **`on-failure`** (default) - Try build only once when *ROS_ENABLE_AUTOSTART* is enabled and the startup failed (any error). 
    On successful build, startup will be retried. \
    High boot performance impact, but only when rebuild is needed (typically only once).
    - `always` - Always build on startup. \
    Very high boot performance impact.

# Container features
## SSH into the container
`ssh rex@[ip-here] -p [port-here]`
e.g. `ssh rex@localhost -p 2123`

## Build the repo
> Obviously only works from inside the container ;)

`cd raptor_ws`

`colcon build --symlink-install`
> If colcon fails with permission denied run:
`chmod g+rw -R /home/rex/raptor_ws`

## Using the rex service
### Stop the rex main ros program:
`sudo service rex stop`

### Start the rex main ros program:
`sudo service rex start`

### Tail logfile
`sudo service rex logs`
