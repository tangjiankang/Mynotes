新建用户worker，家目录/home/worker
直接修改/etc/passwd 的worker改家目录为/data/logstash/logs

或者
useradd -d /data/test -s /bin/bash worker
