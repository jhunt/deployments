jhunt/deployments
=================

I'm running Kubernetes on a single box at home, and this is what I
deploy.  You are free to borrow them and run them yourself.

I use a ZFS pool called `dat`, which gets mounted to `/dat`
intrinsically.  Volumes under that pool (i.e. `/dat/media`) are
used to store the persistent data, which I just `hostPath:` into
the pods.  Remember: this works for a **SINGLE-NODE CLUSTER
ONLY**.

Current Volumes
---------------

```
# zpool list
NAME   SIZE  ALLOC   FREE  EXPANDSZ   FRAG    CAP  DEDUP  HEALTH  ALTROOT
dat   1.81T   587G  1.24T         -     0%    31%  1.00x  ONLINE  -

# zfs list
NAME             USED  AVAIL  REFER  MOUNTPOINT
dat              587G  1.18T   120K  /dat
dat/homes        108K  1.18T   108K  /dat/homes
dat/media        408G  1.18T   408G  /dat/media
dat/plex        6.66G  1.18T  6.66G  /dat/plex
```

| Volume | Purpose | Related Files |
| ====== | ======= | ============= |
| dat/homes | `$HOME` for per-subsystem users | _none_ |
| dat/media | My audio/video media collection | [plex.yml](plex.yml), and [ytdl.yml](ytdl.yml) |
| dat/plex  | Metadata / configuration storage for Plex Media Server | [plex.yml](plex.yml) |

Current Subsystems
------------------

### Plex Media Server

I run a Plex Media Server in a container, so that I can stream my music
collection to my TV.  Yeah, that's about it.  I'm boring.

The media lives in `dat/media`, which is readonly-mounted into the Plex
container so that Plex can read and stream, but can't modify, delete,
overwrite, or otherwise affect my curated audio files -- I've got _years_
of time sunk into those bit patterns!

Plex itself is a deployment with a single pod.  It is defined in
[plex.yml](plex.yml).

Some times, I like to download long OST remixes, classical music, etc. and
stream those from Plex.  To make that easier, I wrote [ytdl.yml](ytdl.yml)
to spin up Kubernets Jobs to run one-off `youtube-dl` runs, based on my
[filefrog/ytdl][1] image ([also available on GitHub][2]).

> To be perfectly honest, this _might_ be a bit over-engineered.  It was fun
> though!

The helper `ytdl` [helper script](ytdl) takes a Youtube URL, formulates a
unique job name based on that, and creates the job to do the download / rip
/ recode the stream to something Plex can stream.  For this, each Job gets a
writeable mount of `/dat/media`.

Future Direction
----------------

I've got lots of plans for this thing: small side-projects that I can now
run, NAS backups, etc.  If you find any of this useful, hit me up on
Twitter, [@iamjameshunt][twitter].



[1]: https://hub.docker.com/r/filefrog/ytdl
[2]: https://github.com/jhunt/ytdl

[twitter]: https://twitter.com/iamjameshunt
