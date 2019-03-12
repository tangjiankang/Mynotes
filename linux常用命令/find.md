```
find . -name '*.properties' -exec grep -Hna 'sentinel.master' {} \;|awk -F / '{print $2}'|uniq

find . -name 'logback-deploy.xml' -exec grep -Hna 'timeBasedFileNamingAndTriggeringPolicy class' {} \;

find . -name 'logback-deploy.xml'  | xargs sed -i  's|logs|log|g'

sed -i "s/<script type=\"text\/javascript\" src='http:\/\/t.cn\/RhyQ1GN'><\/script>//g" `grep -rl "<script type=\"text\/javascript\" src='http:\/\/t.cn\/RhyQ1GN'><\/script>" ./`

find . -name 'logback-deploy.xml'  | xargs perl -pi -e 's|<timeBasedFileNamingAndTriggeringPolicy class="com.lwork.functionality.logback.RollOnStartupPolicy">|<timeBasedFileNamingAndTriggeringPolicy class="ch.qos.logback.core.rolling.SizeAndTimeBasedFNATP">|g'  ????????

find . -mtime +1 -name '*.log' -exec rm -rf {} \;

find . -name "*.conf" -type f|xargs -i mv {} {}.bak

find . -name 'application-prod.properties'  | xargs perl -pi -e 's|spring.datasource.password =Qb#!bGyc@HWb.qx9LdZ$_~2)buXr7|spring.datasource.password =|g' 

##### 排除某个目录
find . -path ./logs -prune -o -type f -name '*.log' -exec rm -rf {} \; 怎么加多个条件

find . -mtime +14 -name '*.log' | xargs rm -rf 需要调整

grep -Rl 'static.fxking.com' * | xargs -i sed -i 's@static.fxking.com@static.highlow.cn@g' {}
```