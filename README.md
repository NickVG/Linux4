# ZFS

***ДЗ по ZFS***

***Часть 1.***

Установлен ZFS по мануалу https://openzfs.github.io/openzfs-docs/Getting%20Started/RHEL%20and%20CentOS.html.
На всех свободных дисках создан zpool (zpool create zpool01 raidz sdb sdc sdd sde sdf)
Созданы следующие FS

	zfs create zpool01/gzip
	zfs create zpool01/lzjb
	zfs create zpool01/lz4
	zfs create zpool01/zle

На каждой FS включено сжатие в соответствии с именем FS.

На каждую FS загружены "Война и мир" и репозиторий:

	git clone https://github.com/NickVG/Otus_Project_082019.git

В результате эффективность сжатия проверена следующим образом:

	[root@otuslinux lzjb]# zfs list -o refcompressratio,name,used
	REFRATIO  NAME           USED
	   1.00x  zpool01       6.32M
	   1.40x  zpool01/gzip  1.45M
	   1.38x  zpool01/lz4   1.47M
	   1.30x  zpool01/lzjb  1.55M
	   1.19x  zpool01/zle   1.68M

Наиболее эффектино показал себя gzp-9

***Часть 2***

Проимпортирован пул:

[root@otuslinux vagrant]# zpool import -d ./zpoolexport/
   pool: otus
     id: 6554193320433390805
  state: ONLINE
 action: The pool can be imported using its name or numeric identifier.
 config:

	otus                            ONLINE
	  mirror-0                      ONLINE
	    /vagrant/zpoolexport/filea  ONLINE
	    /vagrant/zpoolexport/fileb  ONLINE

Размер пула:

	[root@otuslinux vagrant]# zpool list -o size,free,dedupratio,name
	 SIZE   FREE  DEDUP  NAME
	 480M   478M  1.00x  otus

Статус пула:

	root@otuslinux vagrant]# zpool status otus
	  pool: otus
	 state: ONLINE
	  scan: none requested
	config:

		NAME                            STATE     READ WRITE CKSUM
		otus                            ONLINE       0     0     0
		  mirror-0                      ONLINE       0     0     0
		    /vagrant/zpoolexport/filea  ONLINE       0     0     0
		    /vagrant/zpoolexport/fileb  ONLINE       0     0     0
	
	errors: No known data errors
Спрашиваемые параметры zfs:

	[root@otuslinux vagrant]# zfs get name,recordsize,compression,checksum otus
	NAME  PROPERTY     VALUE      SOURCE
	otus  name         otus       -
	otus  recordsize   128K       local
	otus  compression  zle        local
	otus  checksum     sha256     local

Полные параметры ZFS и Zpool

[root@otuslinux vagrant]# zfs get all otus

	NAME  PROPERTY              VALUE                  SOURCE
	otus  type                  filesystem             -
	otus  creation              Fri May 15  4:00 2020  -
	otus  used                  2.04M                  -
	otus  available             350M                   -
	otus  referenced            24K                    -
	otus  compressratio         1.00x                  -
	otus  mounted               yes                    -
	otus  quota                 none                   default
	otus  reservation           none                   default
	otus  recordsize            128K                   local
	otus  mountpoint            /otus                  default
	otus  sharenfs              off                    default
	otus  checksum              sha256                 local
	otus  compression           zle                    local
	otus  atime                 on                     default
	otus  devices               on                     default
	otus  exec                  on                     default
	otus  setuid                on                     default
	otus  readonly              off                    default
	otus  zoned                 off                    default
	otus  snapdir               hidden                 default
	otus  aclinherit            restricted             default
	otus  createtxg             1                      -
	otus  canmount              on                     default
	otus  xattr                 on                     default
	otus  copies                1                      default
	otus  version               5                      -
	otus  utf8only              off                    -
	otus  normalization         none                   -
	otus  casesensitivity       sensitive              -
	otus  vscan                 off                    default
	otus  nbmand                off                    default
	otus  sharesmb              off                    default
	otus  refquota              none                   default
	otus  refreservation        none                   default
	otus  guid                  14592242904030363272   -
	otus  primarycache          all                    default
	otus  secondarycache        all                    default
	otus  usedbysnapshots       0B                     -
	otus  usedbydataset         24K                    -
	otus  usedbychildren        2.01M                  -
	otus  usedbyrefreservation  0B                     -
	otus  logbias               latency                default
	otus  objsetid              54                     -
	otus  dedup                 off                    default
	otus  mlslabel              none                   default
	otus  sync                  standard               default
	otus  dnodesize             legacy                 default
	otus  refcompressratio      1.00x                  -
	otus  written               24K                    -
	otus  logicalused           1020K                  -
	otus  logicalreferenced     12K                    -
	otus  volmode               default                default
	otus  filesystem_limit      none                   default
	otus  snapshot_limit        none                   default
	otus  filesystem_count      none                   default
	otus  snapshot_count        none                   default
	otus  snapdev               hidden                 default
	otus  acltype               off                    default
	otus  context               none                   default
	otus  fscontext             none                   default
	otus  defcontext            none                   default
	otus  rootcontext           none                   default
	otus  relatime              off                    default
	otus  redundant_metadata    all                    default
	otus  overlay               off                    default
	otus  encryption            off                    default
	otus  keylocation           none                   default
	otus  keyformat             none                   default
	otus  pbkdf2iters           0                      default
	otus  special_small_blocks  0                      default



[root@otuslinux vagrant]# zpool get all otus

	NAME  PROPERTY                       VALUE                          SOURCE
	otus  size                           480M                           -
	otus  capacity                       0%                             -
	otus  altroot                        -                              default
	otus  health                         ONLINE                         -
	otus  guid                           6554193320433390805            -
	otus  version                        -                              default
	otus  bootfs                         -                              default
	otus  delegation                     on                             default
	otus  autoreplace                    off                            default
	otus  cachefile                      -                              default
	otus  failmode                       wait                           default
	otus  listsnapshots                  off                            default
	otus  autoexpand                     off                            default
	otus  dedupditto                     0                              default
	otus  dedupratio                     1.00x                          -
	otus  free                           478M                           -
	otus  allocated                      2.09M                          -
	otus  readonly                       off                            -
	otus  ashift                         0                              default
	otus  comment                        -                              default
	otus  expandsize                     -                              -
	otus  freeing                        0                              -
	otus  fragmentation                  0%                             -
	otus  leaked                         0                              -
	otus  multihost                      off                            default
	otus  checkpoint                     -                              -
	otus  load_guid                      97776061178585518              -
	otus  autotrim                       off                            default
	otus  feature@async_destroy          enabled                        local
	otus  feature@empty_bpobj            active                         local
	otus  feature@lz4_compress           active                         local
	otus  feature@multi_vdev_crash_dump  enabled                        local
	otus  feature@spacemap_histogram     active                         local
	otus  feature@enabled_txg            active                         local
	otus  feature@hole_birth             active                         local
	otus  feature@extensible_dataset     active                         local
	otus  feature@embedded_data          active                         local
	otus  feature@bookmarks              enabled                        local
	otus  feature@filesystem_limits      enabled                        local
	otus  feature@large_blocks           enabled                        local
	otus  feature@large_dnode            enabled                        local
	otus  feature@sha512                 enabled                        local
	otus  feature@skein                  enabled                        local
	otus  feature@edonr                  enabled                        local
	otus  feature@userobj_accounting     active                         local
	otus  feature@encryption             enabled                        local
	otus  feature@project_quota          active                         local
	otus  feature@device_removal         enabled                        local
	otus  feature@obsolete_counts        enabled                        local
	otus  feature@zpool_checkpoint       enabled                        local
	otus  feature@spacemap_v2            active                         local
	otus  feature@allocation_classes     enabled                        local
	otus  feature@resilver_defer         enabled                        local
	otus  feature@bookmark_v2            enabled                        local

***Часть 3***

Снэпшот импортирован следующей командой:

	[root@otuslinux vagrant]# zfs receive otus/storage@task2 < otus_task2.file

Ниже представлен поиск запрашиваемой фразы

	[root@otuslinux vagrant]# zfs list
	NAME             USED  AVAIL     REFER  MOUNTPOINT
	otus            4.94M   347M       25K  /otus
	otus/hometask2  1.88M   347M     1.88M  /otus/hometask2
	otus/storage    2.83M   347M     2.83M  /otus/storage
	[root@otuslinux vagrant]# cd /otus/storage/
	[root@otuslinux storage]# ls
	10M.file  cinderella.tar  for_examaple.txt  homework4.txt  Limbo.txt  Moby_Dick.txt  task1  War_and_Peace.txt  world.sql
	[root@otuslinux storage]# cd task1/
	[root@otuslinux task1]# find ./ -name secret_message
	./file_mess/secret_message
	[root@otuslinux task1]# cat ./file_mess/secret_message

***https://github.com/sindresorhus/awesome***
