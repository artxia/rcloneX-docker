
# rcloneX-docker
群晖docker挂载<br>
支持世-纪-互-联-onedrive<br>
支持同-济-sharepoint

### 感谢原项目：
[mumiehub/rclone-mount](https://hub.docker.com/r/mumiehub/rclone-mount)<br>
[rclone](https://github.com/rclone/rclone)

# Usage Example:

    docker run -d --name rclonex-docker \
        --restart=unless-stopped \
        --cap-add SYS_ADMIN \
        --device /dev/fuse \
        --security-opt apparmor:unconfined \
        -e RemotePath="mediaefs:" \
        -e MountCommands="--allow-other --allow-non-empty" \
        -v /path/to/config:/config \
        -v /host/mount/point:/mnt/mediaefs:shared \
        artxia/rclonex-docker


> mandatory docker commands:

- --cap-add SYS_ADMIN --device /dev/fuse --security-opt apparmor:unconfined


> needed volume mappings:

- -v /path/to/config:/config
- -v /host/mount/point:/mnt/mediaefs:shared


# Environment Variables:

| Variable |  | Description |
|---|--------|----|
|`RemotePath`="mediaefs:path" | |remote name in your rclone config, can be your crypt remote: + path/foo/bar|
|`MountPoint`="/mnt/mediaefs"| |#INSIDE Container: needs to match mapping -v /host/mount/point:`/mnt/mediaefs:shared`|
|`ConfigDir`="/config"| |#INSIDE Container: -v /path/to/config:/config|
|`ConfigName`=".rclone.conf"| |#INSIDE Container: /config/.rclone.conf|
|`MountCommands`="--allow-other --allow-non-empty"| |default mount commands, (if you not parse anything, defaults will be used)|
|`UnmountCommands`="-u -z"| |default unmount commands|
|`AccessFolder`="/mnt" ||access with --volumes-from rclone-mount, changes of AccessFolder have no impact because its the exposed folder in the dockerfile.|


## Use your own MountCommands with:
```vim
-e MountCommands="--allow-other --allow-non-empty --dir-cache-time 48h --poll-interval 5m --buffer-size 128M"
```

All Commands can be found at [https://rclone.org/commands/rclone_mount/](https://rclone.org/commands/rclone_mount/). Use `--buffer-size 256M` (dont go too high), when you encounter some "Direct Stream" problems on Plex Server for example.

## Troubleshooting:
When you force remove the container, you have to `sudo fusermount -u -z /host/mount/point` on the hostsystem!


# 群晖
如果遇到挂载参数错误、`linux mounts: path xx is mounted on xx but it is not a shared mount`、`Docker API 失败`等问题时，需要执行`sudo mount --make-shared /volume1`

群晖docker只能用代码挂载，挂载成功后不要使用GUI界面进行任何修改，改了就出错。出错只能删了重建。
