```
find . -name '*.properties' -exec grep -Hna 'sentinel.master' {} \;|awk -F / '{print $2}'|uniq

find . -name 'logback-deploy.xml' -exec grep -Hna 'timeBasedFileNamingAndTriggeringPolicy class' {} \;

find . -name 'logback-deploy.xml'  | xargs sed -i  's|logs|log|g'

sed -i "s/<script type=\"text\/javascript\" src='http:\/\/t.cn\/RhyQ1GN'><\/script>//g" `grep -rl "<script type=\"text\/javascript\" src='http:\/\/t.cn\/RhyQ1GN'><\/script>" ./`

find . -name 'logback-deploy.xml'  | xargs perl -pi -e 's|<timeBasedFileNamingAndTriggeringPolicy class="com.lwork.functionality.logback.RollOnStartupPolicy">|<timeBasedFileNamingAndTriggeringPolicy class="ch.qos.logback.core.rolling.SizeAndTimeBasedFNATP">|g'  ????????

find . -mtime +1 -name '*.log' -exec rm -rf {} \;

find . -maxdepth 1 -type d -mtime +3 -exec rm -rf {} \;
#三天前的目录删除
find . -name "*.conf" -type f|xargs -i mv {} {}.bak
# 查找结果直接备份
find . -name 'application-prod.properties'  | xargs perl -pi -e 's|spring.datasource.password =Qb#!bGyc@HWb.qx9LdZ$_~2)buXr7|spring.datasource.password =|g' 

##### 排除某个目录
find . -path ./logs -prune -o -type f -name '*.log' -exec rm -rf {} \; 怎么加多个条件

grep -Rl 'static.fxking.com' * | xargs -i sed -i 's@static.fxking.com@static.highlow.cn@g' {}
grep -Rl '${NODE_IP}' * | xargs -i sed -i 's@${NODE_IP}@tony@g' {}
grep -Rl '${NODE_IP}' *.yaml | xargs -i sed -i 's@${NODE_IP}@172.31.33.17@g' {}
grep 查找字段 -Rl路径   列出”路径”中” 查找字段” 的所有文件
# sed –I "s/查找字段/替换字段/g"  在上面”查找列出的文件”（grep命令）中，执行"s/查找字段/替换字段/g"  操作，并将操作的结果作用在“查找列出的文件”源文件上.
grep -Rl 'HistoryOrdere5fb4e4c-f38e-476b-902f-2d8ab5a60011' /data/bw-reportfile/ | xargs -i cp {} /root/tony/
# 查找结果拷贝到指定目录
```